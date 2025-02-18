# Lab1 PART II

**NetID:** ll372  **Name:** Leo Lu   **Course:** ECE557   **Date:** 2/17/2025

**1)Control Circuit Implementation

1. Finite State Machine

   For control Circuit Implementation, I defined a 6 stage finite state machine: sIdle, XOR_first, keccakf_first, XOR_second, keccakf_second, out_done. Tracing back to the original C code implementation, in sha3_update function, we first XOR the st[ ] variable then called the keccakf. Then, in the sha3_final function, we XOR st[ ] again and called the keccakf again. sIdle state is the state that waits for the input data, and the out_done is the state signifying that the resulting hash has been computed.
   
2. Communication with Data Path

   In order to communicate with the datapath, four enable signals (read_enable, xor_enable, keccakf_enable, output_enable) are used as output to tell the data path which unique state we are in. Beside, it has four inputs (input_done, xor_done, keccakf_done, output_done) so that the data path can tell the control module when it finishes the corresponding process, and based on this the control module can move to the next state.

**2)Data Path Circuit Implementation

1. state(sIdle -> the input stage)

when we get a high read_enable signal (plus io.message_val && io.message_rdy), it means that we can start reading in the input data. We put the inpug message into a register and set the input_done signal as high. It is important to notice that when the read_enable signal is off, we need to set the input_done as off in order to refresh this value (otherwise it will stay high!). This mindset is also applied to other signal. 

2. state (XOR)

Following a similar method, a for loop is used to XOR the state register (full of 0 upon initialization). When it is done, xor_done is set to high.

4. state (keccakf)

Since there are several stages in this keccakf, I first create 3 temporary register (theta_out, rhopi_out, iota_out) to store temporary results of each stage. In addition, a register storing the round variable is created. In each cycle, the round variable is incremented by 1. The process end when the round == 23 (24 rounds in total). Finally, we set the keccakf_done signal accordingly. 

4. state (output)
In the output state. when hash_rdy and hash_val is high, while output_enable is high, we set the output message (OutMsg) and make output_done high. 


