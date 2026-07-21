# Turbopump

This section will not go into too much depth about the design process, as it closely followed processes and uses equations for pump and turbine component design from Huzel and Huang's "Modern Engineering for Design of Liquid-Propellant Rocket Engines".

It will, however, explain some of the reasoning behind chosen parameters and dimensions. The goal was not to optimize or target maximum efficiency, but develop a turbopump system that would deliver the required propellant mass flow rates, pressures (including losses along the way to the thrust chamber), manufacturability with tools found in a "home shop", and low cost without requiring turbine gas temperatures or mass flow rate that would further un-justify the deployment of a turbopump at this small scale.

The overall strategy was to develop low-power pump and turbine prototypes in 3D printed plastic with inert working fluid to build comfort with the design, manufacturing, and testing cycles. Using the dynamometer, the prototypes would be fully characterized: pump head-flow curves over speed, turbine power, required mass flow rates, enthalpy drops at design speeds. Efficiency could be measured and sources of losses identified. For instance, it was found that at this scale, mechanical losses from shaft seals played a significant role in lowering shaft-to-fluid power efficiency. 

### Process Outline
The 1D design was completed in an Excel spreadsheet, with pump head requirements set by engine chamber pressure, estimated coolant passage pressure drop, and other losses in the fluid circuit. Mass flow rate delivered by the pumps considered the main flow rate into the engine, additional flow used for film cooling, and flow required by the gas generator. This being a circular calculation (as gas generator mass flow is set by turbine power requirements, which is set by pump power requirements, which are set by pump mass flow rates, etc), iterative formula calculation mode is needed. 

The 3D design is accomplished with parametric modeling in Autodesk Fusion. Major parameters like impeller diameter, passage widths, volute casing throat diameter, etc are set up so values from the 1D design in Excel can be entered and efficiently modify 3D geometry.

When 3D design candidates are ready, they are 3D printed in plastic, pre-processed (sanding, epoxy coating for water tightness, and sealing surfaces lapped) and are characterized on the dynamometer and water test rig. 

Data is collected at various operating points to generate characteristic curves. If expected head is not achieved at the desired flow rate and shaft speed, deeper analysis is performed to attempt to isolate major sources of output and efficiency loss.

# Pumps

To simplify pump suction design requirements, the propellant supply tanks would be pressurized up to around 50 psig and a suction specific speed of 2000 was chosen to a) eliminate need for inducer, b) keep shaft speeds reasonable, and c) keep the pump impeller at a manufacturable size. Modest tank pressurization was chosen to alleviate some of the burden of pumps to generate suction without cavitation, as well as enable turbine startup using tank head pressure.

Eventually, the pumps will be cast or DMLS printed in aluminum alloy or stainless steel for strength at operating pressures and chemical compatibility with both propellants.

## Design

### Fuel Pump
The fuel pump is required to deliver 0.204 kg/s of 95% Ethanol to the thrust chamber for main combustion and 5% film cooling, along with an additional 0.012 kg/s for the gas generator. It also must generate 280 m of headrise for a total output pressure of 25 bar (360 psia) which is required to overcome pressure losses through the plumbing, cooling jacket, and provide sufficient pressure drop across the injector into the 300 psia thrust chamber.

Suction specific speed was chosen as a design point and set to 2000, as literature pointed to designs exceeding 10000 typically requiring inducers. The goal of this project was not to maximize suction performance (thus reducing weight of tanks and excessive propellant pressurization hardware). The chosen suction specific speed value also yielded reasonable shaft speed of ~24000 RPM, which is near the upper limit of COTS bearings found on McMaster-Carr at this small scale. A lower suction specific speed would theoretically provide more margin before cavitation would occur. Additionally, any higher suction specific speed and higher shaft speed would mean smaller impellers and volutes, which would pose increasing challenges with manufacturing. 

The performance requirements puts the specific speed of the fuel pump at 300 (imperial units), which is at the lower end of radial centrifugal geometry territory. 

Based on literature on prior state of the art, head coefficient was chosen to be 0.7 with an inlet flow coefficient around 0.1 - a somewhat aggressive high head, low flow design. This yielded very small impeller inlet and outlet passage widths, under 1 mm. Any lower and boundary layer effects and risk of blockage may impede performance. However, this was not thoroughly studied during this project.

The efficiency target is 30% based on empirical data found in literature, and test results from the Pathfinder pump. Since this ultimately affects turbine power requirements, and thus gas generator mass flow rate, and thus fuel pump output flow and hydraulic power, the exact efficiency will be determined from component level tests. The design process may be iterative, and will eventually converge on a set of operating points for the pump and turbine that yields the required fuel mass flow rate and outlet pressure. 

