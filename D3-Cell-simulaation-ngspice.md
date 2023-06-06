# SKY130_PD_WS_DAY3
# Design Library cell using MAGIC Layout and ngspice characterization


# Table of Contents
1. CMOS inverter ngspice simulations
   - IO placer revision
   - SPICE Deck Creation and Simulation for CMOS inverter
   - Switching Threshold Vm
   - Lab steps to git clone vsdstdcelldesign
2. Inception of Layout and CMOS Fabrication Process
   - Mask CMOS Fabrication
   - SKY130 basic layer layout and LEF using inverter
   - Designing standard cell and SPICE extraction in MAGIC 
3. SKY130 Tech File Labs
   - Create Final SPICE Deck 
   - Standard cell characterisation CMOS Inverter
   - LAB exercise and DRC Challenges
       - Intrdocution of Magic and Skywater DRC's
       - Sky130s pdk intro and Steps to download labs
       - Load Sky130tech rules for drc challenges


# CMOS inverter ngspice simulations

### IO Placer revision
 - PnR is a iterative flow and hence, we can make changes to the environment variables in the fly to observe the changes in our design. 
 - Let us say If I want to change my pin configuration along the core from equvi distance randomly placed to someother placement, we just set that IO mode variable on command prompt as shown below
 ```
 set ::env(FP_IO_MODE) 2
```

### SPICE Deck Creation and Simulation for CMOS inverter

- Before performing a SPICE simulation we need to create SPICE Deck
SPICE Deck provides information about the following:
- Component connectivity - Connectivity of the Vdd, Vss,Vin, substrate. Substrate tunes the threshold voltage of the MOS.
- component values - values of PMOS and NMOS, Output load, Input Gate Voltage, supply voltage.
- Node Identification and naming - Nodes are required to define the SPICE Netlist
     For example ```M1 out in vdd vdd pmos w = 0.375u L = 0.25u``` , ```cload out 0 10f```
- Simulation commands
- Model file - information of parameters related to transistors

Simulation of CMOS using different width and lengths. From the waveform, irrespective of switching the shape of it are almost same.

- ![image](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/5b494ae5-341a-41ff-a2bb-1db066fa2b72)

From the waveform we can see the characteristics are maintained  across all sizes of CMOS. So CMOS as a circuit is a robust device hence use in designing of logic gates. Parameters that define the robustness of the CMOS are

### Switching Threshold Vm

- The Switching Threshold of a CMOS inverter is the point where the Vin = Vout on the DC Transfer characreristics. 
- At this point, both the transistors are in saturation region, means both are turned on and have high chances of current flowing driectly from VDD to Ground called Leakage current.

![image](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/3393442e-1b4b-434a-bc7e-6e2ed4fde218)


Through transient analysis, we calculate the rise and fall delays of the CMOS by SPICE Simulation. As we know delays are calculated at 50% of the final values.

### Lab steps to git clone vsdstdcelldesign

- First, clone the required mag files and spicemodels of inverter,pmos and nmos sky130. The command to clone files from github link is:
```
git clone https://github.com/nickson-jose/vsdstdcelldesign.git
```
once I run this command, it will create ``vsdstdcelldesign`` folder in openlane directory. I have got an error saying `` could not  resolve host: github.com`` 
somehow I figured it was due incorrect proxy, so I used this command  `` git config --global --unset https:proxy``. It worked and could clone the files from github to my openlane.

![day3-1](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/5fcb599b-48bf-4667-995c-2ff99e7b6358)

The information of inverter layout is mentioned in ``sky130_inv.mag`` file.

![day3-7](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/d42a28e9-34a0-46c7-8799-03c668709207)

Inorder to open the mag file and run magic, we need tech file for magic in that location. So, lets copy the tech file from magic which is in pdks directory to vsdstdcelldesign folder
command to copy the file is

``` cp sky130A.tech /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign/ ```

once we run this command, the tech file will be copied to vsdstdcelldesign folder.

![image](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/172d04b0-5701-4bdd-b020-e32775932bf4)

For layout we run magic command

``` magic -T sky130A.tech sky130_inv.mag & ```

Ampersand at the end makes the next prompt line free, otherwise magic keeps the prompt line busy. Once we run the magic command we get the layout of the inverter in the magic window

![day3-3](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/47174505-3cbc-4110-b342-317eef4d849d)

Press G in the magic console and you can see the grids being disaplyed

