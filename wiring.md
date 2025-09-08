# Wiring
Wiring Diagram:
```{image} ../images/wiring-diagram.png
:alt: Controller wiring diagram
:class: bg-primary mb-1
:width: 1000px
:align: left
```

```{image} ../images/wiring-photo.jpg
:alt: Controller wiring
:class: bg-primary mb-1
:width: 1000px
:align: left
```


Wiring Table:
```{image} ../images/wiring-table.png
:alt: Controller UI and UO table
:class: bg-primary mb-1
:width: 600px
:align: left
```


## Power
The red and black terminal blocks are connected to live 24VDC power and ground, respectively. These are then connected to the controller and any components that need power.

## Pump
The pump is connected to 120VAC power with the spring terminals on the right inside the cover. + is conected to 120VAC (hot), L1 is connected to 120VAC (neutral), and L2/N is connected to ground.

The pump is connected to a 4-20mA control signal using the spring terminals on the left inside the cover. The pump uses 2-10V control, but has a 500 ohm resistor pre-installed which converts the 4-20mA control signal into a 2-10V signal. Vdc is connected to UO5 and COM is connected to the controller COM.

## Zone Valves
The zone valves come with pre-installed wire bundles. The red cable is 24VDC (hot), black is 24VDC (ground), orange is the feedback signal (2-10 VDC) and white is the control signal (2-10 VDC).

## Temp Sensors
The Belimo temp sensors need 2 wires connected to the pos. and neg. spring terminals inside their casings. Either a twisted pair or separate green (pos.) and white (neg.) wires with ferrules are used. The thermistor should be connected similarly, although the direction of current flow doesn’t matter.

## Flow Meters
The flow meters must be connected to arduinos to process their signals for the controller. The flow meters output pulses, which are counted over 1 second by the arduino to calculate a flow rate in L/m. The arduino outputs a 0-5V signal with scaling set in the code. 

The flow meters come with female jumper cables, which are connected to the arduinos with 22 awg solid core wire. The red wire connects to 5V, black to ground, and green to D2 (data input). 

22 awg solid core wire is also used to connect the arduinos to power and the controller. Red connects Vin to 24VDC from the terminal block, black connect ground to terminal block ground, green connects A0 (data output) to the controller UI, and white connects ground to the controller COM.

## BACNet Devices
BACNet devices including the power meter and thermostat are daisy-chained to the first bus on the controller. The last device in the chain should have an internal terminal resistor switched on, or an external terminal resistor should be used.