<p align="center">
  <img src="Photos/exp6.gif"/>
</p>

This report is Markdown-typed and was submitted in Spring 2025 by students [Nour Mostafa](https://github.com/Nour-MK) with ID 2021004938 and [Mohamed Abouissa](https://github.com/Mohamed-Abouissa) with ID 2021005188 in partial fulfillment of the requirements for the Bachelor of Science degree in Computer Engineering. We extend our sincere appreciation to Eng. Umar Adeel for his insightful feedback, which has significantly contributed to the successful completion of this experiment. Sure! Here's a more polished version in Markdown: To view this report in light or dark mode, adjust your appearance settings [here](https://github.com/settings/appearance).

---

Introduction 

## Hardware Implementation

## Keil Simulation

## C Code on EK-TM4C123GXL

```c
#include "tm4c123gh6pm.h"  				// Include header file for TM4C123 microcontroller
#include "PLL.h"           				// Include header file for Phase-Locked Loop (PLL) configuration
#include "Sound.h"         				// Include header file for sound functions
#include "Music.h"         				// Include header file for music functions
#include "Switch.h"        				// Include header file for switch input functions
																																		// Define macros for accessing specific PortF pins
#define PF4  (*((volatile unsigned long *)0x40025040)) 	// PF4 (Switch 1)
#define PF2  (*((volatile unsigned long *)0x40025010)) 	// PF2 (Used for toggling in ISR)
#define PF0  (*((volatile unsigned long *)0x40025004)) 	// PF0 (Switch 2)

							// Basic functions defined at end of startup.s
void EnableInterrupts(void);  				// Enable interrupts
void Delay1ms(int time);      				// blind wait

int main(void){    
  
int voice;						// Variable to store the current voice selection
  PLL_Init();          					// Initialize PLL to set the system clock to 80 MHz
  Switch_Init();       					// Initialize switches (Port F)
  DAC_Init();          					// Initialize Digital-to-Analog Converter (DAC) on Port B
  Sound_Init(50000);   					// Initialize SysTick timer for sound generation
  Piano_Init();        					// Initialize piano keys on Port E
  Sound_Voice(SineWave);  				// Set the default voice waveform to SineWave
  voice=0;						// Start with voice selection 0
 
  EnableInterrupts();					// Enable global interrupts
	
  while(1){                				// Infinite loop
    switch(Piano_In()){					// Read input from piano keys and generate corresponding tones
      case 1 : Sound_Tone(C0); break;			// Play note C0
      case 2 : Sound_Tone(D);  break;			// Play note D
      case 4 : Sound_Tone(E);  break;			// Play note E
      case 8 : Sound_Tone(G);  break;			// Play note G
      default : Sound_Off();				// Turn off sound if no key is pressed
    } 
		
																																		// Read input from switches to change voice or play music
    switch(Switch_In()){
      case 1  :   					// If SW2 (PF0) is pressed
          voice = (voice+1)%6; 				// Cycle through 6 different voice waveforms
          switch(voice){
            case 0  : Sound_Voice(SineWave); 
		voice = (voice+1)%6; 			// Immediately move to next voice
            case 1  : Sound_Voice(Trumpet);  
		break;   
            case 2  : Sound_Voice(Horn);     
		break;   
            case 3  : Sound_Voice(Bassoon);  
		break;  
            case 4  : Sound_Voice(Flute);    
		break;   
            case 5  : Sound_Voice(Guitar);   
		break;   
          } 
		break;
            case 16 : Music_PlayMario();     		// If SW1 (PF4) is pressed // Play Mario theme music
		break;   																			
    } 
    Delay1ms(25);           				// Debounce delay to prevent switch bouncing
    PF2 ^= 0x04; 					// Toggle PF2 (used for debugging or profiling)
  }             
}
																																		// Function to create a simple delay (very approximate)
void Delay1ms(int time){ int i;
  for(;time;time--){					// Loop for the specified time in milliseconds
    for(i=0;i<1200;i++) Delay();			// Inner loop to create delay
  }
}
```

## Conclusion

## Resources
