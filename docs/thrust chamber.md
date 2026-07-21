# Thrust Chamber Assembly

This section outlines the performance requirements, design, and manufacturing methods of the thrust chamber assembly (TCA). The engine design was driven with additive manufacturing in mind, specifically the FDM + Sintering type process deployed by Markforged with their Metal X or FX10 system, or the Desktop Metal Studio system. This seems to be a novel approach to LRE hardware manufacturing - however the only close example I could find is the Cal Poly Pomona Liquid Rocket Lab's ZEUS engine, featuring Metal X printed parts: [Development of a Small Turbopump-Fed Bipropellant Rocket Engine | AIAA SciTech Forum](https://arc.aiaa.org/doi/10.2514/6.2025-0763)

The original goal was a 500 lbf engine, as that thrust class better justified both turbopump-fed propellant delivery and regenerative cooling. However, manufacturing constraints ultimately drove the design toward a 250 lbf engine.

However, to satisfy constraints my target manufacturing process imposed, the footprint of the engine had to be reduced and a 250 lbf target was chosen.

## Requirements

There are dozens of small liquid rocket engine projects out there at varying levels of performance and complexity. Usually, one starts out with simple architecture ([see ROCKETLAB China Lake liquid rocket engine text](https://risacher.org/rocket/)) and geometry that's relatively easy to fabricate. But the goal for this project wasn't to just build a liquid rocket engine, it was to explore a few key areas: 

1. FDM Metal + Sintering workflow for the metal components
	- Can mockup parts and workflows in plastic, using same FDM process
	- Manufacturing localized within US (no ITAR concerns or long lead times)
	- Reduced cost versus specialized tooling or large machines for traditionally manufactured parts, or significantly reduced material and processing cost compared to other metal AM workflows like powder bed fusion.
	- Relatively unexplored approach for rocket engine components
	- Metal parts that could be manufactured with a typical consumer grade printer
	
2. Turbopump Cycle & regenerative  Cooling
	- Poses a technical challenge
	- Turbopump eliminates need for expensive high-pressure vessels and related plumbing and control equipment
	- regenerative cooling allows longer burn times (versus heatsink or ablative engines) for thorough testing
	
3. Manufacturability with home shop tools
	 - Without investing in tooling worth more than the final engine, use tooling that is source-able with extra paycheck money and fits into modest home garage space
	 - Keep part and consumable costs low, however without making significant design compromises by whatever happens to be available
	 - Uses common imperial or metric threads (where applicable), avoiding specialty tools with no more than 1 use case.
	
4. Non-Toxic and Easy to Source Propellants
	- LOx was chosen because to avoid the hazards associated with nitrous oxide, up to explosive decomposition.
	- Ethanol is easy to source, affordable, and provides good cooling characteristics, and is non toxic when ingested in moderation.

As previously mentioned, the chosen thrust level was ultimately a function of what engine size would fit on the print bed (after ~130% vertical scaling) of a Prusa MK3S+, and what regenerative cooling system and pump size that would tolerate very low flow rates. 

Additionally, I selected a chamber pressure of 300 psia because it represented a reasonable target for a first liquid rocket engine, yielded reasonable pump specific speeds, and with a comfortable factor of safety, allowed a 1.5 - 2 mm chamber liner thickness. The effectiveness of the regenerative cooling system is a strong function of the liner thickness, so any increase in thickness greatly penalized ability to reduce wall temperature. 

The oxidizer-to-fuel ratio (OFR) was chosen from a study of Ethanol - LOx combustion data from NASA CEA (I created Excel lookup tables from a range of outputs with OFR and chamber pressure as inputs). A ratio of 1.5 was found to yield reasonable combustion temperature, closely matched specific speeds between the fuel and oxidizer pumps, and sufficient fuel flow rates for regenerative cooling. Metrics like thrust coefficient and characteristic velocity are shown in Table 1, but are not design drivers.

| Parameter | Value         | Unit        |
| --------- | ------------- | ----------- |
| Thrust    | 1112 \| 250   | N \| lbf    |
| ISP       | 251           | s           |
| Pc        | 20.7 \| 300   | bar \| psia |
| OFR       | 1.5           | -           |
| c*        | 1709          | m/s         |
| Cf        | 1.44          | -           |
| Fuel      | Ethanol (95%) |             |
| Oxidizer  | Liquid Oxygen |             |
| Cooling   | regenerative   |             |
| Cycle     | Gas Generator |             |

*Table 1. Engine specifications*

## Design

### Thrust Chamber

As previously mentioned, the TCA  geometry was designed with additive manufacturing (AM) in mind, therefore curved nozzle contours, regenerative  cooling channels, and higher performing (from from a thermal standpoint) material could be deployed.

Since the maximum build height of my Prusa MK3S+ is 250 mm, and vertical green part scaling between 126% and 135% was found to be necessary for sufficient final part dimensional accuracy, the engine height limit is somewhere between 180 and 198 mm.  

For this reason, the chamber characteristic length "L*" was set to 0.89 m (35 in), just below textbook values. Combined with a "Rao" type 80% bell nozzle (to help optimize expansion while reducing length), this yielded a design that would fit on the print bed without significant performance compromises.

The nozzle expansion ratio is 4.15, which corresponds to an exit pressure just under 1 atmosphere, yielding an opportunity to produce mach diamonds in the exhaust.

The chamber material was chosen to be 17-4 PH stainless steel, one of the two filament options offered by BASF (the other being 316L stainless steel). There are other manufacturers of COTS metal filament like Virtual Foundry, but the post-processing support for the BASF Ultrafuse filament seemed stronger and more streamlined. 

When comparing 17-4 PH and 316L during preliminary mechanical stress calculations for the chamber, the 17-4 PH was the clear winner for its significantly higher yield strength (especially at elevated temperature), higher modulus of elasticity, and higher thermal conductivity. However, it was unclear if these properties would be disadvantageous during the debind and sintering process.

| Property               | Unit   | 17-4 PH | 316L  |
| ---------------------- | ------ | ------- | ----- |
| Density                | kg/m3  | 7800    | 7990  |
| Yield Strength (20 C)  | MPa    | 885     | 240   |
| Yield Strength (538 C) | MPa    | 641     | 143   |
| Modulus of Elasticity  | GPa    | 195     | 179   |
| Poisson's Ratio        | -      | 0.272   | 0.280 |
| Thermal Conductivity   | W/mK   | 17.9    | 16.3  |
| CTE                    | um/m-C | 11.25   | 16.6  |
| Melting Temperature    | C      | 1405    | 1385  |

*Table 2. Material properties*

Material properties were sourced from MatWeb and filament manufacturer data.

TCA  design (as of 06-29-2026) images are shown in Figures 1 & 2. 
![](../resources/assets/Pasted%20image%2020260629113147.png)

*Figure 1. Current TCA  assembly cross section*

Keen-eyed observers may note that only one coolant channel and no injector orifices are visible. This is due to this particular cut plane and the odd number of coolant channels and injector elements.

![](../resources/assets/Pasted%20image%2020260629113438.png)

*Figure 2. Current TCA assembly*

The final design is a single piece as it was projected to fit in one print bed, simplified flange, and smoothed out external features to reduce risk of cracking during sintering process.
#### 3D Design Iterations
The TCA  assembly has gone through dozens of iterations with changes in dimensions, injector integration, flange designs, split designs, and optimizations for additive manufacturing. A selection of these iterations are shown in Figures 3 - 8.

![](../resources/assets/Pasted%20image%2020260629114236.png)

*Figure 3. 2kN design with high contraction ratio and coolant channel exit manifold*

The design in Figure 3 was the original 2 kN (500 lbf) configuration, and the contraction ratio of around 12 allowed for a shorter chamber section while still achieving the target characteristic length (L*). This design also featured an exit manifold for the cooling channels. 

![](../resources/assets/Pasted%20image%2020260629114345.png)

*Figure 4. 2kN design* with integrated channel exit for machined injector

The design in Figure 4 was also a 2 kN iteration with lower contraction ratio and coolant channel exits designed for integration with a machined injector (Figure 15). This design would not fit in the Prusa MK3S+ print volume after vertical scaling.

![](../resources/assets/Pasted%20image%2020260629114505.png)

*Figure 5. 1kN split chamber design to improve manufacturability*

The engine's thrust was scaled down to 1 kN for a variety of reasons including smaller footprint for manufacturing. The chamber and nozzle were split and a bolted flange added as this would ensure fitment in the print volume. Additionally, it would lower the risk of print failure scrapping the entire TCA during printing, and lower stresses during the sintering process by breaking the supported mass up.

![](../resources/assets/Pasted%20image%2020260629114614.png)

*Figure 6. 1kN split chamber design with 12 x 4 mm flange bolt*

The design in Figure 6 was virtually identical to the one in Figure 5, but for integration with an additively manufactured injector (Figure 17).

![](../resources/assets/Pasted%20image%2020260629114737.png)

*Figure 7. 1kN split chamber with 8 x 6 mm flange bolt*

The design in Figure 7 is the same as the design in Figure 6, but with a reduction in flange fastener count and change from M4 to M6 screws. The reasoning was better factor of safety with the M6 fasteners and reduced part count.


![](../resources/assets/Pasted%20image%2020260629115023.png)

*Figure 8. Single piece 1kN chamber with smoothed exterior features*

After manufacturing trials (discussed later) it was found that even with scaling, the TCA would fit in the print volume. The support ribs below the nozzle were removed, as they introduced stress concentrations during the sintering process and following a small scale trial, didn't seem to be necessary after all. However, one-piece manufacturing would introduce more risk of print failure and increase loads on the lower portion of the TCA during sintering. For this reason, a smaller, scaled down 100 lbf engine was fabricated for manufacturing trials (discussed later).

#### Heat Transfer
Preliminary 1D thermal calculations using Bartz correlation for gas side convection and equations from H&H text provided rough wall thickness and cooling channel geometry. The 1D calculations were performed at the throat region, where maximum heat flux is expected. My calculations yielded a heat flux of between 5 - 6 MW/m2, which matched textbook expectations. This also factored in an assumed adiabatic wall temperature of 1050 K, where the melting temperature of 17-4 PH is reported to be 1678 K. Rocket Propulsion Analysis (RPA) software allowed iteration of a few different combinations of wall thickness and channel height and width. Results are shown in Figure 9.

![](../resources/assets/Pasted%20image%2020260614192418.png)

*Figure 9. Table of possible cooling channel dimensions*

If I recall correctly, the max temperature estimates (Figure 1) also took into consideration 5% film cooling (I have since lost the exact configuration I used for that simulation).

#### Cooling Channel Manifold CFD Study
When iterating on cooling channel and manifolding design, I considered two major design paths: 1) coolant channels would discharge into a common manifold before exiting the jacket and travel along a short run of plumbing leading into a singular inlet in the injector, and 2) cooling channels would discharge directly into the injector - which slightly complicates the injector-chamber interface.