From preliminary tests discussed in the following sections, the actual efficiency of the "production" intent pump was significantly lower - around 14%. However, mechanical losses from the seals and operating far away from the design point (only 10,000 RPM) are likely the cause of the very low efficiency. With improvements to seal design (moving from contacting lip seals to non-contacting labyrinth seals) and being able to test closer to the best efficiency point (less of the motor's power is spent overcoming seal friction) is expected to improve performance. At this small scale, such low efficiencies greatly penalize the engine cycle performance - with excessive turbine power requirements follows gas generator mass flow rates approaching or even exceeding main thrust chamber flow rates!

Therefore, it is imperative that efficiency close to the 30% target is met. 

The fuel pump specs are listed in Table 1:

| Parameter                  | Value | Unit |
| -------------------------- | ----- | ---- |
| Suction Specific Speed     | 2000  | -    |
| NPSHa                      | 44.7  | m    |
| Specific Speed             | 300   | -    |
| Headrise                   | 280   | m    |
| Flow Rate                  | 0.22  | kg/s |
| Shaft Speed                | 24005 | RPM  |
| Hydraulic Power            | 593   | W    |
| Head Coefficient           | 0.7   |      |
| Discharge Flow Coefficient | 0.06  |      |
| Inlet Flow Coefficient     | 0.1   |      |
| Impeller Outlet Angle      | 30    | deg  |
| Impeller Inlet Angle       | 5.73  | deg  |
| Impeller Diameter          | 49.9  | mm   |
| Blade Count                | 9     |      |

*Table 1. Fuel pump specifications (as of 5/2026)*

![](../resources/assets/Pasted%20image%2020260504203029.png)

*Figure 1. Fuel pump cross section at the radial plane*

![](../resources/assets/Pasted%20image%2020260504203346.png)

*Figure 2. Fuel pump cross section at the axial plane*

![](../resources/assets/Pasted%20image%2020260504203443.png)

*Figure 3. Fuel pump isometric view

![](../resources/assets/Pasted%20image%2020260504214646.png)

*Figure 4. Cross section rendering of early production pump designed for testing with water*

#### Construction
Both inlet and outlets are sized for a 1/4 NPT fitting with hex profile for easier work holding. There are 12 x 4 mm bolts that secure the "core" (bearing + seal + shaft housing) to the volute casing. An elastomer O-ring acts as a static seal between the core and casing. The core has 6 x 4 mm threaded holes for attachment to the turbine. The bearings and seals are not pictures in Figures 1 or 2, as the selection has not been finalized. The bearing sits just next to the impeller to a) minimize overhung mass and b) allow for some cooling and lubrication from the pumped fluid. In testing so far, stainless steel open ball bearings were used successfully. Seal design will be discussed in later sections, but the overall strategy is to employ a primary seal to reduce most of the leakage, which will then be drained overboard (or possibly re-introduced into a low pressure area on the suction side). A secondary seal will prevent any remaining leakage from entering the turbine along the shaft. 

The design of the pump had additive manufacturing or casting in mind, therefore only the necessary material was included. This resulted in thin walls, ribs for stiffness, and scalloped flanges. Mating surfaces and bearing / seal bores are then easily machined to final dimensions.

In the final configuration, the pump impeller and volute will mirrored, as the shaft will be spinning in the counterclockwise direction from the perspective of the fuel pump inlet.
### Oxidizer Pump
The oxidizer pump is responsible for delivering liquid oxygen at 0.27 kg/s to the thrust chamber and 0.006 kg/s to the gas generator at around 25 bar. It is assumed the the oxidizer pump is riding on a common shaft with the fuel pump and turbine, and this spins at 24,000 RPM - yielding a suction specific speed of 3177. This is still well within design space that doesn't require an inducer, particularly since the oxidizer pump is supplied from a 51 psia tank - giving 22 m of available NPSH (vapor head is 9.1 m).

The oxidizer specific speed is calculated to be 375 (imperial), well within radial centrifugal design territory.

| Parameter                  | Value | Unit |
| -------------------------- | ----- | ---- |
| Suction Specific Speed     | 3177  | -    |
| NPSHa                      | 22.2  | m    |
| Specific Speed             | 375   | -    |
| Headrise                   | 192   | m    |
| Flow Rate                  | 0.28  | kg/s |
| Shaft Speed                | 24005 | RPM  |
| Hydraulic Power            | 522   | W    |
| Head Coefficient           | 0.7   |      |
| Discharge Flow Coefficient | 0.06  |      |
| Inlet Flow Coefficient     | 0.1   |      |
| Impeller Outlet Angle      | 30    | deg  |
| Impeller Inlet Angle       | 5.73  | deg  |
| Impeller Diameter          | 41.3  | mm   |
| Blade Count                | 9     |      |

*Table 2. Oxidizer pump specifications (as of 5/2026)*

![](../resources/assets/Pasted%20image%2020260504204854.png)

*Figure 5. Oxidizer pump cross section at radial plane*

![](../resources/assets/Pasted%20image%2020260504204951.png)

*Figure 6. Oxidizer pump cross section at axial plane*

![](../resources/assets/Pasted%20image%2020260504205036.png)

*Figure 7. Oxidizer pump isometric view

#### Construction
The 3D design is mechanically similar to the fuel pump, with correctly scaled parameters for liquid oxygen service. Recycling the fuel pump design and leveraging Autodesk Fusion's parameterization makes scaling and dimensional changes relativity trivial. 

