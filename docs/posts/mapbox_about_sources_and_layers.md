# About Mapbox Sources and Layers
So, about sources and layers. Each source can be the parent of many map layers. Remember, that in the sample above, we had
two sources. That does not mean that we have only two layers. Each source can hold the information needed to make many layers.
In our map, we do just that. And we let Mapbox know which layers to make from each source by adding that information into the
Mapbox configuration file, which, as mentioned previously, is called mapStyles.js in our project.
```
            {
                id: "HRUS Fill Colors",
                type: "fill",
                source: "HRU",
                "source-layer": "no_simp_prec5", // make sure this name is right
                "layout": {
                    "visibility": "visible"
                },
                paint: {
                    "fill-color": {
                        "property": "SoilMoisture",
                        "type": 'categorical',
                        "stops": [
                            ["","#000000"],
                            ["very low","#CC4C02"],
                            ["low", "#EDAA5F"],
                            ["average","#FED98E"],
                            ["high","#A7B9D7"],
                            ["very high","#144873"],
                        ]
                    },
                    "fill-opacity": 1
                },
                "showButton": true
            },
``` 
Above, is a sample of that shows how a map layer is defined. The detail that is important here is to note the 'source-layer'
key. This has to match the name of the 'source-layer' in the tiles, and the name is defined by the person creating the tiles.
So, it sometimes changes. If you find that a layer is not showing as expected, check that the 'source-layer' has the correct name.