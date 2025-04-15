This file contains my intervew experience on most commonly asked VLSI physical Design questions 
#### what are the causes routing congestion?
- Missing placement blockages
- placing macros without gving routinig space for interconnections between macros
- improper macro placement and macro channels (placing macros in the middle of the core area) 
- High cell density(High local utilization)
- If design has more No.of AOI/OAI cells you can see the congestion issue
- Placement of standard cells near macros i.e., not setting proper keepout margins for macros
- High pin density on one edge of the clock
- too buffers added for optimization
- Module splitting i.e., not applying bonds as needed
## What are the congestion Fixes? 
### 1. Adding placement blockages in  channels and around macro corners
##### icc_shell> create_placement_blockage -bbox {10 20 100 200}
#####            create_placement_blockage -name ICC_THIN_CHAN_PLACE_BLKG_#0 -type hard -bbox { 425.565 815.215 883.790 823.215}
to create a hard placement blockage in the area enclosed by a rectangle with corners at (10, 20) and (100, 200)
Defining a Hard Macro Blockage
A hard macro blockage prevents the placement of hard macros within the specified area, but
not standard cells. To create a hard macro blockage, use the -no_hard_macro option.
To define a hard macro blockage, specify the bounding box, type (-no_hard_macro option),
and optionally the name for the placement blockage.
For example, to define a hard macro blockage enclosed by a rectangle with corners at (120,
75) and (230, 200), use the following command:
###### icc_shell> create_placement_blockage -bbox {120 75 230 200} -no_hard_macro
Defining a Soft Placement Blockage
To define a soft blockage, specify the boundary, type (-type soft option), and optionally the
name for the placement blockage.
For example, to define a soft blockage enclosed by a rectangle with corners at (120, 75) and
(230, 200), use the following command:
##### icc_shell> create_placement_blockage -bbox {120 75 230 200} -type soft
Defining a Partial Placement Blockage
To define a partial blockage, specify the boundary, type (-type partial option), blockage
percentage (-blocked_percentage option), and optionally the name for the placement
blockage.
For example, to define a partial blockage with a maximum allowed cell density of 60 percent
(a blocked percentage of 40), enclosed by the rectangle with corners at (10, 20) and (100,
200), use the following command:
icc_shell> create_placement_blockage -bbox {10 20 100 200} \
-type partial -blocked_percentage 40
Note:
To allow unlimited usage of a partial blockage area, specify a blockage percentage of
zero (–blocked_percentage 0 option).
In addition to controlling the maximum cell density of partial blockage, you can also control
the types of cells that are placed within the partial blockage area. Note that the following
options are mutually exclusive, and must be specified along with the -type partial option.
•
Buffer-only
A buffer-only blockage is a special type of partial blockage that allows only buffers and
inverters to be placed within the blockage. To create a buffer-only blockage, use the
-type partial, –blocked_percentage, and -buffer_only options.
For example, to define a partial blockage that allows only the placement of buffers and
inverters, with a cell density of 30 percent, use the following commands:
##### icc_shell> create_placement_blockage -bbox {10 20 100 200} -type partial -blocked_percentage 70 -buffer_only
No-register
A no-register blockage is a special type of partial blockage that prevents registers from
being placed within the blockage. To create a no-register blockage, use the -type
partial, –blocked_percentage, and -no_register options.
For example, to define a partial blockage that excludes registers, but allows a cell density
of 50 percent for other cells, use the following commands:
##### icc_shell> create_placement_blockage -bbox {10 20 100 200} -type partial -blocked_percentage 50 -no_register
### 2. Review the macro placement
### 3. Redusing local cell density using density screens, density screen is applied to limit the density of standard cells in an area to reduce congestion due to high pin density
### 4. reordering scan chain to reduce congestion
##### place_opt _optimize_dft 
“Reorder scan during placement" Reorders scan chains
### 5. congestion driven placement with high effort
#### In placement stage: 
##### create_placement -congestion --> congestion placement of cells

##### set_app_options -name place.coarse.max_density -value 0.6
specify a max density that controls how densely the tool can place cells in uncongested areas during wire ldength driven placement
 
##### set_app_options -name place.coarse.congestion_driven_max_util -value 0.6
specifies a max utilization that controls how densely the tool can place cells in less congested areas that surround highly congested areas, so that the cells in the congested areas can be spread out to reduce the congestion