To compare the different manifolding concepts, I performed CFD simulations in SimScale to examine coolant velocity distribution between channels. This study was conducted before the RPA simulations helped optimize cooling channel dimensions and count, so overall results didn't meet theoretical targets, but it did help for comparative analysis. 

Three designs were considered:
1. Tangential - Coolant enters tangentially to the inlet manifold (near nozzle exit), conceptually to more evenly distribute velocity. Coolant exits tangentially from the outlet manifold.
2. Normal - Coolant enters essentially normal to the inlet manifold, which is a more geometrically simple design. Coolant exits normally from the outlet manifold.
3. Integrated - Coolant enters normal to the inlet manifold, but exits directly into the injector around its circumference.

![](../resources/assets/Pasted%20image%2020260629110448.png)

*Figure 10. Tangential design channel velocity distribution*

![](../resources/assets/Pasted%20image%2020260629110541.png)

*Figure 11. Normal design channel velocity distribution*

![](../resources/assets/Pasted%20image%2020260629110615.png)

*Figure 12. Integrated design channel velocity distribution*

![](../resources/assets/Pasted%20image%2020260629110657.png)

*Figure 13. Channel velocity by channel number, by design*

![](../resources/assets/Pasted%20image%2020260629110752.png)

*Figure 14. Overall design performance statistics*

