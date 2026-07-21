# Test Bench
The "Test Bench", as I've named it, is a breadboard control system for my combustion device testing (igniters, gas generator, thrust chamber). It was devised as a solution to the problem of controlling multiple valves in the proper sequence and logging sensor data. In the future, the electronics of Test Bench will ideally be consolidated onto a PCB and act as the on-board engine controller. Sequencing software would also be ported into microcontroller code and logging would occur on-board with some interface for external debugging/logging.

The concept was devised in late January 2025, and by mid-March 2025, it was ready for igniter testing. Since then it's completed over 150 hot-fire igniter and gas generator tests, as well as helped automate some pump testing.

The code for Test Bench can be viewed on [GitHub](https://github.com/jondotschuitema/test-bench)
## Control Module

### Design
The Test Bench was intended to cover all ground testing needs - offering all I/O's necessary to run an engine. This would include inputs for several pressure sensors, thermocouples (regen system performance), robust servo actuated ball valves for main propellant flow, and fast acting solenoid valves for tank management and ignition, along with the ignition spark driver.

An early concept diagram is shown in Figure 1.

![](../resources/assets/Pasted%20image%2020260706123830.png)

*Figure 1. Test Bench hardware architecture* (early sketch)

#### Sensors
##### Pressure
TB includes inputs for 12 pressure sensors, enabling an acceptable amount of data for system characterization:
1. Chamber Pressure - directly measures the TCA pressure, the main driver of TCA performance for this engine
2. Fuel Injector Pressure - confirms fuel pressure drop into engine meets stiffness requirements
3. Oxidizer Injector Pressure - confirms oxidizer pressure drop into engine meets stiffness requirements
4. Fuel Pump Outlet Pressure - confirms pump head performance and pressure drop across cooling jacket and lines
5. Oxidizer Pump Outlet Pressure - confirms pump head performance and pressure drop in lines
6. Fuel Pump Inlet Pressure - confirms suction performance
7. Oxidizer Pump Inlet Pressure - confirms suction performance
8. Fuel Tank Pressure - confirms supply pressure is adequate, and drives ullage pressurization closed-loop control
9. Oxidizer Tank Pressure - confirms supply pressure is adequate, and drives ullage pressurization closed-loop control
10. Purge/Pressurant Supply Pressure - confirms supply pressure (post regulator) is adequate for providing tank ullage pressure, turbopump IPS purge, and propellant line purging
11. Purge/Pressurant Tank Pressure - confirms tank pressure (pre regulator) is adequate for driving purge gas supply
12. Igniter Chamber Pressure - used to confirm ignition

##### Temperature
TB includes 4 K-type thermocouple slots:
1. TCA Coolant Inlet Temperature - confirm coolant is at expected initial temperature
2. TCA Coolant Outlet Temperature - can find the temperature rise, and therefore heat added to coolant before injection. Also confirm coolant remains below critical temperature at outlet
3. Oxidizer Tank Temperature - Monitors temperature of liquid oxygen in tank
4. Fuel Tank Temperature - Monitors temperature of ethanol in tank

##### Load Cell
Test Bench includes one slot for a load cell using an on-board HX711 amplifier. The load cell will be used for direct engine thrust measurement. However currently, the load cell amplifier is not being used - instead load cell readings are read from a serial connection with the dynamometer. This is for automated control and logging of dynamometer functions using Test Bench, including closed loop motor speed control, torque and speed measurement.

##### Hall Effect Sensor
Test Bench includes one or potentially more slots for a hall effect sensor, intended to measure turbopump shaft speed. This would aid in characterizing the turbopump system and help in governing over-speed situations that may damage bearings, seals, and lead to cavitation.

#### Servo Valves
There are four servo control outputs, all driven with 8 volts and PWM control from the Arduino:

1. SV1 - Fuel isolation valve
2. SV2 - Fuel main valve
3. SV3 - Oxidizer isolation valve
4. SV4 - Oxidizer main valve

*During pump testing campaigns, SV1 and SV2 are used for isolation and main, respectively. SV3 and SV4 are used during gas generator characterization testing as fuel and oxidizer main valves, respectively.*
#### Solenoid Valves
Test Bench offers seven solenoid valve outputs, with two at 24V and five at 12V, although this can be easily reconfigured based on each solenoid valve's voltage requirement.

1. S1 - Torch igniter fuel valve
2. S2 - Torch igniter oxidizer valve
3. S3 - TCA purge gas valve
4. S5 - Fuel tank drain valve
5. S6 - Fuel tank pressurant vent valve
6. S7 - Oxidizer tank drain valve
7. S8 - Oxidizer tank pressurant vent valve

The DC voltage output is binary, controlled by relays that the Arduino can directly actuate with digitalWrite() calls.

*"S4" is reserved for the CDI (spark driver), described below.*
#### CDI (Spark)
The Capacitive Discharge Igniter (CDI) unit that energizes the torch igniter's spark plug is driven with 6 volts, connected to Test Bench slot S4. The CDI is controlled similarly to the solenoid valves, however the frequency of spark is specified in the Arduino's code - meaning when slot S4 is in the "ON" state, the driving voltage is applied in discrete bursts with some frequency. Currently, the frequency is 100 Hz. 

The CDI module used during testing is the Rcexl Automatic Single Ignition CDI for the NGK CM6 spark plug. 

![](../resources/assets/Pasted%20image%2020260716202514.png)

*Figure 2. Rcexl CDI unit*

#### Communication Protocol
I wanted to ensure that the communication loop between the controller software and hardware (since I opted for no physical interface) was:
- Fast
- Reliable
- Extensible

The easiest interface to implement was the two-way hardware serial (USB) available on the Arduino, with strong serial support in Python. The supported baud rates were determined to adequately cover the transmission requirements, based on the sample rate and transmitted packet size described below.

For example, a 60-byte data packet is 480 bits, transmitted at 200 Hz is 96,000 bits per second. Therefore, a standard baud rate of 115,200 bps accommodates the data throughput with margin. In practice, this met the speed requirement.

The communication protocol is extensible by nature, as evident in Tables 1 and 2, there is flexibility in the data field. The Arduino Mega can reportedly support up to 1 Mbps, which would allow 622 Bytes of data (not including sync, counter, checksum) at 200 Hz. I can't imagine a future where Test Bench or the final engine controller would need to transit that much data. Instead, that would allow for increased sample rate of over 2000 Hz with the existing 60 Byte packet.

Reliability would be achieved via four factors:

1. Synchronization - Every packet shared on the bus begins with a standard "sync" byte. Both the Arduino and Python continuously monitor the serial bus and watch for the sync byte, which always begins the packet. This way, the packet is decoded from the correct position every time. Of course, the sync byte is not unique and could be found in any other position of the data packet. There is where factor 2 comes in:
2. Fixed Packet Sizes - The size of the expected incoming and outgoing packet in both the Arduino and Python is hard-coded. So once either device begins reading the packet from the sync byte, it reads the rest of the message of N expected chunk size. If all expected number of bytes are accounted for and the read operation begins at the sync byte, then the packet is accepted and parsed. *Note: There is an edge case where a byte in the middle of the packet that matches the sync byte could be interpreted as the sync byte, then the remainder of the packet and a portion of the next packet is read - passing the checks of factors 1 and 2. The following factors will address this case, since the probability of grabbing the wrong sync byte is about 20%*
3. Counter - Every packet contains a 1-byte counter that continuously increments before it is sent. When the receiver detects a jump of more than 1 in the counter, it's indicative of a dropped packet (for whatever reason) or if the counter doesn't change, it's indicative of an issue with the transmitter software. Too many dropped packets (or too much time between fresh packets), and the Arduino sets itself into the abort "safe" state.
4. Checksum - A simple 1 byte XOR checksum is calculated for the entire packet to provide a basic level of error checking. Of course, CRC would be more robust, and if there's a need in the future to implement it, I will. However, the XOR checksum not only helps with confirming transmission integrity (packets with mismatching checksums are discarded and an error code is thrown to notify user) but adds more robustness with synchronization. For example, the probability of a false sync is 1 in over 1000 packets with the 1-byte sync and 1 byte checksum. If synchronization is lost, say, every 100,000 packets, then the odds of false sync is 1 in over 110 million packets. 

This is the first pass at the communication protocol, which currently meets all requirements, as it's demonstrated its ability to safely operate all systems under test without incident. There is plenty of headroom to add more robust error checking like a CRC, but so far, the simple implementations have done their job effectively and consistently. At some point, I will begin tracking statistics like number or frequency of re-syncs, timeouts, checksum errors, serial hardware errors to inform decision to graduate to a more robust protocol or not.

##### Structure
The data packets sent by the Arduino and Python software have mostly the same structure:

| Position | Field    | Size (Bytes) | Description                                                           |
| -------- | -------- | ------------ | --------------------------------------------------------------------- |
| 0        | Sync     | 1            | Used for synchronization - ensures reading at correct start of packet |
| 1 - 17   | Data     | 18           | Carries Python software commands (valves, etc)                        |
| 18       | Counter  | 1            | Continuously incrementing counter used to detect dropped packets      |
| 19       | Checksum | 1            | An XOR checksum that helps confirm transmission integrity of packet   |

*Table 1. Arduino input packet structure*

| Position | Field      | Size (Bytes) | Description                                                                                                                 |
| -------- | ---------- | ------------ | --------------------------------------------------------------------------------------------------------------------------- |
| 0        | Sync       | 1            | Used for synchronization - ensures reading at correct start of packet                                                       |
| 1 - 56   | Data       | 57           | Carries sensor readings: time, servo positions, solenoid states, pressure transducers, thermocouples, loadcell, tachometer  |
| 57       | Counter    | 1            | Continuously incrementing counter used to detect dropped packets                                                            |
| 58       | Error Code | 1            | Used to communicate errors that may occur on the hardware side to be displayed on software side: timeout, checksum, counter |
| 59       | Checksum   | 1            | An XOR checksum that helps confirm transmission integrity of packet                                                         |

*Table 2. Arduino output packet structure*

##### Interface
A screenshot of the Python GUI is shown in Figure 3.

![](../resources/assets/Pasted%20image%2020260717124910.png)

*Figure 3. Test Bench GUI screenshot*

The screen elements are as follows, going top to bottom, left to right:

- COM port (or simulation) selection and connection buttons 
- Signals file selection (changes how data is decoded)
- Logging filename, rate, and save folder selection
- Start / Stop logging button ("Record")
- Program selection, start delay, and start/stop buttons
- Error code indicators (click to acknowledge and clear)
- 4 x thermocouple digital readouts
- 4 x pressure digital readouts (can be changed to any of the 12 pressure signals)
- Load cell digital readout with zeroing button
- Tachometer digital readout with motor duty cycle control dials and start/stop button
- 4 x servo valve control dials, position digital readout, and quick open/close controls
- 8 x solenoid/ignition coil control buttons and status indicators
- Live plot with user-selectable signals 
- Status bar (bottom of window) showing communication, monitoring, display, and logging cycle times

##### Signals
The signals received from the Arduino in the Python software must be converted from their raw byte form to physical units for display and logging purposes. So, a signal dictionary was formulated to decode the data by identifying the start byte, length, scale, and offset. With this information, all signals can be parsed. If the data schema changes, either in how the packet is assembled or a sensor with a different range is connected, the signals dictionary can be changed easily. It is similar in principle to a database (DBC) file for a CAN bus. An example is shown below, with excerpts from the default Test Bench signals file:

```json
{  
  "time": {  
    "start": 0,  
    "length": 4,  
    "scale": 0.001,  
    "offset":0,  
    "min": 0,  
    "max": 1000000,  
    "precision": 3,  
    "unit": "s"  
  },  
  "servo1_command":{  
    "start": 4,  
    "length": 1,  
    "scale": 1,  
    "offset":0,  
    "min": 0,  
    "max": 100,  
    "precision": 0,  
    "unit": "-"  
  },  
  "pressure1":{  
    "start": 20,  
    "length": 2,  
    "scale": 0.122189638,  
    "offset":-12.5,  
    "min": 0,  
    "max": 100,  
    "precision": 0,  
    "unit": "psig",  
    "label": "oxidizer tank"  
  },  
  "temperature1": {  
    "start": 44,  
    "length": 2,  
    "scale": 1,  
    "offset":0,  
    "min": -200,  
    "max": 1260,  
    "precision": 0,  
    "unit": "C"  
  },  
  "load_cell": {  
    "start": 52,  
    "length": 2,  
    "scale": 0.000622935,  
    "offset":0,  
    "min": 0,  
    "max": 10000,  
    "precision": 3,  
    "unit": "Nm",  
    "label": "Motor Torque"  
  },  
  "tachometer": {  
    "start": 54,  
    "length": 2,  
    "scale": 1,  
    "offset":0,  
    "min": 0,  
    "max": 100000,  
    "precision": 0,  
    "unit": "RPM"  
  },  
  "counter": {  
    "start": 56,  
    "length": 1,  
    "scale": 1,  
    "offset":0,  
    "precision": 0,  
    "min": 0,  
    "max": 255,  
    "unit": "-"  
  }  
}
```

##### Commands
The Python side sends commands to the Arduino, either by user input (via buttons, dials on the UI, or hotkeys) or by the automated sequencing programs. An actively running programs overrides user commands (and locks out the GUI) and replaces the data portion of the transmission packet with its commands (determined in the state machine logic). There is also a "STOP" command that is triggered by a button on the GUI or by COM port disconnection that immediately sends the Arduino into its safe "abort" state. 

The Arduino doesn't send the Python GUI commands, only data for logging and internal error codes for display on the GUI.

Commands are sent via bytes in the outgoing data packet from the Python to Arduino.

##### Programs
The programs used to automate control sequencing are written as Python functions that contain state machine logic. One function (program) at a time is dynamically evaluated when a program is running. Inputs include a variables dictionary that stores data between function calls (returned by function, then passed in on next call), parameters which are loaded from a JSON file that allows user to tune behavior during application runtime (doesn't require restarting of software), command dictionary (commands 
to control Arduino), data dictionary (sensor readings from Arduino output), the previous state, and the last timer value that can be periodically updated throughout the state machine to track time-dependent mechanisms.

Adding programs is as easy as adding the function to the Python file (programs.py) and creating a corresponding parameters.json file. If the function syntax is valid, Test Bench will expose it for user selection the next time the application is run.

While the Arduino program contains logic to safe the system if there are any internal errors, communication issues, or from the stop command, the Python programs are still responsible for defining abort logic based on behavior of the system under control.

Think of a program abort as a "soft stop" whereas the Arduino internal abort as a "hard stop". It is preferrable to make the program abort logic as robust as possible, as what is safe for the physical system may not be the same thing as when all control is ceased during internal abort (ie - all solenoid valves lose power, and servo valves are commanded to close).

As an added safety feature, the program stop button latches when pressed, preventing the start button from being pressed until the stop button is un-latched (released). Additionally, all controls are disabled while the stop button is latched. This makes it so there is deliberate user action to continue operating the controls. It's essentially a software "E-stop". 


![](../resources/assets/Pasted%20image%2020260717175739.png)

*Figure 4. Program stop button in default state*

![](../resources/assets/Pasted%20image%2020260717175801.png)

*Figure 5. Program stop button in latched state*

### Build
The build consisted of sourcing all components (boards, wires, connectors, tools) based on architecture in Figure 1. The main electrical components were laid out in CAD and arranged to optimize for minimal footprint, serviceability, and ease of wiring between modules.

![](../resources/assets/Pasted%20image%2020260717213127.png)

*Figure 6. Component layout in CAD*

After boards were laid out, the enclosure was designed around them. Bosses were extruded up from the nearest surface to each board and would eventually accommodate a 2-56 or 4-40 threaded insert so that the boards could be screwed into the enclosure. 

![](../resources/assets/Pasted%20image%2020260717181317.png)

*Figure 7. CAD rendering of control module (front)*

Power connections were made with the Anderson PowerPole type with 30A pins to interface with the 20 AWG wire. The connectors on the rear (for power input) and front of the enclosure (solenoid output) are secured by 2-56 screws. 

The servo output connections were of the 3-pin M8 A-Code type, which provide positive engagement (threaded), some level of IP rating, adequate current throughput and are relatively inexpensive.

The pressure transducers are connected to the Arduino via DB39 and a custom made "squid" harness covered later in the build documentation. Similarly, the four thermocouples interface via a DB9. The spark ignition output signal, load cell, and tachometer inputs share a DB9 connection interface.

DB connectors were chosen for their low cost, somewhat compact form factor for given number of pins, adequate current throughput, and ability to positively engage via two 4-40 screws. 

#### Process
This section covers some highlights and commentary during the hardware build process. There is much that isn't covered since I spent more time building and less time photographing, but it should give a rough idea of what went into this over the course of ~2 weeks.

![](../resources/assets/Pasted%20image%2020260717214917.png)

*Figure 8. PETG enclosure with threaded inserts and boards installed*

![](../resources/assets/Pasted%20image%2020260717215007.png)

*Figure 9. Installation of power wires and PowerPole connectors*

An additional 5V bus was added since the initial CAD design, this one powered from the Arduinos 5V output for every 5V device other than the pressure transducers. Those were connected to their own 5V bus from external power.

![](../resources/assets/Pasted%20image%2020260717215859.png)

*Figure 10. Soldering of the DB39 connector for pressure transducers*

![](../resources/assets/Pasted%20image%2020260717215334.png)

*Figure 11. Installation of DB39 connector for pressure transducers and more power wires*

![](../resources/assets/Pasted%20image%2020260718134033.png)

*Figure 12. All front panel connectors installed with wires routed*

![](../resources/assets/Pasted%20image%2020260718134554.png)

*Figure 13. Wiring of the pressure transducer "squid" cable*

![](../resources/assets/Pasted%20image%2020260717215942.png)

*Figure 14. Completed controller (w/o lid) during initial function check*

Throughout the whole process, I took special care to confirm all pins in each connector and with my multimeter, tested continuity for every wire to confirm it made it into the correct pin. 

All subboard and sensors were grounded to the Arduino.

Servos required calibration, that is finding the exact PWM frequency command for the open (90 degree) and closed (0 degree) positions. Because the servos and the valves they were mated to were similar, but axle and horn positions not consistent between units, the calibrations values varied slightly. Servos were then matched to their slot on the controller and labeled.

During function testing, the 12V solenoid valves would predictably cause the Arduino to brown out (and throw timeout error codes while the board reset). At first, I had no idea what was going on as the 24V solenoids didn't have the same issue. As my circuits knowledge is somewhat limited to one undergrad class during freshmen year of my Mechanical Engineering program, I recruited ChatGPT for help and learned about inductive kickback. There must have been a high enough voltage spike to shock the relay board and therefore the Arduino as they are directly connected. Clamping a 1N5406 rectifier diode across the solenoid's terminals solved the problem.

![](../resources/assets/Pasted%20image%2020260717221521.png)

*Figure 15. Diode installation into solenoid connector*


![](../resources/assets/Pasted%20image%2020260717221342.png)

*Figure 16. Completed Control Module*

One might ask why I didn't purchase a COTS solution for control and logging. The answer is twofold:

1. This offered a learning experience in wiring, terminating connectors, enclosure design, and an exercise for my Python / C++ skills
2. The BOM totaled around $400 (for reference, a perpetual software license for LabView is $1,960)

## Power Module

### Design
Since sensors, valves, and capacitive discharge ignition (CDI) system run on different voltages, I devised a system of buck converters and power buses for power distribution at the different voltages. Separating the power module from the control module would reduce packaging complexity (and reduce footprint of control module, which was already approaching printer bed plate limits) and make buck converter output adjustments easier through better access.

The power system is fed by 24V, since 24V is the highest voltage requirement from the control module. All of the sensors and valves were estimated to draw 6.4 amps (if powered simultaneously). A cheap 24V 144W power adapter was purchased on Amazon but didn't offer overhead in the event all devices are powered. If this proved to be an issue in practice, a higher output adapter would be purchased (however, it hasn't been an issue yet).

