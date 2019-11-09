# Mapbox GL - Tile Layers Overzoom
'Overzoom' is a feature of Mapbox gl that allows it to make an educated 'guess' and display a map layer for tiles that
don't really exist in the tile set. This works for both raster and vector tiles. However, a nice feature of vector 
map tiles is that the ability to zoom in past the 'real' zoom level only slightly reduces image quality -- loss in quality is greater for raster tiles. 
Yet, even with vector tiles 'overzoom' creates some loss of precision, so good judgment should be used in situations where 
the exactness of the map is paramount. 
However, in many situations having a mapping library such as Mapbox gl js mathematically extend zooms levels is an 
acceptable option. To get this to work you need to keep a few things in mind.

Mapbox gl js sets the map zoom level extents in three different places
```
1) Where the map is created
2) Where the source is declared
3) Where the layer is defined
```
Let's talk about these separately. First, when the map is created we can set a pair of zoom level parameters.
```
Here is an example of setting map creation parameters in an application using the Vue framework and Vue components. This 
code would be located in the 'script' section.
        },
        data() {
            return {
                mapStyle: mapStyles.style,
                container: 'map',
                zoom: 3,
                minZoom: 3,
                maxZoom: 24,
                center: [-75.072994, 40.544454],
                pitch: 0, // tips the map from 0 to 60 degrees
                bearing: 0, // starting rotation of the map from 0 to 360
                hoveredHRUId: null,
                legendTitle: 'Legend'
            }
        },
```
Setting the 'minZoom' (note the term 'minZoom' is not the 'official' Mapbox term, which is 'minzoom' (all one word, all lower case), 
I can't really get into a full explanation of why this now, other than to say it is a Vue framework related thing, so just pretend that the term used is 'minzoom',
and we will move on). Notice that the minimum and maximum here are 3, and 24. The complete range of zoom levels of
which Mapbox gl is capable of rendering is 0 to 24. The setting of 0 represents the entire world (mercator projection) in 
view port (the area of the screen in which the map is allowed to show), while 24 represents an extremely zoomed in version.
A internet search of 'mapbox zoom levels' will give you a full explanation of the topic. 

What the above code does is set the 'hard stops' of the map. The map will not zoom out past zoom level 3 (which is just a bit bigger than the size of the continental US) nor will it
zoom in past level 24. Of course, level 24 is the limit that Mapbox gl can display, so that line could be omitted without a 
change in application behavior, however for completeness will leave it in.  

So far this all seems straight forward and sensible, however the next steps required to tell Mapbox gl to overzoom are 
not totally intuitive. As mentioned above, there are three places in which we tell Mapbox gl how to control zoom levels.
The second one mentioned, 'Where the source is declared', is critical to getting the overzoom to function. 
```
// This is a snippet from the Mapbox style sheet (in our application it is usually /src/assets/mapStyles/mapStyles.js)
 sources: {
    openmaptiles: {
        type: 'vector',
        'tiles': ['http://wbeep-test-website.s3-website-us-west-2.amazonaws.com/openmaptiles/{z}/{x}/{y}.pbf'],
        "minzoom": 0,
        "maxzoom": 14
    },
```
It is important to note that the 'minzoom' and 'maxzoom' are not required parameters. Leaving them out will cause the map
to default to real tile extents. The main advantage of setting these parameters to the high and low end zoom levels of your 
tile set is that Mapbox gl will not attempt to look for any source layers outside of this range. This saves many wasted 
web calls for resources that don't exist. 
```
Important stuff about setting the zoom level parameters in the source
1)  If you do not specify a minimum or maximum zoom, your map layer for that tile will go blank. 
        when you zoom past the limit of your tile set
2) If you set the limits past the extents of your tile set, there are no ill effects, however your map layer for
        that tile will go blank just as mentioned above.  
3) If you set the 'minzoom' limit inside your tile extent, any tile above that zoom will appear be blank.
4) If you set the 'maxzoom' limit inside your tile extent, Mapbox gl will stop loading the real tiles and will 
        'overzoom' the remaining zoom levels to the limit you set in the first step of the section,
        back when the map was created.
5) If you set the 'maxzoom' limit to the match the limit of your tile set, Mapbox gl will begin to overzoom
        when the zoom level reachs the actual level of your tiles.
6) IMPORTANT - If you set the 'maxzoom' greater than the extent of your actual tileset,
     'overzoom' will NOT work.
```

The moral of the above is that if you want Mapbox gl to 'overzoom' your tiles you must set the 'maxzoom' in source declaration
to less than or equal to your actual set extent.

Now this brings us to the third place where the zoom extents in the are defined, and that is at the layer level. So far
we have discussed how to control the zoom levels of the entire map (at map creation), of the entire source (when we
define the tile source), and now we will describe the zoom extents at the layer level and how that relates to 'overzoom'.
After the 'sources' are defined in the Mapbox style sheet the different layers are styled. Each vector tile 'source' can contain
many 'layers', and each of those can be given 'minzoom' and 'maxzoom' parameters. 
```
Example of a 'layer' defined in the Mapbox gl style sheet
            {
                'filter': ['all', ['==', '$type', 'LineString'],
                    ['!in', 'brunnel', 'tunnel', 'bridge'],
                    ['==', 'intermittent', 1]
                ],
                'id': 'waterway_intermittent',
                'paint': {
                    'line-color': 'hsl(205, 56%, 73%)',
                    'line-opacity': 1,
                    'line-width': {
                        'base': 1.4,
                        'stops': [
                            [8, 1],
                            [20, 8]
                        ]
                    },
                    'line-dasharray': [2, 1]
                },
                'minzoom': 3,  // here are the settings we have an interest in 
                'maxzoom': 24,
                'source': 'openmaptiles',
                'source-layer': 'waterway',
                'type': 'line',
                'layout': {
                    'visibility': 'visible'
                },
                'showButton': false,
                'inLegend' : false
            },
```
Just as with the map and the source definitions, layer definitions do not require you to set the 'maxzoom' or the 'minzoom'.
However, setting these will give you control as to the zoom level a particular layer will first appear. For example,
having a 'minzoom' set to 3, as above, in conjunction with the map and source settings, will cause the layer to show
as soon as the map appears. This is because map has a 'minzoom' of 3, which is the same as the layer. If we set the 
layer 'minzoom' to 8, for example, the user would have to zoom in five levels before the layer will show.

To make the 'overzoom' work for any given layer, we must set the layer 'maxzoom' to greater than the 'maxzoom' of the
source (and obviously the map). We also must have the source 'maxzoom' set to less than or equal to the real zoom levels
available in the tile set. 

To sum up, if you want to 'overzoom', you must 1) let the map allow it by setting the 'maxzoom' to a number greater than
your real tile set extent, 2) set the source 'maxzoom' definition to less than or equal to the real tile extent, and 3) set the 
layer definition to greater than the source definition but less or equal to the map 'maxzoom'. 