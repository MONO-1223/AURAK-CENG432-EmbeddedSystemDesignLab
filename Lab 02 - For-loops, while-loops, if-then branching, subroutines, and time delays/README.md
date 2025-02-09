<p align="center">
  <img src="Photos/exp2.gif"/>
</p>

This report is Markdown-typed and submitted in Spring 2025 by students [Nour Mostafa](https://github.com/Nour-MK) with ID 2021004938 and [Mohamed Abouissa](https://github.com/Mohamed-Abouissa) with ID 2021005188 in partial fulfillment of the requirements for the Bachelor of Science degree in Computer Engineering. We extend our sincere appreciation to Eng. Umar Adeel for his insightful feedback which has significantly contributed to the successful completion of this experiment.

---

In this lab, we will explore the fundamentals of simple programming structures in assembly language using Keil uVision5 and the [Tiva C LaunchPad (TM4C123) microcontroller](Photos/TM4C123GXL.png). The primary objective is to gain hands-on experience in estimating software execution time and using this information to create a time delay function. Additionally, we will learn how to utilize an oscilloscope to measure and verify time delays in our assembly programs.

Through this lab, we will develop key assembly programming skills such as masking, toggling, implementing conditional if-then statements, creating subroutines, and applying looping constructs. By the end of the lab, we will have acquired practical experience in assembly language programming and time-based control in embedded systems using the TM4C123 microcontroller.

## Part 1: Toggling the Green LED With a Switch (100ms Delay)

<img src="Photos/100delay.gif" width="250" height="300" align="left">
<img src="Photos/transparentpic.png" width="8" height="300" align="left">

In this part of the lab, we will build upon the concepts learned in the previous lab, specifically in section number 2. We will continue using the same Port F and pin configuration, but this time, we will write and explain the code in assembly language instead of C. The main focus will be on creating a subroutine for the delay function, allowing us to gain a deeper understanding of how to control and fine-tune the LED delay. Additionally, we will explore how assembly language offers precise control over timing and verify the delay using an oscilloscope. By the end of this part, you will have gained practical experience in subroutine creation and controlling timing behavior at a low level using assembly.

According to the Tiva board datasheet, our system bus runs at `16 MHz`, meaning each clock cycle has a period of `62.5 ns`. This defines how long it takes to execute an instruction. In our delay subroutine, the two main instructions involved are `SUBS` and `BNE`. The `SUBS` instruction, which decrements the loop counter, takes `one cycle`, while the `BNE` instruction, which checks whether to branch back, can take between `0 to 3 cycles`, depending on the processor's state.

Through simulation, we observed that `BNE takes approximately 3 cycles per iteration`, whereas on the actual hardware, `it takes 2 cycles`. This discrepancy affects our delay calculations. To achieve a `100 ms delay`, we first determine the total number of required cycles by `dividing 100 ms by 62.5 ns`, resulting in `1.6 million cycles`. In simulation, where each iteration takes `4 cycles (1 for SUBS and 3 for BNE)`, we need a loop count of `400,000`. However, on the actual board, where each iteration takes `3 cycles (1 for SUBS and 2 for BNE)`, we need a loop count of `533,333`. Therefore, when testing in simulation, we initialize our counter with `400,000`, while for real hardware execution, we use `533,333` to ensure an accurate `100 ms delay` for the green LED.

<details>
  <summary>C Code on EK-TM4C123GXL</summary>
<br>

```C
// Here we just saving the base address + offset for each register i will use 
// Port F have the base address 0x40025000 (TM4C123 Data Sheet, 659)

GPIO_PORTF_DATA_R       EQU   0x400253FC            // ???????
GPIO_PORTF_DIR_R        EQU   0x40025400            // (TM4C123 Data Sheet, 633)  
GPIO_PORTF_AFSEL_R      EQU   0x40025420            // (TM4C123 Data Sheet, 671)        
GPIO_PORTF_PUR_R        EQU   0x40025510            // (TM4C123 Data Sheet, 677)
GPIO_PORTF_DEN_R        EQU   0x4002551C            // (TM4C123 Data Sheet, 682)
GPIO_PORTF_AMSEL_R      EQU   0x40025528            // (TM4C123 Data Sheet, 687)
GPIO_PORTF_PCTL_R       EQU   0x4002552C            // (TM4C123 Data Sheet, 688)
SYSCTL_RCGCGPIO_R       EQU   0x400FE608            // (TM4C123 Data Sheet, 340)
                                                    //
        AREA    |.text|, CODE, READONLY, ALIGN=2    //
        THUMB                                       //
        EXPORT  Start                               //
Start                                               //
SWITCH  EQU 0x40025040                              //
LED     EQU 0x40025020                              //
                                                    //
                                                    // Activate clock for Port F
                                                    //
      LDR R1, =SYSCTL_RCGCGPIO_R                    //
      LDR R0, [R1]                                  //
      ORR R0, R0, #0x20                             // Clock for F you need to se the Last bit (OR with the Base address)(0010 0000)
      STR R0, [R1]                                  // (TM4C123 Data Sheet, 340)
      NOP                                           // NOP = No Opration
      NOP                                           // Allow time to finish activating
                                                    // No need to unlock PE2,PE3,PE4
                                                    // Disable analog functionality
      LDR R1, =GPIO_PORTF_AMSEL_R                   // (TM4C123 Data Sheet, 687)
      LDR R0, [R1]                                  //
      BIC R0, R0, #0x18                             // No analog functionality on  PF3,PF4 , BIC stand for Bit Clear so We clear PF3,PF4
      STR R0, [R1]                                  //
                                               	    //
                                                    // Configure as GPIO
      LDR R1, =GPIO_PORTF_PCTL_R                    // (TM4C123 Data Sheet, 688)
      LDR R0, [R1]                                  //
      LDR R2, =0x000FF000                           // Regular function on PF3,PF4
      BIC R0, R0, R2                                //
      STR R0, [R1]                                  // 
                                                    //
                                                    // Set direction register
      LDR R1, =GPIO_PORTF_DIR_R                     //
      LDR R0, [R1]                                  // (TM4C123 Data Sheet, 633)
      ORR R0, R0, #0x08                             // Output on PF3  set bit PF3
      BIC R0, R0, #0x10                             // Input on PF4   set bit PF4
      STR R0, [R1]                                  // 
                                                    // Regular port function
      LDR R1, =GPIO_PORTF_AFSEL_R                   // (TM4C123 Data Sheet, 671)
      LDR R0, [R1]                                  //
      BIC R0, R0, #0x18                             // GPIO on PF3,PF4
      STR R0, [R1]                                  // 
	                                            //
      LDR R1, =GPIO_PORTF_PUR_R                     // (TM4C123 Data Sheet, 677)
      LDR R0, [R1]                                  //
      ORR R0, R0, #0x10                             // Enable pullup on PF4
      STR R0, [R1]                                  //
                                                    // Enable digital port
      LDR R1, =GPIO_PORTF_DEN_R                     // (TM4C123 Data Sheet, 682)
      LDR R0, [R1]                                  //
      ORR R0, R0, #0x18                             // Enable data on PF3,PF4
      STR R0, [R1]                                  //
	                                            //
      LDR R1,=SWITCH                                // R1 = PF4, 0x40025040   
      LDR R2,=LED                                   // R2 = PF3, 0x40025020    
	                                            //
						    //
						    //
clear MOV  R0,#00                                   // LED off
      STR  R0,[R2]                                  //
	                                            //
loop  BL   Delay                                    // 100 ms
      LDR  R0,[R1]                                  // R0 = PF4, 0x00,0x10
      ANDS R0,#0x10                                 //
      BNE  clear                                    // LED off if switch not pressed
      LDR  R0,[R2]                                  //
      EOR  R0,R0,#0x08                              // toggle
      STR  R0,[R2]                                  // if switch pressed
      B    loop                                     //
	                                            //
						    //
						    //
                                                    // 
						    // 4 cycles in simulation, 5 on the real board
Delay LDR  R0,=400000                               // 
wait  SUBS R0,#1                                    // 
      BNE  wait                                     //
      BX   LR                                       //
                                                    //
                                                    //
    ALIGN                                           // Make sure the end of this section is aligned
    END                                             // End of file
```
</details>

<details>
  <summary>Texas Launchpad Simulation</summary>	
<br>

<p align="center">
  <img src="Photos/launchpad100delaynotpressed.png" style="width: 49%; height: 300px;" title="Green LED is Off" /> <img src="Photos/launchpad100delaypressed.png" style="width: 49%; height: 300px;" title="Green LED is On" />
</p>
	
</details>

## Conclusion

// Nour

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





