<p align="center">
  <img src="Photos/exp5.gif"/>
</p>

This report is Markdown-typed and was submitted in Spring 2025 by students [Nour Mostafa](https://github.com/Nour-MK) with ID 2021004938 and [Mohamed Abouissa](https://github.com/Mohamed-Abouissa) with ID 2021005188 in partial fulfillment of the requirements for the Bachelor of Science degree in Computer Engineering. We extend our sincere appreciation to Eng. Umar Adeel for his insightful feedback, which has significantly contributed to the successful completion of this experiment. Sure! Here's a more polished version in Markdown: To view this report in light or dark mode, adjust your appearance settings [here](https://github.com/settings/appearance).

---

This lab focuses on several key objectives, including understanding and implementing linked data structures, learning how to create a software system, and studying real-time synchronization by designing a finite state machine controller. Throughout the lab, essential software skills will be developed, such as advanced indexed addressing, working with linked data structures, creating fixed-time delays using the SysTick timer, and debugging real-time systems. These concepts will provide a strong foundation for designing efficient and reliable embedded systems.

The experiment focuses on designing a digital control system for a two-street traffic light intersection using the [Tiva C (TM4C123) microcontroller](Photos/TM4C123GXL.png). The intersection consists of two one-way streets, labeled North and East, for northbound and eastbound vehicles, respectively. The goal is to create a functional traffic light system that detects the presence of vehicles using sensors and controls six LEDs representing the red, yellow, and green lights for each direction.

The system operates based on two inputs: a North Sensor and an East Sensor. The North Sensor is activated when one or more cars approach the intersection from the south, and the East Sensor functions similarly for eastbound traffic. These sensors are simulated using buttons, where pressing a button indicates the presence of a vehicle, and releasing it signifies an empty road. The system also includes six LED outputs to control the traffic lights for both directions, ensuring proper signaling. While the design does not aim to optimize traffic flow or minimize waiting times like a real-world traffic management system, it must be able to handle multiple vehicle requests by cycling through them efficiently. The primary focus is on building a structured and adaptable computer engineering solution that is easy to understand and modify.

The implementation will utilize a linked data structure stored in ROM, where each finite state machine (FSM) state corresponds directly to an element in the data structure. A complete solution is expected to include between ten and thirty FSM states, allowing the system to respond dynamically to sensor inputs. Since real-world intersections require significant wait times, this project will accelerate the traffic flow, making it more convenient for debugging and demonstration purposes.

Equipment essential for this experiment includes the [red LED](https://www.mouser.com/datasheet/2/239/lite-on_lite-s-a0003806513-1-1737505.pdf), [yellow LED](https://users.ece.utexas.edu/~valvano/Datasheets/LED_yellow.pdf), [green LED](https://users.ece.utexas.edu/~valvano/Datasheets/LED_green.pdf), popular PE-74N breadboard, switches, male-male and female-male wires, complemented by the Keil uVision 5 IDE. 

## Hardware Implementation

<p align="center">
  <img src="Photos/main-demo.gif" style="width: 1000px" title="Real Life Test"  />
</p>

Demonstrated above is the functional traffic light system. Note that the photo is flipped 180 degrees which makes it seems as though the North traffic light is at the North even though it should be at the South since it is serving the Northbound vehicles coming from the South. Although we've already been handed a [plan](Photos/plan.png) for the input/output pins to use, if you decide which port pins you will use for the inputs and outputs, keep in mind that you must void the pins with hardware already connected. Refer to this diagram to know the [pins' connections](Photos/pins.jpg). 

A well-designed Finite State Machine (FSM) exhibits several key qualities that contribute to its effectiveness and efficiency in various applications. Firstly, clarity and simplicity are essential; a good FSM should have clearly defined states and transitions, making it easy to understand and maintain. This clarity ensures that developers can quickly grasp its behavior and make necessary modifications without confusion. Secondly, robustness is crucial; the FSM should handle all possible inputs and transitions gracefully, avoiding unintended behaviors or errors. This reliability is achieved through thorough testing and validation across different scenarios. Thirdly, flexibility is valuable; a good FSM should be adaptable to changes in requirements or environments, allowing for scalability and future-proofing. This flexibility ensures that it can evolve alongside the system it controls, accommodating new features or conditions seamlessly. Lastly, efficiency is key; an optimal FSM minimizes resource usage, such as memory and processing power, while still executing tasks promptly. This efficiency is particularly crucial in real-time applications where responsiveness is critical. Together, these qualities define a well-rounded FSM that not only performs its intended functions reliably but also remains adaptable and efficient in dynamic environments. The following is the state transition table based on which we draw our [finite state machine diagram (FSM)](Photos/fsm.png).

<p align="center">
  <img src="Photos/table.png" style="width: 1000px" title="State Transition Table" />
</p>

The table above tells a clear story about how traffic light states transition based on switch inputs, which simulate a sensor detecting the presence of cars on the road. Each switch represents a different traffic direction: 00 means no cars are detected, 01 indicates cars are waiting on the eastbound road, 10 signals cars are waiting on the northbound road, and 11 shows that cars are present in both directions. When the system is in the Go North state, it means the North switch was previously pressed, indicating that northbound cars are waiting at the traffic light. In this state, the traffic signal allows vehicles on the northbound road to proceed with a green light while the eastbound road remains red. However, if the East switch is pressed while we are in the Go North state, the system recognizes that eastbound cars are now also waiting. Before allowing them to proceed, we must first transition through a Wait North state, where the northbound green light turns yellow, signaling an impending stop. After this brief delay, we transition to the Go East state, where the eastbound traffic light turns green, allowing those vehicles to move while the northbound light switches to red. As long as the East switch remains pressed, the system will stay in the Go East state, maintaining eastbound traffic flow. However, if at any point the North switch is pressed while in this state, indicating that new northbound traffic has arrived, the system will transition to a Wait East state. Here, the eastbound green light turns yellow momentarily to signal a stop before switching to red. Finally, the system transitions back to the Go North state, where the northbound traffic light turns green again, allowing vehicles in that direction to proceed. This cyclic process ensures that traffic is managed efficiently based on real-time vehicle demand, preventing unnecessary delays while ensuring smooth transitions between the two roads.

Note that when going from $\color{#007c00}{\textsf{green}}$ to $\color{#f70000}{\textsf{red}}$ we trasition using $\color{#f7f701}{\textsf{yellow}}$ first but when we go from $\color{#f70000}{\textsf{red}}$ to $\color{#007c00}{\textsf{green}}$ it is __direct__ and if you press a swicth and you wre already in its state nothing happens but when you press the switch and you're not in its state the traffic lights will respond and transition their state. If both switches are pressed at the same time it will keep switching between the states continusously. For a clearer view of the practical connection, check this [schema](Photos/fritzing.png). To recall the breadboard's internal connections setup, click [here](Photos/recall.jpg).

## Keil Simulation

<p align="center">
  <img src="Photos/debug.png" style="width: 1000px" title="Simulation Test"/>
</p>

To experimentally prove that the finite state machine (FSM) controlling the traffic light system functions as intended, we need to collect real-time data on its behavior and compare it against the expected transitions. The most critical data includes the state of the North and East switches at every time step, the state transitions the FSM undergoes, the traffic light outputs corresponding to each state, the duration spent in each state, and the response time to input changes. By logging these details, we can verify that the system correctly reacts to traffic demand and adheres to predetermined timing constraints. This data can be gathered through an integrated logging mechanism within the FSM software, where each state transition and input change is recorded. Additionally, physical testing on an FPGA or microcontroller using LEDs to represent traffic lights would allow direct observation and validation of system behavior.

If an accident were to occur, proving that the FSM functioned correctly would require presenting logged data demonstrating that the system transitioned between states as designed. A timestamped log of state transitions and inputs would be crucial in showing that the software made decisions based on real-time sensor data. Running a simulation or replaying recorded inputs could further reinforce that the FSM responded as expected. Additionally, verifying that there were no undefined or unintended state transitions would serve as strong evidence that the system adhered to its programmed logic. By presenting this information, we could theoretically demonstrate to a judge and jury that the FSM-controlled traffic light system was operating correctly and was not the cause of the accident.

The FSM implemented in our system is a Mealy machine, meaning that the outputs (traffic light signals) depend not only on the current state but also on the real-time inputs from the switches. This allows for a more dynamic response to changing traffic conditions. Other types of FSMs include Moore machines, where outputs depend solely on the current state, making transitions more predictable but sometimes requiring additional states to achieve the same functionality. Hybrid FSMs combine characteristics of Mealy and Moore models, offering a balance between complexity and efficiency, while hierarchical FSMs introduce nested states to manage complex behaviors in a structured manner.

Our FSM consists of four states: Go North, Wait North, Go East, and Wait East. Each state transitions based on the input from the North and East switches. The FSM has a total of six directed transition arrows connecting the states. This structured arrangement ensures that every possible traffic scenario is accounted for, allowing the system to manage the flow of vehicles efficiently. The FSM is implemented using a linked data structure, where each state is represented as a node with directed edges pointing to possible next states. This approach allows for efficient state transitions, as each state node contains pointers to its valid successors based on input conditions. By using this structure, the system can dynamically update the current state in response to traffic demand while maintaining a clear and maintainable design. Whether represented as a state transition table in software or as a finite state register in hardware, this implementation ensures that the traffic light system remains responsive, predictable, and easily verifiable.

## C Code on EK-TM4C123GXL

The `SysTick_Wait` function creates a delay using a **busy-wait loop**, relying on the SysTick timer of the TM4C123GH6PM microcontroller. It waits for a specified number of clock cycles before allowing the program to proceed. The function begins by capturing the **starting time** from `NVIC_ST_CURRENT_R`, which holds the current value of the SysTick timer. This serves as the reference point for measuring elapsed time. Since the SysTick timer is a **24-bit down counter**, its value decreases on every clock cycle until it reaches zero and wraps around.   To determine when the requested delay has elapsed, the function enters a **loop**, where it continuously calculates the time difference between the starting value and the current value of the SysTick timer. This is done using the expression `(startTime - NVIC_ST_CURRENT_R) & 0x00FFFFFF`, ensuring that only the lower 24 bits are considered. The loop runs until the elapsed time surpasses the specified delay. Because the function operates at the system clock speed, the actual delay duration depends on the **clock frequency**. For instance, with a **50 MHz system clock**, each cycle takes **20 nanoseconds**, meaning that a delay of **500,000 cycles** corresponds to **10 milliseconds**. The `SysTick_Wait10ms` function builds upon this by repeatedly calling `SysTick_Wait(500000)` in a loop. This allows the creation of a delay in **multiples of 10 milliseconds**. For example, if `SysTick_Wait10ms(5)` is called, it executes `SysTick_Wait(500000)` five times, resulting in a total delay of **50 milliseconds**. While this approach effectively introduces delays, it is not the most efficient method, as the **busy-wait loop** keeps the CPU occupied instead of allowing it to perform other tasks or enter a low-power state using **interrupt-driven delays**.

If we want to create a 1-second delay without using a hardware timer and without relying on a busy-wait loop, we can take advantage of software-based clock cycles using instruction execution time. Instead of a simple decrementing loop, we can use nested loops or NOP (No Operation) instructions to create more controlled delays while keeping CPU usage minimal. The code would define a simple delay function, `Delay_1s()`, that creates an approximate 1-second delay using nested loops and the nop (No Operation) assembly instruction. The outer loop runs 1,000 times, and within each iteration, the inner loop executes 10,000 times, effectively multiplying the total number of iterations. The `nop` instruction ensures a consistent execution time for each iteration, preventing the compiler from optimizing the loop away. Since the function does not use a hardware timer, it relies on the processor's clock speed to determine the actual delay duration, meaning it may require fine-tuning for different system frequencies. To prove that the Delay_1s() function actually creates a 1-second delay, we need to measure its execution time using various techniques. One approach is to use an oscilloscope or logic analyzer by modifying the code to toggle a GPIO pin before and after calling the function. By measuring the time between successive pulses, we can confirm whether the delay matches 1 second. Another method is to compare the function’s runtime against a hardware timer by recording the timer value before and after executing `Delay_1s()`. A simpler, yet less precise, verification method involves running the function in a loop, calling it ten times, and using a stopwatch to check if the total runtime is approximately ten seconds. Since the accuracy of this delay depends on the processor’s clock speed and loop iteration count, calibration may be needed to fine-tune the delay for different hardware platforms.

To add more input or output signals to the traffic light system, we need to modify both the hardware connections and the software implementation, particularly the finite state machine (FSM). For additional input signals, such as a detector for a southbound road or a pedestrian crossing request, we must connect new sensors to available GPIO pins. The `#define SENSOR` macro should be updated to accommodate the extra input bits, and the FSM’s Next array must be adjusted to handle the new input combinations. Since the current system processes a 2-bit input (PE1 and PE0), adding more sensors requires expanding the input width, such as using a 3-bit or 4-bit configuration to represent all possible scenarios. For additional output signals, such as traffic lights for a newly added road, we must define extra traffic light controls and connect them to unused GPIO pins. The `#define LIGHT` macro must be modified to include these new output bits, and the `Out` field in the FSM states should be expanded to control the additional signals properly. Every FSM state must be updated to ensure that the new lights behave in sync with the existing traffic signals while managing new road directions or pedestrian crossings.

When the C compiler aligns objects in memory, it places variables at memory addresses that are multiples of their data type's alignment requirement. This means that certain data types, such as integers or doubles, are stored at addresses that conform to specific boundaries, typically powers of two. For example, a 4-byte integer is often stored at an address that is a multiple of 4, while an 8-byte double is stored at an address that is a multiple of 8. This alignment ensures that the CPU can access these variables efficiently, as many processors are optimized to fetch data from aligned addresses in fewer memory operations. The compiler performs alignment to improve performance and avoid hardware penalties. Some processors impose strict alignment requirements, meaning that accessing misaligned data could cause a fault or require multiple memory accesses, slowing down execution. Even on architectures that allow misaligned access, unaligned memory operations may require additional processing cycles, making them less efficient. Moreover, alignment helps with vectorized operations and cache efficiency, as properly aligned data is more likely to be fetched in fewer cache lines, reducing memory latency. While alignment may introduce padding bytes between variables to satisfy these requirements, the trade-off in memory space is often justified by the performance gains.

```c
#include <stdint.h>
#include "PLL.h"        																							// Header file for configuring the system clock
#include "SysTick.h"    																							// Header file for configuring the SysTick timer

																																			// Define memory-mapped register addresses for controlling GPIOs
#define LIGHT                   (*((volatile uint32_t *)0x400050FC))  // PB5-PB0 output (traffic lights)
#define GPIO_PORTB_DIR_R        (*((volatile uint32_t *)0x40005400))  // Direction register for Port B
#define GPIO_PORTB_AFSEL_R      (*((volatile uint32_t *)0x40005420))  // Alternate function select for Port B
#define GPIO_PORTB_DEN_R        (*((volatile uint32_t *)0x4000551C))  // Digital enable for Port B
#define GPIO_PORTB_AMSEL_R      (*((volatile uint32_t *)0x40005528))  // Analog mode select for Port B
#define GPIO_PORTB_PCTL_R       (*((volatile uint32_t *)0x4000552C))  // Port control register for Port B

#define SENSOR                  (*((volatile uint32_t *)0x4002400C))  // PE1-PE0 input (car detectors)
#define GPIO_PORTE_DIR_R        (*((volatile uint32_t *)0x40024400))  // Direction register for Port E
#define GPIO_PORTE_AFSEL_R      (*((volatile uint32_t *)0x40024420))  // Alternate function select for Port E
#define GPIO_PORTE_DEN_R        (*((volatile uint32_t *)0x4002451C))  // Digital enable for Port E
#define GPIO_PORTE_AMSEL_R      (*((volatile uint32_t *)0x40024528))  // Analog mode select for Port E
#define GPIO_PORTE_PCTL_R       (*((volatile uint32_t *)0x4002452C))  // Port control register for Port E

#define SYSCTL_RCGCGPIO_R       (*((volatile uint32_t *)0x400FE608))  // Clock gating control for GPIO
#define SYSCTL_PRGPIO_R         (*((volatile uint32_t *)0x400FEA08))  // GPIO peripheral ready status

																																			// Define the finite state machine (FSM) structure
struct State {
  uint32_t Out;      																									// 6-bit output representing the traffic light state
  uint32_t Time;     																									// Time delay in 10ms units
  uint8_t Next[4];   																									// Next state transitions based on 2-bit sensor input
};

typedef const struct State STyp;

																																			// Define state names for better readability
#define goN   0  																											// Green for north, Red for east
#define waitN 1  																											// Yellow for north, Red for east
#define goE   2  																											// Green for east, Red for north
#define waitE 3  																											// Yellow for east, Red for north

																																			// Define FSM states
STyp FSM[4]={
																																			//  { Out, Time,   Next states based on sensor input [00, 01, 10, 11] }
  {0x21, 30, {goN, waitN, goN, waitN}}, 															// goN state: Green North, Red East
  {0x22,  5, {goE, goE, goE, goE}},     															// waitN state: Yellow North, Red East
  {0x0C, 30, {goE, goE, waitE, waitE}}, 															// goE state: Green East, Red North
  {0x14,  5, {goN, goN, goN, goN}}      															// waitE state: Yellow East, Red North
};

int main(void){
  uint8_t n;         																									// Variable to store the current state
  uint32_t Input;    																									// Variable to store sensor input
  
  PLL_Init(Bus80MHz);               																	// Initialize the system clock to 80 MHz
  SysTick_Init();                   																	// Initialize SysTick timer for delays
  
  SYSCTL_RCGCGPIO_R |= 0x12;        																	// Enable clock for Port E (bit 4) and Port B (bit 1)
  
																																			// Wait until the GPIO ports are ready
  while((SYSCTL_PRGPIO_R & 0x12) == 0){};

																																			// Configure Port B (PB5-PB0) as output (traffic lights)
  GPIO_PORTB_DIR_R |= 0x3F;         																	// Set PB5-PB0 as outputs
  GPIO_PORTB_AFSEL_R &= ~0x3F;      																	// Disable alternate functions for PB5-PB0
  GPIO_PORTB_DEN_R |= 0x3F;         																	// Enable digital functionality for PB5-PB0
  GPIO_PORTB_PCTL_R &= ~0x00FFFFFF; 																	// Configure PB5-PB0 as GPIO
  GPIO_PORTB_AMSEL_R &= ~0x3F;      																	// Disable analog functionality on PB5-PB0

																																			// Configure Port E (PE1-PE0) as input (car detectors)
  GPIO_PORTE_DIR_R &= ~0x03;        																	// Set PE1-PE0 as inputs
  GPIO_PORTE_AFSEL_R &= ~0x03;      																	// Disable alternate functions for PE1-PE0
  GPIO_PORTE_DEN_R |= 0x03;         																	// Enable digital functionality for PE1-PE0
  GPIO_PORTE_PCTL_R &= 0xFFFFFF00;  																	// Configure PE1-PE0 as GPIO
  GPIO_PORTE_AMSEL_R &= ~0x03;      																	// Disable analog functionality on PE1-PE0

  n = goN;                          																	// Start in the goN state (Green for North, Red for East)

  while(1){
    LIGHT = FSM[n].Out;             																	// Set traffic lights based on current FSM state
    SysTick_Wait10ms(FSM[n].Time);  																	// Wait for the specified time (FSM[n].Time * 10ms)
    Input = SENSOR;                 																	// Read sensor input (PE1-PE0) to determine car presence
    n = FSM[n].Next[Input];         																	// Transition to the next state based on sensor input
  }
}

```

## Conclusion

There are many civil engineering questions that arise when designing a traffic light system, and how these questions are addressed can determine the effectiveness of the solution and the quality of the civil engineer. One key question is how long each state should last. The timing should balance responsiveness with predictability, ensuring smooth transitions while allowing enough time for vehicles to pass safely. Another consideration is what happens if multiple sensors are activated at the same time. The best approach is to cycle through the requests in a round-robin fashion, servicing one direction before moving to the next to ensure fairness and prevent starvation. If a car sensor is activated but released before being serviced, the system should ignore the request, as the car may have stopped and legally turned, or it could process the request depending on when it was registered. A more practical approach is to ensure that once a request is recorded, it remains in the queue until serviced, preventing any missed detections. Another scenario to address is when no cars are present, and the light remains green for northbound traffic. If a car arrives from the east, the system can either recognize the new input immediately or wait until the current cycle ends. The most efficient solution is to wait until the end of the current timer, ensuring that ongoing traffic is not disrupted. However, for long wait times, breaking the state into multiple shorter states with the same output can improve responsiveness without unnecessary delays. Each of these decisions contributes to a well-structured and adaptable system, ensuring a reliable and efficient traffic control mechanism.

In real products that we market to consumers, we put the executable instructions and the finite state machine linked data structure into the nonvolatile memory such as Flash. A good implementation will allow minor changes to the finite machine (adding states, modifying times, removing states, moving transition arrows, changing the initial state) simply by changing the linked data structure without changing the executable instructions. Making changes to executable code requires you to debug/verify the system again. If there is a 1-1 mapping from FSM to linked data structure, then if we just change the state graph and follow the 1-1 mapping, we can be confident that our new system operates the new FSM properly. Obviously, if we add another input sensor or output light, it may be necessary to update the executable part of the software, re-assemble or re-compile and retest the system.

The mathematical equation used to determine the next state in the traffic light system is based on the current state and the input from the sensors. This is represented by the equation: $\text{Next State} = \text{FSM}[n].\text{Next}[\text{Input}]$, where $n$ is the current state, and $\text{Input}$ is a 2-bit value read from the sensors (PE1 and PE0), calculated as $\text{Input} = (\text{PE1} \times 2) + \text{PE0}$. Since the input is 2 bits, it can take four possible values (0 to 3), and each state in the FSM table contains an array with four next-state values corresponding to these inputs. The system transitions by using the input value as an index into the state's `Next` array, determining the next state dynamically. For example, if the system is in state `goN` (state 0) and the input is `10` (PE1 = 1, PE0 = 0, decimal 2), the next state is given by `FSM[0].Next[2]`, which is `goN`, meaning the system remains in the same state. In general, the next state is computed using the formula: $\text{Next State} = \text{FSM}[n].\text{Next}[(\text{PE1} \times 2) + \text{PE0}]$, ensuring that state transitions follow the predefined FSM logic based on real-time sensor inputs.


All in all, we designed and implemented a digital control system for a two-street traffic light intersection. The system utilized a finite state machine (FSM) to manage traffic light transitions based on sensor inputs, simulating real-time traffic conditions. Key achievements included the development of a linked data structure stored in ROM, the creation of a functional FSM, and the implementation of real-time synchronization using the SysTick timer. The system demonstrated the ability to handle multiple vehicle requests efficiently, transitioning between states dynamically based on sensor inputs. Applications of this work in the industry include the design of embedded systems for traffic management, where real-time responsiveness and reliability are critical. The skills developed in this lab, such as advanced indexed addressing, working with linked data structures, and debugging real-time systems, are directly applicable to the development of efficient and scalable embedded systems in various fields, including automotive, smart city infrastructure, and IoT devices. The experiment also highlighted the importance of modular design, allowing for easy modifications to the FSM without altering the executable code, which is a valuable practice in industrial software development.

This same codebase can be adapted to perform other varied tasks with slight modifications, showcasing its flexibility and scalability. For instance, the delay between state transitions can be easily adjusted by modifying the timing parameters in the `SysTick_Wait` function, allowing for longer or shorter intervals to better simulate real-world traffic conditions or optimize flow. Additionally, pedestrian walk or stop states can be incorporated by expanding the finite state machine (FSM) to include new states and transitions, integrating pedestrian buttons as additional inputs and orange and red LEDs to signal pedestrian walk and stop as outputs. This would enable the system to halt vehicle traffic temporarily and allow safe pedestrian crossing. Furthermore, the system can be extended to include two additional roads for southbound and westbound traffic by expanding the FSM to accommodate four directions. This would involve adding more sensors, outputs for additional traffic lights, and updating the state transition logic to manage the increased complexity.

## Resources

[1] Cortex-M4 Technical Reference Manual. (2009). <br> https://users.ece.utexas.edu/~valvano/EE345L/Labs/Fall2011/CortexM4_TRM_r0p1.pdf  
[2] FSM Diagram—Draw.io. (n.d.). <br> https://app.diagrams.net/  
[3] Jonathan Valvano (Director). (2015, January 27). C10 4D Traffic light FSM [Video recording]. <br> https://www.youtube.com/watch?v=kgABPjf9qLI  
[4] Starter files for embedded systems. (n.d.). <br> https://users.ece.utexas.edu/%7Evalvano/arm/  
[5] Texas Instruments Incorporated. (2014). Tiva™ TM4C123GH6PM Microcontroller data sheet. Texas Instruments Incorporated. <br> https://www.ti.com/lit/ds/symlink/tm4c123gh6pm.pdf  
[6] Texas Instruments Incorporated. (2013). Tiva™ C Series TM4C123G LaunchPad (User's Guide). Texas Instruments Incorporated. <br>  https://www.ti.com/lit/ug/spmu296/spmu296.pdf  
[7] UT.6.01x C10 Finite System Machines. (n.d.). YouTube. <br> http://www.youtube.com/playlist?list=PLyg2vmIzGxXEle4_R2VA_J5uwTWktdWTu  
[8] Valvano, J. W. (2014). Embedded systems: Introduction to ARM® Cortex-M microcontrollers (5th ed., Vol. 1). Self-published. <br> https://users.ece.utexas.edu/~valvano/Volume1/E-Book/   
[9] Fritzing. (n.d.). <br> https://fritzing.org/


<br>

```mermaid
gantt
    title Work Division Gantt Chart
    tickInterval 1day
    todayMarker off
    axisFormat %a-%Y-%m-%d
    section Preparation         
        Nour Mostafa : crit, 2025-02-20 00:00, 02h
        Mohamed Abouissa : crit, 2025-02-20 00:00, 02h
    section Keil         
        Mohamed Abouissa : crit,2025-02-21 00:00, 1d
    section Results       
        Nour Mostafa : crit, 2025-02-21 00:00, 1d
    section Report
        Nour Mostafa : crit, 2025-03-01 00:00, 2d
        Mohamed Abouissa : crit, 2025-03-02 00:00, 2d
```

This publication adheres to all regulatory laws and guidelines established by the [American University of Ras Al Khaimah (AURAK)](https://aurak.ac.ae/) regarding the dissemination of academic materials.

