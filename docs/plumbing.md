
# Valves

To speed up development of the engine system, all valves would be comprised of commercial off the shelf (COTS) parts. This section will briefly cover design decisions, fabrication, and initial testing.

## Main Propellant Valves

The flow of the propellants would need to be controlled such that it could be stopped, started, and have limited adjustability. 

The materials of the valves would also need to be chemically compatible and withstand temperatures of the propellants - particularly for the cryogenic liquid oxygen.

The valves would also need to be remotely operated, so actuation by servo, pneumatics, or solenoids were considered.

Valve selection for liquid oxygen service would also include considerations for avoiding trapped volumes, which would eventually boil off and create regions of extreme pressure.


### Design

It was desirable to maximize flow coefficient (Cv) to minimize pressure drop and thus minimize pressurization requirements, however the tradeoff would be a larger valve with accommodations for larger, more expensive fittings. Therefore, it was desirable to select the smallest size valve that produces a reasonable pressure drop penalty. This strategy almost immediately eliminated solenoid valves, which for roughly the same physical size as a ball valve, yielded lower Cv values, were difficult to find with liquid oxygen compatible seals, and usually required higher voltages to operate. Additionally, solenoid valves require constant power to operate (depending on NO/NC configuration), imposing higher power requirements and potentially would dump unwanted heat into the cryogenic oxidizer during continuous operation.

Manual ball valves were found to be an acceptable approach: relatively low cost, compact packaging, decent flow coefficients, constructed from propellant-compatible materials, and would not require constant power input to hold a certain position. Ball valves also offered the advantage of quicker actuation times versus globe valves (also, fine flow adjustment is not necessary as the engine is designed for one operation point, and could be calibrated with more simple controls like orifices). Additionally, they can be gradually opened and closed versus options like solenoid valves. To automate valve actuation, COTS servos would offer a low cost, low power, potentially reliable solution. The COTS ball valves also offered NPT fittings up to 1", leaving flexibility for fitting choice (which would be a strong function of plumbing pipe size).

#### Safety
One possible downside to ball valve without some automatic return device would be if that valve were to fail the open position, uncontrollably releasing propellants into downstream components. This could happen due to a few factors: power loss to servo while valve is open, servo mechanically fails, servo-to-valve connection breaks, in the case of the oxidizer valve - thermal shrinkage of internal components prevents movement or ice buildup freezes the external valve actuation mechanism. The more safe failure mode would be for the valve to fail closed, unless the intention is to dump propellant into a safe area. However, it will be the job of the propellant tank fill, vent, and drain valves to handle this task. Failing closed could be accomplished by a) a spring return system (but this imposes burden on servo to continuously overcome the spring force) or b) a separate power supply (eg - capacitors) connected to servo via normally open relays which are powered by the servo power supply - such that in the event of servo power loss, separate power is sent to the servo to close the valve.

#### Flow Requirements
First, valves were selected. Tables 1 and 2 illustrate the pressure drop across the valve for various Cv values (from available COTS valves) at the target flow rates for the fuel and oxidizer, respectively. The pressure drop is calculated using the formula: 

$$  
\Delta P = SG \left( \frac{Q}{C_v} \right)^2  
$$
Where SG is specific gravity, Q is flow rate in gal/min, Cv is flow coefficient.

![](../resources/assets/Pasted%20image%2020260517230436.png)

*Figure 1. Compact threaded on/off valve from McMaster-Carr* for fuel service

| Cv  | Pressure Drop @ 4.11 gal/min (psi) | Fitting | McMaster PN |
| --- | ---------------------------------- | ------- | ----------- |
| 6   | 0.37                               | 1/8 NPT | 45395K211   |
| 8.2 | 0.19                               | 1/4 NPT | 45395K212   |
| 8.2 | 0.19                               | 3/8 NPT | 45395K213   |
| 15  | 0.06                               | 1/2 NPT | 45395K214   |

*Table 1. Pressure drop versus Cv for fuel valve options*

![](../resources/assets/Pasted%20image%2020260517231432.png)

*Figure 2. Candidate compact threaded on/off valve from McMaster-Carr for oxidizer service*

| Cv   | Pressure Drop @ 3.77 gal/min (psi) | Fitting | McMaster PN |
| ---- | ---------------------------------- | ------- | ----------- |
| 1.3  | 9.62                               | 1/4 NPT | 8393N117    |
| 2.6  | 2.40                               | 3/8 NPT | 8393N118    |
| 9.2  | 0.19                               | 1/2 NPT | 8393N119    |
| 12.6 | 0.10                               | 3/4 NPT | 8393N121    |

*Table 2. Pressure drop versus Cv for oxidizer valve options*

