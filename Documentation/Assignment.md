# ECEN-361 Lab-05: Interprocess Communication Examples

     Student Name:  Fill-in HERE

## Introduction and Objective of the Lab

This lab shows how various independent tasks can communicate through the RTOS-controlled signaling mechanisms. 
It also shows a good practice handling interrupts by using semaphores to signal their respective tasks. 


This lab will create the following Tasks:

1.) SW-Based timer 
2.) Mutex-Controlled CountUp Task ()
3.) 
```c
void Semaphore_Toggle_Task(void *argument);
void NotifyToggleTask(void *argument);
void SW_Timer_Task(void *argument);
void Mutex_CountUpTask(void *argument);
void Mutex_CountDownTask(void *argument);
void UpdateGlobDisplayProcess(void *argument);
void ResetGlobalTask(void *argument);
void SW_Timer_Countdown(void *argument);
```

This lab will demonstrate the following Interprocess Communication mechanisms by the described methods:
### Semaphores
### Mutexes
Mutex_Protected_Count
### Protected Global_Variables
### Notification (Software_Timer)



## Interrupts / Buttons / Semaphores
In a multitasking systems, one way to handle interrupts, such as the buttons on our board generate, is to have them set global flags ("semaphores") that can be looked at by the processes that may be interested in those buttons.  This is a common practice because it allows a single input to be used by any number of processes AND keeps the processing time spent in the ISR to a minimum.  Recall that any time spent in an ISR is out of control of the RTOS, so good code practice is to minimize ISR cycles. 

Looking at the code, you can see that the interrupts from the buttons are handled by simply setting a semaphore (towards the bottom of main.c):

```c

void HAL_GPIO_EXTI_Callback(uint16_t GPIO_Pin)
	{
	// All three buttons generate GPIO  interrupts
	switch(GPIO_Pin)
		{
		case Button_1_Pin:
			// Got the pin -- Give the semaphore
			osSemaphoreRelease(Button_1_SemaphoreHandle);
			break;
...

```

Any process that is waiting for a button can simply hold on the semaphore of interest.  For example, here's a task that just waits for the button, does something, then waits again:

```c
 for(;;)
	  {
		osSemaphoreAcquire(Button_1_SemaphoreHandle,100000);
      /* Got the button, so Do Something here */
		osDelay(1);
	  }
```

This is a good example of a binary semaphore -- a flag used to control access to a shared resource.   Binary semaphores have two operations, namely wait (P) and signal (V) operations.  In CMSIS language, this is "Acquire" and "Release" respectively. 


## Part 1: Create Tasks
Using standard FreeRTOS (CMSIS) calls, create four tasks as follows:

1.)





### Add 2 more timer interrupts that blink LEDs


## Part 1 Questions (2 pts)

Note the speed of D1/D2/D3 – they should seem like a 3-bit binary counter.

Once you have all three LEDs blinking properly, answer the following questions:

1. How fast does D1 turn on/off? [*answer here*]

2. Do all LEDs toggle at *exactly* the same time? [*answer here*]

## Part 2: Changing the clock tree

Change the clock tree to adjust the rates at which the LEDs blink.

1. Open the ioc Configuration GUI
2. Change the APB1 and ABP2-Prescalers to “/8” (Changing both of them guarantees that whatever timer you chose will be affected.)

![A computer screen shot of a diagram Description automatically generated](media/a1a4a08f8ac2f1b714fa0a5456b5e07e.png)

3. Compile and re-run and observe the behavior of the LEDs

## Part 2 Questions (3 pts)

1. What has happened to the speed of the timers? [*answer here*]

2. What is the new frequency of LED D1? [*answer here*]

3. When we changed the frequency, did the Seven-Segment Light update rate change?  (hint, look at the clocks driving the APB1, APB2 buses and which timers are on which bus.  Recall that the Seven-Segment timer is Tim17) [*answer here*]

## Part 3: Reaction Timer (5 pts)

In addition to performing useful tasks at set intervals, timers can also be used to measure elapsed time of an event. The events can be triggered by software, or by a hardware input.

For this part of the lab, we’ll make a small “reaction timer” that measures how fast your hand/eye coordination can be, in milliseconds.

We’ll define the buttons and display as shown:

![](media/2b43c113169efb48ce00225bd55358ff.png)

* **START button (S1):** Initiates a random wait. After the random wait, all the SevenSeg lights go on. As soon as the lights go on, a timer starts counting milliseconds

* **STOP button (S2):** Stops the millisecond reaction timer and shows it on the display

* **FASTEST button (S3):** Extra Credit – This button shows the fastest speed.

Code for this part is organized in the **ReactionTester.c** source file and **main.c**. Fill in between the comments:

```c
/* Student Start HERE */

/* Student End HERE */
```

Read thru the comments in the code. Most of the structure is in place, and you should only have to modify places between Student_Start / Student_End.

Note that for the reaction timer to be accurate, because you changed the prescaler above in Part2, you’ll need to reset it back to the default of no-prescale, x1. 

## Extra-Credit (5pts maximum)

* In the current code, there’s no penalty for “Cheating” by pushing the stop button before all the “Go” lights turn on.  Implement some sort of indicator that the
  Stop button was pushed prematurely.

* Change the “Go” lights to be all of the D1..4 LEDs instead of display all ‘8888’ on the SevenSegments.

* Make the final reaction time flash on/off

If you do any of these items – just mention what and how it worked, [*here*].
