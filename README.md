# PLL_Design_Using_Google_SkyWater_130nm_PDK
PLL Design using open source ngspice and magic tools with 130nm open source PDK 
# Introduction to PLL (Phase Locked Loop)
PLL is widely used to recover the clock signal from a receiver signal in some of the wireless applications. It can also be used to generate a signal with a frequency that is multiplied by a fixed factor compared to the frequency of a reference signal.
# Uses of PLL
A clock signal can be generated with the help of a Quartz crystal or using a VCO(Voltage Controlled Oscillator). But they do have their own limitations. Quartz Crystal can give a signal with pure spectrum( With single frequency) but we do not have the flexibility of changing the frequency of the signal generated. On the other hand, VCO can generate signals with flexible frequencies but there is phase noise. Therefore, the purpose of the PLL is to leverage both the features of VCO and Quartz crystal and get a flexible signals with less noise.
# Modules/Blocks inside the PLL
![PLL_generic_inline_optional_N svg](https://user-images.githubusercontent.com/18748519/133935585-1615114a-3647-458b-b194-b158aa9a513f.png)
(Source: By Krishnavedala - Own work, CC0, https://commons.wikimedia.org/w/index.php?curid=64092961)

Below is the description of each circuit present inside the PLL
# Phase Frequency Detector (PFD)
The basic functionality of this circuit is to compare the input reference and feedback signals and generate Up and Down signals depending on whether feedback signal leads or lags compared to reference signal.

Below is the circuit of PFD 
#############################

Depending on the Up and Down values, we have the following state diagram
#############################

But there is an issue in the above circuit. When the phase difference between reference signal and feedback signal is very small, the PFD circuit does not detect as Up/Down signals are not given enough time to toggle completely. This condition is called Dead Zone and in order to avoid this, a more precise and sensitive PFD circuit should be designed.

# Charge pump (CP)
The output of the PFD is fed to Charge Pump. This coverts the digital input to a analog signal which is used by Voltage Controlled Oscillator(VCO) circuit. Charge Pump can ge designed using current steering circuits.

Below is the circuit diagram of a Charge Pump

################################

If the Up signal is 1, the current flows from Vdd to output capacitor and charges it.

If Down signal is 1, the current flows from output capacitor to Ground and discharges the Capacitor.

The output of the charge pump depends on the average time of Up being 1 and Down being 1. If the average time with Up being 1 is more, output voltage increases and the voltage across capacitor decreases in other case. 

But there can be leakage current even if both Up and Down are 0.This leakage current can impact the output signal and inturn the performance of VCO. Moreover, there can be frequency fluctuations at the output of the Charge Pump and this can be taken care with the help of a Low Pass Filter (LPF) at the output. 

########################

Charge Pump Circuit with LPF at the output

#########################

LPF not only helps in limiting high frequency fluctuations but also maintains the stability of the entire PLL control system as we are adding a Pole to the system.
 
Thumb rules that are to be followed while selecting the component values
1) Output Capacitance = Capacitance of LPF/10
2) Loop Filter Bandwidth ~= (Highest output Frequency)/10 
Loop filter Bandwidth is 1/(1+RC1) where C1 = (C*Cx)/C+Cx

# Voltage Controlled Oscillator (VCO) 
The functionality of this circuit is to generate a signal with a frequency that varies depending on the voltage of the input signal. It can be implemented using a Ring Oscillator which is a circuit with odd number of inverters with a specific delay connected in a series fashion. This makes the output signal to toggle after a certain amount of delay.

###########################################

Example Circuit of a VCO

  Time Period of the signal generated = 2*(Delay of each inverter)*(number of inverters)
  
  Frequency depends on the time period which inturn depends on the delay and which inturn depends on the current supplied. If a large current is supplied at the output, the output gets charged faster. Current starving mechanism can be used to obtain variable frequencies. The important design criteria that should be noted while designing this circuit is that the frequencies that VCO generates must be in the same range of frequencies that the PLL supports.
  
  # Frequency Divider Circuit (FD):
  A frequency divider circuit generates an output signal with a frequency reduced by a certain factor compared to the input signal.
  
  For example, if a signal with frequency F is given to a frequency divided by 2 circuit, it generates an output signal with frequency F/2.
  
  ##################################
  Frequency divider by 2 circuit
  
  The cascaded Frequency divider by 2 circuit can be used to get frequency divided by a certain factor (multiple of 2) circuit. This circuit is optional in case of PLL.
  # PLL Jargon:
  1) Lock range: It is the range of frequencies for which PLL will maintain its locked state given that it is already in locked state.
  
  2) Capture Range: It is the range of frequencies for which PLL comes to locked state from unlocked state. Generally this rage is less than Lock range.
  
  3) Settling Time: It is the time taken by the PLL to attain a lock from the initial unlocked state.