### 6. continue the iterations until good congestion results are achieved
#### 7. Creating bounds to a module : By default soft bounds if type will not be mentioned
Defining Move Bounds
A move bound is a fixed region within which to place a set of cells. It is comprised of one or
more rectangular or rectilinear shapes, which can be abutted, disjoint, or overlapping.
To define the boundaries of these shapes, use the -coordinate option with the
create_bounds command.
•To specify the boundary of a rectangle shape, use the following format to specify its
lower-left and upper-right corners:
{llx1 lly1 urx1 ury1}
•To specify the boundary of a rectilinear shape, use the following format to specify the
lower-left and upper-right corners of the individual rectangles that make up the rectilinear
shape:
{llx1 lly1 urx1 ury1 llx2 lly2 urx2 ury2 ...}
The tool supports the following types of move bounds:
•Soft move bounds
For soft move bounds, the tool tries to place the cells within the specified region;
however, there is no guarantee that the cells are placed inside the bounds.
To define a soft move bound, use the following syntax:
##### create_bounds -name name -coordinate {coordinates} [-type soft] [bound_objects]
For example, to create a rectangular soft move bound for the INST_1 cell instance with
its lower-left corner at (100, 100) and its upper-right corner at (200, 200), use the
following command:
##### icc_shell> create_bounds -name b1 -coordinate {100 100 200 200} INST_1
•Hard move bounds
For hard move bounds, the tool must place the cells within the specified region.
To define a hard move bound, use the following syntax:
##### create_bounds -name name -coordinate {coordinates} -type hard [bound_objects]
####  setting keepout margins for macros for pin density
Defining Cell-Specific Keepout Margins
To define a keepout margin with different widths on each side or to define a keepout margin
for specific cells, use the set_keepout_margin command (or choose Placement > Set
Keepout Margin in the GUI). This is the full command syntax:
##### set_keepout_margin
##### [-type hard | soft]
##### [-outer {lx by rx ty}]
##### [-tracks_per_macro_pin value]
##### [-min_padding_per_macro value]
##### [-max_padding_per_macro value]
##### [-all_macros] [-macro_masters][-macro_instances]
##### [object_list]
##### [-north]
For example,
##### icc_shell> set_keepout_margin -type hard -all_macros -outer {10 10 10 10}
##### icc_shell> set_keepout_margin -type soft -all_macros -outer {10 10 10 10}
##### icc2_shell> create_kepout_margin -type hard -outer {1.48 1.2 148 1.2} [get_cells -physical_context -filter design_type==macro]
##### icc_shell> create_keepout_margin -type hard [get_cells *AOI]
#####                                             [get_cells *OAI]
Use -type hard or -type soft to specify the type of keepout, either hard or soft. 
##### The default is hard keepout margin. 
Use -all_macros to apply the keepout margins to all macros,
-macro_masters object_list for all instances of specified macro masters, or
-macro_instances object_list for specified instances of macros.
You can specify explicit keepout margins by using -outer {lx by rx ty}, where the four
numbers are the left, bottom, right, and top margins. A value of 0 results in no keepout
margin for that side. The -north option sets the margins with respect to the north orientation
of the cell.
Instead of specifying the keepout margins explicitly, you can have them derived
automatically by using -tracks_per_macro_pin value. In that case, the keepout margin is

calculated from the track width, the number of macro pins, and the specified track-to-pin
ratio, which is typically set to a value near 0.5. A larger value results in larger keepout
margins. The derived keepout margin is always hard; the -type setting is ignored. For
derived margins, the -all_macros, -macro_masters, and -macro_instances options are
not allowed. The derived margins are subject the minimum and maximum values specified
by the -min_padding_per_macro and -max_padding_per_macro options. Here is an
example:
icc_shell> set_keepout_margin -tracks_per_macro_pin 0.6 \
-min_padding_per_macro 0.1 -max_padding_per_macro 0.2
To report the cell-specific keepout margins in your design, use the
##### report_keepout_margin. 
It reports the keepout margin values set by the set_keepout_margin command, the values of any pin-count-based parameters defined by
the set_keepout_margin command, and all derived keepout margins for the specified
macro cells and standard cells.
To remove the cell-specific keepout margins from your design, use the
##### remove_keepout_margin

## Q. Placement optimizations? 4-30 page
##### place_opt --effort low | medium |high
                -area_recovery
                -power
                -cts
                -optimize_dft
                -consider_scan
                -continue_on_missing_scandef
                -congestion
                -spg
## Q. Optimizing Clock-Gating Logic? 4-38 page
##### Synthesis_stage: set_clock_gating_registers -include_instance [all_registers -clock clock]
#####                  compile_ultra -gate_clock

##### Placement stage: create_placement -optimize_icgs command 
followed by the 
##### place_opt -optimize_icgs -skip_initial_placement

## Q. Synthesis optimizations?
##### compile_ultra -gate_clock -timing_high_effort_script
#####               -gate_clock -area_high_effort_script

## Q. applying shielding on nets 7-43 page
##### i) Define the shielding rules.
To define shielding rules, use the -shield_spacings and -shield_widths options of
the define_routing_rule command. For example, to specify a shielding rule that uses
spacing of 0.1 microns and width of 0.1 microns for metal1 through metal5 and spacing
of 0.3 microns and width of 0.3 microns for metal6, enter the following command:
##### icc_shell> define_routing_rule shield_rule -shield_widths {M1 0.1 M2 0.1 M3 0.1 M4 0.1 M5 0.1 M6 0.3} -shield_spacings {M1 0.1 M2 0.1 M3 0.1 M4 0.1 M5 0.1 M6 0.3}
ote:
The **set_clock_tree_options** command assigns shielding rules to clock nets only **before clock tree synthesis**. 
**After clock tree synthesis**, you must use the **set_net_routing_rule** command to assign shielding rules to clock nets.
**create_zrt_shield -with_ground** To provide the most protection for critical clock nets, perform shielding on those nets after clock tree routing but before signal net routing.
## Q. creating corners?
   **create_corner slow**
######             fast
   **create_mode** 
   **create_scenario -name scenario_name -c corner_name -m mode_name**


## Q. calculating delay through any net? 
**To get a detailed report on the delay calculation at a given point along a timing path, use the
report_delay_calculation command. This is the command syntax:
report_delay_calculation
-from from_pin -to to_pin
[-min] [-max]
[-nosplit]
[-crosstalk]
[-from_rise_transition value]
[-from_fall_transition value]**


## Q. How many Modes, corners and scenarions there?
## setting operating conditions?
**set_operating_conditions -max WORST -max_library Typ.db -min BEST -min_library HoldTyp.db**
