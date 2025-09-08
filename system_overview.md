# System Overview

The hydronic router includes devices to control and collect data on 4 zones with attachable cooling components, all installed on a 4' x 4' x 4' climate chamber. See diagram below. 

```{image} ../images/hydronic-router.png
:alt: hydronic router system overview
:class: bg-primary mb-1
:width: 600px
:align: center
```
An EasyIO FS-32 controller is integrated into the system to allow user-designed control loops to be tested on the on the components. The radiant panel, coil (with fan, forming a fan-coil unit), and radiant slab have all been implemented in the chamber with plumbing in place to allow a thermal storage tank or other fourth component to be attached. 

Measured variables for the system are:
- Inlet water temp
- 4 zone outlet temps
- 4 zone flow rates
- Chamber air temp
- Chamber relative humidity
- For radiant panel: object temp in chamber
- For fan-coil unit: flow air temp, static pressure
- For radiant slab: slab surface temp

Controllable variables for the system are:
- Pump speed
- 4 zone valve positions
- Chilled water setpoint (manually)
- For fan-coil unit: Fan speed, damper actuator positions