Five LM2596 DC-DC buck converters then step the 24V down to the 12V, 8V, 6V, and 5V required for the various sensors and actuators. Each buck converter is capable of 3A throughput, which I estimated to be sufficient based on current draw of the various connected devices. Output connectors are of the Anderson PowerPole type.

Like the control module, the power module started with electrical component layout in CAD and the enclosure designed around them. The boards sit on bosses and are secured with machine screws. The enclosure also includes external mounting holes for 1/4-20" fasteners in the event it would be affixed to a t-slot test rig frame. 

Chronologically, the power module was the first module designed and built as its smaller and more simple construction acted as a proof-of-concept for my enclosure design workflow.

![](../resources/assets/Pasted%20image%2020260718140433.png)

*Figure 17. Power module CAD layout*

![](../resources/assets/Pasted%20image%2020260718140556.png)

*Figure 18. Power module CAD rendering, external*

### Build

![](../resources/assets/Pasted%20image%2020260718140908.png)

*Figure 19. Power module during assembly process*

![](../resources/assets/Pasted%20image%2020260718141039.png)

*Figure 20. Power module with 4 of 5 buck converters installed, powered on*

The wire routing and soldering was particularly difficult, as the position of the PowerPole connectors and output terminals were flipped, so output wires needed to be crossed. While packaging was compact, there could have been some more room for wire routing to make assembly easier.

