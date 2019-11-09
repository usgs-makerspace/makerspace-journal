# Mapbox GL - Working Base Tile Layers Tips and Tricks
Makerspace has been on a quest to get base tile layers that we can use with our maps. We tried many different options. 
Including some internal USGS sources. None of these panned out. For a short time we had hope that we could use tiles served from
Mapbox as long as we stayed inside of the 'free tier', but we were later told that if it required use of an access token (and all Mapbox services require a token)
than we could not use it. The reasoning behind this is that Mapbox would then be acting as a 'cloud resource' which falls in the purview of CHS (Cloud Hosting
Solutions) and would require their official sanction, which they have not given. We also tested a number of free layers, including
those from http://maps.stamen.com, but they all had limitations (however http://maps.stamen.com, does have some very interesting
layer style examples that may be of use at some point).
 
In the end we decided to use OpenMapTiles. OpenMapTiles provides a base layer (with streets, waterways, etc) that is free to use in 
open source projects provided an 'attribution' is added to the map.
## MBUtil
Just as note, OpenMapTiles downloads come as a single '.mbtiles' file. Since we need the tiles to be in '.pbf' form to use on S3 we needed to open the '.mbtiles' file in some way. At first we attempted to convert the file to GeoJSON using the 'Tippecanoe' (https://github.com/mapbox/tippecanoe) program and this command, tippecanoe-decode yourmbtilesfile.mbtiles > yourjsonfile.json . This worked fairly well. The original '.mbtiles' for the United States OpenStreetMap vector tiles was about 7.2GB. The conversion to GeoJSON took about two hours and resulted in a file approximately 75GB. However we were stymied when attempting to convert the GeoJSON to the required '.pbf' format. The Tippecanoe command tippecanoe -e diretoryYouWantTheFilesIn/ -Z 3 -z 10 yourgeojsonfile.json seemed as if it would work. However, running this command seemed to invoke a process that may have run for several days as well as taking up all available hard drive space.

Rather than continue down a path that seemed doomed. We switched direction and found a program called MBUTIL (https://github.com/mapbox/mbutil). Using MBUTIL we were able to export the 7.5GB '.mbtiles' file to a directory of '.pbf' file with this command, mb-util world.mbtiles tiles (note the word 'tiles' denotes the name of a directory that must NOT already exist. MBUTIL was able to 'explode' the '.mbtiles' file to '.pbf's in less than 40 minutes.
``` 
A generalized example of using MBUTIL
mb-util yourtiles.mbtiles targetDirectory 
// note the 'targetDirectory' directory must not already exist 
// and this example also assumes the command is run from the directory containing the '.mbtiles' file.

Here is an specific example
mb-util openmaptiles_us.mbtiles pbf

```
Once we had the correct format, we attempted to upload them to S3. This proved to be a huge pain in the hinder, due to network issues and the large file size (75GB+). What did prove to be a saving grace was the use of the AWS CLI 'sync' command. This command wastes time for normal copy procedures where 'cp' is the more applicable choice. However, in this case, it was a life saver since the 'sync' command allows you to pick up where you left off after you have been disconnected do to VPN or other issues. Since 'sync' compares what we want to upload to want is already in the destination there is an initial delay in the process while AWS CLI processes the differences (with files loads this size, the compare was taking up to 15 minutes), However the payback is that you can recover from issues that interrupt the upload without having to start over.

This is an example of the correct commands
```
Loading all of the current working directory to S3:
aws s3 sync . s3://wbeep-test-website/openmaptiles/ --content-encoding 'gzip' --content-type 'application/x-protobuf'

Loading a specific directory
 - loading files from 14th zoom level locally, to 14 zoom level on S3:
aws s3 sync ./14 s3://wbeep-test-website/openmaptiles/14/ --content-encoding 'gzip' --content-type 'application/x-protobuf'.
```