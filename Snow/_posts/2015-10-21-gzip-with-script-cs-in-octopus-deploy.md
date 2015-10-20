---
layout: post
category: Octopus Deploy, scriptcs
title: GZip with scriptcs in Octopus Deploy
---

I had this crazy problem tonight. I wanted to upload all my assets for my website into AWS S3, but I needed to gzip them first before sending them.

Usually I do this in Team City with Grunt, but all my variable replacement is done in Octopus Deploy depending on the environment. 

* Staging = S3 URLs
* Production = CloudFront URLs

If the compression is done in Team City, variable replacement cannot happen on the GZipped file. 

I decided to move it to Octopus Deploy, my first thought was, could I run a node.js task in Octopus Deploy, the more I thought about it tho, I realised I don't want to install a dependency on node.js on the server.

Second thought was to use Powershell... but I'm a real powershell no0b. 

<!--excerpt-->

However! Octopus Deploy has this little button!


![New Step in Octopus Deploy showing 'C#' option][0]

OMG I can write... C# code... in my deployment process. 

SO I set out to read about `System.IO.Compression` on [MSDN](0).

Working from this I came up with the following script:

	using System;
	using System.IO;
	using System.IO.Compression;
	
	public static void Compress()
	{
	    var directory = Octopus.Parameters["Octopus.Action[Deploy Assets].Package.CustomInstallationDirectory"];
	    
	    Console.WriteLine("Trying to compress files in: " + directory);
	    
	    foreach (var fileToCompress in Directory.GetFiles(directory, "*.min.*", SearchOption.AllDirectories))
	    {
	        var fileInfo = new FileInfo(fileToCompress);
	
	        if (!fileInfo.FullName.EndsWith(".min.js") && !fileInfo.FullName.EndsWith(".min.css"))
	        {
	            continue;
	        }
	
	        var fileName = fileInfo.FullName.Replace(".min.js", "").Replace(".min.css", "") + ".gz" + fileInfo.Extension;
	
	        using (FileStream originalFileStream = fileInfo.Open(FileMode.Open))
	        using (FileStream compressedFileStream = File.Create(fileName))
	        using (GZipStream compressionStream = new GZipStream(compressedFileStream, CompressionMode.Compress))
	        {
	            originalFileStream.CopyTo(compressionStream);
	
	            Console.WriteLine("Compressed: " + fileInfo.FullName);
	        }
	    }
	}
	
	Compress();

I have a build step called **Deploy Assets** which has a bunch of sub steps. This has a package which stores all the CSS/Images/JavaScript files that have been generated in Team City.

In here I update the URLs in all the files, and then run a Compress JS, and finally upload them all to S3. 

![Octopus Deploy build steps][2]

Basically what this script does is iterates over all files in the deploy folder looking for anything that was already minified. 

    if (!fileInfo.FullName.EndsWith(".min.js") && !fileInfo.FullName.EndsWith(".min.css"))
    {
        continue;
    }

If it's not a `.min.js` or `.min.css` file, then it's ignored.

It then creates a new `fileName` stripping off the existing extension and appending `.gz` followed by the last extension of the file. 

e.g `main.min.css` becomes `main.gz.css` 

Then GZips the file and saves it.

Here's an example of the `main.js` file minified and compressed:

![Example compressed file][3]

And that's all there is to it. You can basically write any C# code you want. 

Please refer to [scriptcs website][4] for more information on how to load dependencies and such.


[0]: /images/octopus-deploy-script-cs-01.png
[1]: https://msdn.microsoft.com/en-us/library/system.io.compression.gzipstream(v=vs.110).aspx
[2]: /images/octopus-deploy-script-cs-02.png
[3]: /images/octopus-deploy-script-cs-03.png
[4]: http://scriptcs.net/