---
title: 'Optimal start'
date: 2019-03-28
permalink: /blogs/2019/03/optimal-start/
tags:
  - Modelica
  - EnergyPlus
---

There are two approaches to approximate the optimal start time for the HAVC system: temperature gradient and regression functions. The first approach is commonly seen in commercial controllers, such as Carrier, Trane and Honeywell controllers although each company has its own way to implement the algorithm. The second approach is recommended by ASHRAE handbook, but it is more difficult to implement.

## Temperature gradient
This approach is implemented in EnergyPlus. The class name is SystemAvailabilityManager.
### Constant temperature gradient  
Temperature gradient $T_g$ quantifies how fast the zone temperature rises or drops per unit time, i.e. $^{\circ} C/h$. Temperature gradient should have separate values for space heating and cooling.

$$
t_{opt} = \begin{vmatrix} \frac {T_{setpoint}-T_{zone}}{T_g} \end{vmatrix}
$$

If using constant $T_g$ in the model, the parameter should be user-input.

### Adaptive temperature gradient
In this scenario, the algorithm updates the temperature gradient based on simulation results from previous days. The algorithm needs to calculate $T_g$ for each day; then it
The user provides initial value for temperature gradient and the number of previous days (default = 2) for averaging.

$$
T_{g,i} = mean(T_{g,i-1},T_{g,i-2},...)
$$

## Regression
This method is included in ASHRAE handbook. Optimal preheat time is correlated to zone temperature, outdoor temperature and setpoint schedules, while optimal precool time is less sensitive to outdoor temperature and setpoints.
### Precool

$$
t_{opt} = a_0 + a_1T_{zone}+a_2T_{zone}^2
$$

### Preheat

$$
t_{opt} = b_0 + (1-\omega)(b_1T_{zone}+b_2T_{zone}^2+\omega a_3T_{out})
$$

$$
\omega = 1000^{-{(T_{zone}-T_{sp,occ})}/{(T_{sp,occ}-T_{sp,unocc})}}
$$

### Issues
$t_{opt}$ is difficult to obtain.
Different sets of equations need to be used for different zones.
