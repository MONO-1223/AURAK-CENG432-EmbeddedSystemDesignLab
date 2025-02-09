<p align="center">
  <img src="Photos/exp2.gif"/>
</p>

This report is Markdown-typed and submitted in Spring 2025 by students [Nour Mostafa](https://github.com/Nour-MK) with ID 2021004938 and [Mohamed Abouissa](https://github.com/Mohamed-Abouissa) with ID 2021005188 in partial fulfillment of the requirements for the Bachelor of Science degree in Computer Engineering. We extend our sincere appreciation to Eng. Umar Adeel for his insightful feedback which has significantly contributed to the successful completion of this experiment.

---

Time is very important to embedded systems. As engineers, we rely heavily on understanding the execution times of software instructions to ensure optimal system performance. One effective method we employ is measuring the execution time of each instruction, a technique crucial for developing efficient embedded software. To achieve this on our [Tiva C LaunchPad (TM4C123) microcontroller](Photos/TM4C123GXL.png), we begin by utilizing the ARM data sheet—as the TM4C123 microcontroller is powered by an ARM Cortex-M4 core (CPU)—which provides detailed specifications essential for calculating execution times accurately. Another more accurate method to measure time uses a logic analyzer or oscilloscope. In the simulator, we will use a simulated logic analyzer, and on the real board we will use an oscilloscope. To measure execution time, we cause rising and falling edges (LED toggles) on a digital output pin that occur at known places within the software execution. We can then use the logic analyzer or oscilloscope to measure the elapsed time between the rising and falling edges. 

In this lab, we will continue using the same Port F and pin configuration, but this time, we will program in the assembly language instead of C. We start by interfacing a negative logic switch and a positive logic LED on GPIO ports. A negative logic switch, represented by PF4, registers a high signal (3.3V) when unpressed and a low signal (0V) when pressed. Conversely, a positive logic green LED, controlled via PF3, illuminates when the software outputs a high signal (3.3V) and remains off with a low signal (0V). The primary objective of this lab is threefold: first, to establish the switch at PF4 as an input and the LED at PF3 as an output with pull-up resistor (PUR) enabled. Second, to initiate the system with the LED initially off. Third, to implement a toggle mechanism for the LED every 100 milliseconds when the switch is pressed, ensuring it remains off when the switch is not pressed. This procedure will be iterated with delays of 100ms, 150ms, and 200ms to explore varying time intervals. You can get a snapshot of this program’s flow and logic by referring to the [flowchart](Photos/flowchart.png).

Note that, for the 100 ms trial, any delay between 80 ms and 120 ms is considered acceptable. To determine the tolerance range, we [subtract the minimum delay from the maximum delay](Photos/tolerance-range.png). Next, we calculate the tolerance percentage by [dividing the tolerance range by the target delay and multiplying by 100](Photos/tolerance-percentage.png). Therefore, the delay can vary by ±40% from the target delay of 100 ms. Similarly for [150 ms ±40%](Photos/40of150.png) (i.e. between [90 ms](Photos/150-40.png) and [210 ms](Photos/150+40.png)) and [200 ms ±40%](Photos/40of200.png) (i.e. between [120 ms](Photos/200-40.png) and [280 ms](Photos/200+40.png)).

In embedded systems, I/O (Input/Output) ports are used to interface with external devices, such as switches, LEDs, and sensors. On the TM4C123 microcontroller, these I/O ports are managed through registers, and how we access them is an important part of programming embedded systems. At the early stages of design, it is recommended to access entire I/O ports through a register like GPIO_PORTF_DATA_R. This approach allows us to read or write to the whole I/O port (in this case, Port F) at once, which is simpler to understand and use. When we access the entire port, we're dealing with all the pins on that port as a group, rather than focusing on each individual pin. This method is more straightforward for beginners because one doesn't need to deal with the complexities of individual bits representing different I/O pins. Once we become more familiar with how I/O ports work, we can use bit-specific addressing to control individual pins on a port. Instead of dealing with the whole port at once, this allows us to access and modify individual bits which gives us finer control over individual pins without affecting others.

To estimate the time it takes for an operation to complete, we first need to understand the clock cycle of the microcontroller. For the TM4C123 microcontroller, with a default bus clock of 16 MHz ±1%, we know that each millisecond encompasses approximately 16,000 bus clock cycles. This understanding allows us to estimate how long it will take to execute specific tasks. For example, if we are working with a loop, we need to determine the number of cycles required to execute the loop once. Suppose it takes 4 cycles to execute the loop once; to achieve a 1 ms delay, we would need to execute the loop 4,000 times (because 4,000 cycles x 4 cycles per loop = 16,000 cycles, which is equivalent to 1 ms). In this estimation process, we focus primarily on the cycles spent inside the loop, neglecting the impact of instructions outside the loop, as their execution time is significantly smaller in comparison (4000 times less, in this case). This simplification makes it easier to calculate the required number of loop executions to achieve a precise time delay without being bogged down by the minor effects of external instructions.

According to the Tiva board datasheet, our system bus runs at 16 MHz, and since the period of a clock cycle is the reciprocal of the clock frequency, we can calculate the clock period to be [62.5 ns](Photos/period.png) (i.e. the time required to complete one cycle on the board ). In our delay subroutine, the two main instructions involved are `SUBS` and `BNE`. The `SUBS` instruction, which decrements the loop counter, takes 1 cycle, while the `BNE` instruction, which checks whether to branch back, can take between 1 to 4 cycles (a branch takes 0 to 3 cycles to refill the pipeline), depending on the processor's state. Through simulation, we observed that `BNE` takes approximately 3 cycles per iteration, whereas on the actual hardware, it takes 2 cycles. This discrepancy affects our delay calculations with regard to designing a program to run accurately on the LaunchPad or in real life. 


By engaging in these practical exercises, we aim not only to grasp essential programming structures in assembly (e.g. masking, toggling, if-then, subroutines, and looping) but also to refine our ability to estimate software execution times. Furthermore, we will utilize an oscilloscope—calibrating it vertically and horizontally—to precisely measure these time delays, fostering a comprehensive understanding of real-time system performance monitoring.

## Toggling the Green LED using Switch 1 (100 ms Delay)

<img src="Photos/100delay.gif" width="380" height="300" align="left">
<img src="Photos/transparentpic.png" width="8" height="300" align="left">

<br>

In simulation, where each iteration takes 4 cycles (1 for `SUBS` and 3 for `BNE`), the number of iterations is calculated by dividing the desired time delay by the time taken per iteration. Hence, to achieve a 100 ms delay in a period of 62.5 ns and 4 cycles in simulation, we calculate that we need [400,000 iterations](Photos/simulation100delay.png). Whereas, on the actual board, where each iteration takes 3 cycles (1 for `SUBS` and 2 for `BNE`), it would be [533,333 iterations](Photos/hardware100delay.png) on actual hardware. Knowing the number of iterations is crucial because it directly determines how long our delay loop will run to achieve a precise time delay. Since the execution time of each instruction varies between simulation and actual hardware, understanding the number of iterations ensures that our program behaves as expected.  
<br><br>

<p align="center">
  <img src="Photos/launchpad100delaynotpressed.png" style="width: 49%; height: 300px;" title="Green LED is Off" /> <img src="Photos/launchpad100delaypressed.png" style="width: 49%; height: 300px;" title="Green LED is On" />
</p>

In the left picture, we can see that no switch is pressed on the LaunchPad, causing the green LED to remain off. The oscilloscope reading above confirms this, showing the switch signal at 1 (active low), which indicates that it is not pressed, while the LED signal remains at 0 (active high). The right picture shows that when the switch is pressed, its signal transitions to 0, indicating activation. As a result, we observe the LED toggling on and off. By analyzing the oscilloscope waveform, we can measure the time between consecutive ON states, which is approximately 0.100 seconds (100 ms), confirming the expected delay. However, slight variations in the timing occur due to the uncertainty of whether the `BNE` (branch if not equal) instruction takes the branch or not, meaning each iteration does not always take exactly 4 cycles. This variability accounts for the minor fluctuations in the measured delay.


## Toggling the Green LED using Switch 1 (150 ms Delay)

<img src="Photos/150delay.gif" width="380" height="300" align="left">
<img src="Photos/transparentpic.png" width="8" height="300" align="left">


In simulation, where each iteration takes 4 cycles (1 for `SUBS` and 3 for `BNE`), the number of iterations is calculated by dividing the desired time delay by the time taken per iteration. Hence, to achieve a 150 ms delay in a period of 62.5 ns and 4 cycles in simulation, we calculate that we need [600,000 iterations](Photos/simulation150delay.png). Whereas, on the actual board, where each iteration takes 3 cycles (1 for `SUBS` and 2 for `BNE`), it would be [800,000 iterations](Photos/hardware150delay.png) on actual hardware. In our experiment, the simulation provides the most accurate results since it operates in a controlled environment without external factors affecting execution time. However, on actual hardware, the program's timing may be less precise due to voltage drops across the board and bus delays. These real-world factors introduce slight variations in execution time, making hardware performance deviate from the idealized simulation results. Understanding these differences is key to accounting for hardware imperfections.
<br><br>

<p align="center">
  <img src="Photos/launchpad150delaynotpressed.png" style="width: 49%; height: 300px;" title="Green LED is Off" /> <img src="Photos/launchpad150delaypressed.png" style="width: 49%; height: 300px;" title="Green LED is On" />
</p>

The screenshot to the left shows that when SW1 is not pressed on the LaunchPad, the green LED stays off until the SW1 is pressed (as shown in the right screenshot) then the green LED starts toggling between the on and off states as illustrated by the oscillations. In the simulation, d (delta) represents the difference between two cursor values, where 0.157s = 157ms. This delta is measured from the start to the end of the ON state or similarly for the OFF state. We measure the state duration to ensure accurate delay setting. To compare between the hardware and the simulation result, we can note the "Width" reading on the oscilloscope as it reads 113 ms compared to the rather accurate 157 ms. Nonetheless, 113 ms is within the tolerance range and is therefore deemed an acceptable result.


## Toggling the Green LED using Switch 1 (200 ms Delay)

<img src="Photos/200delay.gif" width="380" height="300" align="left">
<img src="Photos/transparentpic.png" width="8" height="300" align="left">

<br>

In simulation, where each iteration takes 4 cycles (1 for `SUBS` and 3 for `BNE`), the number of iterations is calculated by dividing the desired time delay by the time taken per iteration. Hence, to achieve a 200 ms delay in a period of 62.5 ns and 4 cycles in simulation, we calculate that we need [800,000 iterations](Photos/simulation200delay.png). Whereas, on the actual board, where each iteration takes 3 cycles (1 for `SUBS` and 2 for `BNE`), it would be [1,066,666 iterations](Photos/hardware200delay.png) on actual hardware. In the oscilloscope, we focus on the width in the measurements. The width measurement actually represents the duration of a signal's HIGH (ON) or LOW (OFF) state, allowing us to verify the timing accuracy of our system. In the osci readings, the peaks and lows are in bright thick yellow and the vertical lines appear thin due to the measurement probe picking up noise.
<br><br><br>

<p align="center">
  <img src="Photos/launchpad200delaynotpressed.png" style="width: 49%; height: 300px;" title="Green LED is Off" /> <img src="Photos/launchpad200delaypressed.png" style="width: 49%; height: 300px;" title="Green LED is On" />
</p>

Again, the left photo shows that, when no switch is pressed on the LaunchPad, the green LED remains off. The oscilloscope reading above confirms this, showing the switch signal at 1, which indicates that it is not pressed, while the LED signal remains at 0. When the switch is pressed in the right photo, its signal transitions to 0, indicating activation. As a result, we observe the LED toggling on and off through the signal's oscillations. In the simulation, the delta is measured to be 0.203 s compared to the Width in the oscilloscope which read 150 ms (i.e. LED is ON for 150 ms and OFF for 150 ms). Both results are valid as they are within the tolerance range from 120 ms to 280 ms.

## Assembly Code on EK-TM4C123GXL

Configuring a register in assembly typically follows three main steps. First, we load the base address of the register into a general-purpose register. For example, to enable the system clock for GPIO, we load the base address of `SYSCTL_RCGCGPIO_R` into `R1`, then retrieve the value stored at that address into R0 using the `LDR` instruction. Next, we modify the necessary bits based on whether we need to set or clear specific values. To set a bit, we use the `ORR` (bitwise OR) instruction, while to clear a bit, we use `BIC` (bit clear). For instance, to enable Port F, we apply `ORR R0, R0, #0x20`, which ensures that bit 5 is set while leaving other bits unchanged. Finally, we store the updated value back into the register using the `STR` instruction. This writes the modified data in R0 back to the memory address stored in `R1`, ensuring the configuration takes effect. By following this structured approach, we can effectively configure hardware registers in assembly language.

The delay function in the given ARM assembly code creates a simple loop-based delay by loading an initial value into register `R0` and decrementing it iteratively until it reaches zero. The instruction `LDR R0,=400000` loads the immediate value `400000` (or any other desired delay value) into `R0`, setting the number of loop iterations. The `SUBS R0,#1` instruction then decrements `R0` by 1 while updating the condition flags in the CPSR (Current Program Status Register). The `BNE wait` (Branch if Not Equal) instruction checks if `R0` is zero; if not, it loops back to the `wait` label, continuing the decrement process. Once `R0` reaches zero, the loop terminates, and execution proceeds to `BX LR`, which returns control to the calling function. The duration of the delay is determined by the initial value loaded into `R0`, with larger values resulting in longer delays. For instance, if we were to design for a 1 ms delay rather than 100 ms, then we would exchange the 400,000 with [4,000](Photos/). 

In our program, we used various addressing modes to efficiently access and manipulate data. For example, the LDR instruction `LDR R1, =SYSCTL_RCGCGPIO_R` with an immediate value (denoted by =) loads the address `SYSCTL_RCGCGPIO_R` directly into register `R1`. This is considered __immediate addressing__ because the operand is directly provided as an immediate value, not calculated via memory. The instruction `LDR R0, [R1]` loads the value from the memory address stored in register `R1` into `R0`. The value in `R1` is used as the memory address, and the contents at that address are loaded into `R0`. This is a classic example of register __indirect addressing__. The MOV instruction `MOV R0, #00` moves the immediate value `00` into register `R0`. Since the operand is a constant, this uses __immediate addressing__, where the value is directly supplied to the instruction. Similar to LDR, this STR (store) instruction `STR R0, [R2]` stores the value in register `R0` at the memory address held in register `R2`. It uses __register indirect addressing__ because the address is specified in `R2`, and the data in `R0` is written to that address. The EOR (exclusive OR) instruction `EOR R0, R0, #0x08` takes the value in `R0` and applies the immediate value `0x08` to it. It then stores the result in `R0`. This is an example of __immediate addressing__ because the value `0x08` is supplied directly to the instruction. The BNE (branch if not equal) instruction `BNE wait` performs a branch based on the condition flags. The target label wait is specified as an offset from the current program counter `PC`. The branch will jump to the address corresponding to the label. This is an example of __PC-relative addressing__, as the destination address is computed relative to the current `PC`.

A __Reset Vector__ is the initial memory address where the processor begins execution when it is reset. It's essentially the starting point of the program after a system reset or power-up. The Reset Vector typically points to the entry point of the program's startup routine, which initializes hardware components, sets up memory, and prepares the system for further execution. In embedded systems like microcontrollers, the Reset Vector is crucial because it ensures the program begins execution in a known state.

The `AREA` directive in assembly language is used to define a section of code or data within the program. It specifies the name of the section, its type (such as CODE or DATA), and alignment requirements. For example, `AREA |.text|, CODE, READONLY, ALIGN=2` defines a code section named `.text`, specifying that the section is read-only and aligned to 2-byte boundaries. This ensures that the assembly code is organized properly in memory, making it more efficient and allowing the assembler to manage sections of the program accordingly.

The `EXPORT` directive in assembly language is used to make a label or symbol visible to other modules or files. By marking a label with `EXPORT`, it can be accessed from other parts of the program, even if those parts are located in separate files or modules. For example, `EXPORT Start` makes the Start label available for external references, which is especially useful in modular or multi-file projects where different pieces of code need to interact with each other. This allows for better modularity and code reuse.

```asm
; Here we just saving the base address + offset for each register i will use 
; Port F have the base address 0x40025000 (TM4C123 Data Sheet, 659)

GPIO_PORTF_DATA_R       EQU   0x400253FC            ; ???????
GPIO_PORTF_DIR_R        EQU   0x40025400            ; (TM4C123 Data Sheet, 633)  
GPIO_PORTF_AFSEL_R      EQU   0x40025420            ; (TM4C123 Data Sheet, 671)        
GPIO_PORTF_PUR_R        EQU   0x40025510            ; (TM4C123 Data Sheet, 677)
GPIO_PORTF_DEN_R        EQU   0x4002551C            ; (TM4C123 Data Sheet, 682)
GPIO_PORTF_AMSEL_R      EQU   0x40025528            ; (TM4C123 Data Sheet, 687)
GPIO_PORTF_PCTL_R       EQU   0x4002552C            ; (TM4C123 Data Sheet, 688)
SYSCTL_RCGCGPIO_R       EQU   0x400FE608            ; (TM4C123 Data Sheet, 340)
                                                    
        AREA    |.text|, CODE, READONLY, ALIGN=2    ;
        THUMB                                       ; ****************************
        EXPORT  Start                               ;
Start                                               ;
SWITCH  EQU 0x40025040                              ; PF4
LED     EQU 0x40025020                              ; PF3
                                                    
                                                    ; Activate clock for Port F
      LDR R1, =SYSCTL_RCGCGPIO_R                    ;
      LDR R0, [R1]                                  ;
      ORR R0, R0, #0x20                             ; Clock for F you need to set the Last bit (OR with the Base address)(0010 0000)
      STR R0, [R1]                                  ; (TM4C123 Data Sheet, 340)
      NOP                                           ; NOP = No Opration
      NOP                                           ; Allow time to finish activating
                                                    ; No need to unlock PE2,PE3,PE4
                                                    ; Disable analog functionality
      LDR R1, =GPIO_PORTF_AMSEL_R                   ; (TM4C123 Data Sheet, 687)
      LDR R0, [R1]                                  ;
      BIC R0, R0, #0x18                             ; No analog functionality on  PF3,PF4 , BIC stand for Bit Clear so We clear PF3,PF4
      STR R0, [R1]                                  ;
                                               	    
                                                    ; Configure as GPIO
      LDR R1, =GPIO_PORTF_PCTL_R                    ; (TM4C123 Data Sheet, 688)
      LDR R0, [R1]                                  ;
      LDR R2, =0x000FF000                           ; Regular function on PF3,PF4
      BIC R0, R0, R2                                ;
      STR R0, [R1]                                  ; 
                                                    
                                                    ; Set direction register
      LDR R1, =GPIO_PORTF_DIR_R                     ;
      LDR R0, [R1]                                  ; (TM4C123 Data Sheet, 633)
      ORR R0, R0, #0x08                             ; Output on PF3  set bit PF3
      BIC R0, R0, #0x10                             ; Input on PF4   set bit PF4
      STR R0, [R1]                                  ; 
                                                    ; Regular port function
      LDR R1, =GPIO_PORTF_AFSEL_R                   ; (TM4C123 Data Sheet, 671)
      LDR R0, [R1]                                  ;
      BIC R0, R0, #0x18                             ; GPIO on PF3,PF4 set bit PF3 and PF4
      STR R0, [R1]                                  ; 
	                                            ;
      LDR R1, =GPIO_PORTF_PUR_R                     ; (TM4C123 Data Sheet, 677)
      LDR R0, [R1]                                  ;
      ORR R0, R0, #0x10                             ; Enable pullup on PF4 so sit PF4
      STR R0, [R1]                                  ;
                                                    ; Enable digital port
      LDR R1, =GPIO_PORTF_DEN_R                     ; (TM4C123 Data Sheet, 682)
      LDR R0, [R1]                                  ;
      ORR R0, R0, #0x18                             ; Enable data on PF3,PF4 set bit PF3 and PF4
      STR R0, [R1]                                  ;
	                                            
      LDR R1,=SWITCH                                ; R1 = PF4, 0x40025040   
      LDR R2,=LED                                   ; R2 = PF3, 0x40025020    
	                                            
clear MOV  R0,#00                                   ; LED off
      STR  R0,[R2]                                  ;
	                                            
loop  BL   Delay                                    ; 100 ms
      LDR  R0,[R1]                                  ; R0 = PF4, 0x00,0x10
      ANDS R0,#0x10                                 ;
      BNE  clear                                    ; LED off if switch not pressed
      LDR  R0,[R2]                                  ;
      EOR  R0,R0,#0x08                              ; toggle
      STR  R0,[R2]                                  ; if switch pressed
      B    loop                                     ; branch to label "loop"
						     
Delay LDR  R0,=400000                               ; Load immediate value 400,000 into R0 (loop counter) - (THIS IS THE LINE YOU CHANGE THE VALUE IN FROM 400,000 TO 600,000 TO 800,000)
wait  SUBS R0,#1                                    ; Subtract 1 from R0 and update flags (N, Z, C, V)
      BNE  wait                                     ; If R0 ≠ 0, branch back to 'wait' (repeat the loop)
      BX   LR                                       ; Return from subroutine
                                                    
    ALIGN                                           ; Make sure the end of this section is aligned *******************
    END                                             ; End of file
```


## Conclusion

This experiment provided valuable hands-on experience in measuring execution time in embedded systems, reinforcing the importance of precise timing for real-time applications. By implementing LED toggling through switch interactions, we explored critical concepts such as negative logic switches, positive logic LEDs, and timing variations between simulation and hardware execution. Through assembly-level programming on the TM4C123 microcontroller, we examined how different execution environments—simulated and real-world—affect timing accuracy, highlighting the discrepancies caused by pipeline behavior and hardware imperfections. By utilizing an oscilloscope for real-time measurement, we ensured accurate delay verification and gained deeper insights into execution cycle estimations. The observed variations between theoretical calculations, simulation results, and actual hardware performance underscored the practical challenges engineers face when designing embedded systems. Ultimately, this lab enhanced our understanding of low-level programming, timing analysis, and hardware behavior.





## Resources

[1] Texas Instruments Incorporated. (2014). Tiva™ TM4C123GH6PM Microcontroller data sheet. Texas Instruments Incorporated. <br> https://www.ti.com/lit/ds/symlink/tm4c123gh6pm.pdf  
[2] Texas Instruments Incorporated. (2013). Tiva™ C Series TM4C123G LaunchPad (User's Guide). Texas Instruments Incorporated. <br>  https://www.ti.com/lit/ug/spmu296/spmu296.pdf  
[3] Valvano, J. W. (2014). Embedded systems: Introduction to ARM® Cortex-M microcontrollers (5th ed., Vol. 1). Self-published. <br> https://users.ece.utexas.edu/~valvano/Volume1/E-Book/   


<br>

```mermaid
gantt
    title Work Division Gantt Chart
    tickInterval 1day
    todayMarker off
    axisFormat %a-%Y-%m-%d
    section Preparation         
        Nour Mostafa : crit, 2025-02-2 00:00, 02h
        Mohamed Abouissa : crit, 2025-02-2 00:00, 02h
    section Keil         
        Mohamed Abouissa : crit,2025-02-5 00:00, 1d
    section Results       
        Nour Mostafa : crit, 2025-02-3 00:00, 1d
    section Report
        Nour Mostafa : crit, 2025-02-6 00:00, 2d
        Mohamed Abouissa : crit, 2025-02-9 00:00, 2d
```

This publication adheres to all regulatory laws and guidelines established by the [American University of Ras Al Khaimah (AURAK)](https://aurak.ac.ae/) regarding the dissemination of academic materials.





