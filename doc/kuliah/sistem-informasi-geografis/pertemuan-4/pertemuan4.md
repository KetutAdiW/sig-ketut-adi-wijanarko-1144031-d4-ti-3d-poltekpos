Overview

The Python Shapefile Library (pyshp) provides read and write support for the Esri Shapefile format. The Shapefile format is a popular Geographic Information System vector data format created by Esri. For more information about this format please read the well-written “ESRI Shapefile Technical Description - July 1998” located at http://www.esri.com/library/whitepapers/p dfs/shapefile.pdf . The Esri document describes the shp and shx file formats. However a third file format called dbf is also required. This format is documented on the web as the “XBase File Format Description” and is a simple file-based database format created in the 1960’s. For more on this specification see: http://www.clicketyclick.dk/databases/xbase/format/index.html

Both the Esri and XBase file-formats are very simple in design and memory efficient which is part of the reason the shapefile format remains popular despite the numerous ways to store and exchange GIS data available today.

Pyshp is compatible with Python 2.4-3.x.

This document provides examples for using pyshp to read and write shapefiles. However many more examples are continually added to the pyshp wiki on GitHub, the blog http://GeospatialPython.com, and by searching for pyshp on http://gis.stackexchange.com.

Currently the sample census blockgroup shapefile referenced in the examples is available on the GitHub project site at https://github.com/GeospatialPython/pyshp. These examples are straight-forward and you can also easily run them against your own shapefiles with minimal modification.

Important: If you are new to GIS you should read about map projections. Please visit: https://github.com/GeospatialPython/pyshp/wiki/Map-Projections

I sincerely hope this library eliminates the mundane distraction of simply reading and writing data, and allows you to focus on the challenging and FUN part of your geospatial project.

Examples

Before doing anything you must import the library.

>>> import shapefile
The examples below will use a shapefile created from the U.S. Census Bureau Blockgroups data set near San Francisco, CA and available in the github repository of the pyshp GitHub site.

Reading Shapefiles
To read a shapefile create a new “Reader” object and pass it the name of an existing shapefile. The shapefile format is acutally a collection of three files. You specify the base filename of the shapefile or the complete filename of any of the shapefile component files.

>>> sf = shapefile.Reader("shapefiles/blockgroups")
OR

>>> sf = shapefile.Reader("shapefiles/blockgroups.shp")
OR

>>> sf = shapefile.Reader("shapefiles/blockgroups.dbf")
OR any of the other 5+ formats which are potentially part of a shapefile. The library does not care about file extensions.

Reading Shapefiles from File-Like Objects
You can also load shapefiles from any Python file-like object using keyword arguments to specify any of the three files. This feature is very powerful and allows you to load shapefiles from a url, from a zip file, serialized object, or in some cases a database.

>>> myshp = open("shapefiles/blockgroups.shp", "rb")
>>> mydbf = open("shapefiles/blockgroups.dbf", "rb")
>>> r = shapefile.Reader(shp=myshp, dbf=mydbf)
Notice in the examples above the shx file is never used. The shx file is a very simple fixed-record index for the variable length records in the shp file. This file is optional for reading. If it’s available pyshp will use the shx file to access shape records a little faster but will do just fine without it.

Reading Geometry
A shapefile’s geometry is the collection of points or shapes made from verticies and implied arcs representing physical locations. All types of shapefiles just store points. The metadata about the points determine how they are handled by software.

You can get the a list of the shapefile’s geometry by calling the shapes() method.

>>> shapes = sf.shapes()
The shapes method returns a list of Shape objects describing the geometry of each shape record.

>>> len(shapes)
663
You can iterate through the shapefile’s geometry using the iterShapes() method.

>>> len(list(sf.iterShapes()))
663
Each shape record contains the following attributes:

>>> for name in dir(shapes[3]):
...     if not name.startswith('__'):
...         name
'bbox'
'parts'
'points'
'shapeType'

reference :https://pypi.python.org/pypi/pyshp
