University of Puerto Rico - Río Piedras Campus

Department of Computer Science

CCOM-4086 Computer Architecture

Dr. Humberto Ortiz Zuazaga

Bianca I. Colón Rosado 

#Design and implementation of a simple 16-bit CPU

We implement an 16-bit CPU up from logic gates in Logisim. Only using and gates, or gates or inverters. We used RAM for the program memory, but we built everything else up.

Our CPU will execute 16-bit instructions. With 16 registers we can let 4 bits represent the rs, rt and rd registers, and have 4 bits for the op.

##We prove our CPU with two Programs. 


This small program count to n in 16-bit PseudoMIPS Assembly:

```
		add $1, $0, $0 
		addi $1, 10		    ; set n to 10
		add $2, $0, $0		; set i to 0
	loop:
		addi $2, 1
		addi $1, -1
		bnz $1, loop		  ; loop is PC - 2
```		
		
We can translate this by hand to the machine language:

```
             binary |  hex
0000 0001 0000 0000 | 0100
1000 0001 0000 1010 | 810A
0000 0010 0000 0000 | 0200
1000 0010 0000 0001 | 8201
1000 0001 1111 1111 | 81FF
1011 0001 1111 1110 | B1FE
```

Loading the instruction memory with these values will allow us to simulate the execution of the program. (http://www.hpcf.upr.edu/~humberto/courses/arch2013/less-broken-cpu.html)

The other program Compute 10 elements of [Fibonacci Sequence](https://gist.github.com/humberto-ortiz/7467201)

>The design is a simplified version of the MIPS design presented in the textbook David A Patterson, John L Hennessy, Computer Organization and Design, Fourth Edition: The Hardware/Software Interface, 2009. ISBN 978-0-12-374493-7


A **register file** with 16-bit registers feeds into a simple ALU. We use multiplexers, decoders and demultiplexers to select the registers for the ALU to process. A control table drives the components based on the instruction to be executed.

##Gates: 
* Not   
* and    
* or   

##Multiplexer:
	The output reflect the value that has our Selector: A = 0, B= 1. 
	The value of A = 1, and the value of B = 0. 
	Our Selector = 1 = B, the output is 0 because that was the value of B.
| A | B | Selector | Output|
|---|---|:--------:|------:|
| 0 | 0 |  0 | 0 |
| 0 | 0 | 1 | 0 |
| 0 | 1 | 0 | 0 |
| 0 | 1 | 1 | 1 |
| 1 | 0 | 0 | 1 |
| 1 | 0 | 1 | 0 |
| 1 | 1 | 0 | 1 |
| 1 | 1 | 1 | 1 |

##Full Adder:
  
Adds 3 one bit inputs, outputs result and carry.
  
| A | B | C1 | Output| C2 |
|---|---|----|-------|----| 
| 0 | 0 |  0 | 0     | 0  |
| 0 | 0 | 1  |     0 | 1  |
| 0 | 1 | 0  | 0     |1   |
| 0 | 1 | 1  | 1     |   0|
| 1 | 0 | 0  | 1     |1   |
| 1 | 0 | 1 | 0 |0|
| 1 | 1 | 0 | 1 |0|
| 1 | 1 | 1 | 1 |1|


> Our *Instruction Memory* and *Data Memory* are **RAM**. And our *Control* is **ROM**. 

## Program Counter (PC): 
- Increases the instructions +1, unless you get a branch or jump.

#Example: 
	
##Code instructions:
###Add Immediate: 
```
addi $1, 10		; set n to 10
```
- 4 bits representation of addi is rd imm 8
- Translate the instruction to the 4 bit representation: *1000 0001 0000 1010* in hexadecimal is **810A**.
- Send 1000 (addi) to the control. The control send the instruction to add 0000 + 1010 (0+10), and save the result in the register 1 ($1).  

###Jump:
- 4 bit representation of j is jumps to PC + imm12 
- Translate the instruction to the 4 bit representation: **1111**
- Send 1111 (j) to the control. Take the immediate and added to PC.

###Branch zero:
- 4 bit representation of bz is jumps to PC + imm8
- Translate the instruction to the 4 bit representation: **1010**
- Send 1010 (bz) to the control. Take the immediate and added to PC.
			
###Branch not zero:	
```
bnz $1, loop		; loop is PC - 2
```
- 4 bit representation of bnz is jumps to PC + imm8:
- Translate the instruction to the 4 bit representation: 1011 0001 1111 1110 in hexadecimal is **B1FE**.
- Send 1011 (bnz) to the control. Take the immediate (-2)  and added to PC.  
