# ASIC_DESIGN_PES 
## CONTENT :
- [DAY 1](https://github.com/FF-Industries/ASIC_DESIGN_PES/blob/main/README.md#day-1)
- [DAY 2](https://github.com/FF-Industries/ASIC_DESIGN_PES/blob/main/README.md#day-2)
- [DAY 3](https://github.com/FF-Industries/ASIC_DESIGN_PES/blob/main/README.md#day-3)
- [DAY 4](https://github.com/FF-Industries/ASIC_DESIGN_PES/blob/main/README.md#day-4)
- [DAY 5](https://github.com/FF-Industries/ASIC_DESIGN_PES/blob/main/README.md#day-5)

### DAY 1
#### INTRODUCTION TO OPENLANE
* PDK stands for Process Design Kit.
* It is a collection of files used to model a fabrication process for the EDA tools used to design an IC like: Process Design Rules, Device Models, Digital Standard Cell Libraries, I/O Libraries.
* Synthesis -> Floor/Power Planning -> Placement -> Clock Tree Synthesis -> Routing -> Signoff
*  **Synthesis** : Converts RTL to a ciruit, out of compomments from the standard cell library.
*  **Floor and Power Planning** - Obejctive here is to plan the silicon area and create robust power distribution network to power the chip.

      Chip Floor Planning - Partition the chip die between different system building blocks and place the I/O pads.
 
      Macro Floor Planning - We define the macro dimensions, pin locations and rows are defined.

      Power Planning - The power distribution network is contructed.
* **Placement** - Placing the cells on the floorplan rows, aligned with the sites. There are 2 steps: Global and Detailed.
*  **Clock Tree Synthesis** - To deliver the clock to all sequential elements.
*  **Routing** - Implement the interconnect using the available metal layers.
*  **Sign Off** - Perform physical verification such as DRC(Design Rule Check) and LVS(Layout vs Synthesis). Also perform STA(Static Timiing Analysis).

<img width="578" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/e2573601-468a-4835-9fc5-e83be559af67">

#### LAB
Commands to run :
```
docker
./flow.tcl -interactive
prep -design picorv32a
run_synthesis
```
Output :

<img width="196" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/036762c9-630c-46ba-a4df-03afb88548a1">&nbsp;

<img width="247" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/dd55712a-e294-4563-ac99-2f1935553106">&nbsp;

### DAY 2
#### FLOORPLANNING
* **Utilisation Factor** : The ratio of area occupied by the cells in the netlist to the total area of the core.

<img width="361" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/cc5b9cd9-2d7d-41d6-9676-391c2cdff3dd">&nbsp;

* **Aspect Ratio** : The ratio of height to the width of the die.

<img width="216" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/e27d13d3-561e-4f81-9ec7-9c24449166c6">&nbsp;

* Whenever there is a complex logic which is repeated multiple times or a design given by a third-party it can be perceived as abstract black box with input and output ports, clocks and others ,These modules can be either macros or IP.
* Macro - It is a module such as CPU Core which are developed by the entity fabicating the chip
* IP - It is an "Intellectual Propertly" which the entity fabricating the chip gets as a package from a third party or even packaged Hard IPs developed by the same entity. Common examples of IPs are SRAM, PLL, Protocol Converters and others.
* Decoupling Capacitors to the pre placed cells ,The power lines can have some RLC component causing the voltage to drop at the node where it enters the Blocks or the ground of the cell can be at a higher potential than ideally 0V
When this happens, there is a chance such that the logic transitions are not to the upper or lower noise margins but to the forbidden state causing the circuit to misbehave.
* Power Planning , When there are several cells or blocks drawing power from the same power rail and sinking power to the same ground pin the following effects are observed.Whenever there is alogic transition from 1 to 0 in a large number of cells then there is a Voltage Droop in the power lines as Voltage Drops from Vdd.Whenever there is a logic transition from 0 to 1 in a large number of cells simultaneously causes the ground potential to raise above 0V calles as Ground Bump.These effects pose a risk of driving the logic state out of the specified noise margin.
* Pin Placement, The input and output and Clock pins are placed optimally such that there is less complication in routing or optimised delay.There are different styles of pin placement in openlane like random pin placement , uniformly spaced.
#### LAB
Command to run :
```
run_floorplan
```
Output :

<img width="900" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/36a78145-d0c3-4273-a7e5-ad9f09f8c1f7">

To view the floorplan, Magic is invoked after moving to the results/floorplan directory:

Command to run :
```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```
Output :

<img width="721" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/6aa2887e-e583-42ce-b471-c1ee2d6cb474">

One can zoom into Magic layout by selecting an area with left and right mouse click followed by pressing "z" key.Various components can be identified by using the what command in tkcon window after making a selection on the component.

<img width="720" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/13ff276b-d9b0-478c-a0c6-12b57848c30d">

First we need to bind the netlist with physical cells. We have shapes for OR, AND and every cell for pratice purpose. But in reality we dont have such shapes, we have give an physical dimensions like rectangles or squares weight and width. This information is given in libs and lefs. Now we place these cells in our design by initilaising it.

#### Optimize Placement
* In this stage, we estimate length & capacitance of wires to determine the optimized placement of cells. So if we have not maintained signal integrity, then we use buffers to reduce wire length and capacitance and have optimized placement.
* Placement occurs in two stages: GLobal & detailed placement. Global Placement: It finds optimal position for all cells which may not be legal and cells may overlap. Optimization is done through reduction of half parameter wire length.Detailed Placement: It alters the position of cells post global placement so as to legalise them.

#### LAB
Command to run :
```
run_placement
```
Output :

<img width="165" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/150127c6-ba14-49fd-8063-36899cebcf4a">

<img width="723" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/d0fb2d55-5c33-40d3-a162-40eabbf40e88">


#### Cell design
Standard cell design flow involves the following:

-Inputs: PDKs, DRC & LVS rules, SPICE models, libraries, user-defined specifications.

-Design steps: Circuit design, Layout design (Art of layout Euler's path and stick diagram), Extraction of parasitics, Characterization (timing, noise, power).

-Outputs: CDL (circuit description language), LEF, GDSII, extracted SPICE netlist (.cir), timing, noise and power .lib file

#### Standard Cell Characterization Flow
A typical standard cell characterization flow that is followed in the industry includes the following steps:

1. Read in the models and tech files

2. Read extracted spice Netlist

3. Recognise behavior of the cells

4. Read the subcircuits

5. Attach power sources

6. Apply stimulus to characterization setup

7. Provide neccesary output capacitance loads

8. Provide neccesary simulation commands

Now all these 8 steps are fed in together as a configuration file to a characterization software called GUNA. This software generates timing, noise, power models. These .libs are classified as Timing characterization, power characterization and noise characterization.

#### TIMING CHARACTERIZATION

We have timing threshold definitions as follows:

## Timing threshold definitions 
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

**Propagation Delay** :The time difference between when the transitional input reaches 50% of its final value and when the output reaches 50% of its final value. Poor choice of threshold values lead to negative delay values. Even thought you have taken good threshold values, sometimes depending upon how good or bad the slew, the dealy might be still +ve or -ve.
```
Propagation delay = time(out_thr) - time(in_thr)
```
**Transition Time** :The time it takes the signal to move between states is the transition time , where the time is measured between 10% and 90% or 20% to 80% of the signal levels.
```
Rise transition time = time(slew_high_rise_thr) - time (slew_low_rise_thr)
```
```
Low transition time = time(slew_high_fall_thr) - time (slew_low_fall_thr)
```

### DAY 3
#### Design Library Cell using ngspice simulations
IO Placer revision
* PnR is a iterative flow and hence, we can make changes to the environment variables in the fly to observe the changes in our design.
* Let us say If I want to change my pin configuration along the core from equvi distance randomly placed to someother placement, we just set that IO mode variable on command prompt as shown below.

#### SPICE Deck Creation and Simulation for CMOS inverterBefore performing a SPICE simulation we need to create SPICE Deck SPICE Deck provides information about the following:
*Component connectivity - Connectivity of the Vdd, Vss,Vin, substrate. Substrate tunes the threshold voltage of the MOS.
*component values - values of PMOS and NMOS, Output load, Input Gate Voltage, supply voltage.
*Node Identification and naming - Nodes are required to define the SPICE Netlist For example M1 out in vdd vdd pmos w = 0.375u L = 0.25u , cload out 0 10f

<img width="584" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/d1525eca-e35a-49cf-a504-f512ed47dc9d">&nbsp;

#### Switching Threshold Vm

*The Switching Threshold of a CMOS inverter is the point where the Vin = Vout on the DC Transfer characreristics.
*At this point, both the transistors are in saturation region, means both are turned on and have high chances of current flowing driectly from VDD to Ground called Leakage current.

<img width="598" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/60f7860e-2c87-4910-bac3-e5e1fc309ba8">

#### LAB
Command to run :
```
git clone https://github.com/nickson-jose/vsdstdcelldesign.git
magic -T sky130A.tech sky130_inv.mag &
```
output :

<img width="263" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/ccdc7d64-3649-4dbe-bead-941dd43cf781">

#### Mask CMOS Fabrication
The 16-mask CMOS (Complementary Metal-Oxide-Semiconductor) fabrication process involves several crucial steps for creating integrated circuits. Let's break it down with some jargon:

1. Substrate Selection: In the initial phase, the appropriate semiconductor substrate is chosen.

2. Active Region Creation: To isolate the active regions for transistors, the process begins with the deposition of SiO2 and Si3N4 layers, followed by photolithography and silicon nitride etching.This is known as LOCOS (Local Oxidation of Silicon), where oxide is grown in certain regions.Subsequently, Si3N4 is removed using hot phosphoric acid.

3. N-Well and P-Well Formation:The N-well and P-well regions are created separately.P-well formation involves photolithography and ion implantation of p-type Boron material into the p-substrate.N-well is formed similarly with n-type Phosphorus material.High-temperature furnace processes drive-in diffusion to establish well depths, known as the tub process.

4. Gate Formation: The gate is a pivotal CMOS transistor terminal that controls threshold voltages for transistor switching.A polysilicon layer is deposited and photolithography techniques are applied to create NMOS and PMOS gates.Important parameters for gate formation include oxide capacitance and doping concentration.

5. Lightly Doped Drain (LDD) Formation: LDD is created to mitigate hot electron and short channel effects.

6. Source & Drain Formation: Thin oxide layers are added to avoid channel effects during ion implantation.N+ and P+ implants are performed using Arsenic implantation and high-temperature annealing.

7. Local Interconnect Formation: Thin screen oxide is removed through etching in HF solution.Titanium deposition through sputtering is initiated.  Heat treatment results in chemical reactions, producing low-resistant titanium silicon dioxide for interconnect contacts and titanium nitride for top-level connections, enabling local communication.

8. Higher Level Metal Formation: To achieve suitable metal interconnects, non-planar surface topography is addressed.Chemical Mechanical Polishing (CMP) is utilized by doping silicon oxide with Boron or Phosphorus to achieve surface planarization.TiN and blanket Tungsten layers are deposited and subjected to CMP.An aluminum (Al) layer is added and subjected to photolithography and CMP.This constitutes the first level of interconnects, and additional interconnect layers are added to reach higher-level metal layers.

9. Dielectric Layer Addition: Finally, a dielectric layer, typically Si3N4, is applied to safeguard the chip.

#### Designing standard cell and SPICE extraction in MAGIC

In this section, we will verify the logic implemented by layout by extracting spice netlist and performing simulation in ngspice.

Commands to run :
```
pwd
extract all
ext2spice cthresh 0 rthresh 0
ext2spice
```

```
ngspice sky130_inv.spice
plot y vs time a
```
Output: 

![image](https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/9417a0e7-a082-4cc6-b4a5-909b3cbb78d4)

<img width="619" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/ce27c2f6-6c2d-4610-8f07-8c1d3daa5052">

<img width="624" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/ae977d84-4f92-4ab2-8055-79d87d4e275e">

<img width="621" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/a800456b-f5c0-4f92-9aa8-506db669a4a9">

#### DRC violations

Commands to run :
```
wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz
tar xfz drc_tests.tgz
magic -d XR met3.mag
```

Output :

<img width="599" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/0151020c-65be-42a0-bccc-dfb1051b9b68">

<img width="623" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/29441c9e-5e6f-4695-bd2a-4f30ab8b05b1">


#### Load Sky130 tech rules for drc challenges

In tkcon window use ```drc why``` to see error.
First load the poly file by load poly.mag on tkcon window.There is a DRC error in the poly.mag file in 'poly.9'.

<img width="620" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/c971499d-0cde-4f99-b85c-0b1dc2423476">

In line :
```
spacing npres *nsd 480 touching_illegal \
	"poly.resistor spacing to N-tap < %d (poly.9)"
```
Change to :
```
spacing npres allpolynonres 480 touching_illegal \
	"poly.resistor spacing to N-tap < %d (poly.9)"
```
In line ;
```
spacing xhrpoly,uhrpoly,xpc alldiff 480 touching_illegal \
	"xhrpoly/uhrpoly resistor spacing to diffusion < %d (poly.9)"
```
Change to :
```
spacing xhrpoly,uhrpoly,xpc allpolynonres 480 touching_illegal \
	"xhrpoly/uhrpoly resistor spacing to diffusion < %d (poly.9)"
```

<img width="612" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/eafc8dd7-d12b-4b4d-9097-0b65418df3bd">

### DAY 4

#### Timing Analysis & CTS

A requirement for ports as specified in tracks.info is that they should be at intersection of horizontal and vertical tracks. The CMOS Inverter ports A and Y are on li1 layer. It needs to be ensured that they're on the intersection of horizontal and vertical tracks. We access the tracks.info file by ```less tracks.info```.

<img width="186" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/23b99b85-df15-4afe-b6bb-3f099ff6155f">

To ensure that ports lie on the intersection point, the grid spacing in Magic (tkcon) must be changed to the li1 X and li1 Y values. Convergence of grid and tracks can be achieved using the following command:
```
grid 0.46um 0.34um 0.23um 0.17um
```
LEF extraction can be carried out in tkcon as follows:
```
lef write
```
This generates sky130_vsdinv.lef file.

Type less sky130_vsdinv.lef

<img width="615" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/450adac1-d663-4b74-9e4b-d81eb1a7fe09">

Then copy the file .

<img width="618" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/b5546516-f9aa-4688-9aaa-0e23efbbad24">


#### Steps to include custom cell in ASIC design
We have created a custom standard cell in previous steps of an inverter. Copy lef file, sky130_fd_sc_hd_typical.lib, sky130_fd_sc_hd_slow.lib & sky130_fd_sc_hd_fast.lib to src folder of picorv32a from libs folder vsdstdcelldesign. Then modify the config.tcl as follows.
```
set ::env(DESIGN_NAME) "picorv32a"

set ::env(VERILOG_FILES) "$::env(DESIGN_DIR)/src/picorv32a.v"

set ::env(CLOCK_PORT) "clk"
set ::env(CLOCK_NET) $::env(CLOCK_PORT)

set ::env(GLB_RESIZER_TIMING_OPTIMIZATIONS) {1}

set ::env(LIB_SYNTH) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"
set ::env(LIB_SLOWEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib"
set ::env(LIB_FASTEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib"
set ::env(LIB_TYPICAL) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"

set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]

set filename $::env(DESIGN_DIR)/$::env(PDK)_$::env(STD_CELL_LIBRARY)_config.tcl
if { [file exists $filename] == 1} {
	source $filename
}
```
To integrate standard cell in openlane flow after make mount , perform following commands:
```
prep -design picorv32a -tag RUN_2023.09.09_20.37.18 -overwrite 
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
run_synthesis
```

<img width="648" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/811d1275-73da-4e2c-b5a9-76cb39e84795">

<img width="275" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/278a9583-ea31-4d29-b4c6-17ab56304d54">

Next floorplan is run, followed by placement:
```
init_floorplan
run_placement
```

<img width="621" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/3aefbe06-d6ce-43c7-80a9-bd846f09992b">

#### Post-synthesis timing analysis Using OpenSTA

Timing analysis is carried out outside the openLANE flow using OpenSTA tool. For this, a new file pre_sta.conf is created. This file would be reqiured to carry out the STA analysis. Invoke OpenSTA outside the openLANE flow as follows: ```sta pre_sta.conf```.

<img width="613" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/ad546e75-7f83-43f4-aeaa-44b3e424602e">

There is a slack violation.
Setting MAX_FANOUT value to 4 reduces the slack violation : ```set ::env(SYNTH_MAX_FANOUT) 4```.

#### Clock Tree Synthesis 
The purpose of building a clock tree is enable the clock input to reach every element and to ensure a zero clock skew. H-tree is a common methodology followed in CTS. To run CTS use the below command: ```run_cts```.

The CTS run adds clock buffers in therefore buffer delays come into picture and our analysis from here on deals with real clocks. Setup and hold time slacks may now be analysed in the post-CTS STA anlysis in OpenROAD within the openLANE flow:
```
openroad
write_db pico_cts.db
read_db pico_cts.db
read_verilog /openLANE_flow/designs/picorv32a/runs/03-07_11-25/results/synthesis/picorv32a.synthesis_cts.v
read_liberty $::env(LIB_SYNTH_COMPLETE)
link_design picorv32a
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc
set_propagated_clock (all_clocks)
report_checks -path_delay min_max -format full_clock_expanded -digits 4
```

<img width="501" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/2dd0b7aa-2892-4a6c-87f8-703a524125e2">

<img width="484" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/7d1cfafa-ae60-4434-9c3e-48a859ca2a90">

```
report_clock_skew -hold
report clock_skew -setup
```

<img width="463" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/66d3c9b9-ec07-46a3-a49a-91b2acf51df3">

### DAY 5
#### Final steps in RTL2GDS

* Routing is the process of establishing a physical connection between two pins. Algorithms designed for routing take source and target pins and aim to find the most efficient path between them, ensuring a valid connection exists.

* The Maze Routing algorithm, such as the Lee algorithm, is one approach for solving routing problems. In this method, a grid similar to the one created during cell customization is utilized for routing purposes. The Lee algorithm starts with two designated points, the source and target, and leverages the routing grid to identify the shortest or optimal route between them.

<img width="414" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/56a60717-a6b2-46b4-a78e-a669d8b8eaed">

* DRC verifies whether a design meets the predefined process technology rules given by the foundry for its manufacturing. DRC checking is an essential part of the physical design flow and ensures the design meets manufacturing requirements and will not result in a chip failure. It defines the Quality of chip. They are so many DRCs.

#### Power Distribution Network generation

Unlike the general ASIC flow, Power Distribution Network generation is not a part of floorplan run in OpenLANE. PDN must be generated after CTS and post-CTS STA analyses: ```gen_pdn```.

<img width="561" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/da7c5417-8795-4559-bb19-0609c734b6d8">

```gen_pdn``` – Generate a power distribution network The power distribution network must use design_cts.def as the input def file. This creates a grid and band for Vdd and floor. These are placed around the standard cell. A standard cell is designed so that its height is a multiple of the distance between its Vdd and ground bar. 

<img width="535" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/035cc2e0-ce68-46d5-8666-07e530efda10">

Start routing by using : ```run_routing```.

<img width="569" alt="image" src="https://github.com/FF-Industries/ASIC_DESIGN_PES/assets/136846161/e0c5ff26-c9e7-4651-8d80-65287107fa7a">

#### SPEF Extraction
After routing has been completed interconnect parasitics can be extracted to perform sign-off post-route STA analysis. The parasitics are extracted into a SPEF file.