As seen visually in Figure 10 and by the lowest measured variation in Figure 14, the integrated design yields the most consistent channel velocities, which was the intended behavior of the design. The reasoning being to avoid hotspots and excessive thermal gradients around the circumference of the chamber. The summed Root Mean Squared Error (RMSE) between the simulated and target velocity of each channel was relatively consistent across each design. Not surprisingly, the RMSE was lowest when simulating with ethanol, as the cooling system velocity target was designed with ethanol in mind. 

In summary, this comparative analysis drove the decision to proceed with the integrated design.
### Injector
Not much consideration was given to injection other than impinging designs, as these are proven and simple in design when compared with designs like pintles or coaxial swirl. To simplify manifolding (and thus manufacturing), an unlike-doublet design was chosen - also because the flow rate for this engine is relatively low and already requires very small orifices. Higher orifice count for designs like triplets or pentads would penalize manufacturing and possible pressure drop by requiring orifices smaller than 0.02" in diameter. 

The final design is oxidizer centered to help with wall compatibility and make fuel manifolding easier. It also allows fuel to be easily dispensed along chamber wall for film cooling.

The biggest design decision was between a traditionally machined 6061 Al and additively manufactured 17-4 PH design. 

The machined design had the benefit of better dimensional control, particularly for the orifices. Also, drilling into aluminum was expected to be significantly easier than through stainless steel. However, this introduced some complication in fuel manifolding, introduced need for o-ring seals, and was somewhat dimensionally complicated. 

