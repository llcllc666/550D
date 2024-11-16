# ALU checkpoint 2

**NetID:** ll372   **Name:** Liuchang (Leo) Lu, Zhuoyi Chen   **Course:** ECE550D   **Date:** 10/29/2024

**1) Clock Dividers:**
The processor requires several clocks for each of its clocked elements: imem, dmem, processor clock, register file clock. Given the original clock signal CLK, we create clock dividers clk_div_2/4, which changes the frequency of the original clock signal by 2 or 4. It is achieved by putting the original signal to a Dflipflop and set the enable always to 1. Since Dflipflop is rising edge triggered, the resulting wave has half frequency of the original one. 
In our design, the imem is the inverted version of the CLK, dmem has the same wave form of CLK, regfile and processor has 1/4 frequency of CLK. 