The bearing selection is not finalized, but will likely be a 440C stainless steel open ball design. To prevent metal-on-metal contact in a LOX environment, the stainless steel balls may be replaced with ceramic balls. Alternatively, the bearing may be of hydrodynamic type, or a ball bearing design but located inboard of the seal system. 

The static seal between the core and casing will be a LOX compatible material such as PTFE, which are easy to find on McMaster-Carr.

The dynamic shaft seals will also be LOX compatible, likely PTFE or graphite-filled PTFE lip or labyrinth type.

Note: Limited testing with graphite-filled PTFE discussed later showed poor sealing performance and high degradation rate.

## Pathfinder

To prove out first-time centrifugal pump design, a "pathfinder" pump was designed to output 21 m (30 psig for water) of head at 1 kg/s (15.9 gal/min) and a modest 3000 RPM. The mechanical design is very similar to the "production intent" units, as was actually the original design on which the scaled down units are based. 

The purpose of this campaign was to a) get comfortable with 1D and 3D design of pump hardware, b) explore manufacturing and assembly methods, c) compare 1D design predictions to actual hardware performance and d) eventually scale the design down (in size) to "production intent" geometry

The work on the Pathfinder pump culminated in a series of testing in summer of 2024. 

### Design
The pathfinder pump would be supplied from an open tank at atmospheric pressure, therefore suction requirements were kept conservative to prevent cavitation. 

Note: Specific speeds reported are in Imperial units.

| Parameter                  | Value | Unit |
| -------------------------- | ----- | ---- |
| Suction Specific Speed     | 1535  | -    |
| NPSHa                      | 9.4   | m    |
| NPSHr                      | 4.7   | m    |
| Specific Speed             | 498   | -    |
| Headrise                   | 21    | m    |
| Flow Rate                  | 1     | kg/s |
| Shaft Speed                | 3000  | RPM  |
| Hydraulic Power            | 207   | W    |
| Estimated Efficiency       | 43    | %    |
| Shaft Power Estimate       | 478   | W    |
| Head Coefficient           | 0.17  |      |
| Discharge Flow Coefficient | 0.01  |      |
| Impeller Outlet Angle      | 22    | deg  |
| Impeller Inlet Angle       | 30    | deg  |
| Impeller Diameter          | 111.5 | mm   |
| Blade Count                | 4     |      |

*Table 3. Initial design parameters of Pathfinder pump*

![](../resources/assets/Pasted%20image%2020260504212312.png)

*Figure 8. Isometric view of Pathfinder pump with fittings and pressure transducers installed*

### Construction and Testing

This section will roughly outline the build and test process of the Pathfinder pump. There were two major iterations of the volute casing and a few impeller variations. A lot of this process was not documented due to being busy with fabrication, testing, and data analysis. However there will be some references to the design variations in the test result data and discussion.

The first volute casing was FDM printed in PETG in two halves with the split in the radial centerline of the casing. The reasoning was to avoid support material on any of the wetted internal cavities. 

![](../resources/assets/Pasted%20image%2020260504220431.png)

*Figure 9. One half of the first Pathfinder volute casing *

The volute casing would seal against the core using an elastomer O-ring.

![](../resources/assets/Pasted%20image%2020260504220601.png)

*Figure 10. Rear view of one half of the volute casing with impeller*

The pump core was also FDM printed in PETG and mounted directly to the dynamometer.

![](../resources/assets/Pasted%20image%2020260504220718.png)

*Figure 11. Pump core mounted to dynamometer*


#### The Test Rig

To provide working fluid to the pump, and allow for indefinitely long test runs, a water tank and plumbing system was designed and built - featuring a 5 gallon tank with 1" NPT fitting, 1" Schedule 40 PVC piping, a 20 gal/min flow meter, and ball valves on the pump inlet and outlet.

![](../resources/assets/Pasted%20image%2020260504221012.png)

*Figure 12. Water test rig concept*

![](../resources/assets/Pasted%20image%2020260504221159.png)

*Figure 13. Assembled water test rig with pump integrated*

Sections of clear tubing were included at the pump inlet and outlet just before the flow meter for visual inspection for cavitation bubbles and FOD.

The dynamometer would provide driving power for the pump as well as direct torque and shaft speed measurement and logging. The dyno software would also sync up the inlet and outlet pressure readings with the shaft power data.

![](../resources/assets/Pasted%20image%2020260504222445.png)

*Figure 14. Pathfinder pump mounted to dynamometer*

#### Testing

Early tests were just to prove the system worked, but to eventually produce useful data for pump characterization, the test procedure is outlined as follows:

1.  Start with both valves in the fully open position
2. Select a target flow rate from the test matrix
	- For full H-Q characterization, a sweep from 0 GPM - 20 GPM in increments of 5 GPM
	- For design point tests, just the target flow rate (~16 GPM)
3. Power on the motor, adjust speed such that the steady state reading from the flow meter matches the target flow rate
4. Record 5 seconds of data
5. Close outlet valve 1/4 turn and adjust motor speed such that target flow rate is achieved.
6. Repeat steps 4 and 5  for outlet valve positions of 75%, 50%, 25%, 0%
7. If performing a full H-Q sweep, repeat steps 1 - 6 for Q = 10, 15, 20, 0 gal/min (0 is run last in the event of damage due to over-pressurization)