![](../resources/assets/Pasted%20image%2020260718141354.png)

*Figure 21. Completed power module with control module*

While not visible in Figure 21, the power input connections of the control module are on the rear panel. While the PowerPole connectors offer sufficient current carrying capacity, ease of use, and limited positive engagement, they aren't immediately "poka-yoke". So, it would be very easy for a user to connect a power module output to the wrong control module input (ie - frying the 5V circuit with 24V input). PowerPoles do come with varied colors which would improve odds of not misconnecting, but for this first iteration of the controller, special care must be taken to match the power and control module connections. In the future, connectors with poka-yoke features could be included, however its more likely that the next control module will include its own power distribution board.

## Thermocouple Module

### Design
Since space in the control module was limited, a separate module was created for the thermocouple amplifier boards. The Adafruit MAX31855 digital output (SPI) was chosen since the analog inputs in the control module's Arduino Mega were allocated for the pressure transducers. The board is designed for K-type thermocouples, which are general purpose enough to cover the operating temperatures of all fluids (with the exception of thrust chamber gas) in the engine system. 

The module can be powered from a dedicated 5V DC input or from the DB9 carrying the data signals to the Arduino, which also includes 5V power from the Arduino. 

SPI communication protocol is used to connected the MAX31855's to the Arduino Mega in the control module. The clock (CLK() and data out (D0) pins which are common between all MAX31855's and two digital pins on the Arduino are consolidated on a bus (Figure 22) before being connected to the Arduino via the DB9 connector. The four individual chip select (CS) are each wired to the DB9.


