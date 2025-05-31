This file contains my intervew experience on most commonly asked VLSI physical Design questions 
#### what are the causes routing congestion?
- Missing placement blockages
- placing macros without giving routinig space for interconnections between macros
- improper macro placement and macro channels (placing macros in the middle of the core area) 
- High cell density(High local utilization)
- If design has more No.of AOI/OAI cells you can see the congestion issue
- Placement of standard cells near macros i.e., not setting proper keepout margins for macros
- High pin density on one edge of the clock
- too buffers added for optimization
- Module splitting i.e., not applying bonds as needed
#
## What are the congestion Fixes? 
### 1. Adding placement blockages in  channels and around macro corners
##### ##### icc_shell> create_placement_blockage -bbox {10 20 100 200}
#####            create_placement_blockage -name ICC_THIN_CHAN_PLACE_BLKG_#0 -type hard -bbox { 425.565 815.215 883.790 823.215}
to create a hard placement blockage in the area enclosed by a rectangle with corners at (10, 20) and (100, 200)
Defining a Hard Macro Blockage
A hard macro blockage prevents the placement of hard macros within the specified area, but
not standard cells. To create a hard macro blockage, use the -no_hard_macro option.
To define a hard macro blockage, specify the bounding box, type (-no_hard_macro option),
and optionally the name for the placement blockage.
For example, to define a hard macro blockage enclosed by a rectangle with corners at (120,
75) and (230, 200), use the following command:
###### ##### icc_shell> create_placement_blockage -bbox {120 75 230 200} -no_hard_macro
Defining a Soft Placement Blockage
To define a soft blockage, specify the boundary, type (-type soft option), and optionally the
name for the placement blockage.
For example, to define a soft blockage enclosed by a rectangle with corners at (120, 75) and
(230, 200), use the following command:
##### ##### icc_shell> create_placement_blockage -bbox {120 75 230 200} -type soft
Defining a Partial Placement Blockage
To define a partial blockage, specify the boundary, type (-type partial option), blockage
percentage (-blocked_percentage option), and optionally the name for the placement
blockage.
For example, to define a partial blockage with a maximum allowed cell density of 60 percent
(a blocked percentage of 40), enclosed by the rectangle with corners at (10, 20) and (100,
200), use the following command:
##### icc_shell> create_placement_blockage -bbox {10 20 100 200} \
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
##### ##### icc_shell> create_placement_blockage -bbox {10 20 100 200} -type partial -blocked_percentage 70 -buffer_only
No-register
A no-register blockage is a special type of partial blockage that prevents registers from
being placed within the blockage. To create a no-register blockage, use the -type
partial, –blocked_percentage, and -no_register options.
For example, to define a partial blockage that excludes registers, but allows a cell density
of 50 percent for other cells, use the following commands:
##### ##### icc_shell> create_placement_blockage -bbox {10 20 100 200} -type partial -blocked_percentage 50 -no_register
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
##### ##### icc_shell> create_bounds -name b1 -coordinate {100 100 200 200} INST_1
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
##### ##### icc_shell> set_keepout_margin -type hard -all_macros -outer {10 10 10 10}
##### ##### icc_shell> set_keepout_margin -type soft -all_macros -outer {10 10 10 10}
##### icc2_shell> create_kepout_margin -type hard -outer {1.48 1.2 148 1.2} [get_cells -physical_context -filter design_type==macro]
##### ##### icc_shell> create_keepout_margin -type hard [get_cells *AOI]
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
##### icc_shell> set_keepout_margin -tracks_per_macro_pin 0.6 \
-min_padding_per_macro 0.1 -max_padding_per_macro 0.2
To report the cell-specific keepout margins in your design, use the
##### report_keepout_margin. 
It reports the keepout margin values set by the set_keepout_margin command, the values of any pin-count-based parameters defined by
the set_keepout_margin command, and all derived keepout margins for the specified
macro cells and standard cells.
To remove the cell-specific keepout margins from your design, use the
##### remove_keepout_margin
#
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
#
## Q. Optimizing Clock-Gating Logic? 4-38 page
##### Synthesis_stage: set_clock_gating_registers -include_instance [all_registers -clock clock]
#####                  compile_ultra -gate_clock