Ultimately, the additive design was selected for its relatively low-effort manufacturing despite the risk of loss of dimensional control and inferior surface roughness. Also, sensor bungs and torch igniter mount were significantly easier to integrate. The orifices could be cleaned up in the part "green state" and again chased in the final stage without having to hog through significant material with the small and delicate drill bits. The success of this design hinged on being able to effectively scale and post process the parts. This later proved to be a feasible approach. 

![](../resources/assets/Pasted%20image%2020260629120629.png)

*Figure 15. Machined injector*


![](../resources/assets/Pasted%20image%2020260629120656.png)

*Figure 16. Machined injector cross section*

![](../resources/assets/Pasted%20image%2020260629120733.png)

*Figure 17. Additive injector*

![](../resources/assets/Pasted%20image%2020260629120847.png)

*Figure 18. Additive injector cross section*

## Manufacturing

The plan for manufacturing from the start was to leverage processes similar to those of Desktop Metal Studio or MarkForged Metal X system. This process of FDM Print -> Debind -> Sinter promised to lower material costs (filament somewhere around $200 - $400 per kg) and generally reduce hazards and waste that comes with handling powdered metal for powder bed fusion or metal binder jet technologies. 

As of 2024-2025, BASF sold Ultrafuse 17-4PH and 316L filament which could be purchased from select vendors, along with $50 / kg processing tickets for debind/sintering service partners. 

***Unfortunately, sometime in Q1 of 2026 BASF discontinued the filament and ended secondary support for processing***. 

![](../resources/assets/Pasted%20image%2020260702134220.png)

*Figure 19. Spool of BASF Ultrafuse 17-4 PH*
### Process

#### Design
I followed the BASF design guidelines in Figure 20, taking special care in avoiding overhangs that would necessitate use of support material. This is because the support interface layer material was not available in the US, making the removal of any supports very difficult (and impossible in enclosed volumes like coolant manifolds) ![](../resources/assets/Ultrafuse_metal_User_Guideline.pdf)
*Figure 20. BASF Ultrafuse material guide*

I was also careful to keep wall thicknesses above 2 mm (this drove much of the regenerative cooling system design) and relatively consistent. In some early tests with the filament in 2024, I found that areas where drastically different wall thicknesses met were subject to significant warpage and cracking. Using less than 100% infill would help mitigate this, but I only tested with minimum 80% infill and didn't produce a ton of samples to know if the less dense infill or design itself aided in avoiding sintering issues.
#### Simulation