![](../resources/assets/Pasted%20image%2020260719202023.png)

*Figure 22. Thermocouple module internals layout*

![](../resources/assets/Pasted%20image%2020260719202129.png)

*Figure 23. Thermocouple module in CAD*

The design easily allows for expansion of number of thermocouples: up to 7 total if two of the pins in the data output are re-allocated for chip select connections.

### Build
The thermocouple module was designed, printed, and assembled using the same workflow as for the power and control modules. Panel-mount K-type thermocouple connectors from McMaster-Carr are featured on the main interface panel for easy thermocouple mini-connector installation.

There aren't build photos of the thermocouple module other than Figure 24 showing an example of a failed print (layer shift and collapsed support material). My Prusa Core One had issues completing the thermocouple module enclosure, as it took 3 - 4 attempts. Finally, after changing print orientation, infill pattern, swapped support material type from organic to snug, and re-calibrated each printer axis, the prints completed successfully.

![](../resources/assets/Pasted%20image%2020260719211029.png)

*Figure 24. Failed thermocouple enclosure print* 

Finally, I purchased a Pelican Air 1595 case with configurable foam insert to house all Test Bench hardware (Figure 25). The IP67 rated case provides convenient and rugged and easy transportation to/from test sites. A laptop that runs the Test Bench software is also stored in the left-hand compartment, with cables stored underneath (laptop now shown in Figure 25)

