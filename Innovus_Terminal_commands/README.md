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
#
Get all input and output ports
##### dbGet top.numInputs
##### llength [dbGet top.terms.inoutput 1 -p]
#
Get all the clock and scan clock pins
##### llength [dbGet top.terms.isClk 1 -p]
##### llength [dbGet top.terms.isScanClk -1 p]
#
Find the name of all the macros in your design
##### dbGet [dbGet top.insts.cell.baseClass block -p2].name
suppose you want to exclude few macros like ESD cells or any other from this list, then you can use -V option with this like below,
##### dbGet [dbGet top.insts.cell.baseClass -block -p2].name -V <unique_name_pattern_of_those_macros>
**llength** will return the count of macros
#
Find the name of all sequential elements (registers/flipflops) in the design
##### dbGet [dbGet top.insts.cell.isSequential 1 -p2].name
#
Find the total no.of physical cell in##### stances used in the design
##### llength [dbGet top.insts.cell.isPhysOnly 1 -p2]
#
Find all the instances which have only one pins (like tie cells, antenna cells)
##### llength [dbGet top.insts.cells.numTerms 1 -p2]
#
Find the total number of well tap instances used in the design
##### llength [dbGet top.insts.cell.name <*TAP*> -p2]
**NOTE:** Depending on the stanard cell library nomenclature the keyword *TAP* could be vary
#
Find the total types of well tap cels used in the design
##### llength [dbGet top.insts.cell.name <*TAP*> -p -u
**NOTE:** -p may be used inplace of -p1
#
Get all the physical cells present in the design
##### dbGet [dbGet top.insts.cell.isPhyOnly 1 -p].name -u
Suppose you want to print all the names in a newline, you can use **join** then like below,
##### join [dbGet top.insts.cell.isPhyOnly 1 -p].name -u ]\n
suppose you want only number that how many types of physical cells have been used, **llength** can be used in that case before the dbGet cmd,
##### llength [dbGet [dbGet top.insts.cell.isPhysOnly 1 -p].name -u]
#
Get the edge number where a port is placed
##### dbGet [dbGet top.terms.name <port_name> -p].edge
#
Get all the feedthru ports name which is placed on a [articular edge number(suppose3)
##### dbGet [dbGet top.terms.edge 3 -p].name *ft*
#
Get the names of all the instances present in the design
##### dbGet top.insts.name
#
Get all the instances name of a particular cell
##### dbGet [dbGet top.insts.cell.name <cell_name> -p2].name
#
get all the routing layers names
##### dbGet [dbGet head.layers.type routing -p].name
or **getAllLayres metal** can also be used
#
Get the parametes of any particular routing layer (like masks, deirections, Pitch, minwidth, minspacing etc)
##### dbGet [dbGet head.layers.type routing -p].name *2 -p].minWidth
#
Get the information of a cell which is present in the standard cell librray but not in the design
##### dbGet head.libCells.name <cell_name>
#
Find the top name of the design
##### dbGet top.name
#
get all the attributes of a selected object
##### dbGet selected
#
Find the name of all the dont touch instance
##### dbGet [dbGet top.insts.isDontTouch 1 -p].name
#
Get the names of all the instances in your design which has fixed placement status
##### dbGet [dbGet top.insts.pStatus fixed -p].name
#
get the lower left coordinate of an instance
##### dbGet [dbGet top.insts.name <inst_name> -p].pt
Or you can first select the instance and then use following cmd
##### dbget selected.pt
#
In case you want only x-coordinate or only y coordinate, you may use following cmd
##### dbGet selected.pt_x
##### dbGet selected.pt_y
#
Find the width and height of a cell
##### set width [dbGet [dbGet top.insts.cell.name <cell_name> -p].size_x -u]