Flow rate (Q) was chosen as the set point as it was the easiest variable to control and measure - the pump motor was not closed-loop controlled for constant RPM and head (pressure) readings were too sensitive and noisy. 

Affinity laws and/or interpolation could be used to find common RPM values across the range of flow rates, thus enabling the plotting of head versus flow for a constant RPM. 
##### May 18, 2024
The first test of Pathfinder ended before it even started. The interfaces between the PVC connectors and pump leaked significantly even without the motor running. There was also leakage through the layer lines of the pump casing. The pump was run briefly and achieved around 12 psig at 15 gal/min before one of the un-cemented PVC connections broke and dumped the contents of the tank.

![](../resources/assets/Pasted%20image%2020260504222039.png)

*Figure 15. Screenshot of dynamometer software during first Pathfinder test*

##### May 24, 2024
The interfaces were "reinforced" with hot glue for the second test. From the 5/18 test, it was also clear that the O-ring was doing nothing for sealing, so it was replaced with silicone gasket maker.

![](../resources/assets/Pasted%20image%2020260504221441.png)

*Figure 15. Photo from second test of Pathfinder pump*

Seven tests were run at increasing shaft speeds, however the pressure data was erratic and non-responsive to changes in operating conditions. After some research into these relatively cheap Amazon pressure transducers, it was recommended to not let them be in direct contact with water for prolonged periods, as eventually the sensing circuitry would corrode if no properly sealed. Following the second test, both transducers were replaced with new units. For all future tests, they are removed immediately after testing, shaken to remove water, and let to air dry which solved the problem.

![](../resources/assets/Pasted%20image%2020260504224528.png)

*Figure 16. Screenshot showing useless pressure data during 16 gal/min run*

To compound issues, during a max RPM deadhead test, the pump case cracked and split near the line joining the two halves. However, it did not seem to be the PA adhesive failure, rather PETG material failure. It was unclear at what pressure the case failed due to the nonexistent pressure transducer readings. Following the fracture, the pump would not be able to generate pressure.

![](../resources/assets/Pasted%20image%2020260504224723.png)

*Figure 17. Crack in pump casing following second test campaign*

![](../resources/assets/Pasted%20image%2020260504224813.png)

*Figure 18. Condition of silicone gasket following second test campaign*

##### June 7, 2024
A new casing was printed in PETG, also in two halves to preserve interior surface quality but without an O-ring gland. This second and final major iteration of Pathfinder would make use of a liberal amount of gasket maker for sealing. Additionally, the entire outer surface of the casing and core would be coated with 2 coats of 2-part JB Weld plastic epoxy. The reasoning behind this was to a) seal the surface to prevent leaks through layer lines and b) add some structural stiffness.

![](../resources/assets/Pasted%20image%2020260504225441.png)

*Figure 19. Completed print of the second iteration of Pathfinder pump*


![](../resources/assets/Pasted%20image%2020260504225539.png)

*Figure 20. Second iteration hardware during epoxy curing*

An analog pressure gauge was added to the test rig at the outlet side of the pump to offer backup pressure reading in case of erroneous readings from the transducer. A vacuum gauge was added to the inlet to measure suction conditions.

![](../resources/assets/Pasted%20image%2020260504225900.png)

*Figure 21. Test rig with analog gauges added*

##### June 25, 2024
Testing resumed and yielded moderate success in that the pump operated without excessive leakage through undesirable paths and didn't suffer structural failures.

![](../resources/assets/Pasted%20image%2020260504232109.png)

*Figure 22. Primary seal leakage directed away from pump core via 1/4 tube*

The test procedure was simple: the motor was run at a sweep of duty cycles in increments of 25%. The outlet valve was adjusted incrementally and the pressure and flow rate were observed. 

The highest outlet pressure recorded was about 33 psig, however at 0 gal/min (deadhead condition) and at 3500 RPM. The pressure recorded near operating point of 15 gal/min was around 18 psig -  only 60% of the target pressure. This was at a shaft speed of 2590 RPM. I had mistakenly recalled the target flow rate to be 15, not 16 gal/min, but the data collected is within ballpark.

![](../resources/assets/Pasted%20image%2020260504232456.png)

*Figure 23. Screenshot of dynamometer readings at 100% motor duty cycle at 0 gal/min*

![](../resources/assets/Pasted%20image%2020260504232914.png)

*Figure 25. Screenshot of dynamometer readings at 15 gal/min*

It was clear that to achieve the target pressure at 16 gal/min (1 kg/s), either redesign of the pump or increase in shaft speed was necessary. Increasing shaft speed seemed like the easier route to take, but at the time it was beginning to become evident that the dynamometer was already at its power limits. At this stage, it was time to measure losses in order to quantify the gap between actual power delivered to working fluid and power that made it to the shaft.

The pump was run dry for short durations at a variety of increasing speeds while power was recorded. This would isolate the majority of the mechanical losses (seals, bearings, shaft misalignment) from the power delivered to the working fluid. 

![](../resources/assets/Pasted%20image%2020260504234154.png)