To get an idea of where stress concentrations may form in the design, and anticipate warpage and areas of failure, some rough FEA simulations can be performed. Approximating material properties in its "brown state" (post debinding, pre sintering), the model is subjected to gravitational force load. BASF supplied material properties (for 316L) and stress limits in the document in Figure 21. 

![](../resources/assets/Debinding-and-Simulation-Guidelines.pdf)

*Figure 21. BASF simulation guidelines*

I didn't see strong correlation between the simulation and actual results (in terms of part success/failure) - the simulation setup (namely, constraints), actual versus simulated material properties, and any other the many other variables in the process could have contributed to the deviation. The simulations did however expose areas of stress concentration - paying some attention to the stress values but mostly looking at relative numbers - I was able to mitigate some risk by adding filleted transitions or removing sharp features altogether. 


![](../resources/assets/Pasted%20image%2020260702170300.png)

*Figure 22. Fusion FEA YY direction stress for 1kN chamber in brown state*

![](../resources/assets/Pasted%20image%2020260702170147.png)

*Figure 23. Fusion FEA Z direction stress for 1kN chamber in brown state*

I did find a strong correlation between the part success and the ratio of contact area (CA) to volume (V). Thus, parts with CA to volume ratios less than 11% tended to fail and be unusable - either by severe "barrel" warpage, cracking, or liftoff from the contacting surface. These would include parts like thin-walled cylinders (for example, a thrust chamber). Parts relatively immune to failure were the injectors for the TCA and gas generator.

A list of parts, CA:V ratios, and the result is shown in Figure 24.

![](../resources/assets/Pasted%20image%2020260702164320.png)

*Figure 24. Results of selected test prints*

#### Slicing

I followed the recommended slicing settings outlined in Figure 21, and used Prusa's built-in "Ultrafuse Metal" profile in PrusaSlicer for nozzle and bed temperatures. I didn't tweak any of the default settings, other than using a layer height of 0.1 mm and infill of 100% (80% in the case of a couple non-TCA test prints).

To avoid a potential issue with metal prints, It's also advisable to set seams to a random setting (PrusaSlicer seam position = "random") as sometimes during printing of plastic mockups, seams on inner perimeters lined up with coolant channels and created voids. These would later leak when flow testing with water.

Determining optimal part scaling proved to be a difficult endeavor. The goal was to get as close as possible to the target dimensions, and overshoot slightly (by up to 0.01" - 0.02") to allow room for finish machining. After all, it would be easier to subtract material than to add it.

The recommended settings for 17-4PH are 120% in XY and 124% in Z, but these immediately proved to be non-optimal following by first batch of test prints.

Measuring the final versus design dimensions (taking care to not include dimensions that may have been altered during green part machining / sanding) yielded about a -1.6% in XY and -2.2% error in Z. Because of the consistency of error across different parts, new scaling factors were calculated to be 121.5% in XY and 126.81% in Z.

Following more test prints which were physically larger in height and mass, the errors from nominal were -0.8% in XY and 6.2% in Z. XY scaling only then needed a small tweak to 122.46% and Z a more significant bump to 135.18%. 

***As of May 2026, parts with these new scaling factors were printed and prepared for debind & sintering, but because the processing ticket support ended, the price increased to the provider's normal rate. As of July 2026, these parts did not yet and are unlikely to be sent for processing.***

In summary, for this application, I would recommend starting with the higher scaling values of 122.46% and 135.18% for the 17-4PH filament. 

#### Printing
The bed plate should be smooth - in my case I used a smooth PEI sheet from Prusa. **Eventually, the sheet did get destroyed between the PEI layer separating and gouges from print removal**. Before heating the bed, I applied Magigoo Pro Metal adhesive liberally to the print area. About 3 - 4 thick passes, then a light pass to even the coating out. The bed was left to dry as it heated up before printing.

Print times were usually significant, on the order of several hours. The entire TCA  assembly took somewhere around 40-50 hours and almost 3 kg of filament. Because I had purchased 1 kg spools, I make sure to be around when a spool ran out to immediately replace it. I wanted to avoid the printer timing out following filament runout and cooling down mid-print as I was not sure what this would affect the green part. I opted for 1 kg spools since I was not sure if the extruder pulling on a 3 kg spool would cause the surprisingly brittle and delicate filament to break mid-print. This later proved to not be a problem when printing the 100 lbf chamber in one shot.

