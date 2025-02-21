<p align="center">
  <img src="Photos/exp4.gif"/>
</p>

This report is Markdown-typed and submitted in Spring 2025 by students [Nour Mostafa](https://github.com/Nour-MK) with ID 2021004938 and [Mohamed Abouissa](https://github.com/Mohamed-Abouissa) with ID 2021005188 in partial fulfillment of the requirements for the Bachelor of Science degree in Computer Engineering. We extend our sincere appreciation to Eng. Umar Adeel for his insightful feedback which has significantly contributed to the successful completion of this experiment.

---

The purpose of this lab is to learn minimally intrusive debugging skills to experimentally verify the correct operation of our system. In particular, the use of a dump and a heartbeat. In this lab, we will build on the Lab 3 code, incorporating additional functionality while maintaining its core structure. The heartbeat signal using the on-chip blue LED will be implemented on `Port F`. Meanwhile, toggling the off-chip red LED using the switch will be performed on `Port E`. The debugging techniques in this lab use both hardware and software and are appropriate for the [Tiva C (TM4C123) microcontroller](Photos/TM4C123GXL.png). Software skills that we will gain through this experiment include indexed addressing, array data structures, the PLL (phase-locked loop), the SysTick timer, and subroutines.

When visualizing software running in real-time on an actual microcomputer, it is important to use minimally intrusive debugging tools. We call a debugging instrument minimally intrusive when the time it takes to collect and store the information is short compared to the time between when information is collected. The first objective of this lab is to develop an instrument called a dump, which does not depend on the availability of a debugger. A dump allows you to capture strategic information that will be viewed at a later time. Many programmers use the `printf` statement to display useful information as the programming is being executed. On an embedded system we do not have the time or facilities to use `printf`. Fortunately, we can use a dump in most debugging situations for which a `printf` is desired. Software dumps are an effective technique when debugging real-time software on an actual microcomputer. The second useful debugging technique is called a heartbeat. A heartbeat is a visual means to see that your software is still running. 

There are various types of debugging purposes, just as there are different debugging techniques to achieve them. The difference between __functional debugging__ and __performance debugging__ lies in whether timing information is included. If only input and output data are saved, the dump falls under functional debugging, as it captures how the system responds without considering when events occur. However, by saving both input/output data and timestamps, the dump provides insight into the timing of operations, making it a form of performance debugging. This allows for analyzing not just what happens, but also how efficiently the system executes tasks.

Equipment essential for this experiment includes the [Pro's Kit MT-1280 31/2 digital multimeter CAT III 1000V](https://www.circuitspecialists.com/content/329997/MT-1280_manual.pdf?srsltid=AfmBOopVl2nIapGnuj3aocPx9iyjMpgbFWmxxg3hCIOpympyKfhuPNW7), [BK-Precision 2542C digital oscilloscope](https://datasheet.octopart.com/2542C-B%26K-Precision-datasheet-102877298.pdf), [red LED](https://www.mouser.com/datasheet/2/239/lite-on_lite-s-a0003806513-1-1737505.pdf), popular PE-74N breadboard, switch, male-male and female-male wires, complemented by the Keil uVision 5 IDE. 


## Hardware Implementation

<p align="center">
  <img src="Photos/main-demo.gif" style="width: 1000px" title="Real Life Test" />
</p>

When the switch is pressed, PE1 goes high. When the software sees PE1 high it toggles PE0 every 62ms.

We can implement multiple heartbeats at various strategic places in the software and that toggle much faster than the eye can see. In these situations, we would use a logic analyzer or oscilloscope to visualize many high-speed digital signals all at once. However, in this experiment, there is one heartbeat on PF2, and the heartbeat occurs slow enough to be seen with the eye.

Worthy of note is that when we connected the ground to a pin located far on the other side, the LED started blinking. Similarly, when we connected the 3.3V power to pins that were far apart along the positive rail at the top of the breadboard, the LED also blinked. We have resolved the issue of the red LED blinking without us pressing the switch by ensuring that the wires requiring ground or voltage are placed on the same side of the bus line, rather than on opposite sides of the two halves of the bus. This adjustment ensured a more stable connection, preventing unintended behavior.


 For a clearer view of the connection, check this [schema](Photos/fritzing.png).

<br>


<p align="center">
  <img src="Photos/osci1.png" style="width: 49%; height: 300px;" title="Method 1"/> <img src="Photos/osci2.png" style="width: 49%; height: 300px;" title="Method 2" />
</p>

The heartbeat signal is measured with the oscilloscope which shows it is actually toggled every 68 ms.

In the left photo, 

In the right photo, 



## Keil Simulation

<p align="center">
  <img src="Photos/debug.png" title="Simulation Test"/>
</p>

The simulation output shows the input on PE1, output on PE0, and heartbeat on PF2. Similar data (formatted in a little-endian hexadecimal format) is observed in the Memory window in the debug mode and on the real board showing the results of the dump. We look in the map file in order to find the addresses of the buffers. The map file (usually generated by your linker) contains information about memory allocation, including the addresses of global variables and arrays. Locate the **.map** file in your project directory, which is generated after compilation, and open it to search for the names of your arrays, such as **DataBuffer** and **TimeBuffer**. Within the file, you should find entries indicating their memory allocation, typically under the **.bss** section. The addresses will be listed in a format like `.bss.DataBuffer  0x20000000  0x00000190` and `.bss.TimeBuffer  0x20000190  0x00000190`, where the first column represents the memory section, the second column shows the starting address, and the third column specifies the allocated size. These addresses indicate where your arrays reside in memory, allowing you to use them for debugging or memory dumping. Finally, you can dump the memory to a file using the command `SAVE data.txt 0x20000000, 0x20000190` replacing the values 0x20000000 and 0x20000190 with the start and end addresses of your arrays.

The dumped data starts with some 0x01 data. Next, it oscillates between 0x10 and 0x11 as the switch is pressed, then returns to 0x01 when the switch is released. This [table](Photos/) illustrates how the data easier to visualize after a dump is taken. The table represents how input (PE1) and output (PE0) values are packed into a single saved data value for debugging purposes. PE1 represents the input bit, while PE0 represents the output bit, both being single-bit values. The data is stored by shifting PE1 into bit position 4 while keeping PE0 in bit position 0. When PE1 and PE0 are both 0, the stored value remains `0000 0000₂` (0x00000000). If PE0 is 1 while PE1 is still 0, the stored value becomes `0000 0001₂` (0x00000001). When PE1 is 1 and PE0 is 0, shifting PE1 into bit position 4 results in `0001 0000₂` (0x00000010). Finally, if both PE1 and PE0 are 1, the stored value becomes `0001 0001₂` (0x00000011). This structured bit-packing approach ensures efficient storage of both values while keeping them easily interpretable in hexadecimal format.

## C Code on EK-TM4C123GXL

This code includes both dump and heartbeat instrumentation. The dump functionality is implemented through `Debug_Init()`, which initializes `DataBuffer` and `TimeBuffer` to store data and timestamps. Within the main loop, when space is available in `DataBuffer`, the current time (`NVIC_ST_CURRENT_R`) and the input/output states (`in` and `out`) are logged for debugging purposes. The heartbeat mechanism is achieved by toggling the Blue LED (PF2) using `GPIO_PORTF_DATA_R ^= 0x04;`, providing a visual indication that the program is actively running.

First, the PLL is activated by calling the `TExaS_Init` function, allowing the microcontroller to run at 80 MHz. We adjust the delay function so it delays 62 ms. Then, the SysTick timer is initialized by calling `SysTick_Init`, enabling the 24-bit counter `NVIC_ST_CURRENT_R` to decrement every 12.5 ns. This counter is used to measure time differences of up to 335.5 ms by simply reading its current value. While the system runs in real-time, the dump process continuously stores data from Port E and `NVIC_ST_CURRENT_R` into arrays, allowing the information to be reviewed later. After that, an LED is toggled once per loop iteration to create a visible heartbeat signal.

To implement a dump instrument for debugging, we will write two subroutines: `Debug_Init` and `Debug_Capture`. These subroutines will work together to store both input/output data and timing information. An array will be defined to store approximately 3 seconds' worth of Port E measurements, along with another array for time measurements. If the outer loop of Lab 3 executes in about 62 ms per iteration, the loop will run approximately 50 times within 3 seconds (3000 ms / 62 ms). Therefore, both arrays should be sized to hold 50 elements. Either pointers or counters can be used to store the data in the arrays. The first subroutine, `Debug_Init`, initializes the dump instrument. It activates the SysTick timer, fills both arrays with `0xFFFFFFFF` to indicate that no data has been recorded yet, and initializes any necessary pointers or counters. The second subroutine, `Debug_Capture`, records one data point by storing the input value from PE1 and the output value from PE0 in the first array, and the current SysTick value (`NVIC_ST_CURRENT_R`) in the second array. Since only two bits need to be stored in the first array, the PE1 value will be placed in bit 4, and the PE0 value in bit 0, allowing for easy visualization when displayed in hexadecimal. Place a call to `Debug_Init` at the beginning of the system, and a call to `Debug_Capture` at the start of each execution of the outer loop. 

- The design of the data structures for a pointer-based implementation of this debugging instrument involves several key steps. First, a `DataBuffer` must be allocated in RAM to store approximately 3 seconds of input/output data. Next, a `TimeBuffer` must be allocated in RAM to store 3 seconds' worth of timer data. Finally, two pointers, `DataPt` and `TimePt`, need to be allocated—one for each array—ensuring they point to the correct location for storing the next data entry. These steps ensure efficient data collection for debugging and performance analysis.

- Designing `Debug_Init` using a pointer-based approach involves several key steps. First, all entries in the first buffer must be set to `0xFFFFFFFF` to indicate that no input/output data has been recorded yet. Similarly, all entries in the second buffer should also be initialized to `0xFFFFFFFF` to signify that no timing data has been stored. Next, the two pointers, `DataPt` and `TimePt`, should be initialized to point to the beginning of their respective buffers. Finally, the SysTick timer must be activated by calling `SysTick_Init`, to enable time measurement for debugging purposes.

- Designing `Debug_Capture` with a pointer-based approach follows a structured sequence of steps. First, any necessary registers should be saved to preserve their values. If the buffers are already full, meaning the pointer has moved past the end of the buffer, the subroutine should return immediately to prevent overwriting data. Next, the current values of Port E and the SysTick timer (`NVIC_ST_CURRENT_R`) must be read. To isolate relevant data, only bits 1 and 0 of the Port E input should be captured. The value of bit 1 should then be shifted into bit position 4, while bit 0 should remain in position 0 to ensure proper storage formatting. This processed Port E information should then be stored in `DataBuffer` using the pointer `DataPt`, after which `DataPt` should be incremented to the next address. Similarly, the corresponding time measurement should be stored in `TimeBuffer` using the pointer `TimePt`, and `TimePt` should also be incremented. Finally, any saved registers should be restored before the subroutine returns to normal execution.


```c

       
```

## Conclusion

summary

To estimate the execution speed of the debugging instrument, we assume that each instruction requires approximately two cycles. By counting the instructions in the **Debug_Capture** subroutine and multiplying by two, we can determine the total number of cycles required for execution. Given a bus cycle time of **12.5 ns**, we convert cycles to time by multiplying the total cycles by **12.5 ns**. For example, if Debug_Capture contains **50 instructions**, it requires **100 cycles**, which translates to **1.25 µs** of execution time. Next, we estimate the time between consecutive calls to Debug_Capture, say **100 µs**, and use this to calculate the overhead percentage. The overhead is determined by the formula **(100 × execution time) / (time between calls) × 100**, which quantifies how much system time is occupied by the debugging instrument. Using our example, the overhead would be **1.25%**, meaning that Debug_Capture runs efficiently with minimal impact on system performance. A lower overhead indicates that debugging has little effect on execution, while a higher overhead suggests significant system slowdown. This calculation provides a clear, quantitative measure of the intrusiveness of the debugging instrument.

discuss other ways the problem could have been solved

other varied tasks on the same code with slight changes

applications of this labs outcomes in the industry




## Resources

[2] Cortex-M4 Technical Reference Manual. (2009). <br> https://users.ece.utexas.edu/~valvano/EE345L/Labs/Fall2011/CortexM4_TRM_r0p1.pdf  
[3] Online FlowChart & Diagrams Editor—Mermaid Live Editor. (n.d.). <br> https://mermaid.live  
[4] Texas Instruments Incorporated. (2014). Tiva™ TM4C123GH6PM Microcontroller data sheet. Texas Instruments Incorporated. <br> https://www.ti.com/lit/ds/symlink/tm4c123gh6pm.pdf  
[5] Texas Instruments Incorporated. (2013). Tiva™ C Series TM4C123G LaunchPad (User's Guide). Texas Instruments Incorporated. <br>  https://www.ti.com/lit/ug/spmu296/spmu296.pdf  
[6] Valvano, J. W. (2014). Embedded systems: Introduction to ARM® Cortex-M microcontrollers (5th ed., Vol. 1). Self-published. <br> https://users.ece.utexas.edu/~valvano/Volume1/E-Book/   
[7] Fritzing. (n.d.). <br> https://fritzing.org/
‌
<br>

```mermaid
gantt
    title Work Division Gantt Chart
    tickInterval 1day
    todayMarker off
    axisFormat %a-%Y-%m-%d
    section Preparation         
        Nour Mostafa : crit, 2025-02-13 00:00, 02h
        Mohamed Abouissa : crit, 2025-02-13 00:00, 02h
    section Keil         
        Mohamed Abouissa : crit,2025-02-14 00:00, 1d
    section Results       
        Nour Mostafa : crit, 2025-02-14 00:00, 1d
    section Report
        Nour Mostafa : crit, 2025-02-20 00:00, 2d
        Mohamed Abouissa : crit, 2025-02-22 00:00, 2d
```

This publication adheres to all regulatory laws and guidelines established by the [American University of Ras Al Khaimah (AURAK)](https://aurak.ac.ae/) regarding the dissemination of academic materials.