*Figure 26. Power loss curve for second iteration Pathfinder pump*

A polynomial curve fit was applied to the power loss data, providing a way to estimate mechanical loss at any given speed. This then isolates the remaining losses to the fluid in the form of hydraulic and volumetric losses. Mechanical efficiency can also be calculated.

So for example, the mechanical loss at 2893 RPM (Figure 25) is roughly 35 W. Subtracting this from the measured 263 W of shaft power yields the power delivered to the impeller. Dividing one from the other yields 86.% mechanical efficiency. Not bad.

If the hydraulic power is about 120 W (18.31 psi = 12.9 m head, 15 gal/min = 0.0009 m3/s), then the hydraulic efficiency is 52%. For future designs, increasing this less-than-ideal hydraulic efficiency would be higher priority.

The overall efficiency then at this operating point is 45% - within range of the estimated 43%, albeit at around half the target performance.

After testing, the pump was disassembled to check the condition of the seals, shaft, and bearings. At this stage, it was clear that the silicone gasket maker posed a significant amount of time and effort to clean and re-apply between disassembly cycles. 

![](../resources/assets/Pasted%20image%2020260505200427.png)

*Figure 27. Pathfinder pump casing following disassembly*

![](../resources/assets/Pasted%20image%2020260505200705.png)

*Figure 28. Pathfinder pump shaft showing mild scoring from seals*

![](../resources/assets/Pasted%20image%2020260505200838.png)

*Figure 29. Primary shaft seal lip exhibiting negligible wear*

![](../resources/assets/Pasted%20image%2020260505201143.png)

*Figure 30. Bearing and seals following disassembly*

As seen in Figure 30, the secondary seal exhibits more evidence of wear by the presence of seal material particulate. This is likely due to increased friction heating, as the secondary seal is not in direct contact with the bulk of the pumped fluid during operation as is the case for the primary seal. The pumped fluid likely provides the primary seal with more lubrication and cooling, leading to less noticeable wear.

##### July 4 - November 11, 2024
Several tests were run between July and November to evaluate four impeller designs with major variation parameters listed in Table 4 below:

| Impeller     | Blades | Outlet Angle (deg) | Inlet Angle (deg) |
| ------------ | ------ | ------------------ | ----------------- |
| 1 (Original) | 4      | 22                 | 30                |
| 2            | 7      | 22                 | 30                |
| 3            | 7      | 22                 | 6                 |
| 4            | 12     | 38                 | 30                |

*Table 4. Impeller design parameters tested*

The test procedure enumerated in prior sections was followed to yield data at a sweep of flow rates and pump speeds while outlet static pressure (static head) and shaft power were recorded. The results were compiled in a table like shown in Figure 31 for each impeller tested.

![](../resources/assets/Pasted%20image%2020260505214457.png)

*Figure 31. Impeller 1 test result table*

##### NPSH Testing
NPSH was not evaluated on the Pathfinder pump, however at no point under normal operation (with inlet valve fully open) were there indications of cavitation - either audible or visible through the clear section of pipe just before the inlet or just after the outlet.

For final production intent design candidates, NPSH will be characterized.

### Results
Because the impeller tests were not run at constant speed but "constant" flow, a clean H-Q curve was not immediately ready to be generated from the raw data. Again, this was due to the fact that the motor did not have closed-loop RPM control (at the time) and had relatively course duty cycle control in 1% increments from the motor controller.

Therefore, each flow rate (Q) and head (H) datapoint was normalized over a reference speed using pump affinity laws. With a common speed, an H-Q curve could be generated. Of course, this introduces uncertainty the farther the raw speed data point is away from the reference speed, but this analysis could a) give an overall trend, b) give insight into how pump speed should be adjusted to achieve the performance targets, and c) provide a basis for comparing the different impeller designs.

An example of the  normalized H-Q values for a reference speed of 3000 RPM is shown in Figure 32.

![](../resources/assets/Pasted%20image%2020260505220618.png)

*Figure 32. Normalized reference head and flow data for Impeller 1*

Outliers (rows where speed was at least an order of magnitude away from the reference speed) were removed and H-Q plots were generated. For readability, volumetric flow rate was converted to mass flow rate. An example for Impeller 1 is shown in Figure 33.

![](../resources/assets/Pasted%20image%2020260505221001.png)

*Figure 33. Impeller 1 H-Q curve at 3000 RPM*

The analysis was repeated for the all the tested impellers and the H-Q and Efficiency-Q curves are overlaid in Figure 34.

![](../resources/assets/Pasted%20image%2020260505223305.png)

*Figure 34. Head and efficiency versus flow comparison of impellers*

As is evident in Figure 34, Impeller 4 with the increased outlet angle and number of blades was the best performer in terms of head and efficiency. The overall trend of increasing number of blades from 4 -> 7 -> 12 tended to yield higher head at a given flow rate. Reducing inlet blade angle in Impeller 3 resulted in worse head performance when compared to the other 7-bladed impeller (Impeller 2). 

These results are consistent with theory:

