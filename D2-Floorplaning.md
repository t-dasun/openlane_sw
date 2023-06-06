# SKY130_PD_WS_DAY2
# FLOORPLANNING, LIBRARY CELLS and PLACEMENT

   This is the repoistory of Workshop Day2. In this we cover Floorplanning key parameters, running floorplan and placement using openlane and review layout in magic.

## Table of contents  
1. Floorplanning considerations
    - Utilization factor and Aspect Ratio
    - Pre-placed cells
    - Decoupling capcitors
    - Powerplanning
    - Pin placement 
    - Floorplan run on OpenLANE & review layout in Magic
 2. Library Binding and Placement
    - Netlist binding and Initial Place Design
    - Optimize Placement
    - Congestion aware Placement
    - Need for libraries and Characterization 
 3. Cell design and characterization
    - Standard Cell characterization flow
 4. Timing Characterization

### 1. Floorplanning considerations

### Utilization Factor & Aspect Ratio  

When it comes to floorplanning, the shape and size of the fllorplan is decided by Aspect Ratio and how good the floor plan is decided by Utilisation Factor. They are defined as follows:

``` Utilisation Factor =  (Area occupied by netlist)/(total Area of the core)```
                   
``` Aspect Ratio = (height/width) ```

A Utilisation Factor of 1 signifies 100% utilisation leaving no place for routing and extra logic. However, In real scenario, the Utilisation Factor will usually be  0.5-0.6 ie., 50 to 60% of the area is used for macros, standard cells and rest is used for routing, extralogic. Likewise, an Aspect ratio of 1 signifies that the chip is square shaped. Any value other than 1 signifies rectanglular chip.

### Pre-placed cells 

Once the Utilisation Factor and Aspect Ratio has been decided, the locations of pre-placed cells need to be defined. lets say we have  a combinational logic which does some function, maybe clock buffers, complex cells like clock gating cell, mux, memory. so these cells are implemented separately. Pre-placed cells are IPs and have user defined locations hence are fixed in those locarions. Since they are placed before automated placement and routing, they are called pre-placed cells.

### Decoupling capacitors

Once we place Pre-placed cells, these must be surrounded with decoupling capacitors (decaps). lets say we have logic cell that needs transition. The capcitor demands a huge current and this is provided by the power supply through physical wires. As we know, Physical wires have resistance, capaictance and inductance in it. when the signal/supply voltage travserse through these long wires, they will be voltage drop and the intende supply cannot reach the logic cell that is transitioning.The voltage can lead to undefined region in noise margin. To avoid this, Decaps are invented. These are placed very close to the critical cells.  Decaps are fully charged and have equivalent voltage as of the power supply. Since Decap is close to the logic, its hard for the voltage to drop and the lies with in the Noise Margin range. Their role is to decouple the circuit from power supply by supplying the necessary amount of current to the circuit. They pervent crosstalk.

### Power Planning

As we know, there are millions of cells in our design and its hard to supplu power equally to all over the design. However we cannot place decaps cells everywhere in the design.  So, separate horizontal and vertical Vdd and Vss wires have been created forming like a mesh. Now, each cell can tap the required supply from the nearest wire. They way the power is supplied to all the standara cells is through rings,stripes and followpins.

### Pin Placement

The netlist is the logical connectivity between cells. The area between the core and die is utilised for placing I/O pins. The connectivity information is defined in either VHDL or Verilog and is used to determine the position of I/O pins of various ports. Then, logical cell placement is bloackage is done inorder to avoid any cells getting placed by automated PnR.

### Floorplan run on OpenLANE & review layout in Magic

#### Floorplan envrionment variables or switches:
1. ```FP_CORE_UTIL``` - core utilization percentage
2. ```FP_ASPECT_RATIO``` - the cores aspect ratio
3. ```FP_CORE_MARGIN``` - The length of the margin surrounding the core area
4. ```FP_IO_MODE``` - defines pin configurations around the core(1 = randomly equidistant/0 = not equidistant)
5. ```FP_CORE_VMETAL``` - vertical metal layer where I/O pins are placed
6. ```FP_CORE_HMETAL``` - horizontal metal layer where I/O pins are placed
 
***Note: Usually, the parameter values for vertical metal layer and horizontal metal layer will be 1 more than that specified in the files***

#### Importance files in increasing priority order:
1. ```floorplan.tcl``` - System default settings
2. ```conifg.tcl```
3. ```sky130A_sky130_fd_sc_hd_config.tcl```
 
 To run the picorv32a floorplan in openLANE:
 
 ```
 run_floorplan
 
 ```
 
Post the floorplan run, a .def file will have been created within the ```results/floorplan``` directory. We may review floorplan files by checking the ```floorplan.tcl.``` The system defaults will have been overriden by switches set in conifg.tcl and further overriden by switches set in ```sky130A_sky130_fd_sc_hd_config.tcl.```

To view the floorplan, Magic is invoked after moving to the results/floorplan directory:

```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def

```