![size of row](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/ec4a0ff1-9009-4365-b66c-9e352ba1842a)

# Inception of Layout and CMOS Fabrication Process

### Mask CMOS Fabrication

The 16-mask CMOS process consists of the following steps:

- **Selecting the subtrate**
- **Creating actove region for Transistors** -  Isolation between active region pockets by depositing the oxide layer SiO2, silicon nitride Si3N4,then photoresist and masking the layers followed by photolithography and etch of the silicon nitride and remove photoresist. then this is placed in oxidation furnace, helps in growing oxide layer in other regions. This process is called LOCOS "Local oxidation of silicon" and lastly we strip out Si3N4 using hot phosphoric acid.
- **N-well and P-well formation**: Both wells are created separately. First Pwell is created in steps like Photoresist, mask, photolithpgraphy and a p-type Boron material is diffused into psubstrate using Ion Implantation, forming P-well and same goes for n-well formation by diffusing a n-type Phosphorous material. Till now wells are created, by using High Temperature furnace process, drive-in dussion happens and  creating depths of wells and this process of creating is called tub process.
- **Formation of gate** - Gate is the most important terminal of CMOS transistor, where you control the threshold voltages and these voltages turns off/on the transistor.polysilicon layer is depositied and photolithography techniques are perfomed created NMOS and PMOS gates. The two parameters for gate formation are
   - Oxide capacitance
   - doping concentration    
- **lightly doped drain(LDD) formation** - To prevent hot electron effect and short channel effect
- **Source & drain formation**: In this, a thin layer of oxide is added to avoid the effect of channel during implants. It is where the vector velocity of ions matches with that of the crystalline structure of the p substrate and go directly into the substrate without hitting any silicon atom, when we do alot of ion implanatation. N+,P+ implants are done by Aresenic implantation and Hight temperature annealing.
- **Local interconnect formation**: Remove the thin screen oxide by etching in (hydrofloric) HF solution. There are various step to form local interconnects. One is
   - **deposit titanium on wafer using supttering** - Once it is depoisted and heating is done in N2 ambient, chemical reaction happens creating two kind of metal contacts, titanium silicon dioxide which are low resistant,used for interconnect contacts and titanium nitride(etched using RCA cleaning) used for the connection you want bring to the top,local communication
- **Higher level metal formation**: Non Planar surface topography is not suitable for metal interconnects. Planarize the surface by doping silicon oxide with Boron or Phosphorous to polish the surface. This technque is called chemical Mechanical polishing (CMP). Then we perform Photolithography techniques follwed by  TiN and balnket Tungsten layer deposition and do CMP. Deposit AL layer and repeat photolithography and CMP. This is 1st level of interconnect. Add another layer of interconnects to take to higher level metal layers. Lastly, Layer a dielectric i.e., Si3N4 to protect the chip. 

 ![Screenshot (34)](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/ae5675b9-5b61-4472-81c5-c0cb8f282529)

### SKY130 basic layer layout and LEF using inverter

- From Layout, we see the layers which are required for CMOS inverter. Inverter is, PMOS and NMOS connected together.
- Gates of both PMOS and NMOS are connected together and fed to input(here ,A), NMOS source connected to ground(here, VGND), PMOS source is connected to VDD(here, VPWR), Drains of PMOS and NMOS are connected together and fed to output(here, Y). 

The First layer in skywater130 is ``localinterconnect layer(locali)`` , above that metal 1 is purple color and metal 2 is pink color.

If you want to see connections between two different parts, place the cursor over that area and press S three times. The tkson window gives the connectivity information.

![image](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/fc51c2de-26f5-4ada-8c39-f5f29604999b)

#### Library exchange format (.lef)

- The layout of a design is defined in a specific file called LEF.
-  It includes design rules (tech LEF) and abstract information about the cells. 
    - ```Tech LEF``` -  Technology LEF file contains information about the Metal layer, Via Definition and DRCs.
    - ```Macro LEF``` -  Contains physical information of the cell such as its Size, Pin, their direction.
 
### Designing standard cell and SPICE extraction in MAGIC 

  -  First we need to provide bounding box width and height in tkson window. lets say that width of BBOX is 1.38u and height is 2.72u. The command to give these values to magic is
   ``` property Fixed BBOX (0 0 1.32 2.72)  ```
  - After this, Vdd, GND segments which are in metal 1 layer, their respective contacts and atlast logic gates layout is defined