![](../resources/assets/Pasted%20image%2020260719211804.png)

*Figure 25. Test Bench hardware stored in case*

## Dynamometer Interface

I wanted Test Bench to be able to control/automate pump testing by implementing closed-loop motor speed control and set valves to certain positions. Instead of re-implementing the functions of the dynamometer, I figured I could connect the dynamometer directly to test bench via software serial (as the physical USB ports would be occupied for both devices). 

The following would need to be implemented:

- Tachometer and torque measurements from dynamometer to test bench
- Motor duty command from test bench to dynamometer

I implemented a similar serial protocol packet structure in both dynamometer and test bench Arduino code, however data like RPM measurements, torque (sent as integer, converted to decimal on Test Bench side) were too big to be stored in one byte. So these were packaged and decoded using two bytes.

An MCP4725 12-bit DAC was added to the Test Bench hardware stack to convert the motor duty command from the software to physical motor commands on the dynamometer. The dynamometer uses a potentiometer to vary the 5V signal to the motor, controlling its speed. The Test Bench's DAC would circumvent the potentiometer and send 0-5V commands directly. The increments for motor speed were changed in Test Bench from a 0-100% to 0-4095 range, providing more granular steps in output voltage and making full use of the DAC's resolution. The command from Test Bench was therefore a two byte motor duty cycle packet.

