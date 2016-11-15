Writing Shapefiles

The PSL tries to be as flexible as possible when writing shapefiles while maintaining some degree of automatic validation to make sure you don’t accidentally write an invalid file.

The PSL can write just one of the component files such as the shp or dbf file without writing the others. So in addition to being a complete shapefile library, it can also be used as a basic dbf (xbase) library. Dbf files are a common database format which are often useful as a standalone simple database format. And even shp files occasionaly have uses as a standalone format. Some web-based GIS systems use an user-uploaded shp file to specify an area of interest. Many precision agriculture chemical field sprayers also use the shp format as a control file for the sprayer system (usually in combination with custom database file formats).

To create a shapefile you add geometry and/or attributes using methods in the Writer class until you are ready to save the file.

Create an instance of the Writer class to begin creating a shapefile:
>>> w = shapefile.Writer()



Setting the Shape Type

The shape type defines the type of geometry contained in the shapefile. All of the shapes must match the shape type setting.

Shape types are represented by numbers between 0 and 31 as defined by the shapefile specification. It is important to note that numbering system has several reserved numbers which have not been used yet therefore the numbers of the existing shape types are not sequential.

There are three ways to set the shape type: - Set it when creating the class instance. - Set it by assigning a value to an existing class instance. - Set it automatically to the type of the first shape by saving the shapefile.

To manually set the shape type for a Writer object when creating the Writer:
>>> w = shapefile.Writer(shapeType=1)

>>> w.shapeType
1


OR you can set it after the Writer is created:
>>> w.shapeType = 3

>>> w.shapeType
3



Geometry and Record Balancing

Because every shape must have a corresponding record it is critical that the number of records equals the number of shapes to create a valid shapefile. To help prevent accidental misalignment the PSL has an “auto balance” feature to make sure when you add either a shape or a record the two sides of the equation line up. This feature is NOT turned on by default. To activate it set the attribute autoBalance to 1 (True):
>>> w.autoBalance = 1


You also have the option of manually calling the balance() method each time you add a shape or a record to ensure the other side is up to date. When balancing is used null shapes are created on the geometry side or a record with a value of “NULL” for each field is created on the attribute side.

The balancing option gives you flexibility in how you build the shapefile.

Without auto balancing you can add geometry or records at anytime. You can create all of the shapes and then create all of the records or vice versa. You can use the balance method after creating a shape or record each time and make updates later. If you do not use the balance method and forget to manually balance the geometry and attributes the shapefile will be viewed as corrupt by most shapefile software.

With auto balanacing you can add either shapes or geometry and update blank entries on either side as needed. Even if you forget to update an entry the shapefile will still be valid and handled correctly by most shapefile software.


Adding Geometry

Geometry is added using one of three methods: “null”, “point”, or “poly”. The “null” method is used for null shapes, “point” is used for point shapes, and “poly” is used for everything else.

Adding a Null shape

Because Null shape types (shape type 0) have no geometry the “null” method is called without any arguments.
>>> w = shapefile.Writer()

>>> w.null()


The writer object’s shapes list will now have one null shape:
>>> assert w.shapes()[0].shapeType == shapefile.NULL


Adding a Point shape

Point shapes are added using the “point” method. A point is specified by an x, y, and optional z (elevation) and m (measure) value.
>>> w = shapefile.Writer()

>>> w.point(122, 37) # No elevation or measure values

>>> w.shapes()[0].points
[[122, 37, 0, 0]]

>>> w.point(118, 36, 4, 8)

>>> w.shapes()[1].points
[[118, 36, 4, 8]]


Adding a Poly shape

“Poly” shapes can be either polygons or lines. Shapefile polygons must have at least 5 points and the last point must be the same as the first (i.e. you can’t have a triangle accoring to the shapefile specification even though many popular GIS programs support such shapefiles.) A line must have at least two points. Because of the similarities between these two shape types they are created using a single method called “poly”.
>>> w = shapefile.Writer()

>>> w.poly(shapeType=3, parts=[[[122,37,4,9], [117,36,3,4]], [[115,32,8,8],
... [118,20,6,4], [113,24]]])



Creating Attributes

Creating attributes involves two steps. Step 1 is to create fields to contain attribute values and step 2 is to populate the fields with values for each shape record.

The following attempts to create a complete shapefile:
>>> w = shapefile.Writer(shapefile.POINT)
>>> w.point(1,1)
>>> w.point(3,1)
>>> w.point(4,3)
>>> w.point(2,2)
>>> w.field('FIRST_FLD')
>>> w.field('SECOND_FLD','C','40')
>>> w.record('First','Point')
>>> w.record('Second','Point')
>>> w.record('Third','Point')
>>> w.record('Fourth','Point')
>>> w.save('shapefiles/test/point')

>>> w = shapefile.Writer(shapefile.POLYGON)
>>> w.poly(parts=[[[1,5],[5,5],[5,1],[3,3],[1,1]]])
>>> w.field('FIRST_FLD','C','40')
>>> w.field('SECOND_FLD','C','40')
>>> w.record('First','Polygon')
>>> w.save('shapefiles/test/polygon')

>>> w = shapefile.Writer(shapefile.POLYLINE)
>>> w.line(parts=[[[1,5],[5,5],[5,1],[3,3],[1,1]]])
>>> w.poly(parts=[[[1,3],[5,3]]], shapeType=shapefile.POLYLINE)
>>> w.field('FIRST_FLD','C','40')
>>> w.field('SECOND_FLD','C','40')
>>> w.record('First','Line')
>>> w.record('Second','Line')
>>> w.save('shapefiles/test/line')


You can also add attributes using keyword arguments where the keys are field names.
>>> w = shapefile.Writer(shapefile.POLYLINE)
>>> w.line(parts=[[[1,5],[5,5],[5,1],[3,3],[1,1]]])
>>> w.field('FIRST_FLD','C','40')
>>> w.field('SECOND_FLD','C','40')
>>> w.record(FIRST_FLD='First', SECOND_FLD='Line')
>>> w.save('shapefiles/test/line')



Saving to File-Like Objects

Just as you can read shapefiles from python file-like objects you can also write them.
>>> try:
...     from StringIO import StringIO
... except ImportError:
...     from io import BytesIO as StringIO
>>> shp = StringIO()
>>> shx = StringIO()
>>> dbf = StringIO()
>>> w.saveShp(shp)
>>> w.saveShx(shx)
>>> w.saveDbf(dbf)
>>> # Normally you would call the "StringIO.getvalue()" method on these objects.
>>> shp = shx = dbf = None

reference :
https://pypi.python.org/pypi/pyshp/1.1.4
