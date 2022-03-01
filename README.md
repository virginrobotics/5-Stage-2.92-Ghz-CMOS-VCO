# 5 stage 2.92 GHz CMOS Voltage Controlled Oscillator
Documenting the design process of a VCO on the SYNOPSYS Custom Compiler - 28nm PDK.

### About
Following the [4:16 bottom up decoder](https://github.com/virginrobotics/bottomupdecoder_esim_ngspice), I got the oppurtunity compete in another hackathon - the Analog IC Design Hackathon SYNOPSYS IITH (VSD organized) , and the logical step was to design an analog circuit , hence the VCO. 
Voltage Controlled Oscillators , as the name suggests allow you to linearly tune the output frequency of an oscillator through a control voltage over a frequency range. VCOs are an integral component of PLL(Phase Locked Loop) systems. Most design challanges come in the form of choosing right w/l ratios, biasing, lowering Vdd/Vctrl and power. The VCO designed here was referenced from this [paper](https://www.researchgate.net/publication/333347988_Design_and_Analysis_of_Current_Starved_VCO_Targeting_SCL_180_nm_CMOS_Process), a few tweaks allowed a max output of 2.92 GHz.    

![](images/vcolinear.png)

<h2> Navigation: </h2>

1. [Introduction](#Introduction)
2. [Initial design](#Initial-Design)
3. [Tools ](#Tools)
4. [Fingering](#Fingering)
5. [Reference-VCO-Perfomance](#Reference VCO perfomance)
6. [Parametric Sweep](#Parametric-sweep)
7. [Final Conclusion](#Final-Conclusion)
8. [Thanks to](#Thanks-to)
9. [References](#References)


<hr></hr>

# Introduction

The VCO is made up of a 5 stage ring oscillator where the output of the final inverter stage is fed back to the input of the first inverter stage where the frequency of oscillation is given by the equation. 

![](https://latex.codecogs.com/png.image?\dpi{110}%20f_{osc}%20=%20\frac{I_{D}}{N.C_{tot}.V_{DD}})

where, fosc os frequency of oscillation
Id is bias current 
N is the number of inverter stages
and Ctot is the total input/output capacitance of the inverter stage


# Initial Design

The reference VCO uses a current starving technique to allow use a seperate control voltage instead of just the vdd, acting as the control and source. This lets the output waveform maintain amplitude at or near vdd across the tunable range.

![](images/vcodesign.png)

PMOS M3-M7 and NMOS M20-M24 are the current sources that limit the current to inverter stages - controlling delay. The W and L values were taken straight from the referenced paper , as shown below. 

![](images/wlratio.png)

The W/L ratios are governed by the following equation which relates to the total capacitance in equation 1.

![](https://latex.codecogs.com/png.image?\dpi{110}%20C_{tot}=\frac{5}{2}%20C%27{ox}(W{p}L_{p}+W_{n}L_{n}))

The schematic was converted to a symbol in the Custom Compiler for use in a test bench where a DC source was connected to Vdd and an exponential source to Vctrl. 

Why an exponential source and not a DC source? I had the same question , answered by a fellow contestant in the hackathon forum 

>Schematic doesn't have noise of any kind to begin with , but when we do theoretical calculations for an oscillator we assume that noise/any small perturbation is amplified through +ve feedback so much that sustainable oscillations are met at output. So when you give a ramp/exponential supply you are providing that noise/disturbance deliberately

Hence , a rise time of 5 or 10 nanoseconds was enough to kick the VCO into sustained oscillations. 

Here's the output for control voltage @ 1.8V and Vdd @ 1.8V. Osciallations ~ 140MHz.

![](images/freqamplitudevco.png)

A Vin vs Freq curve for a sweep from 0.4V to 1.8V. The measured VCO gain is ~ 125.27Mhz/V

![](images/vcolinear.png)

#Tools

The hackathon was unique as it had the participants use Synopsys's Custom Compiler Tool through a remote connection to it's servers in Singapore. The Custom Compiler itself was vast in it's capabilities especially in the testbench environment through options for Monte Carlo analysis ,Corner analysis , vide range of waveform measurements - frequency , mean , peak to peak amplitude measurements etc. 

Was the first experience with a commerical tool and thoroughly enjoyed the process and the documentation resources were excellent. 

![image](https://user-images.githubusercontent.com/58078131/156224671-3f01bdd0-c77a-44a6-b981-3b71935c1e69.png)

![image](https://user-images.githubusercontent.com/58078131/156225044-91a741ed-c35d-4ec1-883d-b84ebf1626e2.png)

# Fingering

The Synopsys Custom Compiler didn't allow mosfet node lengths to exceed 3.5um but the design required lengths of 4.8um , 96um etc.

![image](https://user-images.githubusercontent.com/58078131/156202615-750fa222-bd16-4c8c-a23f-765e92403bb6.png)

The solution is to realize the required lengths through multiple fingers , example - to realize a 96um pmos, you create 40 fingers with 2.4um per finger to achieve desired size. 
However, there are penalties in the form of gate resistance , which can be reduced by employing parallel fingers but perimeter source/drain capacitance increases with more fingers. A common convention is to choose a finger width whose resistance is less than the inverse transconductance (gm) associated with the finger. 

![image](https://user-images.githubusercontent.com/58078131/156204059-c37d1023-fda7-49b0-9f44-494a2a7d6a55.png)

Page - 737, Design of Analog CMOS Integrated Circuits - Behzad Razavi

# Reference VCO perfomance

The paper - Design and Analysis of Current Starved VCO Targeting SCL 180 nm CMOS Process by Chandra Shekhar and S.Qureshi was the basis to learn the A B Cs of Analog Design, however the Custom Compiler with the 28nm PDK can go a lot lower than 180nm , so a few tweaks of the W/L ratios were attempted to increase the output frequency, but first the perfomance of the reference design as taken from the paper.

![image](https://user-images.githubusercontent.com/58078131/156208664-40c27a8c-4bbd-4b88-9f2f-999b2b312da8.png)

![image](https://user-images.githubusercontent.com/58078131/156208730-3a92f6db-46ec-42f8-9c47-cee4625ba88a.png)

# Parametric Sweep
The NMOS of the inverter stages were set at their lowest widths and lengths and the PMOS at double the width and lengths of the NMOS mosfets. The current sources were also set at the minimum node lengths and only a few tweaks at the bias stage to get some positive amplitude at the output. 

The Resistor R1 , Vin and Vdd were all set as design variables and a parametric sweep was run to determine the perfomance. Here are the results

![image](https://user-images.githubusercontent.com/58078131/156212093-d87d76b6-7367-4826-a659-1aa4811c97dd.png)

Sweep

Tabular Data

![image](https://user-images.githubusercontent.com/58078131/156212247-0a18408a-e39d-4c44-b21e-1a962926fc25.png)

![image](https://user-images.githubusercontent.com/58078131/156211432-a1c447bc-25f9-44dd-96d2-1641f5d85e77.png)

Output @ R1 = 11.1k Ohms , Vdd @ 1.8V and Vctrl @ 1.6V

# Final Conclusion

The parametric sweep was not done on the node sizes, hence is pretty unrepresentative of the possibilities that the 28nm PDK can achieve but provided a base for learning the many features available in the Synopsys Custom Compiler tool. A proper consideration of the Biasing stage through MOSFET physics are the goals for this project to further improve perfomance. 

A heuristic approach led to a max output frequency of 2.92 Ghz vs the ~143Mhz of the reference design but the output amplitude is low as the biasing stage is not set with proper W and L value, those are the areas of improvement

# Thanks to

#### [VSD](https://www.vlsisystemdesign.com/basic_courses/), [IIT-H](https://www.iith.ac.in/events/2022/02/15/Cloud-Based-Analog-IC-Design-Hackathon/) and [SYNOPSYS](https://www.synopsys.com/) for organizing the hackathon
#### [Kunal (VSD Co-Founder)](https://github.com/kunalg123) , [Chinmaya Panda](chinmaya.panda@ee.iith.ac.in) and everybody in the hackathon forum for the help. Had a great time.
#### [Sameer Durgoji](https://github.com/SameerSDurgoji) for the Custom Compiler walktrough.
#### [Dr. Sudhi Sudharman](https://www.linkedin.com/in/dr-sudhi-sudharman-50507b66/), taught me VLSI Design at CHRIST(Deemed To Be University)

# References 

- Chandra Shekhar & S.Qureshi (2018). Design and Analysis of Current Starved VCO Targeting SCL 180 nm CMOS Process. 86-89. 10.1109/ISES.2018.00027. 
- Suman Shruti & Sharma K & Ghosh Pradip. (2016). Performance Analysis of Voltage Controlled Ring Oscillators. 10.1007/978-981-10-0755-2_4. 
- Razavi, Behzad. 2001. Design of analog CMOS integrated circuits.Boston, MA: McGraw-Hill.