A program for constant motor speed control was written with the following sequencing:

1. Ramp up to nominal operating speed at user-specified rate limit using feed-forward control with gain derived from the motor's Kv value
2. Hold at nominal RPM for user-specified number of seconds using closed loop proportional control (at the time of implementation, the internal variables system was not implemented, therefore derivative and integral terms were not included)
3. Ramp down to zero RPM at user-specified rate limit using feed-forward control

The program also included clamping functions for the speed command to prevent motor saturation (although a runtime error would have occurred if a command larger than the two-byte data limit was reached).

There were also safeguards in place to prevent dynamometer damage during cases where motor load was excessive - for example if the pump impeller or geartrain began to seize. Since Test Bench had access to the actual motor torque measurements, if the torque exceeded a certain threshold (usually 2.2 Nm) and the RPM was below a threshold representing reasonable speed for the input power (3400 RPM), then the program would switch to abort state. During initial testing of the pump program, 2.2 Nm was just under the point at which the dynamometer geartrain would rapidly degrade under high load.

The "IG-LC-Tach" DB9 interface on Test Bench is somewhat configurable - the pins on the Test Bench connection side are currently wired for software serial (3 pins), MCP4725 output (2 pins) and CDI control (2 pins). When used to communicate with the dynamometer, the following pins are allocated:

