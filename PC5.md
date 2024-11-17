# ALU checkpoint 5

**NetID:** ll372, zc244   **Name:** Liuchang (Leo) Lu, Zhuoyi Chen   **Course:** ECE550D   **Date:** 11/17/2024

**1)Control Signal
We start with updating all the control signal. We have created wire isJI and isJII to specify the general type of instructions. Beside, we also created wires isJ, isBne, isJal, isBlt, isBex, isSetx to specify which instruction we are going to use. Finally, we update our Rwe(register write enable) to accomodate isJal and isSetx instruction since they require writing to register file. At the begining of the processor, we will pass in the opcode for the control signal module and create corresponding wire to get the output.  

**2)Instruction Implementation
First, in the processor module, we created two other signal isBranch and isBexBranch. isBranch is true when we actually need to branch the insturction in bne (if ($rd != $rs)) and blt (if ($rd < $rs) ) instruction. isBexBranch is true when ($rstatus != 0) in bex operation. In both cases, we use those two signal to decide whether to update the PC. 
Then, we would update the program counter based on the instruction by using many muxes. if it is one of the J, Jal, Bex(but only when isBexBranch is true), we need to update the PC to PC = T. If isBranch is true (bne and blt), then PC = PC+1+N. the last case we need to update PC in a new way is at JII instruction, where we need to set PC = $rd. If all of these cases are not met, we simply set PC = PC+1. Noticing we have called the alu module to perform those addition and store the result in temporary wires. 

Noticing that we haven't get value of T in the above paragraph, so we create T such that it takes value of q_imem [26:0] when we are performing JI type instruction, and 0 otherwise. Then we extent this 27 bits signal to 32 bits. The next step is to update ctrl_readRegA and ctrl_readRegB. If it is bex operation, we need readRegA to be $30 which is $rstatus. If it is JII operation, readRegA should be $rd, otherwise it remains rs as in PC4. For regB, we add another mux to specify it to be 00000 when isBex is true. Then for the dataWriteReg, we need to write to $rstatus at SexX operation, so we use a or gate for isSetx and isOverflow since we both need to update $rstatus in that case. 

Finally, we update the dataWriteReg. Beside the instruction in PC4, when isSetx is true, it is updated to T, If it is jal instuction, it is updated to PC+1. 
