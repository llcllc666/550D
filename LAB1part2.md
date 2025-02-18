# Lab1 PART II

**NetID:** ll372  **Name:** Leo Lu   **Course:** ECE557   **Date:** 2/17/2025

**1)Control Circuit Implementation

1. Finite State Machine

   For control Circuit Implementation, I defined a 6 stage finite state machine: sIdle, XOR_first, keccakf_first, XOR_second, keccakf_second, out_done. Tracing back to the original C code implementation, in sha3_update function, we first XOR the st[ ] variable then called the keccakf. Then, in the sha3_final function, we XOR st[ ] again and called the keccakf again. sIdle state is the state that waits for the input data, and the out_done is the state signifying that the resulting hash has been computed. 
3. 
4. 

