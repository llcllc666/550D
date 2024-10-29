# ALU checkpoint 2

**NetID:** ll372   **Name:** Liuchang (Leo) Lu, Zhuoyi Chen   **Course:** ECE550D   **Date:** 10/29/2024

**1) Clock Dividers:**
The processor requires several clocks for each of its clocked elements: imem, dmem, processor clock, register file clock. Given the original clock signal CLK, we create clock dividers clk_div_2/4, which changes the frequency of the original clock signal by 2 or 4. It is achieved by putting the original signal to a Dflipflop and set the enable always to 1. Since Dflipflop is rising edge triggered, the resulting wave has half frequency of the original one. 
In our design, the imem is the inverted version of the CLK, dmem has the same wave form of CLK, regfile and processor has 1/4 frequency of CLK. 

**2) Processors:**
For this specific processors, Program counter (PC) increases 1 for each instruction. So, First, we call the give alu to add 1 to the pc_in => pc_in = pc_out + 1. Here, pc_in means the program before adding 1, since it will be the new input for next instruction. Then we passed the pc_in into a 32bit register(not the regfile!) and write the result to pc_out. This pc_out will be the address of imem. Then, for the q_mem, since the format of opcode, rd, rs are the same for both R type and I type, we simply create temporary wire to store them: [4:0] opcode, rs, rd. 

Then, we define two temporary file isRtype and isItype. we set isRtype = ~opcode[4] & ~opcode[3] & ~opcode[2] & ~opcode[1] & ~opcode[0] and isItype = ~isRtype since we only have to modes in this processor (will change for PC5). 
Then we create a mux with control signal isRtype to assign value to rt, shamt, ALUop (if it is R type, assign corresponding digits in q_imem, otherwise all 0). Similarly, create another mux controlled by isItype to assign the immediate for Itype operation. However, since for this immediate value, we need to expand it to 32 bits since later we are going to pass it in our alu, so we write a module called extent17to32 to extend the bits by filling the its most important digit (the signed)from left to make it 32 bits. 

Next, we write the module for control signal. Rwe = isRtype | opcode[3] | ~opcode[1], because we need to write to register only when 1. we have R type operation 2. for addi 3. for lw. among all operation, only lw has opcode[3]=1, and only difference between addi and sw is that its opcode[1]=0. In short, only when we perform sw operation, the Rew is 0. Similarly, Rdst = isRtype & ~ALUop[2], because we only need it to be 1 when we are performing add, sub, and or. Another mux control ALUinB is assigned ~isRtype, since we only need it for I type instruction. For DMwe and Rwd, they are only specific for lw and sw instructions, so we simply write DMwe =  ~opcode[4] & ~opcode[3] & opcode[2] & opcode[1] & opcode[0];
, Rwd = ~opcode[4] & opcode[3] & ~opcode[2] & ~opcode[1] & ~opcode[0]. We might need to change then for PC5, but for this checkpoint, it suffices. 

For the overflow, we noticed that left shift might also lead to overflow, but we do not need to report it. so our expression is isOverflow = overflow & ~ALUop[2], because for all operation (add, addi, sub), its ALUop[2] is 0, but for left shift and right shift, it is 1. 

Now we start to pass signal to the processor. ctrl_readRegA is always rs, but ctrl_readRegB is a mux controlled by Rdst => ctrl_readRegB = Rdst ? rt : rd. For the write register, we need to write to [30] register if there is overflow, and write to rd is no overflow, so we use a mux to control. ctrl_writeReg = isOverflow ? 5'b11110 : rd.  Next, we create another mux to select the input to the ALU. data_operandA = data_readRegA always, but data_operandB depend on ALUinB since it selects from the immediate value or data_readRegB => data_operandB = ALUinB? imme_32bit : data_readRegB. 

Next, we pass the given control signal to another ALU and write its output to address_dmem. However, for the rstatus, we need to handle it specifically due to different report number for different operations. It can be achieved by 2 muxes whose signal are controlled by specific digit in opcode (digit that are different for each operation) and overflow. 

Finally, we assign value to data_writeReg. When there is overflow, it is set to rstatus_value (1/2/3depending on operation). When there is no overflow, but we are able to write to register (sw operation), it will be the data from dmem. Otherwise, it will be the normal output data_result from alu. 2 muxes could be used to control that easily.  