##### Placement stage: create_placement -optimize_icgs command 
followed by the 
##### place_opt -optimize_icgs -skip_initial_placement
#
## Q. Synthesis optimizations?
##### compile_ultra -gate_clock -timing_high_effort_script
#####               -gate_clock -area_high_effort_script
#
## Q. applying shielding on nets 7-43 page
##### i) Define the shielding rules.
To define shielding rules, use the -shield_spacings and -shield_widths options of
the define_routing_rule command. For example, to specify a shielding rule that uses
spacing of 0.1 microns and width of 0.1 microns for metal1 through metal5 and spacing
of 0.3 microns and width of 0.3 microns for metal6, enter the following command:
##### ##### icc_shell> define_routing_rule shield_rule -shield_widths {M1 0.1 M2 0.1 M3 0.1 M4 0.1 M5 0.1 M6 0.3} -shield_spacings {M1 0.1 M2 0.1 M3 0.1 M4 0.1 M5 0.1 M6 0.3}
ote:
The **set_clock_tree_options** command assigns shielding rules to clock nets only **before clock tree synthesis**. 
**After clock tree synthesis**, you must use the **set_net_routing_rule** command to assign shielding rules to clock nets.
**create_zrt_shield -with_ground** To provide the most protection for critical clock nets, perform shielding on those nets after clock tree routing but before signal net routing.
#
## Q. creating corners?
   **create_corner slow**
######             fast
   **create_mode func**
   **current_mode func** 
   **create_scenario -mode func -corner fast -name func_fast**
   **create_scenario  -mode func -corner slow -name func_slow**

#
## Q. calculating delay through any net? 
**TTo get a detailed report on the delay calculation at a given point along a timing path, use the
report_delay_calculation command. This is the command syntax:
report_delay_calculation
-from from_pin -to to_pin
[-min] [-max]
[-nosplit]
[-crosstalk]
[-from_rise_transition value]
[-from_fall_transition value]
Specify the “from” and “to” pins of the cell or net that you want to report. These two pins can
be the input and output pins of a cell to report the calculation of a cell delay, or can be the
driver pin and a load pin of a net to report the calculation of a net delay.**

