# About Serving Tiles from Amazon Web Services Compared to Local Tile Servers
## Important to know: Local tiles and AWS tiles are DIFFERENT!
You may have asked yourself the question, can I run a set of tiles that I have on my local machine, or do I have
to grab the tiles from AWS S3? The answer to your question is YES! You can run tiles from your local machine. However,
you need to know that the environment on your local machine is different from that
on AWS. There are two important differences: 

First, on AWS, the tiles are stored in a Simple Storage Service (S3) 'bucket.' We have told AWS to 'serve' the contents of that bucket
so that they may be used as a website. This is possible because AWS uses a simple web (HTTP) server to deliver the contents of the 
bucket to a web browser. On your local machine, the Vue framework provides a development server that 
delivers the content to a browser.
 
Second, since AWS is a web resource, all the content on AWS has a URL.

These two things combine to influence the way tiles are served runs in each environment. First, as we discuss in a moment,
Mapbox-gl requires map tiles to be identified by as a web resource. That means it needs the tiles to have a URL in order to 
use them. This is fine on AWS, as all the resources have URLs, however locally this will require us to use a 
tile server to generate a URL from which Mapbox-gl can fetch the tiles. Secondly, while all the resources on
AWS have URLs, AWS provides only a simple HTTP server to get those assets. Locally, our tile server(s) will do just a
bit more. The local tile server will take a single '.mbtiles' tile file, break it apart, and serve each part as an individual tile.

Now, we could have added a tile server to our application when deployed on AWS, but we wanted to keep our application
deployment simple (and inexpensive). Application deployments in AWS can use a S3 bucket as a source, only if they are static.
And by 'static' I mean they are applications that run on a user's browser with the browser doing all the processing, and there
is no need for server-side-processing. Adding a tile server would mean we have added server-side-processing and we would no 
longer be able to use S3 as our host; we would have needed to switch to a more complex and expensive Elastic Compute Cloud (EC2)
instance. 

To avoid jumping up in cost and complexity to EC2, we compensated for the AWS HTTP server's inability to clip up '.mbtiles' files
by doing that part ourselves. So, on AWS we store the map tiles not as a single '.mbtiles' file but as a folder of folders,
with each folder containing sets of '.pbf' files that represent map 'zoom' level. Since we have manually done the tile cutting,
the simple HTTP server on AWS can do the rest.

Why is this important:
- on AWS, the the map tiles are 'cut' into '.pbf' files.
- on your local machine, you will have a single '.mbtiles' file (you may have several of these, but the each represent a single map layer)
- on AWS, the simple HTTP server will deliver the tile files
- on your local machine, you will need to start a tile server for each '.mbtiles' file
- on your local machine you can serve '.pbf' tiles by running a local HTTP too

## Check that URLs for the tiles are correct
Locally, the tiles for the map will be served by 'tile servers.' These servers will need to know where the
tiles are stored on your machine and will produce a URL that Mapbox-gl can call. When Mapbox-gl calls,
the tile server will serve up the appropriate tiles. 

Please note that Mapbox-gl requires the map tiles to have a full URL, for example 'http://localhost:8086/data/basemap.json.'
or 'wbeep-test-website.s3-website-us-west-2.amazonaws.com.' This is works fine for web based resources but makes using a relative path impossible, which means
that Mapbox-gl will require you to have a different URL for every deployment option. The path Mapbox-gl
uses are found in the style variable of the component creating the map element, such as 'MapBox.vue.'

```
    let style = {
        version: 8,
        sources: {
            basemap: {
                type: "vector",
                url: "http://localhost:8086/data/basemap.json" // here is one URL
            },
            HRU: {
                type: "vector",
                url: "http://localhost:8085/data/new2.json", // here is another

```
In the above example there are two URLs, one for the base map and a second one for the HRUs. Notice that
these are both local URLs. These URLs may differ slightly from what is required on your local machine. The main 
change would be the file names, which in the example are 'basemap.json' and 'new2.json.' These file names
need to match the names of the tile files (except the ending will be '.json' and not '.mbtiles'), which we will talk about later.

Now, since there are two URLs, we will need two tile servers, one to produce each URL. A perfectly good choice for
a tile server is tileserver-gl-light: https://www.npmjs.com/package/tileserver-gl-light . It is available as a npm
package. Follow the instructions on the tilesserver-gl-light page and install the tile server globally (use the -g flag).

## Start run the tile server
Now that you have a tile server installed locally, you can get the map up and running in the application.
The tileserver will need two items of information, the location of the tiles, and the port address on which 
to run.

```
tileserver-gl-light basemap.mbtiles -p 8086
```
The above example works when starting the tile server from directory containing the tile file to be used. In this case, the tile file
is named 'basemap.mbtiles. That is just an example name. The exact name may be different.
 
Using port 8086 is an arbitrary choice, but since the Vue development server is usually running on port 8080 this keeps conflicts to a 
minimum.  Also, the port number must match the port listed in the URL of the 'style' variable, as 
shown in the code snippet a few sections above and directly below.
```
url: "http://localhost:8086/data/basemap.json" 
// the server for the base maps must use port 8086 or at least match what is in the URL
```
Just as side note, notice that the file name in the above command matches the name of the tile file not 