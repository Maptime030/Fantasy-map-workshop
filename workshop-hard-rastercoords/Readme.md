## Leaflet-rastercoords plugin

Leaflet-rastercoords plugin is a Leaflet plugin for plain image map projection to display large images using tiles generated with gdal2tiles-leaflet.  Have a look at https://github.com/commenthol/leaflet-rastercoords

Have a look at `index.html` open it in your text editor and your browser.

	var rc = new L.RasterCoords(map, [mapwidth,mapheight])

The `RasterCoords` converts the image height and with in pixels to map units. So every map unit equals 1 px. We added a click event so you can discover for yourself. Click at the top most level of the map. This is point `[0,0]`. Now click at the bottom right, this is point `[ 4060, 5500]`. The with and height of our image in pixels! 

By using this coordinate system to project our coordinates we can make them readable for our pop-up!

	var coords = rc.project(event.latlng)

The marker however still has to be placed in the lat lon coordinates, so we need to unproject the marker location before adding it to the map:
		
		var marker = L.marker(rc.unproject(coords)).addTo(map)

## Transforming the scale! 

In order to show the right numbers, the same ones as on the image, we have to measure how many pixels is one map unit! In our case at zoom level 5, 1 px is 1 map unit. 

Have a look at the `.xml` file that came with your tiles. You can find this in your folder of your tiles. 
We need the following information:

- Height and with of image in px
- The zoom level where 1 px = 1 unit

#### the x scale

Look at one tile 5/1/20

![5/1/20](../workshop-medium-leaflet/Meso/5/1/20.png)

One stripe is 84px. (measure with Photoshop or GIMP!)
One stripe is 0.25 on our image axis. 

So after some math:

|zoom   |       |unit                    |object|
|-------|-------|--------------          |------------|
|zoom 5 | 84 px | 84 map units           | CRS.Simple |
|zoom 5 | 84 px | 0.25 map units         | Meso image | 
|zoom 5 | 1 px  | 0.0027654879 map units | Meso image|

More Math:

		Our conversion factor is 0.0027654879.
		The scale = 1/factor = 336
		mapwith/tilewidth = factor
		tile width = 256 px
		map width = 0.7619
		offset = (map width * scale) / 2
		offset = 127.9992 

#### the y scale

Now do the same for the y-axis.

![5/0/3](../workshop-medium-leaflet/Meso/5/0/3.png)

|zoom   |       |unit                    |object|
|-------|-------|--------------          |------------|
|zoom 5 | 163 px | 163 map units | CRS.Simple |
|zoom 5 | 163 px | 0.25 map units | Meso image | 
|zoom 5 | 1 px  | 0.001533742 map units | Meso image|

		Our conversion factor is 0.001533742.
		The scale = 1/factor = 652
		mapwith/tilewidth = factor
		tile width = 256 px
		map width = 0.0.392637
		offset = (map width * scale) / 2
		offset = 127.999972352 

Let's use this in our example! 

#### Making a custom CRS

		var factorx = 0.002765487
		var factory = 0.001533742
		var offsetx = 127.9992
		var offsety = 127.999972352

		//Create custom coordinate system
		L.CRS.mySimple = L.extend({}, L.CRS.Simple, {
		  projection: L.Projection.LonLat, //flat 
		  transformation: new L.Transformation(factorx, offsetx, -factory, offsety ),
		  // Changing the transformation is the key part, everything else is the same.
		  // By specifying a factor, you specify what distance in meters one pixel occupies (as it still is CRS.Simple in all other regards).
		  // In the actual project, I compute all that from the gdal2tiles tilemapresources.xml, 
		  // which gives the necessary information about tilesizes, total bounds and units-per-pixel at different levels.
 
		// Scale, zoom and distance are entirely unchanged from CRS.Simple
		  scale: function(zoom) {
		    return Math.pow(2, zoom);
		  },

		  zoom: function(scale) {
		    return Math.log(scale) / Math.LN2;
		  },

		  distance: function(latlng1, latlng2) {
		    var dx = latlng2.lng - latlng1.lng,
		      dy = latlng2.lat - latlng1.lat;

		    return Math.sqrt(dx * dx + dy * dy);
		  },
		  infinite: true
		});


		//Create Map with CRS.mySimple
		var map = L.map('mapid', {
			crs: L.CRS.mySimple
		}).setView([0,0],2);

Now our scale units are set to our map units!

### Markers with custom coordinates

This little bit of code uses the `L.RasterCoords` plug-in again. 
And a lot is happening to make the coordinates right.. 

	//doing a lot of things here! 
		var rc = new L.RasterCoords(map, [4066,5500])
		map.on('click', function (event) {
		  // any position in leaflet needs to be projected to obtain the image coordinates
		  var coords = rc.project(event.latlng);
		  var marker = L.marker(rc.unproject(coords)).addTo(map);
		  marker.bindPopup('[' + Math.round(((coords.x)*factorx)+68.930309678)  + ',' + Math.round(78.226993708-(((coords.y)*factory)+38.12)) + ']')
		    .openPopup()
		})

The `latlng` coordinates of the click event are projected with the `L.RasterCoords` plug-in. Now the coordinates are in pixels. We know our conversion factor. So multiplying our coordinate with this factor gives us the right distance from 0,0 ! 

**Note!** our x-ax and y-ax have a different transformation factor. If your grid is square instead of rectangle, as with this example, they are the same. 

Because our scales do not start with at 0, but at 79 and 39, we need to add this to it. Also the margin of where the scale starts is subtracted from this! The scale starts with an offset of 000 px which is ... map units. 

The y-ax is turned around!! The starting point of this reference system is at the top left corner. Our scale runs from bottom to top. That is why we need to invert the y-ax by subtracting it from its total range. 


**Note!** the image is slightly skewed... So our coordinate system does not quite fit exactly.. But hey! getting it working approximately was already a hell of a job! 