TRON 3TB4 Summary
=================

* Instructor: Dr. Bokhari
* *Winter 2015*
* McMaster University
* *Primary Author*: Kemal Ahmed

[TOC]

Two's complement: flip everything after the first 1 from right to left

##CanBUS

> Controller Area Network

* 2-wire protocol: `CAN_H` and `CAN_L` that allows microcontrollers and devices to communicate without a host computer
* uses <ins>constructive arbitration</ins> (a.k.a. non-destructive)
	*  Since 2 wires travel the same length, they experience the same external noise, so you can just remove the noise 
* When both lines have same voltage, signal is *recessive*
* If `CAN_H` - `CAN_L` > 0.9V, signal is *dominant*
* No ground reference, i.e. no ground noise
* Maximum transmission rate: 1 Megabit / s
* Each device has its own priority
  * If the controller is busy when a signal has been sent to it, it will be delayed until it has the highest priority
* **Cyclic Redundancy Check (CRC)**: error detection of whether you got the right signal. There are multiple methods:
	* send the same message back and forth a couple times
	* *Hash*: mod the first half and the second half and send them together, do the same to the message when it has been received
* Uses [NRZ](#NRZ)
* **ACKnowledgement slot (ACK)**: 
* **Remote Transmission Request (RTR)**: are you sending data? sending: 0, no: 1
	* *remote frame*: if RTR = 1
	* *data frame*: if RTR = 0

![CAN Data frame](images/can_data_frame.png)

CAN uses non-destructive bitwise [arbitration](#arbitration)

Each CANBUS Node has:

* **Host processor**: generates messages to be sent; deciphers received messages
* **CAN Controller**: takes messages from host and transmits them to the bus
* **Transceiver**:


Bit-Stuffing
--------------

> Inserting extra non-data bits into data signals. The extra bits are removed upon being received.
 
This is useful for:

* increasing bit rates when they're too slow
* Preventing information from being interpreted as control information
	* For example, in X.25 protocol, 6 ones parses each message, so if the actual message has 6 ones, stuff a 0 after the 5th 1


####RZ
> **Return to Zero mode**:
> Pulses will always return to 0 even if there are consecutive pulses. 

The signal is bit-stuffed with 0's

![consecutive pulses](images/RZcode.png)

####NRZ
> **Non-Return to Zero mode**:
> Pulses will never return to 0

The signal is bit-stuffed to keep its value, until the next pulse. If there are too many consecutive 1s or 0s, clock synchronization can be lost, so maximum of 5 1s or 0s in a row. There will automatically be an interruption every 5 consecutive data values whether or not it will become 6

![consecutive pulses](images/NRZcode.png)

###Manchester Coding
> A.K.A. **Phase encoding**

**Biphase Manchester**:

Dominant/Recessive
------------------

If you have multiple signals, the **dominant** signal will change everything and the **recessive** signal won't.

> **Think**
> : *What will be the output when both values are prevalent?* A good example when trying to conceptualize this is an `AND` gate. When one signal is `0` and the other is `1`, what will...*dominate*? The `0`.
> 
> : This can be extended to when you have multiple devices in series with the same power usage...won't change voltage 

---------------------

**Ethernet** uses <ins>destructive conflict resolution</ins> / destructive bitwise [arbitration](#arbitration).

**Prototols**
: how to interpret/represent a set of inputs

**Slew Rate**
: rate of change of voltage / s; how jagged are your square waves?

**Baud Rate**
: number of signals; 1/period; clock frequency / L * P, L = length of signal = sync bit (i.e. 1) + segment1 + segment2, P = Prescaler

**Differential BUS**
: when 2 wires travel along the same path, they experience the same external interference. To determine the message, you need to find the difference between the 2 wires, so you actually end up subtracting out the interference

###Arbitration
> *What will happen when you try to send multiple signals along the same wire?*

* **Non-destructive**: the smallest one will go first(i.e. 1011 before 1100), well...think dominant, not smaller
* **Destructive**: no messages will be transmitted, so you need to retry a random number of times until you get it right

Start Of Message (SOM) Bit
: generally denoted by a `0`

###Polling 

* **Normal mode**: 
* **Loopback mode**: sends Transmit line (TX) from controller to Receive line (RX); don't need acknowledgements
* **Silent mode**: sends RX to TX; more checking if messages are spam
* **Silent-loopback mode**: 

----

Message filtering:

* Software
* Hardware

**Shannon Capacity**:

**ElectroMagnetic Interference (EMI)**:

![Transmission Gate Multiplexor](images/tgate_mux.png)

##Verilog

###Keywords

`default`: catch-all for when none of the conditions are satisfied

####Initial Block
> Executed only once, providing initial values for system

####Always Block
> A block wired non-sequentially
> 
> * Infinitely repeated concurrently
> * Can only have one always block
> * Assignments can only be done to registers
> * Last statement has the highest <ins>priority</ins>

What is meant by priority:

	x = 0
	x = 4
	x = 3
	print x
	 3

**Sequential block**: blocking, one thing at a time
**Combinational Logic**: only dependant on the logic

####Blocking assignment
> A block of code where statements *block* the execution of the following statement(s), i.e. first statement must complete before the second statement

Identify in this format:
```
begin
<statements>;
end
```

####Number Representation

`<number of bits>b<value with minimal prior 0s>`

> e.g.
> `8b1`: 00000001

##Wiring Diagrams

* Intermediate wires are given arbitrary names
* The number of bits transmitted on each wire is labelled on each wire

##Asynchronous Systems
> A system that does not reset depending on the clock

Posedge
: 1 marks the beginning of a high signal

Negedge
: 1 marks the beginning of a low signal

Minterms
: use Sum of Products when the output is 1; 1 is pos, 0 is neg

Maxterms
: use Product of Sums when the output is 0; 0 is pos, 1 is neg 

###Boolean Algebra Axioms

1. $0\cdot0 = 0$
	1. 1+1 = 1
2. $1\cdot1 = 1$
	1. 0+0 = 0
3. $0\cdot1 = 1\cdot0 = 0$
	1. 1+0 = 0 + 1 = 1
3. If x = 0, then $\bar{x} = 1$
	1. If x = 1, then $\bar{x} = 0$
5. $x \cdot 0 = 0$
	1. x + 1 = 1
6. $x \cdot 1 = x$
	1. x + 0 = x
7. $x \cdot x = x$
	1. x + x = x
8. $x \cdot \bar{x} = 0$
	1. $x + \bar{x} = 1$
9. $\bar{\bar{x}} = x$

Timing Diagrams
---------------

Hint
: Make sure you put a small delay between the clock and gate changes

Memory cannot look at the address and command and supply the data immediately, so you need to insert **wait states** that give the program time.

The bacon strip symbols mean an unknown amount where everything stays in the same state.

Ignore the grey parts - they mean the input is invalid.

`chipselect`: equivalent to “bus enable”

`readdata`: data returned by slave

`waitrequest`: slave

##FPD
> *Field Programmable Device*

Standard Chips are inefficient

###PLD
> *A Programable Logic Device is a collection of programmable switches and general structures*

e.g. EEPROM, PAL, PLA, CPLD, FPGA

![pla](images/pla.png)

$f_1 = x_1x_2 + x_1\bar{x}_3 + \bar{x}_1\bar{x}_2x_3$
$f_2 = x_1x_2 + \bar{x}_1\bar{x}_2x_3 + x_1x_3$

LUT chart

####Macrocell
> *Extra circuitry after PAL and PLA circuits*

####CPLDs
> *Complex Programmable Logic Device is several PAL / PLAs in series*

####FPGA
> *Field Programmable Gate Arrays can be re-programmed after they've been deployed*

1. Calculate result and save
2. Use inputs to select the result from table

Changing the clock frequency

$t_{cq}$

$t_h$

$t_{su}$

You can write your own peripherals or use built in peripherals.

**Avalon switch fabric**: a slave-side arbitration scheme, lets multiple masters operate simultaneously

##Digital Signal Processing

Sample purposes of DSPs:

* **Filtering**: isolate useful noise
* **Compression**: efficient storage
* Audio / video processing (*e.g. voice pitch or colour changes*)

**Sampling**: recording periodic values

**Sampling frequency**:  samples/s
$f_s > 2 f_{max}$

**Nyquist frequency**: $f_{max} = \frac{f_s}{2}$

**Normalized frequency**: $\omega = \frac{f}{f_{max}}$

**Gain**: $1 - \omega$

**Shannon's Sampling Theorem**: frequencies should be sampled at a frequency > 2f

$$x = k\times(\sin / \cos\left( 2 \times \pi \times f \times t \right))$$

$$y = gain \times k\times(\sin / \cos\left( 2 \times \pi \times f \times t \right))$$

###Aliasing

* If $f_s < 2 f_{max}$, it causes problems during reconstruction
* Usually, you put an **anti-aliasing** filter on the frequencies that are above the nyquist frequency to avoid aliasing
* waves at different frequencies can share data at different points
* The frequencies are not noise / unwanted, but rather they cannot be sampled properly. They come out as lower frequencies than they actually are.
* a type of low-pass filter

###Fourier

> Really this is [2MX3 review](https://drive.google.com/open?id=0BxW61uJyyN8TQmM5SFY2eGpzV0E&authuser=0), but...
> *Any periodic signal can be decomposed into an infinite sum of sine waves.*
> $x(t) = \sum\limits_{n = 1}^\infty  {\left( {{a_n \cos(n\omega t) + b_n \sin(n\omega t)}} \right)} $
> $\omega = 2 \pi f$

**Fourier Transform**: time domain --> frequency

**Taps**: wires multiplexors

Numbers
---------

16 bit * 16 bit = 32 bit

**General Fixed Point Numbers**: when you want to represent a small range over the entire range of numbers possible. For example, if you want to represent -15 < x < 15 using -32768 to 32768,

$32768/15 = 2184.53 \approx 2048 = 2^{11}$

Or a better way of doing it is rounding 15 up to 16, or $2^4$ and finding out what the max # of numbers ($2^{15}$) is. take 15 - 4 = 11 and you get 2^{11}. This way is probably better because you get Q4.11

**General Fixed Point Notation**: $Qm.n$

* *m* is the number of bits representing the integer as 2's complement and the (m+1)$^{th}$ bit is the signed bit
* *n* is the fraction
* Minimum = $−2^m$
* Maximum = $2^m − 2^{−n}$
* Resolution = $2^{−n}$

Default value of m is 0, i.e. Q0.n = Q.n = Qn

Converting from x to Qn $=x  \times 2^n$

Note: the number of digits = m+n + 1, for sign

###Tri-state buffers

Like a valve, where B can only be 1 bit and A can be many bits.

![Buffers](https://upload.wikimedia.org/wikipedia/commons/thumb/c/c0/Tristate_buffer.svg/343px-Tristate_buffer.svg.png)

|  A |  B | C  |
|:--:|:--:|:--:|
|  0 | 1  | 0  |
|  1 | 1  | 1  |
|  X | 0  | Z  |

*Z*: high impedence

> a short circuit may occur when one device outputs to the bus a logic “1”, the supply voltage, while another is set at logic level “0” or ground

###Hold Time Violation
When a flipflop is sampling the data, there is a certain amount of time that the data should not change after the clock edge if you want the data to be consistent. That is called the hold time. If you change the data value before the end of the hold time, you get a hold time violation.

##ASIP Assembly

ASIP
: Application Specific Instruction Processor

Register 3: delay between stepper motor steps

**Byteenable**
: how to address specific bytes to write to within a given word address. The number of bytes writing to is the number of bits.

> e.g. for a 32-bit slave (i.e. 4 bytes):
> * `1111` writes full 32 bits
> * `0011` writes lower 2 bytes
> * `1100` writes upper 2 bytes
> * `0001` writes byte 0 only
> * `0010` writes byte 1 only
> * `0100` writes byte 2 only
> * `1000` writes byte 3 only

**Chip Select**:
(CE) Enables a memory device for reading or writing. Generally low true

**Refresh**
: one way of storing data is in capacitors. When there are too many accesses or the power leaks, the voltage may go too low to be noticeable. Thus, refreshing re-charges the capacitors. This is primarily used for re-writable DRAM (Dynamic RAM)

###Commands

`ADDI reg, imm3`
> Add the 3-bit unsigned immediate operand to the specified register; store the result into the register

`BR imm5`
> Branch (unconditionally) to PC+imm5, where PC is current program counter and imm5 is signed 5-bit immediate

`BRZ imm5`
> If register R0 is zero, branch to PC+imm5, where PC is current program counter and imm5 is signed 5-bit immediate

`CLR reg`
> Clear (i.e. set to 0) the specified register

`MOV regd, regs`
> Move the content of the regs (source) register into regd (destination) register

`MOVA reg`
> Move the motor by an absolute number of full steps specified in the register. The number in the register is signed, so the motor can move in either direction. After each step, wait for the amount of time specified in the delay register (3 in ASIP) before proceeding

`MOVR reg`
> Move the motor by a relative number of full steps specified in the register

`MOVRHS reg`
> Move the motor by a number of half-steps specified in a register. All other requirements are the same as for `MOVR`

`PAUSE`
> Wait for the amount of time specified inside the delay register

`SR0 imm4`
> Set (fill) 4 lower bits of register 0 with the 4-bit immediate operand

`SRH0 imm4`
> Set (fill) 4 higher bits of register 0 with the 4-bit immediate operand

`SUBI reg, imm3`
> Subtract the 3-bit unsigned immediate operand from the specified register; store the result into the register

FSMD
--------

FSMD
: *F*inite *S*tate *M*achine with *D*atapath 

* An FSM that can handles different data types and variables
* Formal definition: F <S, I, O, V, F, H, $s_0$>
	* S: set of states, ${s_0,s_1,...,s_i}$
	* I: set of inputs, ${i_0,..., i_m}$
	* O: set of outputs, ${o_0,..., o_n}$
	* V: set of variables, ${v_0,...,v_n}$
	* F: transition function mapping states & inputs & variables to states
	* H: action function mapping current state to outputs and variables, (S → O + V )
	* $s_0$: initial state
* zero is never read
* `A`ddress
* `D`ata

###States

* One state to fetch instructions

Cache
-----

**Thrashing**
: When the cache is always changing because you put something in cache and the next thing you replace everything in the cache, but then you need the previous thing. In other words, you're changing what's in the cache so often you can't even take advantage of it, so you're getting constant cache misses.

**Set Associative**
: Breaking lines into sets, i.e. number of lines is divisible by sets

**Direct Mapping**
: 