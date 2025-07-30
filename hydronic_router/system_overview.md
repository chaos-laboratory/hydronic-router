# System Overview

The hydronic router includes devices to control and collect data on 4 zones with attachable components. See diagram below.

```{image} ../images/hydronic-router.png
:alt: hydronic router system overview
:class: bg-primary mb-1
:width: 600px
:align: center
```

Volumetric flow rate is calculated from pressure data using the Bernoulli Equation: 
$P_1 + \frac12 \rho v_1^2 + \rho g h_1 = P_2 + \frac12 \rho v_2^2 + \rho g h_2$
$h_1 = h_2, v_2 = 0,  P_s = P_2 - P_1$
$P_s =  \frac12 \rho v_1^2$
$v_1 = \sqrt{\frac{2P_s}{\rho}}$
Using velocity to find volumetric flow rate:
$Q = Av$
$Q = A\sqrt{\frac{2P_s}{\rho}}$