From Table 1, the 1/4 NPT valve was selected as it offered negligible pressure drop. Between the 1/4 and 3/8 NPT version, the 3/8 NPT would not offer significant benefits - the cost of fittings is higher, the actual bore of the (Yor-lok, explained in subsequent sections) fittings is an identical 0.28", and installation torque would higher.

From Table 2, the 3/8 NPT valve was tentatively selected - while it offers modest pressure drop and liquid oxygen-compatible materials, it was unclear if a) the valve will operate at cryogenic temperatures and b) if it can be sufficiently cleaned for liquid oxygen service. If a more thorough assessment of the valve selection reveals too high risk, then a different valve will be selected. The reasoning behind this valve choice was servo integration design similar to the fuel valve, and low cost. 

In the case of the valve shown in Figure 2, a vent port would need to be drilled into the upstream side of the ball. A valve will be purchased for experimentation with such modifications and exploration of cleaning processes.

The servo actuated ball valves would be used for both isolation and main functions.

#### Servo Selection
The compact valve turning torque was not measured (yet), so some internet research suggested a worst case turning torque of ~ 5 Nm with typical range from 0.5 - 2.5 Nm. Therefore, a 25 kg servo (Miuzei MS62) was selected to cover most of the typical range, topping out at 2.45 Nm at 8.4 V. If this would prove insufficient, there are options up to 100 kg (9.8 Nm) available on Amazon. The final design for the fuel valve and preliminary design for oxidizer valves are shown in Figures 3 and 4, respectively. 

![](../resources/assets/Pasted%20image%2020260518225847.png)

*Figure 3. Fuel valve design*


![](../resources/assets/Pasted%20image%2020260518225958.png)

*Figure 4. Preliminary oxidizer valve design*

The servo controls and calibration are discussed in the Test Bench section.
#### Valve Handle Simulation

The servo to valve stem connection, which effectively replaces the stock valve handle, was analyzed using static stress simulation in Fusion's Simulation environment. The actual part is 3D printed using PETG with 100% infill, so the solid PETG material used in the simulation likely overestimates the safety factor. The force exerted by the servo horn at the servo's maximum rated torque (2.45 Nm) is applied while the slot for the servo stem is held fixed. The resulting safety factor (Figure 5) is 2.451, which should provide some overhead for actual material properties. 

![](../resources/assets/Pasted%20image%2020260521210933.png)

*Figure 5. Safety factor from static stress simulation*

![](../resources/assets/Pasted%20image%2020260521211054.png)

*Figure 6. Von Mises stress from static stress simulation*

Stress results shown in Figure 6 shows some concentration around the slot that interfaces with the valve stem. This may be an area for the addition of a fillet in future iterations.

### Fabrication

The fuel valve was the first to be fabricated, and went through a few iterations mainly to reduce part count and size. A 3D printed PETG cradle confirms to the hexagonal profile of the valve with a lip at one end to partially constrain movement along the flow axis. The servo is mounted on a bracket, and a printed connector transfers servo horn movement to the valve handle. In later iterations, the connector is directly connected to the valve shaft.

These are presented in Figures 7 through 10.

![](../resources/assets/Pasted%20image%2020260518230518.png)

*Figure 7. First valve iteration - two piece construction*

![](../resources/assets/Pasted%20image%2020260518230548.png)

*Figure 8. Second valve iteration - two piece construction w/ direct to shaft connection*

![](../resources/assets/Pasted%20image%2020260518231852.png)

*Figure 9. Third valve iteration (small version)*

![](../resources/assets/Pasted%20image%2020260518232053.png)

*Figure 10. Third iteration valve mounted on water test rig*

## Gas Generator Valves

The gas generator valves are similar to the main valves in construction, but smaller since the flow rates are only about 4% of the main propellant flow rates. Figure 9 shows the fuel valve for the gas generator. 

The servo actuated ball valve design was chosen to aid in tuning O/F ratios during testing and development. There is a remote chance that gas generator flow rate and O/F may be adjusted on the fly during engine operation, so it was desirable to have the same level of flexibility as the main valves.

## Igniter Valves

Solenoid valves will be used for the torch igniter. This is to enable repeatable start up/shutdown and quick response time. These are discussed in more detail in the [[igniter]] section.

## Tank Pressure Management Valves

More information is coming soon and will be discussed in more detail in the [[tanks]] section. In short, each tank will include a normally open (NO) solenoid valve for purging ullage pressure, ullage pressure pre-set relief valve, normally closed (NC) solenoid valve for ullage pressurizing gas influx, burst disk in case of emergency over pressurization, and the servo actuated ball valve for main propellant isolation described above. For initial tests, the cryo oxidizer tank may also include a drilled hole to ensure system eventually passively depressurizes.

# Lines

## Main Propellant

