# Multi-phase Non-overlapping Clock Generator
- This is a submission in lieu of successful completion of following through simulating the proposed circuit as a result of literature survey conducted, for [SFAL Recruitment Hackathon Drive](https://www.sfalcoe.com/sfal_hackathon-recruitment-drive/).

- The aforementioned literature survey can be referred [here](https://github.com/Anmol-wq/Clock_Generator/blob/main/Anmol_Saxena_literature_survey_report.pdf) in the repository, and the .cir file for doing the SPICE simulations can be found [here](https://github.com/Anmol-wq/Clock_Generator/blob/main/clockgen2.cir), as well.

- A multiphase clock generator is a requirement for wide band multi-path transmission line filter, amongst other relevant applications. There are various implementations for the same. The purpose of this project is to implement a 4-phase non-overlapping clock generator using eSim (An open-source Electronic Design Automation tool designed by FOSSEE Project team of Indian Institute of Technology Bombay) and SkyWater 130nm PDK (Process Design Kit) sponsored by Google. It is fully digital and hence the number of parameters to be taken care is fairly less compared to its analog counterparts.

- A frequency divider circuit (Divide-by-4) is realized using the D-FFs and NAND gate visualized in the reference circuit below. The inverter and buffer string further help in obtaining the output as required which are delayed through the provided D-FFs ahead for the multi-phase clock generation.  

## Table of Contents
- [Introduction](#introduction)
- [Reference Circuit Diagram](#reference)
- [Implemented Circuit Details](#implementation)
- [Schematic Diagram and Waveform](#schematic)
- [Methodology](#methodology)
- [Challenge faced](#challenge)
- [Troubleshooting](#troubleshooting)
- [Steps to Reproduce waveforms](#steps)
- [Limitations](#limitations)
- [References](#references)
- [Acknowledgements](#acknowledgement)
- [Author](#author)

## Introduction
- The circuit is a CMOS transistor-based implementation. It consists of 6 D-Flip Flops, 16 inverters: segregated in 2 types owing to their deliberate sizing for generation of non-overlapping waveforms for 4 different phases: (0°, 90°, 180° and 270°), 12 buffers with uniform tapered dimensions towards output, a NAND gate and a NOT gate. A reference clock signal is utilized for generation of desired signals. A tapered buffer is used to drive the transistor gate, which carries a large capacitive load due to the transistor’s parasitic capacitance. The output buffer design was based on using a string of inverters where each has an increasing width compare to the last. By sizing up an inverter, its delay will be reduced, and input capacitance may be increased. The number of stages in the buffer depends on the load capacitance. 

- The schematic creation and the SPICE netlist generation was done using [eSim 2.1](https://esim.fossee.in/home), an open-source EDA tool.

- ngSPICE 31 is used for transient simulation and waveform generation.

- Open source process design kit for usage with SkyWater Technology Foundry's 130nm node, [Google SkyWater SKY130](https://github.com/google/skywater-pdk), is utilized here.
 
 
## Reference Circuit Diagram

![Hackathon2](https://user-images.githubusercontent.com/73732594/153037798-73c62c2a-b92a-4263-939f-9f9303abf294.png)

Recreated the reference diagram from the [source](https://curve.carleton.ca/5ff0a03b-3cf7-42f8-ab3e-cb9a1379b43a) in Microsoft Visio 2016.


## Implemented Circuit Details

- Each D-Flip Flop is a CMOS transmission-gate based implementation, and is sized uniformly with (W/L)pFET/(W/L)nFET = (0.55u/0.15u)/(0.42u/0.15u). 
- It has total 16 transistors, thus 96 transistors for 6 flip-flops here.
- A inverter circuit is used separately to provide clock and inverted clock inputs for the flip flops, with the standard sizing mentioned below.
- For the inverter feeding the flop, and standard sizing for each inverter is taken to be (W/L)pFET/(W/L)nFET = (0.55u/0.15u)/(0.42u/0.15u)
- The transistors in NAND gate is sized following the sizing rules as (W/L)pFET/(W/L)nFET = (1.1u/0.3u)/(1.1u/3u)
- The inverter at the beginning of the inverter string leading to buffer string and hence clock output ports, has (W/L)pFET/(W/L)nFET = (1.26u/0.15u)/(0.42u/0.15u)
- The inverter following it has (W/L)pFET/(W/L)nFET = (0.42u/0.15u)/(0.42u/0.15u). And the same sequence is repeated again for each output path inverter string.
- The first buffer in the trio of buffers leading to output port has (W/L)pFET/(W/L)nFET = (0.55u/0.15u)/(0.42u/0.15u)
- Subsequent buffers are scaled by a multiple of 5 leading to (W/L)pFET/(W/L)nFET = (2.75u/0.75u)/(2.1u/0.75u) and (13.75u/3.75u)/(10.5u/3.75u) respectively.
- Total transistors used = 184 (92 each n and pFETS).
- The FET model chosen is 01v8 model from sky130 PDK.

## Schematic Diagram and Waveform

![image](https://user-images.githubusercontent.com/73732594/153049942-db725543-8855-40fc-b67f-46fca97959c1.png)


- Spice Netlist generated by eSim is modified for the above schematic using 130nm library for MOSFETs.

The final netlist is as follows. 

```
* Path for .cir file: C:\Users\DELL\Desktop\EL519\pre-layout\clockgen2.cir
* Path for sky130_fd_pr: C:\Users\DELL\Desktop\EL519\pre-layout\sky130_fd_pr

.param temp = 27
.param supl = 3.3V

.lib "sky130_fd_pr/Models/sky130.lib.spice" tt

Vdd vdd 0 {supl}

* Sheet Name: /
xM6  Net-_xM3-Pad1_ vclkb Net-_xM11-Pad2_ GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM5  Net-_xM3-Pad1_ vclk Net-_xM11-Pad2_ vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM8  Net-_xM11-Pad2_ vclk Net-_xM10-Pad1_ GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM7  Net-_xM11-Pad2_ vclkb Net-_xM10-Pad1_ vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM11  Net-_xM10-Pad2_ Net-_xM11-Pad2_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM12  Net-_xM10-Pad2_ Net-_xM11-Pad2_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42

* Input Clock circuit		
xM1  vclkb vclk vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM2  vclkb vclk GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42
		
xM9  Net-_xM10-Pad1_ Net-_xM10-Pad2_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM10  Net-_xM10-Pad1_ Net-_xM10-Pad2_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM14  Net-_xM10-Pad2_ vclk Net-_xM13-Pad3_ GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM13  Net-_xM10-Pad2_ vclkb Net-_xM13-Pad3_ vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM16  Net-_xM13-Pad3_ vclkb Net-_xM15-Pad3_ GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM15  Net-_xM13-Pad3_ vclk Net-_xM15-Pad3_ vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM19  Net-_xM17-Pad2_ Net-_xM13-Pad3_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM20  Net-_xM17-Pad2_ Net-_xM13-Pad3_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM17  Net-_xM15-Pad3_ Net-_xM17-Pad2_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM18  Net-_xM15-Pad3_ Net-_xM17-Pad2_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42

*Initial Inverter				
xM3  Net-_xM3-Pad1_ Net-_xM3-Pad2_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM4  Net-_xM3-Pad1_ Net-_xM3-Pad2_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42
		
xM22  Net-_xM17-Pad2_ vclkb Net-_xM21-Pad3_ GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM21  Net-_xM17-Pad2_ vclk Net-_xM21-Pad3_ vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM24  Net-_xM21-Pad3_ vclk Net-_xM23-Pad3_ GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM23  Net-_xM21-Pad3_ vclkb Net-_xM23-Pad3_ vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM27  Net-_xM25-Pad2_ Net-_xM21-Pad3_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM28  Net-_xM25-Pad2_ Net-_xM21-Pad3_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM25  Net-_xM23-Pad3_ Net-_xM25-Pad2_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM26  Net-_xM23-Pad3_ Net-_xM25-Pad2_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM30  Net-_xM25-Pad2_ vclk Net-_xM29-Pad3_ GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM29  Net-_xM25-Pad2_ vclkb Net-_xM29-Pad3_ vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM32  Net-_xM29-Pad3_ vclkb Net-_xM31-Pad3_ GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM31  Net-_xM29-Pad3_ vclk Net-_xM31-Pad3_ vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM35  Net-_xM3-Pad2_ Net-_xM29-Pad3_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM36  Net-_xM3-Pad2_ Net-_xM29-Pad3_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM33  Net-_xM31-Pad3_ Net-_xM3-Pad2_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM34  Net-_xM31-Pad3_ Net-_xM3-Pad2_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42

* NAND Gate		
xM37  Net-_xM37-Pad1_ Net-_xM3-Pad2_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.3 w=1.1		
xM38  Net-_xM37-Pad1_ Net-_xM17-Pad2_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.3 w=1.1		
xM39  Net-_xM37-Pad1_ Net-_xM3-Pad2_ GND GND sky130_fd_pr__nfet_01v8 l=0.3 w=1.1		
xM40  Net-_xM37-Pad1_ Net-_xM17-Pad2_ GND GND sky130_fd_pr__nfet_01v8 l=0.3 w=1.1		

xM42  Net-_xM37-Pad1_ vclkb Net-_xM41-Pad3_ GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM41  Net-_xM37-Pad1_ vclk Net-_xM41-Pad3_ vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM44  Net-_xM41-Pad3_ vclk Net-_xM43-Pad3_ GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM43  Net-_xM41-Pad3_ vclkb Net-_xM43-Pad3_ vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM47  Net-_xM45-Pad2_ Net-_xM41-Pad3_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM48  Net-_xM45-Pad2_ Net-_xM41-Pad3_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM45  Net-_xM43-Pad3_ Net-_xM45-Pad2_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM46  Net-_xM43-Pad3_ Net-_xM45-Pad2_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM50  Net-_xM45-Pad2_ vclk Net-_xM49-Pad3_ GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM49  Net-_xM45-Pad2_ vclkb Net-_xM49-Pad3_ vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM52  Net-_xM49-Pad3_ vclkb Net-_xM51-Pad3_ GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM51  Net-_xM49-Pad3_ vclk Net-_xM51-Pad3_ vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM55  Net-_xM53-Pad2_ Net-_xM49-Pad3_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM56  Net-_xM53-Pad2_ Net-_xM49-Pad3_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM53  Net-_xM51-Pad3_ Net-_xM53-Pad2_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM54  Net-_xM51-Pad3_ Net-_xM53-Pad2_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42

* Inverter string		
xM57  Net-_xM57-Pad1_ Net-_xM53-Pad2_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=1.26		
xM58  Net-_xM57-Pad1_ Net-_xM53-Pad2_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM59  Net-_xM59-Pad1_ Net-_xM57-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.42		
xM60  Net-_xM59-Pad1_ Net-_xM57-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM61  Net-_xM61-Pad1_ Net-_xM59-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=1.26		
xM62  Net-_xM61-Pad1_ Net-_xM59-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM63  Net-_xM63-Pad1_ Net-_xM61-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.42		
xM64  Net-_xM63-Pad1_ Net-_xM61-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42

* Buffer string		
xM65  Net-_xM65-Pad1_ Net-_xM63-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM66  Net-_xM65-Pad1_ Net-_xM63-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM67  Net-_xM67-Pad1_ Net-_xM65-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM68  Net-_xM67-Pad1_ Net-_xM65-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM69  Net-_xM69-Pad1_ Net-_xM67-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.75 w=2.75		
xM70  Net-_xM69-Pad1_ Net-_xM67-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=0.75 w=2.10		
xM71  Net-_xM71-Pad1_ Net-_xM69-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.75 w=2.75		
xM72  Net-_xM71-Pad1_ Net-_xM69-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=0.75 w=2.10		
xM73  Net-_xM73-Pad1_ Net-_xM71-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=3.75 w=13.75		
xM74  Net-_xM73-Pad1_ Net-_xM71-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=3.75 w=10.5		
xM75  vclkout1 Net-_xM73-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=3.75 w=13.75		
xM76  vclkout1 Net-_xM73-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=3.75 w=10.5	

* D-FF	
xM78  Net-_xM53-Pad2_ vclkb Net-_xM77-Pad3_ GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM77  Net-_xM53-Pad2_ vclk Net-_xM77-Pad3_ vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM80  Net-_xM77-Pad3_ vclk Net-_xM79-Pad3_ GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM79  Net-_xM77-Pad3_ vclkb Net-_xM79-Pad3_ vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM83  Net-_xM81-Pad2_ Net-_xM77-Pad3_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM84  Net-_xM81-Pad2_ Net-_xM77-Pad3_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM81  Net-_xM79-Pad3_ Net-_xM81-Pad2_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM82  Net-_xM79-Pad3_ Net-_xM81-Pad2_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM86  Net-_xM81-Pad2_ vclk Net-_xM85-Pad3_ GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM85  Net-_xM81-Pad2_ vclkb Net-_xM85-Pad3_ vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM88  Net-_xM85-Pad3_ vclkb Net-_xM87-Pad3_ GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM87  Net-_xM85-Pad3_ vclk Net-_xM87-Pad3_ vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM91  Net-_xM113-Pad1_ Net-_xM85-Pad3_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM92  Net-_xM113-Pad1_ Net-_xM85-Pad3_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM89  Net-_xM87-Pad3_ Net-_xM113-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM90  Net-_xM87-Pad3_ Net-_xM113-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42

* Inverter string		
xM93  Net-_xM93-Pad1_ Net-_xM113-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=1.26		
xM94  Net-_xM93-Pad1_ Net-_xM113-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM95  Net-_xM95-Pad1_ Net-_xM93-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.42		
xM96  Net-_xM95-Pad1_ Net-_xM93-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM97  Net-_xM100-Pad2_ Net-_xM95-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=1.26		
xM98  Net-_xM100-Pad2_ Net-_xM95-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM99  Net-_xM100-Pad1_ Net-_xM100-Pad2_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.42		
xM100  Net-_xM100-Pad1_ Net-_xM100-Pad2_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42

*Buffer string		
xM101  Net-_xM101-Pad1_ Net-_xM100-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM102  Net-_xM101-Pad1_ Net-_xM100-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM103  Net-_xM103-Pad1_ Net-_xM101-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM104  Net-_xM103-Pad1_ Net-_xM101-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM105  Net-_xM105-Pad1_ Net-_xM103-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.75 w=2.75		
xM106  Net-_xM105-Pad1_ Net-_xM103-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=0.75 w=2.10		
xM107  Net-_xM107-Pad1_ Net-_xM105-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.75 w=2.75		
xM108  Net-_xM107-Pad1_ Net-_xM105-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=0.75 w=2.10		
xM109  Net-_xM109-Pad1_ Net-_xM107-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=3.75 w=13.75		
xM110  Net-_xM109-Pad1_ Net-_xM107-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=3.75 w=10.5		
xM111  vclkout2 Net-_xM109-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=3.75 w=13.75		
xM112  vclkout2 Net-_xM109-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=3.75 w=10.5

* D-FF		
xM114  Net-_xM113-Pad1_ vclkb Net-_xM113-Pad3_ GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM113  Net-_xM113-Pad1_ vclk Net-_xM113-Pad3_ vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM116  Net-_xM113-Pad3_ vclk Net-_xM115-Pad3_ GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM115  Net-_xM113-Pad3_ vclkb Net-_xM115-Pad3_ vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM119  Net-_xM117-Pad2_ Net-_xM113-Pad3_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM120  Net-_xM117-Pad2_ Net-_xM113-Pad3_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM117  Net-_xM115-Pad3_ Net-_xM117-Pad2_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM118  Net-_xM115-Pad3_ Net-_xM117-Pad2_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM122  Net-_xM117-Pad2_ vclk Net-_xM121-Pad3_ GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM121  Net-_xM117-Pad2_ vclkb Net-_xM121-Pad3_ vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM124  Net-_xM121-Pad3_ vclkb Net-_xM123-Pad3_ GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM123  Net-_xM121-Pad3_ vclk Net-_xM123-Pad3_ vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM127  Net-_xM125-Pad2_ Net-_xM121-Pad3_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM128  Net-_xM125-Pad2_ Net-_xM121-Pad3_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM125  Net-_xM123-Pad3_ Net-_xM125-Pad2_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM126  Net-_xM123-Pad3_ Net-_xM125-Pad2_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42

* Inverter string		
xM129  Net-_xM129-Pad1_ Net-_xM125-Pad2_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=1.26		
xM130  Net-_xM129-Pad1_ Net-_xM125-Pad2_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM131  Net-_xM131-Pad1_ Net-_xM129-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.42		
xM132  Net-_xM131-Pad1_ Net-_xM129-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM133  Net-_xM133-Pad1_ Net-_xM131-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=1.26		
xM134  Net-_xM133-Pad1_ Net-_xM131-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM135  Net-_xM135-Pad1_ Net-_xM133-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.42		
xM136  Net-_xM135-Pad1_ Net-_xM133-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42

* Buffer string		
xM137  Net-_xM137-Pad1_ Net-_xM135-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM138  Net-_xM137-Pad1_ Net-_xM135-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM139  Net-_xM139-Pad1_ Net-_xM137-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM140  Net-_xM139-Pad1_ Net-_xM137-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM141  Net-_xM141-Pad1_ Net-_xM139-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.75 w=2.75		
xM142  Net-_xM141-Pad1_ Net-_xM139-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=0.75 w=2.10		
xM143  Net-_xM143-Pad1_ Net-_xM141-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.75 w=2.75		
xM144  Net-_xM143-Pad1_ Net-_xM141-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=0.75 w=2.10		
xM145  Net-_xM145-Pad1_ Net-_xM143-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=3.75 w=13.75		
xM146  Net-_xM145-Pad1_ Net-_xM143-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=3.75 w=10.5		
xM147  vclkout3 Net-_xM145-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=3.75 w=13.75		
xM148  vclkout3 Net-_xM145-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=3.75 w=10.5	

* D-FF	
xM150  Net-_xM125-Pad2_ vclkb Net-_xM149-Pad3_ GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM149  Net-_xM125-Pad2_ vclk Net-_xM149-Pad3_ vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM152  Net-_xM149-Pad3_ vclk Net-_xM151-Pad3_ GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM151  Net-_xM149-Pad3_ vclkb Net-_xM151-Pad3_ vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM155  Net-_xM153-Pad2_ Net-_xM149-Pad3_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM156  Net-_xM153-Pad2_ Net-_xM149-Pad3_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM153  Net-_xM151-Pad3_ Net-_xM153-Pad2_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM154  Net-_xM151-Pad3_ Net-_xM153-Pad2_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM158  Net-_xM153-Pad2_ vclk Net-_xM157-Pad3_ GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM157  Net-_xM153-Pad2_ vclkb Net-_xM157-Pad3_ vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM160  Net-_xM157-Pad3_ vclkb Net-_xM159-Pad3_ GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM159  Net-_xM157-Pad3_ vclk Net-_xM159-Pad3_ vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM163  Net-_xM161-Pad2_ Net-_xM157-Pad3_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM164  Net-_xM161-Pad2_ Net-_xM157-Pad3_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM161  Net-_xM159-Pad3_ Net-_xM161-Pad2_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM162  Net-_xM159-Pad3_ Net-_xM161-Pad2_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42

* Inverter string		
xM165  Net-_xM165-Pad1_ Net-_xM161-Pad2_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=1.26		
xM166  Net-_xM165-Pad1_ Net-_xM161-Pad2_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM167  Net-_xM167-Pad1_ Net-_xM165-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.42		
xM168  Net-_xM167-Pad1_ Net-_xM165-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM169  Net-_xM169-Pad1_ Net-_xM167-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=1.26		
xM170  Net-_xM169-Pad1_ Net-_xM167-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM171  Net-_xM171-Pad1_ Net-_xM169-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.42		
xM172  Net-_xM171-Pad1_ Net-_xM169-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42

* Buffer string		
xM173  Net-_xM173-Pad1_ Net-_xM171-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM174  Net-_xM173-Pad1_ Net-_xM171-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM175  Net-_xM175-Pad1_ Net-_xM173-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.15 w=0.55		
xM176  Net-_xM175-Pad1_ Net-_xM173-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=0.15 w=0.42		
xM177  Net-_xM177-Pad1_ Net-_xM175-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.75 w=2.75		
xM178  Net-_xM177-Pad1_ Net-_xM175-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=0.75 w=2.1		
xM179  Net-_xM179-Pad1_ Net-_xM177-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=0.75 w=2.75		
xM180  Net-_xM179-Pad1_ Net-_xM177-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=0.75 w=2.1		
xM181  Net-_xM181-Pad1_ Net-_xM179-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=3.75 w=13.75		
xM182  Net-_xM181-Pad1_ Net-_xM179-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=3.75 w=10.5		
xM183  vclkout4 Net-_xM181-Pad1_ vdd vdd sky130_fd_pr__pfet_01v8 l=3.75 w=13.75		
xM184  vclkout4 Net-_xM181-Pad1_ GND GND sky130_fd_pr__nfet_01v8 l=3.75 w=10.5	
	
Vclk vclk 0 pulse(0 1.8 0 0.4ns 0.4ns 5us 10us)
.tran 0.1us 300us

.control
run 
plot vclkout1 vclkout2+4 vclkout3+8 vclkout4+12 vclk+16
.endc
.end

```

### Spice Simulation result

- NGSPICE Waveform

![waveform (1)](https://user-images.githubusercontent.com/73732594/153057375-934b0c11-97be-4ece-9657-3a8a871930fd.png)


## Methodology

- Each of the components: the inverter, buffer, D-FF and NAND gate were tested individually from spice netlists for each, for their functionality. The reference sizing was based on the D-FF transistor sizes. 
- A choice between creating subcircuits of each or using them as is on schematic was there. The latter was chosen to have the transistors more accessible in the netlist file and then sized according to the requirements, for more flexibility. Since the design was uniform, the netlist is easy to manually change the values for each transistors.

## Challenge faced

- Initially, the waveform acquired was having overlaps.

![image](https://user-images.githubusercontent.com/73732594/153054393-5d957cbc-d8e8-4ad8-8b0c-54af7cceabbc.png)

### Troubleshooting 

- Firstly, sizing was thought to be culprit, especially for the NAND gate and the inverter strings. 
- After a few iterations, the results remained unchanged. Went a level lower, to look at the schematic, the culprit was found to be the input of NAND, being provided to the flop ahead instead of the output of the NAND gate. Rectifying that helped.

## Steps to Reproduce waveforms

Here are the basic steps to re-do the simulations and see the waveforms

```
git clone https://github.com/Anmol-wq/Clock_Generator/
cd Clock_Generator

ngspice clockgen2.cir
```
## Limitations

- The clock generator initially takes 4 input clock cycles to settle and yield the desired output waveforms.
- The cycle dependency is on frequency and moving from 100 kHz to 100 MHz increased the input clock cycles from 4-5 before it operates as required.
- The waveform appearance is not ideal at 100 MHz but it is functionally correct.

![Screenshot (404)](https://user-images.githubusercontent.com/73732594/153056960-dc85c0a0-00e9-4264-9374-9e1c455f08c5.png)

The final limitation, thus, leads to scope of improvement in design, to achieve better overall performance, and quality of results.

## References

- [S. Z. Adibifard, "Wide-band Tunable Transmission-Line N-Path Filter on CMOS 130 nm”, Accepted M.A.Sc. Thesis, Department of Electronics, Carleton Universty. August, 2021](https://curve.carleton.ca/5ff0a03b-3cf7-42f8-ab3e-cb9a1379b43a)

- [Deepak Verma, "vsdsram_sky130”](https://github.com/Deepak42074/vsdsram_sky130)

- [Sameer Durgoji, Kunal Ghosh, "VSD Intern - 10-bit DAC design using eSim and Sky130"](https://www.udemy.com/course/vsd-intern-10-bit-dac-design-using-esim-and-sky130/)

- [Weste, Neil, Harris, David. "CMOS VLSI Design: A Circuits and Systems Perspective, 4th Edition"](https://www.amazon.in/CMOS-VLSI-Design-Circuits-Perspective/dp/0321547748)

## Acknowledgements

- [Kunal Ghosh](https://github.com/kunalg123), Co-founder of VLSI System Design (VSD) Corp. Pvt. Ltd.
- [SFAL COE, Karnataka](https://www.sfalcoe.com/)
- Sumanto Kar
- Mohammad Khalique Khan

## Author

[Anmol Saxena](https://github.com/Anmol-wq/), M.Tech ICT (2019-21), DA-IICT, Gandhinagar, Gujarat, India
- Contact: anmol.saxena2016@outlook.com, 201911053@daiict.ac.in  <br>
- System configuration during the undertaking:  <br>\
-- Processor: Intel(R) Core(TM) i5-5300U CPU @ 2.30GHz   <br>\
-- Installed RAM: 8.00 GB (7.88 GB usable)  <br>\
-- System type:	Windows 10 Pro, 64-bit operating system, x64-based processor  <br>

