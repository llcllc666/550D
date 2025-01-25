# Lab0

**NetID:** ll372  **Name:** Leo Lu   **Course:** ECE557   **Date:** 1/24/2025

**1)Implementation
The input interface uses a decoupled handshake protocol. There are two output: one is a done signal, another is the desired Fibonacci number. 

Four registers are created by RegInit. 

1. a boolean called processing, which is used to determine if the computation is ongoing. It is initialzied to false
2. A 64 bit register initialzied to 0 called prev, which is the last Fibonacci number that will be added to the current one
3. A 64 bit register initialzied to 0 called current, which is the current Fibonacci number
4. A 8 bit register initialzied to 0, which is determines the length of the Fibonacci sequence

The ready signal (io.in.ready) of the input is assigned as the inverse of processing, which means that the circuit is ready to receive the signal when it is not doing any computation

When io.in.ready and io.in.valid are both true, which means that we start a new cycle of computing. First, count is initialized to io.in.bits since it represents the length of Fibonacci sequence. Then current is set to 1, and prev is set to 0. This is the typical starting point of Fibonacci sequence. Last, we assigned the processing signal as true to notify that the circuit is computing. 

When processing is true and count is bigger than 1 (which means that we haven't reached the desired length of Fibonacci sequence), we update the current and prev based on the rule of Fibonacci sequence. In addition, we decrease count by 1. 

io.done signal is true when processing is true (to tell that we just finished the computation) and count===1

Now we assign io.out = current

Finally, when io.done is true, we set processing as false. Now the circuit is ready to receive the next input

**2)Test

To test the circuit, I first modify the Launcher file by adding 

"Fib" -> { (manager: TesterOptionsManager) =>
      Driver.execute(() => new Fib(), manager) {
        (c) => new FibTest(c)
      }
    }

I found the 1st, 2nd, 3rd, 5th, 10th, 20th, 30th, 40th, 50th, 60th, 70th Fibonacci number online and put them into a map. Then I use poke to send the keys of the map into the circuit to test the output. 

Below is the output of the test case. The input and the output are shwon. 

Result for Fibonacci(5): 5 (Expected: 5, Cycles: 5)

Result for Fibonacci(10): 55 (Expected: 55, Cycles: 10)

Result for Fibonacci(20): 6765 (Expected: 6765, Cycles: 20)

Result for Fibonacci(1): 1 (Expected: 1, Cycles: 1)

Result for Fibonacci(60): 1548008755920 (Expected: 1548008755920, Cycles: 60)

Result for Fibonacci(70): 190392490709135 (Expected: 190392490709135, Cycles: 70)

Result for Fibonacci(2): 1 (Expected: 1, Cycles: 2)

Result for Fibonacci(3): 2 (Expected: 2, Cycles: 3)

Result for Fibonacci(50): 12586269025 (Expected: 12586269025, Cycles: 50)

Result for Fibonacci(40): 102334155 (Expected: 102334155, Cycles: 40)

Result for Fibonacci(30): 832040 (Expected: 832040, Cycles: 30)