Choosing main propellant lines was a trade study considering cost and pressure drop per unit length. Whether 0.035" wall thickness stainless steel or stainless steel braided hose, the primary design parameter is pressure drop. Since this is not a flight engine (yet), weight was not considered, but the wall thickness of hard lines was chosen to be 0.035" - supporting pressures above 2000 psi and withstanding the tube bending process without collapsing. 

At the time of writing this documentation, the plan is to deploy hard lines from the turbopump assembly to the thrust chamber assembly, as the turbopump (TP) and thrust chamber (TC) will be rigidly integrated. Lines from the tanks/isolation valves will likely be braided hose to offer flexibility for mounting on a test stand. However, to alleviate some stress and fatigue due to thermal deformation and vibration, respectively, some short runs of flexible braided hose may be run between the TP and TC.

Since relatively short runs of lines are estimated, a tube diameter of 0.5" was selected, as it's expected to yield pressure drops of less than 1 psi over a meter of line. The next major step down, 3/8", would yield over 4 psi of pressure drop over a meter. Table 3 lists a few considered 304 stainless steel tube sizes, pressure drops, cost, and weight per meter. Pressure drop is estimated using the Darcy-Weisbach equation. Excel sheet calculations are shown in Figure 11.

| Tube OD | Fuel Pressure Drop (psi) per meter | Oxidizer Pressure Drop (psi) per meter | Weight (kg) per meter | Cost (USD) per meter |
| ------- | ---------------------------------- | -------------------------------------- | --------------------- | -------------------- |
| 1/4"    | 54.75                              | 65.28                                  | 0.041                 | $19.20               |
| 3/8"    | 4.25                               | 4.50                                   | 0.065                 | $23.80               |
| 1/2"    | 0.82                               | 0.81                                   | 0.089                 | $29.60               |
| 1"      | 0.02                               | 0.02                                   | 0.185                 | $49.85               |

*Table 3. Performance parameters for various 0.035" wall 304 SS tube sizes*

As seen in Table 3, the 1/2" OD tube is a middle-of-the-road choice for price per meter and a high performer for pressure drop. The 24% cost increase over the 3/8" tube justifies the 80% reduction in pressure drop. The 1" tube offers a 98% reduction in pressure drop over the 1/2" tube, but would make little difference to an already essentially negligible pressure drop, all for 68% increase in cost per meter. For a flight engine, this also represents an over twofold increase in weight per meter. Therefore, there is little reason to increase to 1" diameter tube. If the 1/2" tube pressure drop poses risk to cavitation margins in the pump inlets, then it is likely more trivial to increase tank ullage pressure by a small amount to make up the difference.

![](../resources/assets/Pasted%20image%2020260521232601.png)

*Figure 11. Propellant line pressure drop estimations*

## Sensor Capillaries

This topic has not been thoroughly studied during this project, but 1/8" OD (0.055" ID) stainless steel capillary tubes were deployed for torch igniter chamber and production pump pressure measurements. Depending on pressure measurement requirements (high frequency damping / response time) and working fluid temperature, smaller 1/16" capillaries may be deployed. 
# Fittings

The selection of common fittings for this project was a balance of pressure rating, availability, cost, required tooling, material compatibility with propellants, interface with lines, and serviceability.

Cost was a significant factor due to sheer number of fittings required. Ease of use was also considered, as properly installing all the fittings would impose time and effort burden.

## Static Fittings

Two major classes of fitting were considered: O-ring Boss (ORB) and National Pipe Tapered (NPT).

NPT fittings were chosen for their wide availability, low cost, relative ease of installation, relative ease of machining, and good sealing capability (when properly installed). The PTFE tape used during installation is also chemically compatible with propellants, should they come in contact.

ORB fittings are known to provide superior sealing, especially under higher pressure. However, the offerings on McMaster-Carr have similar pressure ratings to the NPT options, but for almost twice the cost and inclusion of rubber gasket, which is less compatible with the cryogenic oxidizer.

## Line Fittings

Two major classes of fittings were considered: Compression (Swagelok / Yor-Lok) and 37-degree Flared (AN / JIC).

While flared fittings would offer good resistance to vibration and high reusability, they require special tooling that when not properly used, can cause leaks due to improper flaring. They also tend to be more expensive. For this project, and my inexperience going into it with plumbing systems, the investment in tooling and non-confidence in properly flaring the tubes eliminated flared fittings from consideration.

Therefore, compression fittings, which also offer great leak resistance (when properly installed) were selected. McMaster-Carr's Yor-Lok fitting, while not exactly cheap, provide a lower barrier to entry in terms of availability, cost, and performance. These were also expected to perform well with the relatively small main lines and sensor capillaries. In practice during igniter and pump system assembly, they were easy to install repeatably without leakage. 

