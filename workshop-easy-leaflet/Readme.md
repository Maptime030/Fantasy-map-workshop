
# How to get things interactive? 

**Tools:**

- [GDAL2Tiles](http://www.gdal.org/gdal2tiles.html)
- [GDAL2Tiles-Leaflet](https://github.com/commenthol/gdal2tiles-leaflet) from commenthol
- [Leaflet-rastercoords plugin]()
- [Leaflet.js](http://leafletjs.com/) 

**Workshops online:**

- [How to make an interactive story map using leaflet and non geographical images](https://medium.com/@jarednielsen/how-to-make-an-interactive-story-map-using-leaflet-and-non-geographical-images-821f49ff3b0d)
- http://build-failed.blogspot.nl/2012/11/zoomable-image-with-leaflet.html

## GDAL2Tiles

This [example](http://maptime-ams.github.io/gdal2tiles-example/) uses a map of Corsica from the David Rumsey Map Collection. It is made by creating map tiles from an image using GDAL's gdal2tiles.

We can use this tool to run on any image we have. 

**Step 1.** Scan your map or Download an image

**Step 2.** Install gdal2tiles.py 

Prerequisites:

- Install Python2.7
- Install GDAL-core
- Install GDAL-Python bindings


	sudo apt-get install python-gdal

And under /usr/bin/ you will have gdal2tiles.py.

Documentation: http://www.gdal.org/gdal2tiles.html

**Step 3.** run gdal2tiles

To create map tiles from any given image, run gdal2tiles.py from your console:

	gdal2tiles.py -p raster -w none <image> 

The created tiles can be used in Leaflet by using Leaflet's tms option:

	L.tileLayer(tileUrl, {tms: true}).addTo(map);


## gdal2tiles-leaflet

We can also use the [gdal2tiles-leaflet tool form commenthol](https://github.com/commenthol/gdal2tiles-leaflet).
It adds the option -l or --leaflet to generate the resulting images with the reference point [0,0] in the upper-left (North-West) corner, opposed to the standard behaviour for TMS tiles using the lower-left (South-East) corner.

**Step 1.** Get gdal2tiles-leaflet
Prerequisites:

	sudo apt-get install python-gdal

Download or clone the repository. https://github.com/commenthol/gdal2tiles-leaflet

Unzip

**Step 2.** Run the tool

Run the gdal2tiles.py from the gdal2tiles-leaflet folder from your console: 

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


To use the generated tiles with Leaflet there is a small plugin to correctly set the required projection for raster images. Please refer to the documentation at [leaflet-rastercoords](https://github.com/commenthol/leaflet-rastercoords).

## Docker

For convenient usage we already created a gdal2tiles-leaflet docker image. Just run:

	docker run --rm -v `pwd`:/data niene/gdal2tiles-leaflet -l -p raster -z 0-5 -w none /data/<image> /data/<tilesdir> 

More info see https://github.com/NieneB/gdal2tiles-leaflet 
