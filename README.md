# ALU checkpoint 2

**NetID:** ll372   **Name:** Liuchang (Leo) Lu   **Course:** ECE550D   **Date:** 09/16/2024

## Content

1. Structure Overview

2. Add Operation

3. Subtract Operation

4. Overflow

5. isNotEqual

6. isLessThan

7. Bitwise_AND

8. Bitwise_OR

9. SLL

10. SRA

11. Regfile
---

## 1. Structure Overview
**a) project checkpoint 1 includes following file:**

- alu.v: the top-level design of the ALU
- alu_tb.v: the testbench of alu.v
- Fourbit_Carrylookahead_Adder.v: the 4 bit carry look ahead adder
- Complete_CLA_Adder: the complete 32 bit adder based on 4 bit carry look ahead adder
- Mux.v : a 2 bits to 1 bits mux
- Decoder.v : a 5bits to 32 bits decoder that helps determine which operation to use
- isNotEqual.v: determines whether A!=B, performed after subtraction
- isLessThan.v: determines whether A<B, performed after subtraction
- Bitwise_AND.v: perfrom bitwise AND on dataA and dataB
- Bitwise_OR.v: perfrom bitwise OR on dataA and dataB
- SLL.v: left shift module
- SRA.v: right shift module

**b) Input and Output:**
|**Port Name**|**Input/output**|**Description**|
|-|-|-|
|data_operandA[31:0]|Input|First data Input|
|data_operandB[31:0]|Input|Second data Input|
|ctrl_ALUopcode[4:0]|Input|ALU command code that decides adding/subtracting|
|ctrl_shiftmat[4:0]|Input|Shift amount for SLL and SRA|
|data_result[31:0]|Output|Computed Output|
|isNotEqual|Output|Flag - A≠B when subtracting|
|isLessThan|Output|Flag - dataA < dataB when subtracting|
|overflow|Output|Flag - overflow|
|isNotEqual|Output|Flag - performed after subtraction, determine whether dataA!=dataB
|isLessThan|Output|Flag - performed after subtraction, determine whether dataA<dataB

**c) Description**
First, I made a 4 bit carry look ahead adder. Then, I wrote a complete 32 bit carry look ahead adder based on 4 bit carry look ahead adder. This 32 bit carry look ahead adder incorporates overflow feature that xor the last 2 carry bits. The ALU will decide which operation to perform based on the last bit of ctrl_ALUopcode ([0]). If it is addition, the ALU will simply call the 32 bit carry look ahead adder with 0 as the first carry in bit to compute. If it is subtraction, the ALU will first perform a NOT gate on data_operand and call the 32 bit carry look ahead adder with 1 as the first carry bit. The 32 bit carry look ahead adder will produce the computed answer and overflow. When subtraction is performed, isNotEqual will be true if dataA!= dataB, and isLessThan will be true if dataA < dataB. 
The Bitwise_ADD and Bitwise_OR performs ADD and OR operator for each bit in dataA and dataB. The SLL and SRA will shift dataA by left or right for a certain number of digits. 

---

## 2. Add Operation

The adder I used is the hierarchical CLA. The first level is the 8-bit CLA adder. The second level is the 4 8-bit CLA adder block. The look-ahead ability for each bit carried out requires the previous bits 'propagation' and 'generation'. The propagate function (p) is the OR of two input bits and the generate function (G) is the AND of two input bits. For each carry-out in each bit, it is calculated as:

I use the carry look ahead adder. First, I made a 4 bits CLA adder. CLA adder introduces two functions:  carry generate (G) and carry propagate (P). They can be computed as below:
G_i = A_i & B_i
P_i = A_i | B_i
G denotes how the carry is generated for single-bit two inputs regardless of any input carry, while P denotes when the carry is propagated to the next stage with an addition whenever there is an input carry. We can then compute the carry bit for next stages as below: C_(i+1) = (G_i)+(P_i * C_i), and sum_i (the computed result) = A_0 ^ B_0 ^C_0. 

Then based on 4 bits RCA adder, I designed a 32 bits RCA adder that performs 32 bits addition/subtraction and perfrom overflow test. 

## 3. Subtract Operation

Based on the rule of representing signed number in binary, to perform subtraction, we simply translate it to addition: (A-B) = A + (-B). To compute -B, we simply perform a NOT gate on B to get ~B and add 1 to it. Noticing that this extra 1 is added as the c_in of the 32 bit carry look ahead adder. 

## 4. Overflow Flag

Overflow is determined after subtraction and addition operations. It has accomplished the checking when in 32-bit CLA (first level) design. If the carry_in of the last bit is not equal to the carry_out of the last bit, there is an overflow happened. Simply achieved by:

The overflow could be detected by passing the two most significant carry bit (leftmost two) of the adder to a XOR gate. for example, overflow = (carry[8]^carry_addition[7]); where the things in bracket corresponds to two most significant carry out bit. 

## 5. isNotEqual

isNotEqual is equivalent to determine whether the computed result after subtraction is 0 or not. Therefore, by using or operator | on the computed result and (command[1]) ? ((|computed_result) ? 1:0 ): 0 , we will be able to assign the correct value to isNotEqual. Noticing that command[1] determines whether a subtraction has been performed or not. 

## 6. isLessThan

To test if dataA<B, I evaluate overflow ^ computed_result[31]. 

## 7. Bitwise_AND

by using a generative for loop that loop over each bits, use conditional statement data_operandA[i]?(data_operandB[i]? 1 :0 ) : 0

## 8. Bitwise_OR

by using a generative for loop that loop over each bits, use conditional statement data_operandA[i]? 1:(data_operandB[i]?1:0) 

## 9. SLL

Based on what covered in lectures, I use 5 generative for loops, and each loop contain 32 mux. The first loop corresponds to shift by 1 or 0, the second loop corresponds to shift by 2 or 0, and so on. Inside each loop, there is a generative if statement to set one of the input of mux as 0 for certain looping variable. 

## 10.SLA

Similarly,  I use 5 generative for loops, and each loop contain 32 mux. The first loop corresponds to shift by 1 or 0, the second loop corresponds to shift by 2 or 0, and so on. Inside each loop, there is a generative if statement to set one of the input of mux as 1 or 0 based on the sign of dataA for certain looping variable. 

## 11.Regfile
|**Port Name**|**Input/output**|**Description**|
|-|-|-|
|clock|Input|clock signal for the regfile|
|ctrl_writeEnable|Input|Enable signal for the write portion|
|ctrl_reset|Input|Reset signal for regfile|
|ctrl_writeReg[4:0]|Input|register control command for write|
|ctrl_readRegA/B[4:0]|Input|register control command for read A/B|
|data_writeReg[31:0]|Input|32-bit data to be writtened|
|data_readRegA/B[31:0]|Output|32-bit data to read A/B|

First, a decoder is created to decode the 5bit write and read signal. Then based on the 1bit flipflop, I created a 32 bit flipflop by a generate for loop. Then in the main regfile, I created another generate for loop from i=0 to i=31. Inside the loop,  I created an and gate that connect the write enable signal and the write control signal. Then the result of the end gate is passes to the flip-flop. Then the result of the flipflop is passes to the tristate buffer and output to data_regA or dataRegB. 