![Capture8](https://github.com/sindhuk95/later/assets/135046169/7a6d7cf4-aa7a-49c5-984b-d35a1f01b8d6)

One can zoom into Magic layout by selecting an area with left and right mouse click followed by pressing "z" key.

Various components can be identified by using the what command in tkcon window after making a selection on the component.

![Capture7](https://github.com/sindhuk95/later/assets/135046169/c5b43949-39c2-45f6-a986-0ecbbe9a046d)

Zooming in also provides a view of decaps present in picorv32a chip.

The standard cell can be found at the bottom left corner.

![standard cells](https://github.com/sindhuk95/SKY130_PD_WS_DAY2/assets/135046169/6c8fc17c-9af1-452e-ab91-6a308676e295)


You can clearly see I/O pins, Decap cells and Tap cells. Tap cells are placed in a zig zag manner or you can say diagonally

![image](https://github.com/sindhuk95/later/assets/135046169/d0b3526a-989c-4def-b30d-69ac9e85cc8e)


![Capture9](https://github.com/sindhuk95/later/assets/135046169/146ceead-f8b2-4e19-b8bb-2468136063ef)


### 2. Library Binding and Placement

### Netlist Binding and initial place design

First we need to bind the netlist with physical cells. We have shapes for OR, AND and every cell for pratice purpose. But in reality we dont have such shapes, we have give an physical dimensions like rectangles or squares weight and width. This information is given in libs and lefs. Now we place these cells in our design by initilaising it. 

### Optimize Placement

The next step is placement. Once we initial the design, the logic cells in netlist in its physical dimisoins is placed on the floorplan. Placement is perfomed in 2 stages:

Global Placement: Cells will be placed randomly in optimal positions which may not be legal and cells may overlap. Optimization is done through reduction of half parameter wire length.
Detailed Placement: It alters the position of cells post global placement so as to legalise them.
Legalisation of cells is important from timing point of view.

Optimization is stage where we estimate the lenght and capictance, based on that we add buffers. Ideally, Optimization is done for better timing.

### Congestion aware Placement using Replace 

Post placement, the design can be viewed on magic within results/placement directory:

```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def

```

![Capture1](https://github.com/sindhuk95/SKY130_PD_WS_DAY2/assets/135046169/63fb5104-9b15-4976-ac85-b5cb84d0273b)


*Note: Power distribution network generation is usually a part of the floorplan step. However, in the openLANE flow, floorplan does not generate PDN.  It is created after post CTS. The steps are - floorplan, placement, CTS, Post CTS and then PDN**

### 3. Need for libraries and characterization

As we know, From logic synthesis to routing and STA, each and evry stage has one thing in common i.e., logic gates/ logic cells. In order for the tool understand these gates are and their timing, we need to characterize these cells. 

### CELL DESIGN AND CHARACETRIZATION FLOWS

Library is a place where we get information about every cell. It has differents cells with different size, functionality,threshold voltages. There is a typical cell design flow steps.
1. Inputs : PDKS(process design kit) : DRC & LVS, SPICE Models, library & user-defined specs.
2. Design Steps :Circuit design, Layout design (Art of layout Euler's path and stick diagram), Extraction of parasitics, Characterization (timing, noise, power).
3. Outputs: CDL (circuit description language), LEF, GDSII, extracted SPICE netlist (.cir), timing, noise and power .lib files

## Standard Cell Characterization Flow

A typical standard cell characterization flow that is followed in the industry includes the following steps:

1. Read in the models and tech files
2. Read extracted spice Netlist
3. Recognise behavior of the cells
4. Read the subcircuits
5. Attach power sources
6. Apply stimulus to characterization setup
7. Provide neccesary output capacitance loads
8. Provide neccesary simulation commands

Now all these 8 steps are fed in together as a configuration file to a characterization software called GUNA. This software generates timing, noise, power models.
These .libs are classified as Timing characterization, power characterization and noise characterization.

### 4. TIMING CHARACTERIZATION

### Timing threshold definitions 
Timing defintion |	Value
-------------- | --------------
slew_low_rise_thr	| 20% value
slew_high_rise_thr | 80% value
slew_low_fall_thr |	20% value
slew_high_fall_thr |	80% value
in_rise_thr	| 50% value
in_fall_thr |	50% value
out_rise_thr |	50% value
out_fall_thr | 50% value

### Propagation Delay and Transition Time 

Propagation Delay :  the time difference between when the transitional input reaches 50% of its final value and when the output reaches 50% of its final value. Poor choice of threshold values lead to negative delay values. Even thought you have taken good threshold values, sometimes depending upon how good or bad the slew, the dealy might be still +ve or -ve.

```
Propagation delay = time(out_thr) - time(in_thr)
```

### Transition time or Slew 

The time it takes the signal to move between states is the transition time , where the time is measured between 10% and 90% or 20% to 80% of the signal levels.

```
Rise transition time = time(slew_high_rise_thr) - time (slew_low_rise_thr)

Low transition time = time(slew_high_fall_thr) - time (slew_low_fall_thr)
```





   



   




