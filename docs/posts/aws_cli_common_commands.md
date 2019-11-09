# AWS CLI Common Commands
A list of commands that will make life easier
```
Note - for any of this to work, you will need to install the Amazon Web Services Command Line Interface 
        along with the required credentials. Ask a team member for help with the credentials.

Vector Tiles - We have found that to upload vector tiles (pbf format) two flags are required
    1) --content-encoding 'gzip'
    2) --content-type 'application/x-protobuf'

    To copy contents of a local directory to a S3 bucket (run command inside directory containing the files)
        generic version - aws s3 cp <local directory> <s3 target bucket> --recursive --content-encoding 'gzip' --content-type 'application/x-protobuf'
        specific example - aws s3 cp . s3://wbeep-qa-website/tiles --recursive --content-encoding 'gzip' --content-type 'application/x-protobuf'

    You can also 'sync' the files to upload. This works fine on a bucket with a small about of contents, however there will
    be a long delay (15+ minutes) when this command is run on buckets with 50+GB of content. This is caused by the time needed for AWS-CLI
    to compare your local contents and the contents of the bucket. However, using 'sync' is a way to pick up an upload after your connection 
    has been severed (yes, it happens). 
    To 'sync' your local directory with a folder on AWS S3 use the following (run command inside directory containing the files) 
        generic version - aws s3 sync <local directory> <s3 target bucket> --content-encoding 'gzip' --content-type 'application/x-protobuf'
        specific example - aws s3 sync . s3://wbeep-qa-website/tiles --content-encoding 'gzip' --content-type 'application/x-protobuf'         

Rastor tiles - These tiles will use the same commands as above except they will need a different  content-type and content-type flags
    To copy
            generic version - aws s3 cp <local directory> <s3 target bucket> --recursive --content-encoding 'base64' --content-type 'image/png'
            specific example - aws s3 cp . s3://wbeep-qa-website/tiles --recursive --content-encoding 'base64' --content-type 'image/png'
    to sync
            generic version - aws s3 sync <local directory> <s3 target bucket> --content-encoding 'base64' --content-type 'image/png'
            specific example - aws s3 sync . s3://wbeep-qa-website/tiles --content-encoding 'base64' --content-type 'image/png'

In General
    To delete the contents of a directory on S3 
        And Yes, believe it or not, this will take the same amount of time that it took to upload the files
        generic version - aws s3 rm s3://mybucket/myFolder --recursive 
        specific version - aws s3 rm s3://maptiles-prod-website.s3-us-west-2.amazonaws.com/misctilesets/usstatecounties --recursive 

Exclude 
    For the commands listed you can add an additional flag to tell AWS CLI not to mess with some of the contents of 
    a S3 folder or bucket by using the 'exclude' flag.
        generic version - aws s3 rm s3://mybucket/myFolder --recursive --exclude "*.someFileExtension" --exclude "*/someFolder"
        specific version - aws s3 rm s3://maptiles-prod-website.s3-us-west-2.amazonaws.com/misctilesets/usstatecounties --recursive  -exclude "*/2/3"

```