1) Increasing outlet blade angle (Impeller 4) increases the tangential velocity component of fluid leaving the impeller, of which head is directly proportional. Efficiency may increase due to better alignment of flow circulating in the casing.
2) Increasing blade count increases slip factor, therefore increasing actual tangential velocity relative to ideal tangential velocity, therefore increasing head. Efficiency increases due to lower slip loss and better alignment of flow between impeller and casing.

Another noteworthy trend observed in all impellers is the shift of BEP away from the design 1 kg/s flowrate to closer to 1.2 kg/s, possibly suggesting losses, slip, and required flow channel area were overestimated during 1D design process.

When comparing the scaled power (Figure 32) and the measured power (Figure 31) for Impeller 1, it's evident that a significant increase in shaft power would be necessary to drive the pump into target performance territory. The dynamometer was effectively limited to 300 W, which for future high performance tests would require sourcing of a higher output power supply and possibly a higher power motor. The original estimates for the Pathfinder pump put the required shaft power around 480 W, however a 400 W power supply was readily available at the time of sourcing dynamometer parts. It was expected that the power supply would need to be upgraded and affinity laws would help close the performance prediction gap in the analysis process.

For example, using pump affinity laws (Figure 36), to achieve the target 21 m of head, Impeller 4 would need to increase speed from 3000 RPM to 3120 RPM (if using H-Q model) or to 3284 RPM (applying affinity directly to raw data). However, since the H-Q model already makes use of affinity laws for normalization around reference speed, there is likely more uncertainty associated with the 3120 RPM estimate. Brake power would need to increase, possibly up to 530 W if applying affinity laws to the raw data (Figure 35).

![](../resources/assets/Pasted%20image%2020260506211736.png)

*Figure 35. Affinity laws applied to estimate new speed and power for Impeller 4*

![](../resources/assets/Pasted%20image%2020260506210019.png)

