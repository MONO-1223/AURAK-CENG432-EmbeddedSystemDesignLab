<p align="center">
  <img src="Photos/practice.gif"/>
</p>

In this experiment, we revise fundamental digital I/O operations using the [Tiva C (TM4C123) microcontroller](Photos/TM4C123GXL.png). Specifically, we configure Port E Pin 3 (PE3) as an output to control an external LED and Port E Pin 2 (PE2) as an input to read the state of an external switch. The objective is to implement a simple yet effective LED control mechanism based on time-based toggling and user input.

By analyzing the calculations behind the toggling intervals and implementing the logic in code, we reinforce our understanding of hardware-software interaction and real-time processing in microcontroller-based systems.

Equipment essential for this experiment includes the [BK-Precision 2542C digital oscilloscope](https://datasheet.octopart.com/2542C-B%26K-Precision-datasheet-102877298.pdf), [red LED](https://www.mouser.com/datasheet/2/239/lite-on_lite-s-a0003806513-1-1737505.pdf), popular PE-74N breadboard, switches, male-male and female-male wires, complemented by the Keil uVision 5 IDE. 

## Hardware Implementation

<p align="center">
  <img src="Photos/demo-100.gif" style="width: 400%; height: 400px;"/> <img src="Photos/demo-150.gif" style="width: 400%; height: 400px;" />
</p>

For a clearer view of the practical connection, check this [schema](Photos/fritzing.png). To recall the breadboard's internal connections setup, click [here](Photos/recall.jpg).

<p align="center">
  <img src="Photos/osci100.jpg" style="width: 49%; height: 260px;"/> <img src="Photos/osci150.jpg" style="width: 49%; height: 260px;" />
</p>

yap

## Keil Simulation

<p align="center">
  <img src="Photos/100simulation.png" style="width: 49%; height: 300px;"/> <img src="Photos/150simulation.png" style="width: 49%; height: 300px;" />
</p>



## C Code on EK-TM4C123GXL


```c

```


<br>

```mermaid
gantt
    title Work Division Gantt Chart
    tickInterval 1day
    todayMarker off
    axisFormat %a-%Y-%m-%d
    section Preparation         
        Nour Mostafa : 2025-02-27 00:00, 02h
        Mohamed Abouissa : 2025-02-27 00:00, 02h
    section Keil         
        Nour Mostafa : 2025-02-28 00:00, 1d
    section Results       
        Mohamed Abouissa : 2025-02-28 00:00, 1d
    section Report
        Nour Mostafa : 2025-03-02 00:00, 1d
        Mohamed Abouissa : 2025-03-02 00:00, 1d
```

This publication adheres to all regulatory laws and guidelines established by the [American University of Ras Al Khaimah (AURAK)](https://aurak.ac.ae/) regarding the dissemination of academic materials.


