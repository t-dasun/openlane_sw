# OpenLANE_sky130_PD_workshop



## Table of contents
### DAY 1  Introduction to open-source EDA, OpenLANE and SKY130PDK
- HOW TO TALK TO COMPUTERS
   - QFN-48 PAckage, Foundary IPS and Macros
   - RISC-V Instruction set Architecture (ISA)
- SOC DESIGN USING OPENLANE
   - Components of Opensource Digital ASIC Design
   - Simplified RTL2GDS Flow
   - INTRODUCTION TO OPENLANE and STRIVE Chipsets
- Getting Familiar to OpenSource EDA Tools
   - OpenLANE Directory Structure
   - DOCKER, Package and Interactive Mode
   - Design preparation step
   - Synthesis run and Flop ratio
 
### DAY 2 Floorplan and Introcution to Library Cells
- Floorplanning considerations
   - Utilization factor and Aspect Ratio
   - Pre-placed cells
   - Decoupling capcitors
   - Powerplanning
   - Pin placement 
   - Floorplan run on OpenLANE & review layout in Magic
- Library Binding and Placement
   - Netlist binding and Initial Place Design
   - Optimize Placement
   - Congestion aware Placement
   - Need for libraries and Characterization 
- Cell design and characterization
   - Standard Cell characterization flow
- Timing Characterization
  - Timing Threshold definition
  - Propagation Delay and Transition Time
  
### DAY 3 Design Library Cell using ngspice simulations
- CMOS inverter ngspice simulations
   - IO placer revision
   - SPICE Deck Creation and Simulation for CMOS inverter
   - Switching Threshold Vm
   - Lab steps to git clone vsdstdcelldesign
- Inception of Layout and CMOS Fabrication Process
   - Mask CMOS Fabrication
   - SKY130 basic layer layout and LEF using inverter
   - Designing standard cell and SPICE extraction in MAGIC
- SKY130 Tech File Labs
   - Create Final SPICE Deck
   - Standard cell characterisation CMOS Inverter
   - LAB exercise and DRC Challenges
      - Intrdocution of Magic and Skywater DRC's
      - Sky130s pdk intro and Steps to download labs
      - Load Sky130tech rules for drc challenges
     
### DAY 4 Pre-layout Timing analysis and CTS
- Timing Analysis and Clock Tree Synthesis (CTS)
   - Standard cell LEF generation
   - Convert Grid into Track info
   - Create Port definition
   - Set Port class and Port use attributes for layout
   - Custom Cell naming and LEF extraction
   - Intergrating custom cell in OpenLANE
   - Delay tables
- Post-synthesis timing analysis Using OpenSTA
- Clock Tree Synthesis using Tritoncts

### DAY 5 Routing the Final steps in RTL2GDS
- Power Distribution Netwrok generation
- ROUTING
- Features of Tritonroute
- SPEF extraction