Inorder to know the logical functioning of the inverter, we extract the spice and then we do simulation on the spice. To extract it on spice we open TKCON window, the steps are
  - Know the present directory - ``pwd ``
  - create an extration file -  the command is  `` extract all `` and  ``sky130_inv.ext`` files has been created
          
  - create spice file using .ext file to be used with our ngspice tool  - the commands are  
      ``` ext2spice cthresh 0 rthresh 0 ``` - extracts parasatic capcitances also since these are actual layers - nothing is created in the folder
      ``` ext2spice ``` - a file ```sky130_inv.spice``` has been created.
      
     ![image](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/80394fcd-2b43-49fb-b51b-af80304888a0)
     
# SKY130 Tech File Labs
     
### Create Final SPICE Deck 
  
let us see what is inside the spice Deck

In the spice file subcircuit(subckt), pmos and nmos node connections are defined
   
For NMOS  ``` XO Y A VGND VGND sky130_fd_pr_nfet_01v8 ``` . The order is  ``` Cell_name Drain Gate Source Substrate model_name ``` .
For PMOS  ``` X1 Y A VPWR VPWR sky130_fd_pr_pfet_01v8 ``` . The order is   ``` cell_name Drain Gate Source Substrate model_name ```.
   
For transient anaylsis, we would like to define these following connections and extra nodes for these in spice file
  - VGND to VSS
  - Supply voltage from VPWR to Ground - extra nodes here will be 0 and VDD with a value of 3.3v 
  - sweep in/pulse between A pin and VGND (0)
Before, editing the file, make sure scaling is proper, we measure the value of the gride size from the magic layout and define using `` .option scale=0.01u`` in the Deck file.
  
Now keeping the connection in mind, define the required commands in the file. Along with this we need to include libs for nmos ``nshort.lib`` and pmos ``pshort.lib`` and define transient analysis commands too. We comment the subckt since we are trying to input the controls and transient analysis also. Model names are changed to ``nshort_model.0`` and ``pshort_model.0`` according to the libs of nmos and pmos.
  
  These voltage sources and simulation commands are defined in the Deck file.
   ``
   VDD VPWR 0 3.3V
   VSS VGND 0 0V
   Va A VGND PULSE(0V 3.3V 0 0.1ns 0.1ns 2ns 4ns)
   .tran 1n 20n
   .control
   run
   .endc
   .end
   ``
  
    
 ![image](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/f55eb2a7-0251-4fbd-9b40-24a8088f3bbb)
  
### Using ngspice for spice simulation
  
Spice Deck is done and now to run spice simulation invoke ngspice in the tool and pass the source file. 
 
  ``` ngspice sky130_inv.spice ```
  
On the prompt you can see the values the ngspice has taken. To see the plot, use
   
   ``` plot y vs time a ``` .
   
Now we have the transient response, the next objective is to characterise the cell.
   
    
   ![image](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/603a0396-bd48-4d2d-a74e-34815ab26f94)

     
 ### Standard cell characterization of CMOS Iinverter 
 
 characterization of the inverter standard cell depends on Four timing parameters

 **Rise Transition**: Time taken for the output to rise from 20% to 80% of max value
 **Fall Transition**: Time taken for the output to fall from 80% to 20% of max value
 **Cell Rise delay**: difference in time(50% output rise) to time(50% input fall)
 Cell Fall delay = difference in time(50% output fall) to time(50% input rise)
 
 The above timing parameters can be computed by noting down various values from the ngspice waveform.
 
 ``` Rise Transition : 2.25421 - 2.18636 = 0.006785 ns / 67.85ps ```
 
 ``` Fall Transitio : 4.09605 - 4.05554 = 0.04051ns/40.51ps ```
 
 ```Cell Rise Delay : 2.21701 - 2.14989 = 0.06689ns/66.89ps ```

 ```Cell Fall Delay : 4.07816 - 4.05011 = 0.02805ns/28.05ps ```

## LAB exercise and DRC Challenges

### Intrdocution of Magic and Skywater DRC's

  - In-depth overview of Magic's DRC engine
  - Introduction to Google/Skywater DRC rules
  - Lab : Warm-up exercise : Fixing a simple rule error
  - Lab : Main exercie : Fixing or create a complex error
 