# PLL Circuit Design:
8x multiplier PLL specifications used for this workshop
1) TT - Corner (Typical Typical)
2) VDD (Digital Supply) - 1.8V
3) Temperature - Room Temeperature (27C)
4) Both VCO and PLL Modes
5) Reference Clock Frequency - 5MHz to 12.5MHz
6) Output Clock Frequency    - 40MHz to 100MHz
7) Jitter (RMS) <~20ns
8) Duty Cycle -50%

Tools Used: ngspice and magic

#######################################
Individual Circuits used for this PLL

Design Flow Followed for this project

![Design Flow](https://user-images.githubusercontent.com/18748519/133962158-9ecdf238-7bb6-49da-8f91-5da65ac9ac30.jpg)
# Pre-Layout Simulations:

1) Phase Frequency Detector(PFD): 
  
  Below are the simulation results for PFD circuit
  
  ![Screenshot (15)](https://user-images.githubusercontent.com/18748519/133963093-3d0146a7-6a8e-4180-8ffd-d09d3fb94286.png)
  
  
  
  When we zoom in waveform, it looks as below
  
  ![Screenshot (43)](https://user-images.githubusercontent.com/18748519/133963391-3f4faded-8f07-444a-b7fc-cd8e9979be58.png)
  
  ![Screenshot (44)](https://user-images.githubusercontent.com/18748519/133963578-7abea961-21d9-4547-95ee-e25ec7c4fc74.png)
  
  We can observe that Down signal toggled detecting the phase difference between input reference signal and feedback signal.
  
2) Charge Pump (CP): 

   Below are the simulation results for CP circuit
   
   ![Screenshot (46)](https://user-images.githubusercontent.com/18748519/133963979-cb444841-acab-4200-b974-eda404737de5.png)
   
   
   When we zoom in waveform, it looks as below
   
   ![Screenshot (13)](https://user-images.githubusercontent.com/18748519/133964085-b20c8819-a454-4e0a-9cf8-e2ba83c89795.png)
   
   We can observe that output voltage across the capacitor increased exponentially with small ups and downs which is because of Up and Down signals. As Average time of Up being 1 is more than Down being 1, voltage increased.
   
3) Voltage Controlled Oscillator (VCO) :

    Below are the simulation results for VCO circuit
    
    ![Screenshot (47)](https://user-images.githubusercontent.com/18748519/133964532-e1cc89b5-993b-4fc6-8913-c92f9c4eb19c.png)
    
    When we zoom in waveform, it looks as below
    
    ![Screenshot (21)](https://user-images.githubusercontent.com/18748519/133964590-72957431-c054-46f7-9e6c-45e8b903334d.png)
    
    We can observe that oscillations are with full swing and this is because of an extra inverter that is added at the output.
    
 
4) Frequency Divider Circuit (FD) :

   Below are the simulation results for FD circuit
    
   ![Screenshot (18)](https://user-images.githubusercontent.com/18748519/133964873-36a0ecf1-0bdf-402a-848c-dc8e1c33e0a2.png)
   
   When we zoom in waveform, it looks as below
   
   ![Screenshot (48)](https://user-images.githubusercontent.com/18748519/133965039-9b013889-1faf-4a33-adcf-fa4d85499d1b.png)
   
   We can observe that the output signal's frequency is exactly of the frequency of the input signal
   
 
 As individual circuits are working as expected, we create the entire PLL circuit joining all these components using subcircuit block.

5) Phase Locked Loop (PLL) :

   Below are the simulation results for PLL circuit
   
   ![Screenshot (46)](https://user-images.githubusercontent.com/18748519/133965595-05d00eda-aa17-40c4-9f76-5acb38ebe7df.png)
   
   
   When we zoom in waveform, it looks as below
   
   ![Screenshot (47)](https://user-images.githubusercontent.com/18748519/133965675-b7577b1e-e415-4c57-aac9-8ad52cd9a847.png)

   ![Screenshot (45)](https://user-images.githubusercontent.com/18748519/133965727-e7aa06f4-1228-4f68-affb-9de490d89b05.png)

   We can observe that reference signal and feedback signal are mimicing each other. In an ideal case, both signals should overlap without any phase noise. We can also observe signals with frequency X8, X4, X2 which can also be used as frequency multiplier.
   
# Layout Development:
  
  Layouts for the above working circuits are developed individually and then connected to form entire PLL circuit.
  
  1) PFD Circuit: 
     
     ![Screenshot (49)](https://user-images.githubusercontent.com/18748519/133968603-29768479-c428-4a58-815e-f82779cffcb7.png)
     
     
    Area of the layout can be find using box command in the terminal
     
     ![Screenshot (23)](https://user-images.githubusercontent.com/18748519/133968975-eeef78d1-e395-4e70-b01f-b8f15ee079fe.png)












