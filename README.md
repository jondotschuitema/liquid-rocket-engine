
*To eat elephant, one must take one bite at a time.*
# About

The idea for this project traces back to a childhood fascination with space technologies, but fast forward to sophomore year of college: I joined my university rocket club, then just a handful of like-minded members. The club's focus at the time was competing in the IREC 10K and 30K COTS categories, but special projects weren't off the table. Over the last couple years of undergrad I dabbled in hybrid engine design and exploratory turbomachinery 1D design, leveraging additive manufacturing to fabricate mockup turbopump components. Shortly thereafter I graduated and eventually lost access to everything: CAD license, MATLAB, the machine shop, all of it.

The dream didn't stop there. I found workarounds — Siemens NX gave way to Autodesk Fusion, SimScale replaced my CFD tools, Python replaced MATLAB, and a 3D printer and mini-lathe purchases replaced the shop. I was back in business.

Clearly the next logical step was to develop an ethanol/LOx liquid bi-propellant, regeneratively cooled, turbopump-fed rocket engine.

The design and fabrication process is outlined in the sections below. All work was completed by me unless otherwise noted.

_Note: Much of this documentation was written retrospectively._

#### Units
Apologies in advance, but mixed SI and Imperial units appear throughout. 1D and 3D design was done in SI, but converted to Imperial for manufacturing (my machine tool increments are in inches).

#### Links
Documents for each subsystem are found in [docs](docs/). Individual documents are also linked below:

[dynamometer](docs/dynamometer)
[gas generator](docs/gas%20generator)
[igniter](docs/igniter)
[plumbing](plumbing)
[tanks](docs/tanks)
[test bench](docs/test%20bench)
[thrust chamber](docs/thrust%20chamber)
[turbopump](docs/turbopump)

## Timeline

### Goal
The original, and current goal is a hotfire by Q1 of 2028. Design, fabrication, and test of the subsystems has been spread out over the 5 years spanning 2022 - 2027. With a $30,000 budget and up to 8-12 hours a week in the free time away from my full-time engineering job, 5 years seemed reasonable to shoulder the entire end-to-end process (however as of July 2026, this timeline is slipping due to manufacturing setbacks).

The yearly milestones are planned as follows (✅ = Done, ⏳= Pending, 🛠️ = In Progress, ❌ = Cancelled):

#### 2022
- Set performance requirements / design goals ✅
- Develop tooling - 1D design spreadsheet, acquire fabrication tools ✅
- Learn new and refine existing skills - design for AM, CAD, machining ✅
- Read material ✅ - Huzel and Huang's Modern Engineering for Design of Liquid Rocket Engines (NASA SP-125) is a great first source
- Dynamometer design ✅
- Source tools ✅

#### 2023
- Dynamometer fabrication ✅
- 1D design of subsystems (TCA, valves, turbopump, tanks) ✅
- Begin 3D CAD design of components ✅
#### 2024
- Pump fabrication and test ✅
- Injector design ✅
- Turbine design 🛠️
- Metal additive manufacturing exploration 🛠️

#### 2025
- High pressure pump fabrication and test 🛠️
- Valve design ✅
- Plumbing design and exploration 🛠️
-  Test stand controller development ✅
- Combustion device development & test
	- Torch igniter ✅
	- Gas generator 🛠️
- Injector fabrication and test ⏳
- Thrust chamber manufacturing ⏳
- Fuel Tank design ✅

#### 2026
- Pathfinder gas generator test stand development ✅
- Pathfinder gas generator hot fire ✅
- Production gas generator fabrication ❌
- Pump manufacturing method exploration 🛠️
- Custom shaft seal development  ❌
- Engine test stand design⏳
- Turbine fabrication and test ⏳
- Cryogenic tank and plumbing exploration ⏳
- Thrust chamber assembly ❌
	- Injector integration
	- Valve integration
	- Torch igniter integration

#### 2027
- Metal pump manufacturing and test 🛠️
- Turbine test ⏳
- Turbopump assembly development and test ⏳
- Engine test stand fabrication ⏳
- Test stand controller expansion ⏳
	- Additional Pressure and temperature sensors
	- Additional servo ports
	- Additional solenoid valve ports
	- Independent tank control subboards
	- Custom PCB design?
- Final subsystem acceptance testing ⏳
- Hotfire location selection ⏳

#### 2028
- Procedure finalization ⏳
- A/V equipment procurement for documentation ⏳
- Hotfire!!! ⏳

