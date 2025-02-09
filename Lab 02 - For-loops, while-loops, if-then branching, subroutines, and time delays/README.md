# <p align="center">For-loops, While-loops, If-then Branching, Subroutines, and Time Delays</p>

This report is Markdown-typed and submitted in Spring 2025 by students [Nour Mostafa](https://github.com/Nour-MK) with ID 2021004938 and [Mohamed Abouissa](https://github.com/Mohamed-Abouissa) with ID 2021005188 in partial fulfillment of the requirements for the Bachelor of Science degree in Computer Engineering. We extend our sincere appreciation to Eng. Umar Adeel for his insightful feedback which has significantly contributed to the successful completion of this experiment.

---

In this lab, we will explore the fundamentals of simple programming structures in assembly language using Keil uVision5 and the [Tiva C LaunchPad (TM4C123) microcontroller](Photos/TM4C123GXL.png). The primary objective is to gain hands-on experience in estimating software execution time and using this information to create a time delay function. Additionally, we will learn how to utilize an oscilloscope to measure and verify time delays in our assembly programs.

Through this lab, we will develop key assembly programming skills such as masking, toggling, implementing conditional if-then statements, creating subroutines, and applying looping constructs. By the end of the lab, we will have acquired practical experience in assembly language programming and time-based control in embedded systems using the TM4C123 microcontroller.

## Part 1: Toggling the Green LED With a Switch

<img src="Photos/part1.gif" width="250" height="300" align="left">
<img src="Photos/transparentpic.png" width="8" height="300" align="left">

In this part of the lab, we will build upon the concepts learned in the previous lab, specifically in section number 2. We will continue using the same Port F and pin configuration, but this time, we will write and explain the code in assembly language instead of C. The main focus will be on creating a subroutine for the delay function, allowing us to gain a deeper understanding of how to control and fine-tune the LED delay. Additionally, we will explore how assembly language offers precise control over timing and verify the delay using an oscilloscope. By the end of this part, you will have gained practical experience in subroutine creation and controlling timing behavior at a low level using assembly.

<details>
  <summary>C Code on EK-TM4C123GXL</summary>
<br>

```C
// The libraries that we need
#include <stdint.h>
#include "tm4c123gh6pm.h"

#define GPIO_PORTF_DATA_R       (*((volatile unsigned long *)0x400253FC))
#define GPIO_PORTF_DIR_R        (*((volatile unsigned long *)0x40025400))
#define GPIO_PORTF_AFSEL_R      (*((volatile unsigned long *)0x40025420))
#define GPIO_PORTF_DEN_R        (*((volatile unsigned long *)0x4002551C))
#define GPIO_PORTF_AMSEL_R      (*((volatile unsigned long *)0x40025528))
#define GPIO_PORTF_PCTL_R       (*((volatile unsigned long *)0x4002552C))
#define SYSCTL_RCGCGPIO_R       (*((volatile unsigned long *)0x400FE608))
#define SYSCTL_PRGPIO_R         (*((volatile unsigned long *)0x400FEA08))
#define SYSCTL_RCGC2_GPIOF      0x00000020  // port F Clock Gating Control
#define SYSCTL_RCGC2_R          (*((volatile unsigned long *)0x400FE108))
	
//Function Prototypes

void PortF_Init(void);		
void Delay(void);

int main(void){    
  PortF_Init();    			// Call initialization of Port F
 
  while(1){
                                         // My green LED is on Port F pin #3 that mean we need to edit the fourth bit only to work on the green LED
      GPIO_PORTF_DATA_R = 0x08;          // ---- ---- ---- ---- ---- ---- 0000 1000 For That mean we writing the value 1 (Which mean we drive voltege to it) on Pin PF3 (Green LED on)  
                                         // (TM4C123 Data Sheet, 662 - 663)
																				 
		
      Delay();				 // Calling the delay function to wait for 0.1 sec (Read the Clock part on the introduction)
		
      GPIO_PORTF_DATA_R = 0x00;    	 // ---- ---- ---- ---- ---- ---- 0000 0000 For That mean we writing the value 0 (Which mean it conected to the ground) on Pin PF3 (Green LED off)  
		
      Delay();                         	 // wait 0.1 sec (Read the Clock part on the introduction)
  }
}

                                         // The function to initialize port F pins for input and output
void PortF_Init(void){ 
	
  SYSCTL_RCGC2_R= 0x00000020;            // 0000 0000 0000 0000 0000 0000 0010 0000  This for enabling the Prot F clock (Port F,E,D,C,B and A) (10 0000 = 0x20)
                                         // To Enable any port just sit the corresponding bit to the order in the alphabet
                                         // (TM4C123 Data Sheet, 340 - 341)
	
  GPIO_PORTF_AMSEL_R = 0x00;             // ---- ---- ---- ---- ---- ---- 0000 0000 For Disabling the analog function (Becuse we are dealing only with the Digital function in this part)
                                         // (TM4C123 Data Sheet, 687)
	
  GPIO_PORTF_PCTL_R = 0x00000000;        // 0000 0000 0000 0000 0000 0000 0000 0000 We use this register when we have alternate function or dealing with signals but here we clear it all because 
                                         // we going do you our pin in the digital mode
                                         // (TM4C123 Data Sheet, 688 -689)
	
  GPIO_PORTF_DIR_R = 0x08;               // ---- ---- ---- ---- ---- ---- 0000 1000  We just sit pin 3 (Green LED) to be in the OUTPUT mode (DIR regester is to choose our pin mode)
                                         // To make my pin in input mode we clear the bit but if we wanted to be in the output mode we sit the bit
                                         // Above we sit the fourth bit (Which mean PF3 because we strat from PF0 to PF7)
                                         // (TM4C123 Data Sheet, 663)
	
  GPIO_PORTF_AFSEL_R = 0x00;             // ---- ---- ---- ---- ---- ---- 0000 0000  No alternate function (The associated pin functions as a peripheral signal and is
                                         // controlled by the alternate hardware function if it is sit to 1) so we dont want this so we just clear it
                                         // (TM4C123 Data Sheet, 671 - 672)
	
  GPIO_PORTF_DEN_R = 0x08;               // ---- ---- ---- ---- ---- ---- 0000 1000  Enable digital pins PF3 (The DEN register is use to enable the selected pins) here we just want PF3 to 
                                         // enabled so we sit the fourth bit (PF3)
                                         // (TM4C123 Data Sheet, 682 - 683)
}
// The delay Fucntion
void Delay(void){

unsigned long  time;                     // Variable called time
	
  time = 1600000;                        // 0.1 sec  (Read the Clock part on the introduction)
	
  while(time!=0){                        // When the time go to Zero it will exit the function
    time--;
  }
}
```
