# Practical 
<details>
 <summary>Project Structure</summary>
 
- src/include/ - Contains header files (*.vh) with necessary macros or parameter definitions.
- src/module/ - Contains Verilog files for each module in the SoC design.
- output/ - Directory where compiled outputs and simulation files will be generated.

## Setup and Prepare Project Directory
Clone or set up the directory structure as follow:
```txt
VSDBabySoC/
├── src/
│   ├── include/
│   │   ├── sandpiper.vh
│   │   └── other header files...
│   ├── module/
│   │   ├── vsdbabysoc.v      # Top-level module integrating all components
│   │   ├── rvmyth.v          # RISC-V core module
│   │   ├── avsdpll.v         # PLL module
│   │   ├── avsddac.v         # DAC module
│   │   └── testbench.v       # Testbench for simulation
└── output/
└── compiled_tlv/         # Holds compiled intermediate files if needed
```
To clone the directory follow the commands
```
git clone https://github.com/manili/VSDBabySoC.git
```
By cloning the [Git](https://github.com/manili/VSDBabySoC.git) we get the structure as mentioned above.


## TLV to Verilog Conversion for RVMYTH
Initially we see that there is only rvmyth.tlv file inside src/module. To check that follow the commands below.
```
cd VSDBabySoC/src/module/
ls
```
We see only .tlv file which means the RVMYTH core is written in TL-Verilog.

To convert it into a .v file for simulation, follow the steps below:
```
# Step 1: Install python3-venv
sudo apt update
sudo apt install python3-venv python3-pip

# Step 2: Create and activate a virtual environment
cd /sky2syn_week_2/VSDBabySoC/
python3 -m venv sp_env
source sp_env/bin/activate

# Step 3: Install SandPiper-SaaS inside the virtual environment
pip install pyyaml click sandpiper-saas

# Step 4: Convert rvmyth.tlv to Verilog
sandpiper-saas -i ./src/module/*.tlv -o rvmyth.v --bestsv --noline -p verilog --outdir ./src/module/

```
 ![Conversion](Images/conver_to_verilog.png)
  
The rvmyth.v file has been generagted to check them follow the commands:
```
cd VSDBabySoC/src/module/
ls
```
Now we can see .v file

### Note

To use this environment in future sessions, always activate it first:
```
source sp_env/bin/activate
```
To diactivate:
```
diactivate
```
</details>
<details>
 <summary>Simulation</summary>
<details>
 <summary>Pre-Synthesis Simulation</summary>
Run the following command to perform a pre-synthesis simulation:
```
cd VSDBabySoC/
mkdir -p output/pre_synth_sim
cd
iverilog -o /home/bhuvan/Bhuvan/sky2syn_week_2/Practicals/VSDBabySoC/output/pre_synth_sim/pre_synth_sim.out -DPRE_SYNTH_SIM -I /home/bhuvan/Bhuvan/sky2syn_week_2/Practicals/VSDBabySoC/src/include -I /home/bhuvan/Bhuvan/sky2syn_week_2/Practicals/VSDBabySoC/src/module /home/bhuvan/Bhuvan/sky2syn_week_2/Practicals/VSDBabySoC/src/module/testbench.v
```
Then run the below commands for generating .vdc to check the gtkwave and observe the waveform:
```
cd VSDBabySoC/output/pre_synth_sim
./pre_synth_sim.out
gtkwave pre_synth_sim.vcd
```
Drag and drop the CLK, reset, OUT (DAC), and RV TO DAC [9:0] signals to their respective locations in the simulation tool
 ![Pre_Simulation](images/gtkwave_pre.png)
 In this picture we can see the following signals:

CLK: This is the input CLK signal of the RVMYTH core. This signal comes from the PLL, originally.

reset: This is the input reset signal of the RVMYTH core. This signal comes from an external source, originally.


RV_TO_DAC[9:0]: This is the 10-bit output [9:0] OUT port of the RVMYTH core. This port comes from the RVMYTH register #17, originally.

OUT: This is a real datatype wire which can simulate analog values. It is the output wire real OUT signal of the DAC module. This signal comes from the DAC, originally.

This can be viewed by changing the Data Format of the signal to Analog --> Step by right clicking on OUT and then Analog --> Step

 ![Pre_Simulationstep](images/gtkwave_step.png)
 ![Pre_Simulation_step](images/step.png)
 </details>
<details>
 <summary>Post-Synthesis Simulation</summary>