Before doing the lab, Get the knowledge of how Magic performs DRc's and Syntax for DRC rules  from this URL: ``` http://opencircuitdesign.com/magic```.
Nothing works if there is no Technology file. Fortunatley by Google/ Skywater, we have the opportunity to use the fabication process technology now made as an open source.
skywater sky130 pdk URL : ``` https://skywater-pdk.readthedocs.io/en/main/ ```
The two main sources of information PDK:
  - github.com - ```https://github.com/google/skywater-pdk``` - understand the design rules 
  - Documentation - ``` https://skywater-pdk--136.org.readthedox.build```
  
Since github is not used for tutorial and documentation is critical for lab since browser needs to be open for the lab.
  
### Sky130s pdk intro and Steps to download labs
  
  - setup to view the layouts
  - For extracting and generating views, Google/skywater repo files were built with Magic
  - Technology file dependency is more for any layout, this is file is created first.
  - Since, Pdk is still under development, there are some unfinished tech files and these are packaged for magic along with lab exercise layout and bunch of stuff into the tar ball
 
We can download the packaged files from web using ``wget `` command. wget stands for web get, a non-interactive file downloader command.
  
  ``` wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz```
  
The archive file drc_tests.tgz is downloaded into our user directory 
  
 ![wget](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/3358159c-b338-43f4-b811-58a11284e75b)

Now, we need to unzip the archive file. For this we use tar utility command. 

`` tar - tap archiver`` create and extract a tar archive file.

The command to extract the tar file is 

`` tar -xfz drc_tests.tgz``

once extraction is done, drc_tests file is created and you will have all the information about magic layout for this lab exercise

![tar](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/6c27f1d8-57ce-4796-bbc5-9345a4843920)

Now its time to run MAGIC

For better graphics use command ``magic -d XR ``

Now, lets see an example of simple failing set of rules of metal 3 layer.  you can either run this by magic command line `` magic -d XR metal3.mag `` or from the magic console window, `` menu - file - open -load file9here, metal3.mag) ``

![ms](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/ac4b681b-b486-4db0-9eef-23f532d5b685)


We have few drc errors and calls out a rule number. You can see these rules on google skywater pdk read the doc page.

From metal 3.4 drc rules, Via2 must be enclosed by metal3 by atleast 0.065u. 
Magic tablet is of using many derived layers and in which via is one. Via represents the area filled with contact cuts.
Draw a large area of M3 contatc using mouse pointer hovering over the m3contact icon on the side toolbar. Now with cursor box around the m3 area, type command 
``` cif see VIA2 ``` . This create contatc cuts which are bloack sqaure shaped. 
These dont exist on the drawn layout, but they represent as mask layer VIA2, that will end up in the GDS
Futher details about these are metioned in cif output section in tech file.
The view we see is feedback entry and can dismiss it with `` feed clear `` We use snap internal command `` snap int `` for the cursor to move along the edge of the grid.
There are few spacing rukes metioned like spacing between Via2 and metal 3 is 0.065u. 
If we put the curosor between the contact cut and drawn via edge and click box in tkcon command, we will get the distance.
The tool automatically places and do spacing of the contact, there wont be any DRC errors and the distance will not be smaller than the specified one.

![area via2](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/25b99b94-8382-4ca4-b214-1c844ae8dfe8)

### Load Sky130 tech rules for drc challenges

First load the poly file by ``load poly.mag`` on tkcon window.
Finding the error by mouse cursor and find the box area, Poly.9 is violated due to spacing between polyres and poly.

![poly spacing](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/afbfc91e-65b5-4efc-ab28-98eade6b454c)

View the tech file and search whether we have created any drc rule for poly.9. If there is none, create a rule defining spacing between polyres and poly.
This is how we add spacing for all the polyres

![earlier for diff tap](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/74529987-6524-4ecd-9cdf-1598f7d7adb1)

Once the tech file has been modified for drc, we load the tech file in tkcon window and tool doesnot check drc automatically so we need to check drc too. Once loaded, the violations are reduced since spacing rule is met.

`` tech load sky130A.tech `` followed by `` drc check ``

![fizing spacing btwn polyres to poluy](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/b922a896-4f06-444e-af78-319326956082)

Now, poly.9 is spacing between polyres to poly and poly to diff/tap. Once we resolve the polyres to poly, poly to tap / diff got violated. So we provide spacing for tap/diff. in tech, 

![spacing for all diff](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/6fbf7226-9111-4b57-80f9-d1a27f6ed06e)

Again load the tech file, check drc and the issue will be solved.











  
  
  
  




   
    
  
  
  
  

  

   
  
    



