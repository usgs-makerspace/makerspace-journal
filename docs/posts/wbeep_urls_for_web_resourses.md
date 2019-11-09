# Water Budget Estimation and Evaluation (WBEEP) URLs for Application Tiers and Map Resources

For the WBEEP application here is the breakdown as of 11/7/2019.

## Water Budget Estimation and Evaluation (WBEEP) Deployment Tiers and Related URLs
1. 'test' - http://wbeep-test-website.s3-website-us-west-2.amazonaws.com/
2. 'qa' - http://wbeep-qa-website.s3-website-us-west-2.amazonaws.com/
3. 'beta'- http://wbeep-beta-website.s3-website-us-west-2.amazonaws.com/
3. 'beta with CloudFront' - https://d38anyyapxci3p.cloudfront.net/
4. 'prod' - http://wbeep-prod-website.s3-website-us-west-2.amazonaws.com/
4. 'prod with CloudFront' - http: https://d1drhovb0vmgar.cloudfront.net/

The water availability map for the Water Budget Estimation and Evaluation Project requires numerous web resources in order 
to display all the information we want to show.

## Water Budget Estimation and Evaluation (WBEEP) external resources 
Currently the application requires six data calls to external resources in order to render all the details of the 
water availability map. These data calls can be grouped into two sets: 1) calls to our Amazon Web Services (AWS)
Simple Storage Solutions (S3) bucket located at https://maptiles-prod-website.s3-us-west-2.amazonaws.com and 2) calls made to 
to tier specific resources on http://wbeep-<one of our four deployement tiers>-website.s3-website-us-west-2.amazonaws.com/

Calls made to https://maptiles-prod-website.s3-us-west-2.amazonaws.com (notice that these are HTTPS (secure) resources)
1. basemap (states and counties boundaries) - stored at https://maptiles-prod-website.s3-us-west-2.amazonaws.com/misctilesets/usstatecounties
2. NHD Stream Orders - stored at https://maptiles-prod-website.s3-us-west-2.amazonaws.com/nhdstreams_grouped
3. Hill Shading (terrain) - stored at https://maptiles-prod-website.s3-us-west-2.amazonaws.com/openmaptiles/omthillshade
4. OpenMapTiles base layer (streets, lakes, etc) -stored at https://maptiles-prod-website.s3-us-west-2.amazonaws.com/openmaptiles/baselayers

Calls made to tier specific S3 buckets.  
5. Hydrologic Response Units

    on the test tier - http://wbeep-test-website.s3-website-us-west-2.amazonaws.com/tiles/
    
    on the qa tier - http://wbeep-qa-website.s3-website-us-west-2.amazonaws.com/tiles/
    
    on the beta tier (secure) - https://d38anyyapxci3p.cloudfront.net/tiles
    
    on the prod tier (secure) -   https://d1drhovb0vmgar.cloudfront.net/tiles/
    
6. Model Date

    on the test tier - https://wbeep-test-website.s3-us-west-2.amazonaws.com/date/date.txt
    
    on the qa tier - https://wbeep-qa-website.s3-us-west-2.amazonaws.com/date/date.txt
    
    on the beta tier (secure) - https://d38anyyapxci3p.cloudfront.net/date/date.txt
    
    on the prod tier (secure) - https://d1drhovb0vmgar.cloudfront.net/date/date.txt

 



## Water Budget Estimation and Evaluation (WBEEP) Build Mode Cheat Sheet

While you are developing, the most common way to view the application on your local system
is to use the Vue development server with the command line expression . . .
```
// Run the Vue development server locally
npm run serve
```
This will give you the Vue development build, which is to say that Vue is compiling the code
in a way that makes it most useful for debugging. Running this command will also tell Vue to 
use 'test' environment variables (Vue Environment Variables are covered in more detail under the 'Vue Environment Variables'
section of this Journal). The short story is that using the 'test' variables will tell the
application to pull the date.txt (for the model date in the subtitle) and the Hydrologic Response Unit (HRU) tiles from
the test Amazon Web Services (AWS) Simple Storage Solutions (S3) bucket. 

