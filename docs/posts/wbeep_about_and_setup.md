# Water Budget Estimation and Evaluation Project (WBEEP) Visualization
This project produces an interactive, national scale, choropleth map using Hydrologic Resource Units (HRUs) as 
data groupings. A HRU is the smallest spatial unit of the Soil and Water Assessment Tool (SWAT) 
which is widely used to relate farm management practices to effects on surface waters at the
watershed scale. The HRU is not generally defined by physically meaningful boundaries but instead
lumps similar land uses, soils, and slopes within a sub-basin based upon user-defined thresholds. 

This project plots the boundaries of HRUs and colors the interior of each HRU based on calculated 
water data availability values using JavaScript, the Vue framework, and Mapbox-gl to produce a
'zoomable' national level map.

## Project setup
Clone the project. Then run 'npm install' to pull down the required dependencies into the node modules folder 
```
npm install
```

Starting the Vue development server at this point will show a website with headers and footers but no map.

```
npm run serve
```

## How the Map Gets Its Style

There is a file in the project called mapStyles.js (src/mapStyles/mapStyles.js). This is a configuration file for Mapbox and it tells Mapbox a bunch
of important details about how to create the map. What is important to us at the moment are two things, the 'source' and the 
'source-layer.' The 'source' is where Mapbox should look for the 'tiles.' The 'source-layer' is only important to vector
tile sources, so for something like pure GeoJson you will not need it, but since our tiles are vector we will need to make
sure our source layer is correct.

The mapStyles.js is really just a ES6 JSON object called 'style', so like all JSON objects it is made up of key/value pairs.
Of main concern is the 'sources' key. In the following example, we can see that there are two sources for our map layers.
one called 'basemap' and another called 'HRU.' Mapbox needs two bits of information to process the source correctly, the 
'type' and the location. Mapbox can process many different source types, the source we are using are vector, meaning mathematical
created lines rather than dot graphed. Our vector tiles are stored in Amazon Web Services (AWS) and we have to tell that to 
Mapbox. What is important here is to know that our 'tiles' on AWS are in 'protobuf' format (.pbf). Protobuf is one of several
tile formats that Mapbox can use. To load a '.pbf' source, we need the "tiles" key along with the resource location
in the form of an array as shown below 
```
    style: {
        version: 8,
        sources: {
            basemap: {
                type: "vector",
                "tiles": ["https://d38anyyapxci3p.cloudfront.net/baseTiles_3/{z}/{x}/{y}.pbf"]             
            },
            HRU: {
                type: "vector",
                "tiles": ["https://d38anyyapxci3p.cloudfront.net/tileTemp_3/{z}/{x}/{y}.pbf"]               
            }
        },
```

## What if your map is not working? It may be that 
1) the tiles are located in a different location on S3 (the above are just sample locations)
2) you do not have the right 'source-layer' name