"Crumbs" of filament that would bunch up on the nozzle would often fall and gather on the built plate and sometimes inside the prints. For this reason, I make sure there was at least one ~0.5" diameter hole to access large enclosed volumes - in this case the coolant channel inlet manifold at the nozzle and the fuel manifold in the injector. These holes also doubled as the coolant inlet port and fuel pressure sensor bung, respectively. This proved to be an effective strategy, as small pieces which inevitably collected in the volumes were easy to shake out. Larger pieces could be broken up with a flathead screwdriver and then shaken out.

Images of the printing process are shown in Figures 25 - 29

![](../resources/assets/Pasted%20image%2020260702215928.png)

*Figure 25. Combustion chamber printing*

![](../resources/assets/Pasted%20image%2020260702220010.png)

*Figure 26. Nozzle printing*

![](../resources/assets/Pasted%20image%2020260702220032.png)

*Figure 27. Finished nozzle print*

![](../resources/assets/Pasted%20image%2020260702220109.png)

*Figure 28. Finished and stacked TCA green parts*

![](../resources/assets/Pasted%20image%2020260702220144.png)

*Figure 29. Closeup of injector interface with chamber with noticeable warpage*

As seen in Figure 29, green part warpage was usually inevitable. Internal stresses in the material were relieved after breaking the strong bond with the build plate. This led to a rocker-like bowing that became more pronounced at the periphery of parts. The effect was more extreme for scalloped flanges, which is what drove the decision to return to non-scalloped flanges for the final design. The weight and material savings was not worth the manufacturing defects.

In the next section, the mitigation for the warpage issue is discussed.

#### Green Part Machining
Completing as much finish machining as possible in the green state is highly recommended, as on a mini-lathe, the tool loading feels similar to cutting through soft plastics or wood.

On a mini-lathe, making heavy passes through the part in its "white" (post sintered state) is significantly more difficult. The surface finish post sintering is not sealing-surface grade (see Figure 36), so some material should be added to the part to allow for ~0.01" of finishing passes.

So, on surfaces that will require some cleanup, add 0.01" x scaling factor. And on the bottom surface contacting the bed plate, add around 1 - 2 mm of extra material plus the 0.01". Warpage on the bed-plate-contacting surface can be significant, so adding at least 1 mm is critical for dimensionally accurate white state parts.

When adding work-holding features on the parts wasn't practical like in the case of the TCA , bespoke PLA jigs were developed. In the cast of the TCA  (Figure 30) it allowed just enough rigidity for the lathe tool to face the injector flange. The images shown below are from the 100 lbf TCA trial, as I didn't capture photos from the 1 kN engine process.

![](../resources/assets/Pasted%20image%2020260703123145.png)

*Figure 30. 100 lbf TCA flange machining in green state*

![](../resources/assets/Pasted%20image%2020260703123258.png)

*Figure 31. Machining of 100 lbf injector face in green state*

Seen in Figure 31, the lathe chuck had enough material on the central LOx post to grip for machining.

To drill the injector holes, a sized up drill bit (remember the green parts are scaled) is used in conjunction with a 3D printed PLA jig to hold the injector face at the precise angles. The jig includes indentations around the perimeter for indexing each of the holes. Machining debris is evacuated out of the fuel pressure port hole.

![](../resources/assets/Pasted%20image%2020260703123724.png)

*Figure 32. Drilling injector holes in green state*

After machining, the parts are sanded with progressively fine grit, usually from 120 up to 1000. This is mostly cosmetic, but breaks off burrs, over-extrusions, and ensures the bottom surface contacting the sintering plate is as flat as possible.

![](../resources/assets/Pasted%20image%2020260703123950.png)

*Figure 33. 100 lbf injector post-sanding*

![](../resources/assets/Pasted%20image%2020260703124023.png)

*Figure 34. 100 lbf TCA  post-sanding*

