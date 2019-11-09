# Manual Upload of Tiles
If you ever need to manually upload tiles to AWS, this command works provided you have installed AWS-CLI
on your machine and that you have configured the credentials. 
```
// generic example
aws s3 cp <local directory> <s3 target bucket> --recursive --content-encoding 'gzip' --content-type 'application/gzip'

// Note: using the 'content-type' application/x-protobuf also seems to work, and may be the better choice
// Here a specific example
aws s3 cp . s3://wbeep-qa-website/tiles --recursive --content-encoding 'gzip' --content-type 'application/x-protobuf'
```
If you do not configure the file compression correctly, when the application is run, Mapbox-gl will not be able to read the tiles, leaving you with
a map-less web page and a cryptic error in the console.
```
Unimplemented type: 3
```
This means that everything is working except that the compression type is not what Mapbox can use. 
The tiles will have to be reloaded to AWS with the correct content-type and content-encoding.

A note on content type: Using '--content-type 'application/gzip' such as in this example,
aws s3 cp . s3://wbeep-qa-website/tiles --recursive --content-encoding 'gzip' --content-type
 'application/gzip', will work in some browsers. We found that Chrome has no problem using this
 encoding. However, it will cause the 'Unimplemented type: 3' error, as mentioned above, when 
 using Firefox.

 
A second option, which avoids the hassle of configuring the AWS-CLI credential is to use the AWS UI to upload the files. 
Here you can drag and drop the files for uploading, just make sure to set both the 'content-encoding' and the 'content-type.'
The content-encoding will be 'gzip', and the content-type will be application/x-protobuf Note that there is not
a specific item in the drop down menu for these choices, but they can be added in the text box.