# Interrupt_Controller
Author: Vikas Kumar <vikasrajput1306@gmail.com>

This is a 8-input interrupt controller which works in two modes.
Mode 1: Polling
Mode 2: Priority 

Polling: In polling, all the interrupts are checked periodically by interrupt controller
and serviced if found active.
Priority: In priority mode, the interrupts are assigned in a priority order by the uP and 
according to the priority, interrupts are serviced.

Working: 
     After Reset, the controller waits for the commands from the
    processor on the bus. The lowest 2 bits determine the mode of
    operation.

    01 - Polling mode
    10 - Priority mode.

    It keeps waiting for valid input mode.

Polling Mode:
For 1 clk cycle,uP drives the bus as xxxx_xx01. Controller then enters the polling mode.
Then it starts checking for the interrupt sources.
If interrupt found, intr_out=1 (by controller)
then controller waits for the acknowledgement from the processor.
Processor gives acknowledgement by making a transition on intr_in:
High-Low-High for exactly one clk cycle.
Now controller starts driving the bus with the ISR of the interrupt to be served.
ISR format: 01011_intrID.
Every time controller sends anything on the data bus, uP acknowledges it by making transition on intr_in.
After that controller waits for the confirmation from the uP that the interrupt has been serviced.
Once ISR is completed, uP sends confirmation on intr_in as well as on the data bus with the condition code of 10100_intrID.
This intrID should match with the one sent by the controller. If there is a mismatch, controller goes to the reset state.
If no mismatch found in the condition code, controller continues to check for the next interrupt in polling mode.


Priority Mode:
Working is almost similar to the polling mode.
The difference is that the interrupts are serviced according to their priority order.

After reset: uP drives the bus with xxxyyy10 to make the controller work in priority mode.
And priority of the interrupts are also set by the uP in next 4 commands.
xxx is the ID of the highest priority interrupt.
yyy is the ID of the second to highest priority interrupt.
To set the priority for 8 interrupts, uP has to give the commands for the 4 continuous clk cycle.
After setting the priority order, controller checks for the interrupts from the highest to lowest.
If found active, processor services the interrupt same as it does in the polling mode.

The only difference is in the condition codes.
Controller sends anything on the bus with 10011_xxx and uP acknowledges with 01100_xxx.

Note: To understand the working of the code in verilog, you can follow the commennts in the code itself.














