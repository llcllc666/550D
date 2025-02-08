
# Lab1

**NetID:** ll372  **Name:** Leo Lu   **Course:** ECE557   **Date:** 2/8/2025

**1)Implementation of Theta Circuit

To Implement Theta Circuit, the first step is to create a temporary vector (called C here) of length 5 to store bc. Then a for loop is used to assign value to bc. Next, we notice that a variable t is generated for each assignment of st (io.state_o). Since they don't depend on each other, we can generate all 5 t required before assigning value to io.state_o. ROTL64  is a left shift operator, so we simply called rotateLeft(1) in a for loop to assign value to all t (named D in my code). Finally, we update io.state_o by io.state_i ^ D while following the right index. 

**2)Implementation of RhoPi Testbench

I generate a similar testbench as the Theta Testbench. First fill the state with random number, then directly translate the the C code to scala. Finally, we use poke to feed the randomly generated input to our module and use expect() to compare the results. One extra thing I do is to generate two arrays (eccakf_piln, eccakf_rotc) with fixed value since they will be required in the computation. 



**3)Implementation of Chi Circuit

Noticing that in the original C code, for each assignment of st (io.state_o) in a step of 5 (0 to 4, 5-9, 10-14,15-19,20-24), a new bc of length 5 is generated. We can simply break this loop by assigning value of bc into the major loop at a vector of length 25. Then in the major loop, we assign value to io.state_o. Instead of 3 for loop in the c code, we only need 2 here. 


**3)Implementation of Chi Testbench 

Similarly, we follow the same pattern and translate the C code to scala. 




**4)Test Results

All test are passed!