*Figure 36. Pump affinity laws (Wikipedia: [Affinity laws - Wikipedia](https://en.wikipedia.org/wiki/Affinity_laws))*

Due to limited time, the models produced by the data collected on Pathfinder were not validated with additional data collection. A more comprehensive validation would be performed for the production intent pumps. 

### Conclusions from Pathfinder Pump
While the Pathfinder design did not yield the desired performance at tested speeds, it was a good exercise in establishing a design -> build -> test -> analysis process. The results suggest that with a higher speed than what was tested, the target head of 21 m at 1 kg/s could have been achieved. The dynamometer was power limited for this campaign -  with a bottleneck in the 400 W power supply and with around 70% motor efficiency, yielded under 300 W of usable power. For future tests, a higher output power supply would be employed.

In most cases, measured overall efficiency did not meet expectations (43%) which were based on empirical correlations from pump design literature. However, it was found that for impellers with higher blade count and outlet angle, efficiency could meet or exceed the empirical estimations. Production intent designs will adopt this design strategy.

Mechanical efficiency hovered around 88% for all impellers, an acceptable number but could be improved with lower friction seals.

**The Pathfinder pump campaign provided enough confidence to proceed with scaling the geometry down to production pump size and scaling performance up to production  targets.**
## Production Prototypes
The strategy for the production intent (to be referred to as "production" from now on) pumps was to scale the geometry down and increase performance to meet thrust chamber propellant pressurization requirements. The fuel pump was chosen for manufacturing and test trials, as eventually the ethanol fuel would be easier to handle during testing and validation. Until then, water would be used for initial testing, and the H-Q characteristics scaled for the difference in density between ethanol and water.

Similar to the Pathfinder pump, the production prototypes were 3D printed PETG plastic and epoxy coated. However, the mating flange between the production casing and core were machined and lapped flat and smooth for better sealing performance with an elastomer O-ring. This proved successful during testing. 

The primary and secondary shaft seals were rubber lip type sourced inexpensively on McMaster-Carr, but these demonstrated significant mechanical losses (discussed later in results). So, significantly more research was conducted into sealing solutions, including reducing friction with a PTFE shaft sleeve and custom-machined PTFE lip seals. Additionally, the COTS rubber seals were not even close to being rated for the 24,000 RPM shaft speed at the potentially high fluid pressures, however they held up surprisingly well during testing, despite shedding noticeable amounts of debris into the pumped fluid. In actual operation, this would be unacceptable, as it could clog bearings, the narrow impeller channels, valves, and most of all - the small injector holes.

Since the Pathfinder pump, Prusa added support for organic supports in their PrusaSlicer, which enabled printing production casings in one piece using the Prusa MK3S+.  The prints were oriented at a 45-50 degree angle, which the outer surface facing the print bed. This allowed for minimal amount of easy-to-remove organic support structure to contact the wetted internal surface. Light sanding eliminated traces of support material. 

![](../resources/assets/Pasted%20image%2020260506224815.png)

*Figure 37. Fitting integration test on early production prototype*


![](../resources/assets/Pasted%20image%2020260506225131.png)

*Figure 38. First prototype test article with epoxy coating*

![](../resources/assets/Pasted%20image%2020260506225258.png)

*Figure 39. First prototype mounted on dynamometer with 5:1 geartrain*

To achieve higher shaft speeds, a 5:1 helical gearset was added to the dynamometer to step up speed from the motor. This would increase the maximum shaft speed from 5600 RPM to 28,000 RPM.


![](../resources/assets/Pasted%20image%2020260506225615.png)

*Figure 40. Water test rig integration with prototype pump*

The water test rig was modified with smaller 1/4 NPT control valves and a reduction of tube diameter from 1" to 3/8" .  3/8" x 0.035" wall thickness tubing with the full propellant flow rate was estimated to require 4 psi / meter of pressure drop. For testing and short runs of tubing, this would be sufficient.

This was also my first experience with tube bending and Yor-lok compression fittings and did not experience any issues with the process. The test configuration is visible in Figure 40.

![](../resources/assets/Pasted%20image%2020260506225956.png)

*Figure 41. First prototype ready for test*

Several tests were run, sparsely, between January and October 2025. The main objectives were to get the production pumps working and observe their behavior, characterize their performance, and trial a few impeller geometries (Table 5).

| Impeller       | Blades | Outlet Angle (deg) | Inlet Angle (deg) |
| -------------- | ------ | ------------------ | ----------------- |
| 1              | 9      | 30                 | 5.73              |
| 2 (Not Tested) | 9      | 30                 | 38                |
| 3              | 9      | 90                 | 90                |
*Table 5. Production test impeller variations*

Shaft speeds during testing were modest, usually never exceeding 10,000 RPM (2000 RPM at the motor), so measured head at the target flow rate between 3 - 4 gal/min was fairly low. With the additional losses from the gearing, available driving power was lower than for Pathfinder pump tests. In October 2025, this prompted an upgrade to a larger 1400 W motor and 1500 W power supply. As discussed in the dynamometer sections, the increase in power output exposed weaknesses in the PETG plastic geartrain, often melting and shredding gears. This subsequently required re-manufacturing of the geartrain components (PETG-CF improved the situation a little) and much time spent on re-assembly. 

![](../resources/assets/Pasted%20image%2020260507231658.png)

*Figure 42. Inside of prototype pump casing after test*

![](../resources/assets/Pasted%20image%2020260507231738.png)

*Figure 43. Impeller after test*

![](../resources/assets/Pasted%20image%2020260507231819.png)

*Figure 44. Bearing condition after test*

I accidentally purchased a steel bearing, which oxidized and seized immediately after the first test (Figure 44). This was later replaced with a stainless steel bearing.

![](../resources/assets/Pasted%20image%2020260507231944.png)

*Figure 45. Shaft condition after test*

![](../resources/assets/Pasted%20image%2020260507232030.png)

*Figure 46. Seal condition after test*

![](../resources/assets/Pasted%20image%2020260507232104.png)

*Figure 47. Prototype v2 masked for epoxy coating*

![](../resources/assets/Pasted%20image%2020260507232146.png)

*Figure 48. Prototype v1 (right) next to prototype v2 parts (left)*

![](../resources/assets/Pasted%20image%2020260507232545.png)

*Figure 49. 1/4" NPT tapping prototype v2 inlet*

![](../resources/assets/Pasted%20image%2020260507232633.png)

*Figure 50. PTFE-sleeved shaft initial turning*


During production pump testing, it was also becoming clear that the shaft seals imposed a large mechanical loss penalty relative to the hydraulic power of the system. To help mitigate the high friction, a PTFE sleeve was pressed onto a shaft and turned down to the correct diameter (Figure 51). As discussed in the results section, this would do little for efficiency.

![](../resources/assets/Pasted%20image%2020260507232734.png)

*Figure 51. PTFE-sleeved shaft (bottom)*

One issue of note that occurred frequently with prototype v2 was entrained air bubbles in the flow. The exact source wasn't known, but it was hypothesized that the pump was aspirating through a) the primary seal (a PTFE bushing-type seal was used at one point), b) cracks in the casing, c) improperly sealed pressure tap port. Prototype v2 was shelved and testing continued with prototype v1.

In September 2025, the servo-actuated ball valves where integrated into the water test rig (Figure 52). This would enable remote and automated control of testing, with precise control over valve position.

![](../resources/assets/Pasted%20image%2020260507233300.png)

*Figure 52. Servo valves integrated on test rig*

![](../resources/assets/Pasted%20image%2020260507233337.png)

*Figure 53. A full picture of the wet test rig*

![](../resources/assets/Pasted%20image%2020260507233422.png)

*Figure 54. Closeup of prototype v1 mounted on test rig*

Following issues with prototype v2, I opted for a simplified design - no integrated pressure taps, no fancy "weight reduction" geometry. Additionally, a reduction in 3D printed parts would further reduce risk of leakage and aspiration (however unless there was a major structural failure, leakage through the plastic parts was minimal an limited to a few drops per minute). Essentially, this meant that the core would be machined. New (expensive) graphite-filled PTFE seals with LOx service in mind would be tested as well. 

![](../resources/assets/Pasted%20image%2020260507234521.png)

*Figure 55. Simplified pump casing*

![](../resources/assets/Pasted%20image%2020260507234552.png)

 *Figure 56. Lapped finish on simplified casing sealing face*

![](../resources/assets/Pasted%20image%2020260507234644.png)

*Figure 57. Un-ergonomic lathe setup for machining 5 degree taper to match impeller shroud profile*

![](../resources/assets/Pasted%20image%2020260507234731.png)

*Figure 58. Machined core with mounting flange holes and bearing/seal bores completed*

![](../resources/assets/Pasted%20image%2020260507234815.png)

*Figure 59. Dual purpose 3D printed jig for drain port drilling (also used for mounting flange holes)*

![](../resources/assets/Pasted%20image%2020260507234934.png)

*Figure 60. Partially assembled simplified pump*

![](../resources/assets/Pasted%20image%2020260507235013.png)

*Figure 61. Seals integrated into simplified pump core*

![](../resources/assets/Pasted%20image%2020260507235106.png)

*Figure 62. Simplified pump integrated with test rig*

The simplified pump setup included tee fittings at the inlet and outlet to accommodate a vacuum gauge and pressure transducer, respectively. The hard lines were also replaced with stainless steel braided hose for more positional flexibility and ease of use.

Between issues with the geartrain and excessive leakage past the graphite-PTFE shaft seals, the simplified pump did not produce usable data. Due to time constraints and life events, pump testing ended for 2025.

In early 2026, pump testing resumed using prototype v1. At this point, I was using my Test Bench software for valve and motor control and logging, versus the more limited Dynamometer software. 

I had also implemented a computer-vision based solution for automating flow rate reading - a camera is set up with the full scale of the flow meter in view, detects the last movement of the float, and reads the top surface of the float. With pixel-to-gal/min calibration, I would be able to continuously convert the camera's view of the float into flow rate. However, this approach was sensitive to any movement in the flowmeter - including air bubbles. This would highlight a more serious problem during the initial 2026 pump tests.

![](../resources/assets/Pasted%20image%2020260508190404.png)

*Figure 63. Flow rate reading automation with camera (not working in this screenshot)*

The prototype v1 pump began aspirating, which was evident by air bubbles circulating in the water rig. Prototype v1's service would then come to an end following a dead head test at full power - the PETG casing couldn't handle pressures peaking over 200 psi (Figure 64) and the case cracked along a print line (Figure 65). There was also leaking observed in other areas, more prominently from any portion of outer surface with little or no epoxy coverage. At this point, the pump was not able to generate pressure and not able to produce clean flow.

![](../resources/assets/Pasted%20image%2020260508212850.png)
*Figure 64. Crack in prototype v1 casing*


![](../resources/assets/Pasted%20image%2020260508213259.png)

*Figure 65. Dead head test graph showing erratic pressure readings*

## Results
Impellers 1 and 3 were tested - mainly a comparison of backward swept blades and purely radial blades. Impeller 1 was also tested with the PFTE shaft sleeve, which was also installed for the Impeller 3 test. 

Because the average shaft speed during these tests was around 6500 RPM, the extrapolated results at the target 24000 RPM should be taken with a large grain of salt. However, the extrapolated results (calculated using same methods as for Pathfinder pump) are promising - when corrected for density of ethanol, the projected head at the target 0.22 kg/s is 227 m for Impeller 1 and 266 m for Impeller 3 (Figure 66).

![](../resources/assets/Pasted%20image%2020260508214131.png)

*Figure 66. Comparison of production prototype test data by impeller*

However, the BEP is significantly shifted to higher flow rates, yielding 12% at the desired operating point. This result is not ideal, but the pumps were likely operating under a significantly different regime at the tested speeds, which were only ~25% of the target speed.

Because the target operating conditions were not properly tested due to the many issues encountered, these results will not be informing further design and development decisions. 

- The issues with the geartrain were somewhat remedied with stronger material and lubricant, and the losses at the tested speeds were manageable, usually around 4%. At target test speed, the geartrain losses are expected to increase quadratically.
- The material failure of the prototype casings is preventing reliable and steady pressure buildup. The next prototypes will need to be constructed from isotropically stronger material. 
- The dynamometer may not be capable of driving the production pumps to full speed - at 24000 RPM shaft speed, the dyno is expected to need 234 W just to overcome mechanical and geartrain losses, leaving about 890 W allowance at the impeller. For an estimated 593 W of hydraulic power, the pump would need to be 67% hydraulically efficient. Data from this test campaign shows hydraulic efficiency closer to 20% - however with the caveat that the pump was operating well outside of design parameters.

### Conclusions
The production pump build and test campaign yielded valuable insights into the effects of downscaling a centrifugal pump. 3D-printed PETG construction ultimately became a limiting factor, though the material exceeded expectations in many respects.

A few key items remain before the design can be considered reliable: a power input device capable of high speeds, a working dynamic seal solution, and metal construction. The immediate next step is moving to metal fabrication — either casting or outsourced metal additive manufacturing.

If this design proves infeasible to manufacture, alternatives such as a semi-open or Barske style impeller (simpler geometry, used in comparable small-scale projects) may be explored.

In the meantime, turbine R&D will continue and may become a viable high-speed power source for the next pump iterations.
# Turbine

Coming Soon!



