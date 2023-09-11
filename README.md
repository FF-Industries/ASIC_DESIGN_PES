# ASIC_DESIGN_PES 
## CONTENT :
- [DAY 1](https://github.com/FF-Industries/ASIC_DESIGN_PES/blob/main/README.md#day-1)
- [DAY 2](https://github.com/FF-Industries/ASIC_PES-CLASS/blob/main/README.md#day-2)
- [DAY 3](https://github.com/FF-Industries/ASIC_PES-CLASS/blob/main/README.md#day-3)
- [DAY 4](https://github.com/FF-Industries/ASIC_PES-CLASS/blob/main/README.md#day-4)
- [DAY 5](https://github.com/FF-Industries/ASIC_PES-CLASS/blob/main/README.md#day-5)

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
