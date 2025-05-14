This file includes grepping the design objects or design data in terminal using innovus commands
#   
Get the name of the selected object from GUI(instance, net, ports or any objects)
##### dbGet selected.name
#
Get the cell name of a selected Instance from GUI
##### dbGet selected.cell.name
#
Get all the pins of a selected Instanace from GUI
##### dbGet selected.cell.terms.name
#
Get the database unit
##### dbGet head.dbUnits
#
Get the manufacturing grid value
##### dbGet head.mfgGrid
#
Get all the layers name
##### dbGet head.layers.name
#
Get the pointers of all the cells which are used more than 100 times in the design
##### dbGet [dbGet head.allcells.objType libcell -p] {numRefs > 100}
#
Get all the inverter cells available in the library
##### dbGet [dbGet head.libcells.isInverter 1 _p].name
#
Get the size of your block
##### dbGet top.fplan.box.size
#
Get the area of a block
##### dbGet top.fplan.area
#
Get all the power domains available in the design
##### dbGet top.fplan.groups.name
#
Find all the block level ports name
##### dbGet top.terms.name
#
Find the metal layers used in block level IO ports
##### dbGet top.terms.pins.allShapes.layer.name -u
#
Get the count of block level ports
##### dbGet top.numTerms
or you can also use
##### llength [dbGet top.terms]
