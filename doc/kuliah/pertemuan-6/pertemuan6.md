Editor functions for editing on a shapefile. Contohmya delete road. In addition there is also the editor of Writer, Writer is a method in shape file to create a new shp file (shp and dbt)
For langkag-steps in the editor as follows:
Import shape file
Sf = shape file.editor(war.shp)
Sf.point(16,10,0,0)
Sf.record (‘padang’)
Sf.save
Sf.save (‘war.shp’)
a=shapefile.reoder(‘war.shp’)
a.recorders()
a.shapes().points
a.shape()[0]
a.shape()[0] points
[(10,0,10,0)]

for delete
Sf.delete(0)
a.shapes()[0].points [(10,0,10,0)]
sf.points [16,10,0,0]
sf.record(‘padang’)
sf.saver(‘wr.shp’)