1. Serial Tx (Arduino)
2. Serial Rx (Arduino)
3. Ground (Arduino)
4. Motor data signal (MCP)
5. Ground (MCP)

Shown in Figure 26 is the first iteration of the dynamometer control interface, which is a digital display for duty cycle, a control knob, and power rocker switch. I modified the motor speed controller by interrupting the control knob signal and ground wires with DB9 connectors. I also ran wires for software serial from the dynamometer and terminated them in the DB9 connector connected to the motor controller input side. This way, the DB9 from Test Bench could be connected to the dynamometer and share data. 

![](../resources/assets/Pasted%20image%2020260720145452.png)

*Figure 26. First iteration of Test Bench - Dynamometer interface*

![](../resources/assets/Pasted%20image%2020260720145553.png)

*Figure 27. Test Bench and Dynamometer during wiring of connections*

### Results

The interface worked well, functionally. During testing, the feedforward + proportional feedback control would achieve around 10% steady state accuracy. There was also usually significant overshoot following the ramp up phase, which was easily mitigated by lowering feed forward gain. To achieve better steady state accuracy, more tuning could be performed, along with introduction of integral gain. 

I haven't implemented the improvements partly because I haven't established requirements for speed holding accuracy, as it pertains to generating acceptable H-Q curves for the pumps under test. To put things into perspective, a 10% error in RPM according to pump affinity laws, would translate to roughly 20% error in head. For example - a pump designed to generate 30 meters of head at 3000 RPM being driven 10% above at 3300 RPM would produce 36 meters of head, around 9 psi increase in pressure (for water). In the case of my engine plumbing system, 9 psi is well above expected pressure drops across main values and would equate to several meters of piping worth of friction loss. Narrowing the pump speed control error to 1% makes error in head generated only 2%, or less than 1 psi. 

Therefore, if I were to move away from my constant flow rate pump test method to constant speed, I would establish accuracy targets and implement a more robust closed loop control system - first by more rigorous tuning and the introduction of an integral term.