![](../resources/assets/Pasted%20image%2020260703124108.png)

*Figure 35. 100 lbf TCA stackup*

#### Debind + Sintering
Matterhackers.com (seller of my BASF filament) provided processing tickets for DSH Technologies in NJ, USA with the purchase of filament. More tickets could be purchased for $50 / kg of processed material, but unfortunately this program ended as of 2026. 

The process was simple: package the parts, measure their weight and dimensions and include them on a form to be shipped with the parts, then ship to DSH with a preferred vendor. 

In 4 - 8 weeks (including round trip time), the finished parts would arrive on my doorstep.

DSH still provides the service, but at rates for normal customers. They can be contacted for quote and are very professional and timely with responses. In the case of my project, the D&S cost exceeded the budget, so I will have to find a solution to this speedbump in my process.

The D&S results were surprisingly good and surprisingly bad in some cases. Larger parts tended to struggle as seen in Figures 37 - 41.

![](../resources/assets/Pasted%20image%2020260703125723.png)
*Figure 36. Example of post-D&S surface finish on 1 kN injector*

![](../resources/assets/Pasted%20image%2020260703125420.png)

*Figure 37. Failed 1 kN nozzle*

![](../resources/assets/Pasted%20image%2020260703125443.png)

*Figure 38. Interior of failed 1 kN nozzle*

![](../resources/assets/Pasted%20image%2020260703125515.png)

*Figure 39. 1 kN combustion chamber*

![](../resources/assets/Pasted%20image%2020260703125605.png)

*Figure 40. Closeup of crack in combustion chamber coolant channel*

![](../resources/assets/Pasted%20image%2020260703125828.png)

*Figure 41. Barrel warpage on bottom surface of 1 kN combustion chamber*

It's clear from Figures 37 - 41 that the parts were unusable. However this trial did prove that there was promise to this manufacturing method, particularly for the smaller parts with large contact area like the injector.

Since the 1 kN manufacturing trial, wall thicknesses were increased from 1.5 mm to 2 mm. 

A trial involving a scaled down nozzle (around ~60%) showed even more promise - this time with minimal cracking along coolant channels and the inlet manifold remained intact.

![](../resources/assets/Pasted%20image%2020260703130207.png)

*Figure 42. Scaled nozzle green part*


![](../resources/assets/Pasted%20image%2020260703130304.png)

*Figure 43. Scaled nozzle white part*

#### Finish Machining
My Grizzly mini-lathe is not well-equipped to machine work-hardening materials like stainless steel. The lack of rigidity makes starting cuts difficult, as the piece usually pushes the cutting tip away. However, once machining, there is also a large discrepancy between what the indicators on the control knobs read and how much material is being removed from the part.

This could also be a matter of incorrect feeds and speeds, which I kept very low (~800 RPM) while manually applying a stream of coolant.

I didn't have much success machining the parts, and as discussed in the gas generator section, resorted to sanding.

One attempt to machine the injector face is shown in Figure 44. Because the cutting tool "hopped" across the injector face, the surface is somewhat wavy. Aggressive sanding and polishing didn't improve flatness but did offer a preview of what surface finish was possible.

![](../resources/assets/Pasted%20image%2020260703131337.png)
*Figure 44. 1 kN injector face after machining attempt and sanding*

In the future, I would either invest in a large, more rigid lathe or outsource the finish machining operations. 


## Conclusions
Overall, I was pleased with the process I established for producing (almost) usable rocket engine hardware. The cessation of production of the filament and heavily discounted secondary support was a devastating blow to the progress (and future) of this project. Luckily, there are alternatives but finding services that use this FDM + D&S process has been difficult. As of now, the cost advantage of this DIY method when compared to commercial printing services (in the US) has evaporated. 

The path forward may look like any of:
- Using other filament providers (Virtual Foundry is an option) and eating the cost of D&S, or investing in my own D&S equipment.
- Switching to conventionally manufactured design, which will requires significant re-design and likely heavy investment in better home shop tools or outsourcing.
- Outsourcing to commercial printing providers and keeping the current design. To keep costs reasonable, using overseas vendors if I can guarantee ITAR compliance.
