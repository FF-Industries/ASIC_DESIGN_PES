# ASIC_DESIGN_PES 
## CONTENT :
- [DAY 1](https://github.com/FF-Industries/ASIC_PES-CLASS/blob/main/README.md#day-1)
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

```