#
## Q. How many Modes, corners and scenarions there?
Functional Mode: checking the functionality from one flop to other flop with high frequency clock
Scan Mode: Applying the test vector to the input to test flop functionality
Scan Capture Mode: Applying the test vectors to the input are to test the logical circuit present in between the flop. Uses same frequency as functionality frequency clock, if the functionality mode timing is clean then scan capture is also clean.
Memory Built in self test: testing the circuit which is present inside the memory. Uses low frequency clock
#
## Q. setting operating conditions?
**set_operating_conditions -max WORST -max_library Typ.db -min BEST -min_library HoldTyp.db**
#
## Q. report_timing options?
report_timing Command Options
The report_timing command offers a large number of options to control the scope of the
design that is reported, the number of paths to report, and the types of path information to
include in the report. This is the full syntax of the command:
report_timing
[-to to_list] [-rise_to to_list] [-fall_to to_list]
[-from from_list] [-rise_from from_list] [-fall_from from_list]
[-exclude exclude_list]
[-rise_exclude exclude_list] [-fall_exclude exclude_list]
[-through through_list]
[-rise_through through_list]
[-fall_through through_list]
[-path_type
full | end | only | short | start | full_clock | full_clock_expanded]
[-delay_type min | min_rise | min_fall | max | max_rise | max_fall]
[-nworst paths_per_endpoint]
[-max_paths max_path_count]
[-input_pins]
[-nets]
[-transition_time]
[-crosstalk_delta]
[-capacitance]
[-effective_capacitance]
[-lesser_path max_path_delay]
[-greater_path min_path_delay]
[-slack_greater_than greater_slack_limit]
[-slack_lesser_than lesser_slack_limit]
[-loops]
[-enable_preset_clear_arcs]
[-significant_digits digits]
[-nosplit]
[-unique_pins]
[-start_end_pair]
[-physical]
[-attributes]
[-sort_by group | slack]
[-normalized_slack]
[-group group_name]
[-derate]
[-temperature]
[-voltage]
[-trace_latch_borrow]
[-scenario scenario_list]
By default, the report_timing command by itself, without any options, reports the single
worst maximum-delay (setup) path in each path group. Therefore, the number of paths
reported is equal to the number of path groups. By default, there is one path group per clock
used in the design.
Scope of the Design Reported
The -from, -through, and -to options restrict the scope of the report to only those paths
that start, pass through, and end at the specified objects in the design. The options
-rise_from, -fall_to, and so on, further restrict the scope to only those paths that have
a rising-edge signal at the startpoint, a falling-edge signal at the endpoint, and so on. The
specified object can be a clock, a port, or a pin of a launch or capture cell.
For example, the following command restricts the scope of the report to only those paths that
start and end at specified objects:
##### prompt> report_timing -from [get_pins reg08/CP] -to [get_clocks CLK1]
This command reports the single path having the worst maximum-delay (setup) slack that
starts with a rising-edge signal at the CP pin of cell reg08 and ends at a register or port
clocked by CLK1.
You can use (or not use) any combination of -from, -through, and -to type options. For
details, see “Path Specification Methods” on page 3-4.
To restrict the scope of the report to a particular path group, use the -group option. For
example, to report only the paths in the CLK1 path group,
##### prompt> report_timing -group CLK1
By default, paths are divided into groups according to the endpoint clock. For example, a
design that has clocks named CLK1 and CLK2 has two path groups named CLK1 and
CLK2. You can also specify your own path grouping with the group_path command.
To exclude all paths from the report that start from, pass through, or end at specified pins,
ports, nets, or cells, use the -exclude , -rise_exclude, or -fall_exclude option. For
example, to exclude all paths containing the net n234 from reporting, use the following
command:
##### prompt> report_timing -exclude [get_nets n234]
Path Details Reported
By default, each line of the path report shows the point along the path, the incremental
contribution to the delay of the point, and the cumulative delay up to that point. Each point
includes both the cell and net delay from the previous point. For example,
##### prompt> report_timing
![image](https://github.com/user-attachments/assets/a5dc9905-6aca-4336-9364-681ead3c59be)
To display the net names associated with the timing points, use the -nets option:
##### prompt> report_timing -nets
![image](https://github.com/user-attachments/assets/8758af27-22ac-4a97-a23e-3a91ee0d9042)
To display net delays separately from the cell delays, use the -input_pins option, which
lists the cell input pins as well as the cell output pins as timing points:
##### prompt> report_timing -input_pins -significant_digits 5
![image](https://github.com/user-attachments/assets/1a5aa9e2-30b1-4ece-b631-ea219e71bc7f)
The incremental delay leading up to a cell input is a net delay. The incremental delay leading
up to a cell output is the cell delay.
To display the signal transition time at each timing point, use the -transition_time option:
##### prompt> report_timing -transition_time
![image](https://github.com/user-attachments/assets/19a4648d-79a3-4c01-b787-a34b011b0239)
Similarly, use the -capacitance option to display the net capacitance, -physical to display
the physical locations of the driver and load pins, and so on. You can combine multiple
options of this type in a single command. The tool increases the number of columns in the
report as needed.
The -path option specifies what type of timing points are included in the path report. It can
be set to short, full, full_clock, full_clock_expanded, only, or end. The default
report type is full.
A full_clock report is like a full report, except that it also reports the timing points in the
clock paths leading up to the launch and capture devices. A full_clock_expanded report
is like a full_clock report, except that it also reports the timing points between a primary
source clock and a generated clock. For example,
##### prompt> report_timing
![image](https://github.com/user-attachments/assets/85d5823f-fa00-493a-abda-da8686bb83c9)
##### prompt> report_timing -path full_clock
![image](https://github.com/user-attachments/assets/3ac06e2b-4e3b-430d-a8ca-5b5d9e24fda1)
A short report shows only the startpoint and endpoint, omitting the intermediate points. An
only report shows the data path only and omits the required-time and slack calculation.
An end report shows only the endpoint, delay, required time, and slack in a single line, but
all endpoints are reported instead of just the single worst path per path group. The -path
end option is typically used with the -nworst option to restrict the report length. For
example,
##### prompt> report_timing -path end -max_paths 5
![image](https://github.com/user-attachments/assets/4b6efa9c-a1e4-439b-86d5-3c1f71f8f77f)
##### Delay Type (Min/Max) Reported
By default, the report_timing command reports the path or paths having the worst
maximum-delay (setup) slack resulting from the longest delays and earliest required times.
To get a report on the paths having the worst minimum-delay (hold) slack instead, use the
-delay min option. In that case, the command looks for the shortest delays and latest
required times. The minimum-delay slack is the arrival time minus the required time.
You can use the -delay min_rise, -delay max_fall, and similar options to restrict the
scope of the report to just rising or just falling edges of the data signal at the path endpoint.
##### Number of Paths Reported
The -nworst and -max_paths options control the number of paths reported. The -nworst
option specifies the number of paths reported per endpoint. The -max_paths option
specifies the number of paths reported per path group.
For example, the following command reports the eight worst maximum-delay paths per path
group, but no more than two paths per endpoint:
##### prompt> report_timing -nworst 2 -max_paths 8
If the three worst maximum-delay paths in a path group have the same endpoint, only the
first two are reported; the third-worst path reported must have a different endpoint in the path
group.
The -slack_lesser_than and -slack_greater_than options restrict the report to paths
that have slack values within the specified range. For example, the following command lists
up to 50 worst maximum-delay paths per path group that have a slack less than 0.5:
##### prompt> report_timing -path end -max_paths 50 -slack_lesser_than 0.5
The command reports the paths in order of slack, starting with the worst path, and continues
until 50 paths have been reported or until the slack exceeds 0.5 time units.
#
## Q. what is multipoint?
MultiPoint CTS is a hybrid method of conventional CTS and clock mesh. The structure is shown in fig 1, which consists of a mesh driven by a pre-mesh tree. Multi Point drivers connect to the mesh at a limited number of locations referred to as taps. A multi-point clock tree structure driven by the mesh consists of subtrees, each driven by a tap.
![image](https://github.com/user-attachments/assets/fc62a037-aac9-4336-8e99-ca0c6f0f695d)
#

## Q. Difference between the add_buffer_on_route and insert_buffer?
##### Use-case:

After CTS or routing, when long nets need segmentation due to slew/degradation.

You want minimal disruption to logical connectivity.


Adding Repeater Cells on Routed Nets
To add repeater cells on a fully routed net, use the add_buffer_on_route command.
When you use this command, you must specify the following information:
•The net on which to add the repeater cells
•The library cell to use for the repeater cells
By default, the tool assumes that the library cell you specify is a buffer. To add pairs of
inverters as repeater cells instead of buffers, use the -inverter_pair option.
The following example shows how to add pairs of inverters as repeater cells on the net4
net using the mylib/INV1 library cell.
##### icc_shell> add_buffer_on_route net4 [get_lib_cells mylib/INV1] \
-inverter_pair -repeater_distance 100
•
The location to add the repeater cells
You specify the repeater cell locations by using one of the following methods:
❍
Exact locations
To specify the locations of the repeater cells, use the -location option. When you
specify an exact location, you can also specify a layer with an existing routing shape
at that location to connect the repeater cell to.
The following example adds the mylib/BUF1 library cell as a repeater cell on the net1
net at location (100, 200) and connects it to an existing route shape on the M4 layer.
##### icc_shell> add_buffer_on_route net1 [get_lib_cells mylib/BUF1] \
-location {100 200 M4}
❍
Fixed intervals
To specify the distance in microns between the repeater cells, use the
-repeater_distance option. To specify the distance in microns between the driver
of the net and the first repeater cell, use the -first_distance option.
The following example adds the mylib/BUF1 library cell as a repeater cell on the net2
net at an interval of 150 microns. The distance between the driver and the first
repeater cell is 100 microns.
##### ##### icc_shell> add_buffer_on_route net2 [get_lib_cells mylib/BUF1] -first_distance 100 -repeater_distance 150
❍
Relative intervals
To specify the distance between the repeater cells as a ratio of the total net length,
use the -repeater_distance_length_ratio option. To specify the distance
between the driver and the first repeater as a ratio of the total net length, use the
-first_distance_length_ratio option.
The following example adds the mylib/BUF1 library cell as a repeater cell on the net3
net at an interval that is 20 percent of the total net length. The distance between the
driver and the first repeater cell is 10 percent of the total net length.
##### ##### icc_shell> add_buffer_on_route net3 [get_lib_cells mylib/BUF1] \
-first_distance_length_ratio 0.1 \
-repeater_distance_length_ratio 0.2
Instead of specifying the library cells to use and their locations as exact locations, fixed
intervals, or relative intervals, you can specify an exact configuration of repeater cells to
insert by using the -user_specified_buffers option.
When you use the -user_specified_buffers option, for each repeater cell, you must
specify the instance name, the library cell to use, the x- and y-coordinates of the exact
location, and a layer at that location with an existing routing shape to connect to. You can
insert different types of buffers or inverter pairs by using this option, but you cannot combine
both buffers and inverter pairs.
The following example adds two repeater cells named ECO1 and ECO2 on net n22. The
ECO1 cell is of type BUF2, at location (100, 70), connecting to a routing shape on the M3
layer. The ECO2 cell is of type BUF4, at location (150, 70), also connecting to a routing
shape on the M3 layer.
##### icc_shell> add_buffer_on_route net22 \
-user_specified_buffers {ECO1 BUF2 100 70 M3 ECO2 BUF4 150 70 M3}
By default, the add_buffer_on_route command
•
Adds repeater cells over placement blockages, soft macros, and hard macros.
To prevent this, use the -respect_blockages option.
•
Adds repeater cells on both global routed and detail routed nets.
To add cells only on nets that are global routed only, without assigned tracks or detail
routing, use the -only_global_routed_nets option.


#### insert_buffer
Manually insert a prototype driver on the clock-mesh net by using the insert_buffer
command, as shown in the following example:
##### icc_shell> insert_buffer [get_nets CLK] my_lib/clkbuff10 -new_cell_names TAP

adding buffer at RS pin  
##### icc_shell> insert_buffer -lib_cell SAEDRVT10_BUF_2 -new_net_names hold_fix -cell_names hold_fix_inst pwCtrl/inst_stat_reg_reg0/RS
##### Use-case:

During synthesis or floorplanning/placement stages, to reduce fanout or meet setup/hold requirements.

For proper clock tree synthesis or early timing optimization.
#
## Q. what is Double Patterning?
In doube patterning Method dense patterns of metals in a single mask is split into 2 different masks that can be interleaved/ spread to get the original pattern as desired.

Double patterning is a technique to overcome diffraction Problem during the lithography process in chip manufacturing . Instead of trying to create all the tiny features at once, it splits the process.
- First : The mask is designed to create only half of the features, with enough space between them to avoid diffraction problems.
- Second : A second mask, with a different pattern, is used to create the other half of the features. These features are carefully fit in the gaps left by the first exposure.
It's like drawing a complex picture in two stages, first drawing some lines and then going back to fill in the rest.

Allows the creation of much smaller and denser features on chips.
 * It Leads to more powerful and efficient chips.

But it Adds extra steps to the manufacturing process and expensive.
Requires careful planning to make the two masks align perfectly

create_track_pattern -layer M1 -site uint -type uniform -direction vertical -mas_pattern {mask_two mask_one} spacing 0.074 -offesets 0.037
![image](https://github.com/user-attachments/assets/0a18b921-57f1-4002-85b1-e71ffc4dc151)
#
## Q. what is the difference between PnR routing and signoff routing?
**signoff routing** is with accurate parasitic extraction (SPEF).Focuses on SI, EM and extraction accuracy. It only analyzes existing routes

**PnR Routing**focusses on Meeting timing (setup/hold), DRC clean routing & Optimal wirelength and congestion reduction. Some corner cases might not be fully optimized (e.g., coupling, signal integrity).Some shortcuts are taken to prioritize runtime and convergence. It actually routes the nets
Goal:Functional and timing-correct layout	
#
## Q. Types of Clocks?
The signal which is used to trigger all the sequential elements in the design.
Types,
- Synchronous
- Asynchronous
- Exclusive
 **Synchronous Clock:** Two clocks are synchronous with respect to each other if they **share a common source** and have a **fixed phase relationship** and a **common base period**(should have a common multiple).
  Ex: time period of two clocks: 2 and 6, here the common base period is 2.
 **Asynchronous clock:** Two clocks are said to be asynchronous if they **do not have a fixed phase relationship** with each other in the design and **don't have a common base period**.
  Ex: time periodof two clocks: 6 and 7, here there is no common base period
  **Exclusive clock:**
  - Two clocks are exclusive if they do not interact with each other.
  - For instance, a ckt might multiplex two different clock signals onto a clock line, one a fast clock for normal operation and the other a slow clock for low power consumption
  - Only one of the two clocks is enabled at any given time, so there is no interaction between two clocks.
  - You may define  "false path" between these mutually exclusive clocks

**Master clock:** These are clocks which get defined at the main clcok source like oscillator/PLL. In a chip, master clock can be defined at some clock input ports too. When we define these master clocks, proper frequency and source information should be given. Uncertainity also should be specified.
##### >>create_clock -name clock -period 4 [get_ports clock]
• The crete_clock cmd is used to create a clock at the specified source. A source
can be defined at an input port of the design or an internal pin.
• To create a clock on ports C1 and CK2 with a period of 10, a rising edge at 2, and falling edge at 4, enter the cmd
##### >>create_clock –period 10 –waveform {2 4} [get_ports {C1 CK2}]
• With this an ideal clock is created that ignores the delay effects of the clock
network.
**Gated Clock:**
A gated clock is a clock signal under the control of gating logic.Tool performs both setup and hold checks on the gating clock.
**Generated clock:** These are divider/multiplier clocks which get generated from a master clock. Mostly these are defined at the output of a clock divider like flipflop or mux. When we define a generated clock, its source clock, the generation point, division ration and uncertainity value should be provided. Generated clocks can be also defined at any point, if we need to define some exception wrt ths clock.
##### >>create_generated_clock -name scan -period 8 -waveform [0 4] [get_ports clock]
• A generated Clock is a clock signal generated from another clock signal by a circuit within the design itself, such as a clock divider.
• The create_generated_clock cmd is used to create generated clocks in which you can create frequency divided (-divide_by) or frequency multiplied (- multiply_by) clock.
##### >>create_generated_clock –name dclk –source [get_ports CLK] –divide_by 2 [get_ports FF1/Q]
**Virtual clock:** These are  clocks used to time the input/output port. These are imaginary clocks defines only with the clock wavaform and not having source/generation point.
- For virtual clocks defined with the same create_clock command, network latency is calculated using the clock propagation delay of the boundary registers clocked by the individual virtual clocks.
• A virtual clock has no actual source in the current design, but you can use it for setting input or output delays.
• You can use virtual clock cmd to define virtual clocks for signals that interface to external clocked devices (other block).
##### >> create_clock –period 8 –name vclk –waveform {0 4}
- To adjust the I/O timing for virtual clocks, you must define the relationships between the virtual clocks and the real clocks before you adjust the I/O timing as follows:
>> icc_shell> set_latency_adjustment_options -to_clock my_virtual_clock -from_clock my_real_clock
icc_shell> update_clock_latency
>> 
#
## Q. Types of latencies?
Latency is the amount of time it takes for the clock signal to be propagated from the original
clock source to the sequential elements in the design, consisting of two components, source
latency and network latency. 
- **Source latency**, also known as insertion delay, is the time it takes for a clock to be propagated from its ideal waveform origin point to the clock definition point in the design. 
- **Network latency** is the time it takes for a clock to be propagated from the clock definition point in the design to a register clock pin.
 
- The total latency at a register clock pin is the sum of the source latency and network latency.

![image](https://github.com/user-attachments/assets/249182e8-a4fc-4594-b2ca-138eab742202)
-                                                  Source and Network Latency

set_clock_latency
[-rise] [-fall]
[-min] [-max]
[-source]
[-early] [-late]
[-clock clock_list]
delay
object_list

For example, to set the expected rise latency to 1.2 and the fall latency to 0.9 for CLK, enter
>> set_clock_latency -rise 1.2 [get_clock CLK]
>> set_clock_latency -fall [get_clocks CLK]
![image](https://github.com/user-attachments/assets/b2bf3145-ce62-44f3-917c-c0bb03a8a006)
-                                Early/Late Source Latency Waveforms
Removes user-specified clock network or sourceclock latency information from specified objects.
>>remove_clock_latency 
#
## Q. Types of Clock Sources?


#
## Q. What is case analysis?

In some designs, certain signals have a constant value in specific modes. For instance, in functional modes, the test signals do not toggle and are therefore tied either to VDD or VSS depending on their active level. This also applies to signals that do not toggle after the design has been powered up. In the same way, today's designs have multiple functional modes and some signals that are active in some of the functional modes might be inactive in other modes.

To help reduce the analysis space, runtime and memory consumption, it is important to let the Static Timing Engine know about the signals that have a constant value. This is also critical to ensure that non-functional paths and irrelevant paths are not reported.

A signal is declared as inactive to the timing engine with the set_case_analysis command. The command applies to pins and/or ports.

Note: After a case analysis is set on a pin, the timing arcs related to that pin are disabled. The timing engine does not report any path going through disabled timing arcs.

The syntax of the set_case_analysis command is:
##### >> set_case_analysis <value> <pins/ports objects> 

The parameter <value> can be any of the following:
0, 1, zero, one, rise, rising, fall, or falling

When the values rise, rising, fall, or falling are specified, this means that the given pins or ports should only be considered for timing analysis with the specified transition. The other transition is disabled.

A case value can be set on a port, a pin of a leaf cell, or a pin of a hierarchical module.

In the example below, two clocks are created on the input pins of the multiplexer clock_sel but only clk_2 is propagated through the output pin after setting the constant value on the selection pin S.
![image](https://github.com/user-attachments/assets/002018ff-149d-4599-a245-22b003362ee7)

create_clock -name clk_1 -period 10.0 [get_pins clock_sel/I0]
create_clock -name clk_2 -period 15.0 [get_pins clock_sel/I1]
set_case_analysis 1 [get_pins clock_sel/S]

Reference: https://docs.amd.com/r/2021.2-English/ug903-vivado-using-constraints/Case-Analysis

Case analysis propagates the constant values specified on nets forward (but not backward) through the design.

Case Analysis :– Specifies mode settings with user-specified logic constants or logic transitions on ports or pins.

The most common use of case analysis is to disable a scan chain. Setting the SCAN_MODE port to 0 disables the scan chain. As a result, the scan chain is not reported by the report_timing command.

By setting a logic constant of 0 on the SCAN_MODE port, PrimeTime disables the scanchain that starts at SCAN_IN and ends at SCAN_OUT. PrimeTime disables the scan chain because the logic 0 set on the SCAN_MODE port propagates to the FF1/TE, FF2/TE, FF3/ TE, and FF4/TE pins. A logic constant on the TE pin of each scan flip-flop disables the setup and hold arcs from CP to TI because of the sequential case analysis on the FF1, FF2, FF3, and FF4 cells.

To set case analysis with a 0 value, enter
##### pt_shell> set_case_analysis 0 [get_ports "SCAN_MODE"]
![image](https://github.com/user-attachments/assets/946e3ca4-3dca-4dae-ab77-0e3536418e7e)

reference: Prime Time User Gude Page No. 10-4

## Q. What is input delay and output delay?
**Input Delays:**
• In order to do the timing analysis in the paths like I2R and I2O, tool needs
information about the arrival times of the signals at the input ports.
• The set_input_delay cmd is used to specify the min and max amount of delay
from a clock edge to the arrival of a signal at a specified input port.

**Output Delays:**
• In order to do the timing analysis in the paths like R2O and I2O, tool needs
information about the timing requirements at the output ports.
• The set_output_delay cmd is used to specify the min and max amount of delay
between the output port and the external sequential device that captures the
data from that output port is specified at that output port.

## Q. How to resolve reference errors in synthesys?
![image](https://github.com/user-attachments/assets/cb02f46f-1849-441d-bf84-3b7897468d3d)
 
#
## Q. How many PVT Corners are there inn your design?
-Three corners,i)slow ii)typical iii)fast
#
## Q. What are the minimum and maximum voltages ini your design?
min: Hold :ff0p8840c
max: Setup: ss0p6120c
#
## Q. What is the use of physical aware synthesis?
#
## Q. Why timing violations will be in PnR though synthesis Timing report is clear?
#
## Q. How to creating pg vias from M1 layer to M8 layer?
#
## Q. On which parameter skew will be decided?
#
## Q. What will be the change in the cell for different VT cells?
#
## Q. What is the effect, if any VIA will be missed in PG network?
#
## Q. what are LEGO values?
#
## Q. What might be reason for Power violation in M2 and M5?
#
## Q. What are the memory controllers used fo rthe Macros?
#
## Q. TCL Script for placing the Inputs and Output pins on the same metal layer?
#
## Q. TCL script for connectong input floating pin to nearest tie cells in a 3x3 after BTO?
#
## Q. What is Mother and Daughter routing?
#
## Q. What are routing groups?
#
## Q. What are Clock groups?
#
## Q. How +ve clock crosstalk to datapath setup?
#
## Q. What are power switches used in your design?
#
## Q. If there are timing violations in placement stage for reg2reg path 
might be because of multiple logic logic levels 
place_opt -retiming

#
## Q. If there is no Logical equivalence is not maintained then how will you deal in synthesis?
#
## Q. What happens if the skew is not within the limits or your clock tree skew is not balanced?
+ve is good for setup
if the skew is not in limit , it is bad for hold
#
## Q. How can you check the total no.of lines in a file with awk cmd?
#
## Q. awk cmd to report startpoint, end point and the slack in a timing report?
#
##
