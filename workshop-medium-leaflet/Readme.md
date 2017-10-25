# Workshop: How to get things interactive? 

**Step 1.** Clone or Download this repository.
You can use the index.html with the Leaflet example to view your own map. Try it! Open the `index.html` file in your browser. There are already 3 images for you to explore! 

**Step 2.** Scan your map or Download an image
Do you have your own map? Let's get it digital and see if we can convert it to tiles so you can add it to the Leaflet example. 

**Step 3** Get GDAL2Tiles

If you are not confident to use this command line tool yourself, ask someone to do it for you! Skip the next headings and scroll right to the end of this page. 

There are also some tutorial online, that explain how to get your image interactive! 

- [How to make an interactive story map using leaflet and non geographical images](https://medium.com/@jarednielsen/how-to-make-an-interactive-story-map-using-leaflet-and-non-geographical-images-821f49ff3b0d)
- http://build-failed.blogspot.nl/2012/11/zoomable-image-with-leaflet.html

## GDAL2Tiles
We can use this tool to run on any image we have. 


**Step 1.** Install gdal2tiles.py 

Prerequisites:

- Install Python2.7
- Install GDAL-core
- Install GDAL-Python bindings

	sudo apt-get install python-gdal

And under `/usr/bin/` you will have `gdal2tiles.py`.

Documentation: http://www.gdal.org/gdal2tiles.html

**Step 2.** run gdal2tiles

To create map tiles from any given image, run gdal2tiles.py from your console:

	gdal2tiles.py -p raster -w none <image> 

The created tiles can be used in Leaflet by using Leaflet's tms option:

	L.tileLayer(tileUrl, {tms: true}).addTo(map);


### gdal2tiles-leaflet

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

### Docker

For convenient usage we already created a gdal2tiles-leaflet docker image. Just run:

	docker run --rm -v `pwd`:/data niene/gdal2tiles-leaflet -l -p raster -z 0-5 -w none /data/<image> /data/<tilesdir> 

More info see https://github.com/NieneB/gdal2tiles-leaflet 

# workshop continue

**step 4.** Put tiles in Leaflet.
When you have your directory structure with your image cut up into tiles, it is time to put it in Leaflet!

Open the index2.html file in your text editor. 

Change the `L.tileLayer('folder/to/tiles/{z}/{x}/{y}.png')` to the path with your tiles. Like this

		// the tile layer containing the image generated with `gdal2tiles --leaflet -p raster -w none <img> tiles`
		L.tileLayer('Meso/{z}/{x}/{y}.png', {
		  noWrap: true
		}).addTo(map)

If you want to know how this works, learn more about Leaflet, have a look at the [Leaflet beginners workhop](https://github.com/NieneB/Webmapping_for_beginners/wiki/Introduction-Leaflet)

Open the index.html file in your browser and see your image! 

**Step 5.** get correct view.
It would be nice if the centre of your map, is also the centre of your image. And maybe you want to restrict panning and zooming to the borders of your image. Let's see how to do this:

Have a look at the `.xml` file that came with your tiles. You can find this in your folder of your tiles. 
We need the following information:

- Height and with of image in px
- The zoom level where 1 px = 1 unit

Open the `index3.html` file in a text editor and add this information to the script:

		//Image dimensions
		var mapheight = 5500;
		var mapwidth = 4066;
		var zoomlevel = 5; // Level 5, because this is the level where meters-per-pixel is exactly 1

Note! We also gave our map a coordinate system. Namely `L.CRS.Simple`, which represents a square grid.

	var map = L.map('map', {
	    crs: L.CRS.Simple
	});

In a CRS.Simple, one horizontal map unit is mapped to one horizontal pixel, and idem with vertical. If you want to know more, have a look at http://leafletjs.com/examples/crs-simple/crs-simple.html.

The next step is to make a working coordinate reference system that coincides with our image! See the next workshop part. 