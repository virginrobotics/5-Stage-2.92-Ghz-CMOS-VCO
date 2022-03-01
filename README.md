# 5 stage 2.92 GHz CMOS Voltage Controlled Oscillator
Documenting the design process of a VCO on the SYNOPSYS Custom Compiler - 28nm PDK.

### About
Following the [4:16 bottom up decoder](https://github.com/virginrobotics/bottomupdecoder_esim_ngspice), I got the oppurtunity compete in another hackathon - the Analog IC Design Hackathon SYNOPSYS IITH (VSD organized) , and the logical step was to design an analog circuit , hence the VCO. 
Voltage Controlled Oscillators , as the name suggests allow you to linearly tune the output frequency of an oscillator through a control voltage over a frequency range. VCOs are an integral component of PLL(Phase Locked Loop) systems. Most design challanges come in the form of choosing right w/l ratios, biasing, lowering Vdd/Vctrl and power. The VCO designed here was referenced from this [paper](https://www.researchgate.net/publication/333347988_Design_and_Analysis_of_Current_Starved_VCO_Targeting_SCL_180_nm_CMOS_Process), a few tweaks allowed a max output of 2.92 GHz.    

![](images/vcolinear.png)

<h2> Navigation: </h2>

1. [How does a VCO work ?](https://github.com/virginrobotics/bottomupdecoder_esim_ngspice#-a-416-decoder-)
2. [Initial design](https://github.com/virginrobotics/bottomupdecoder_esim_ngspice#not-gate-submodule)
3. [Tools ](https://github.com/virginrobotics/bottomupdecoder_esim_ngspice#and-gate-submodule)
4. [Fingering](https://github.com/virginrobotics/bottomupdecoder_esim_ngspice#24-decoder-submodule)
5. [Reference VCO Perfomance](https://github.com/virginrobotics/bottomupdecoder_esim_ngspice#realizing-the-416-decoder-using-24-submodules)
6. [Tweaks](https://github.com/virginrobotics/bottomupdecoder_esim_ngspice#important-mistakes)
7. [Parametric Sweep](https://github.com/virginrobotics/bottomupdecoder_esim_ngspice#recreate-simulation-output)
8. [Final Results](https://github.com/virginrobotics/bottomupdecoder_esim_ngspice#eda-tools-used)
9. [Improvements](https://github.com/virginrobotics/bottomupdecoder_esim_ngspice#things-to-improve)
10. [Thanks to](https://github.com/virginrobotics/bottomupdecoder_esim_ngspice#thanks-to)
11. [References](https://github.com/virginrobotics/bottomupdecoder_esim_ngspice/blob/main/README.md#references)

<hr></hr>

<h3> How does a VCO work ? </h3>