This notation 'npm run serve', is a 'convenience' method to keep the serve command short and consistent between 
our Vue applications, as an alternative you can run 'npm run serve-test'. This may prove more convenient to you
as it follows a pattern that allows for the following serve and build options.
```
// Various WBEEP Vue CLI (command line interface) options
// the following four commands will allow you to build any of our four versions of the application and run 
// it locally 
npm run serve-test
npm run serve-qa
npm run serve-beta
npm run serve-prod

// the following four commands will allow you do build any of the four 'production' versions of the application
// running this command will not start the Vue Development server, so you will not be able to run the application
// on your local machine using it, however it will 'bundle' the application and send it to the 'dist' folder where 
// you can run it with a local HTTP server such as 'http-server' which can installed with something 
// like 'npm install http-server -g'.
npm run build-test
npm run build-qa
npm run build-beta
npm run build-prod
``` 

With the above commands, you will find that it is now possible to build any version of the application 
and run it locally. This provides an easy way to preview various application builds.

## Water Budget Estimation and Evaluation (WBEEP) Previewing the Application's CloudFront Tiers
So about Cloudfront . . . you may have noticed that some of data calls listed above reach out to weird 'cloudfront.net' URLs. CloudFront is an 
AWS service that, for us, mainly provides a route to obtaining HTTPS (secure) status. CloudFront basically
wraps our non-HTTPS S3 buckets in a secure cloak, how it does this, I have no idea, but rest assured that it 
does. 

The thing to be aware about when dealing with CloudFront is that it is a 'cache' of sorts and as a cache, it 
will often retain old information when you think it has new information. For example, if you are running a local version
of the application's 'beta' build it will access and someone changes the contents of the HRU tile set you may see 
the new changes or you may keep seeing the same old tiles for quite a while. The CloudFront cache, by default,
will check the cache for changes every 24 hours. So, it may be a long time before you see those changes come to 
your local environment. This means you have to be cautious about the trusting that CloudFront resources are the 
most recent version.

## Invalidation of CloudFront Resources.   
So let's say you are dealing with CloudFront resources and you are not sure if you have the 'real' newest
version or the 'cached' old version. One thing that you can do is check the 'invalidations' that have occurred on
that CloudFront 'distribution'. The easiest way of doing this is to use the AWS User Interface. 
```
Check the invalidation status on AWS
Sign into the AWS user interface
Select 'services'
Select 'CloudFront'
There you will find a listing of various CloudFront distributions
Look for one with a comment like 'WBEEP prod Cloudfront Distribution' or 'WBEEP beta Cloudfront Distribution' (depending on your needs)
Select the distribution you need
Once selected there should be a set of tabs one of which is 'Invalidations'
You will see a list of the most recent invalidations
``` 
From the list of invalidations you can see when the last time an invalidation occurred. 

Right now I bet you are saying, 'yeah, so what's an invalidation'. Well, for AWS CloudFront, invalidation means that you are marking the 
files as no longer needed/current/good and that CloudFront should trash them and replace them with new versions from
your 'origin' (the S3 bucket in which the files are stored). So, if an invalidation is listed as 'complete', and
the time listed is pretty near the current time, then chances are you have the newest files in your 'origin'. If the time
is quite a while ago, well, then you probably don't.

Now, if you see that the last invalidation was a while back and you think what the files in the CloudFront
cache is old, you can create your own invalidation to sync the files in the cache to the files in your
origin. 
```
Create an 'invalidation'
Click the 'Create Invalidation' button
enter '/*' (just the / and the *) 
This means invalidate everything in the cache.
You can also selectively remove items, but AWS charges per invalidation (with a 1000 free/month)
So if you are doing the a lot, it may be better to trash the whole cache rather than individual files
```
Then hit the 'Invalidate' button and the invalidation will start. It is important to note that the
invalidation process takes around 15 minutes. So be patient. When the process is done. You will see a 
message listing the invalidation as complete, at which time you will be able to see the newest content from your
origin through the CloudFront URL linked to that origin.
