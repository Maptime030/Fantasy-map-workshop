# Fantasy Maps

#### When: Wednesday 25th October
#### Time: 19:00
#### Where: Social Impact Factory, Utrecht

Not all maps represent real things on the surface of the earth. Fantasy maps are a type of map design that is a visual representation of an imaginary of fictional geography. Think about maps for games, novels and posters, but also historical maps. Fantasy maps can be drafted by hand, computer assisted or even coded.
But how do you make a map of an imaginary world that fires up someone's imagination? How much does cartography and fantasy map drawing have in common? 

[Daniel Hasenbos](http://danielsmaps.com/) is a freelance cartographer specialized in hand-drawn fantasy maps. He makes maps for authors, publishers, game developers and any other projects that require artistic cartography.

"I want to share my creativity, to bring life to ideas and create a world that wasn’t there before. "

He will come show us his work and tell us all about how to make a fantasy map. After that we will work on our own fantasy map, based on a real editors story! 


### Program

19:00-19:30 Daniels Maps

19:30-20:00 Interactive Fantasy Maps

20:00- ...  Hands on! Make your own Fantasy Map!

**Maptime is Hands on! So bring your pencils, markers and laptop to this meetup!!**


### Contribute!

Maptime is all about learning and sharing! Do you make your own Fantasy Maps? Do you use some cool tools or have great skills that could contribute to this topic? Let us know! You can send [a message to one of our organizers](https://www.meetup.com/messages/?new_convo=true) if you have something you think we can add to the program! Feel free to bring your work to the Maptime evening! We would love to see what you are working on!


# Get inspired!
To get you started and interested, we already collected some links on the theme! 

This article: [The most incredible fantasy maps](https://io9.gizmodo.com/the-most-incredible-fantasy-maps-youve-ever-seen-474420566), gives an overview of some of the best and prominent fantasy maps. 

For example the map of the Land of Oz, which was one of the first maps to be included with a fantasy novel. Note the directions east and west have been switched. Because — Oz!

![Land of Oz](https://i.kinja-img.com/gawker-media/image/upload/s--JTCBma_n--/c_fit,fl_progressive,q_80,w_636/18kyaa8q2tn2xjpg.jpg)

# How to Draw a Fantasy map? 

These links will help you learn about cartographic tips and tricks for drawing your own fantasy world! 

The website [Fantastic Maps](http://www.fantasticmaps.com/category/tips-and-tricks/) provides you with all kinds of tips, tricks and tutorials. It is made by Jonathan Roberts who designed the official maps of Game of Thrones for the book The Lands of Ice and Fire. For example start with [how to draw a map]( http://www.fantasticmaps.com/2015/02/how-to-draw-a-map/).

Some other step by step tutorials can be found on [Fantasy Maps.com](http://fantasy-maps.com/tutorials/) by Herwin Wielink. 

And have a look at these [10 tips](https://io9.gizmodo.com/10-rules-for-making-better-fantasy-maps-1680429159) for making a fantasy map. 


# Interactive Fantasy Maps
More and more fantasy maps go beyond a static representation on paper as well as online. On the internet we can find various interactive fantasy maps, have a look at this map about [Game of Thrones](https://quartermaester.info/) (Spoiler control!) and the [GoT viewer guide](http://viewers-guide.hbo.com/game-of-thrones/season-1/episode-1/map/location/19/eyrie). Or the one about [Lord of the Rings](http://lotrproject.com/map/#zoom=3&lat=-1315.5&lon=1500&layers=B) which even has its own reference system for route planning!

After looking at some interactive maps online we will explain some tools and tricks for making one ourselves! A workshop is provided on how to transform your hand-drawn map (or historical map) to a interactive web map. 


----------------------------------
# Work in Progress!


# How to get things interactive? 

**Tools:** 
	- [GDAL2Tiles](http://www.gdal.org/gdal2tiles.html)
	- [GDAL2Tiles-Leaflet](https://github.com/commenthol/gdal2tiles-leaflet) from commenthol
	- [Leaflet-rastercoords plugin]()
	- [Leaflet.js](http://leafletjs.com/) 

**Workshops online:**
	-[How to make an interactive story map using leaflet and non geographical images](https://medium.com/@jarednielsen/how-to-make-an-interactive-story-map-using-leaflet-and-non-geographical-images-821f49ff3b0d)
	- http://build-failed.blogspot.nl/2012/11/zoomable-image-with-leaflet.html

## GDAL2Tiles example

This [example](http://maptime-ams.github.io/gdal2tiles-example/) uses a map of Corsica from the David Rumsey Map Collection. It is made by creating map tiles from an image using GDAL's gdal2tiles.

We can use this tool to run on any image we have. 

**Step 1.** Scan your map or Download an image

**Step 2.** Install gdal2tiles.py or gdal2tiles-leaflet

Prerequisites:

    Install Python2.7
    Install GDAL-core
    Install GDAL-Python bindings

To create map tiles from any given image, run gdal2tiles.py from your console:

	gdal2tiles.py -p raster -w none corsica.jpg

The created tiles can be used in Leaflet by using Leaflet's tms option:

	L.tileLayer(tileUrl, {tms: true}).addTo(map);


We can also use the [gdal2tiles-leaflet tool form commenthol](https://github.com/commenthol/gdal2tiles-leaflet).
It adds the option -l or --leaflet to generate the resulting images with the reference point [0,0] in the upper-left (North-West) corner, opposed to the standard behaviour for TMS tiles using the lower-left (South-East) corner.

Prerequisites:

	sudo apt install python-gdal

Download or clone the repository. https://github.com/commenthol/gdal2tiles-leaflet

Run the gdal2tiles.py from your console: 

	$ gdal2tiles.py -l -p raster -z 0-5 -w none <image> <tilesdir>

Note: The min zoom level for tile generation must be greater or equal to log2(max(width, height)/tilesize)

Assuming an image with 2000x3000 pixels:

	# take the larger dimension -> here height = 3000px
	$ echo "l(3000/256)/l(2)" | bc -l
	# 3.55 --> min zoomlevel for tile generation is 4
	# means: `gdal2tiles.py -l -p raster -z 0-2 ...`
	#                                          \__ is not allowed
	# correct usage
	$ gdal2tiles -l -p raster -z 0-4 ...


To use the generated tiles with Leaflet there is a small plugin to correctly set the required projection for raster images. Please refer to the documention at [leaflet-rastercoords](https://github.com/commenthol/leaflet-rastercoords).


### Docker

For convenient usage we already created a gdal2tiles-leaflet docker image. 

	docker run --rm -v `pwd`:/data niene/gdal2tiles-leaflet -l -p raster -z 0-5 -w none /data/<image> /data/<tilesdir> 

More info see https://github.com/NieneB/gdal2tiles-leaflet 


## Leaflet-rastercoords plugin

https://github.com/commenthol/leaflet-rastercoords




## Workshop 3. Fantasy Reference System CRS:simple

Tools: 
	- CRS:simple
	- RasterCoords

Workshops:
	- http://leafletjs.com/examples/crs-simple/crs-simple.html

## Workshop 4. Random fantasy maps with D3

https://github.com/mewo2/terrain
https://bl.ocks.org/Azgaar/b845ce22ea68090d43a4ecfb914f51bd
https://mewo2.com/notes/terrain/