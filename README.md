# FSM_for_Sequence_Detector
# EXP NO.6.A. Sequence Detector Using Moore Machine and Mealy Machine

# Aim
To design and simulate a Finite-State-Machine-for-Sequence-Detector-1011 using Verilog HDL, and verify its functionality through a testbench in the Vivado 2023.1 environment.

# Apparatus Required
Vivado 2023.1

# Procedure
Launch Vivado 2023.
1. Open Vivado and create a new project.
2.  Design the Verilog Code Write the Verilog code for the RAM,ROM,FIFO Create the Testbench Write a testbench to simulate the memory behavior.
3.  The testbench should apply various and monitor the corresponding output.
4.  Create the Verilog Files Create both the design module and the testbench in the Vivado project. Run Simulation Run the behavioral simulation to verify the output.
5.  Observe the Waveforms Analyze the output waveforms in the simulation window, and verify that the correct read and write operation.
6.  Save and Document Results Capture screenshots of the waveform and save the simulation logs. These will be included in the lab report.

# Code
# Mealy 1011
## Verilog code
```
module mealy_1011_detector(
    input clk,
    input rst,
    input x,
    output reg z
);

reg [2:0] state, next_state;

// State Encoding
parameter S0 = 3'b000,   // No match
          S1 = 3'b001,   // Detected 1
          S2 = 3'b010,   // Detected 10
          S3 = 3'b011;   // Detected 101

// State Register
always @(posedge clk or posedge rst)
begin
    if(rst)
        state <= S0;
    else
        state <= next_state;
end

// Next State Logic and Output Logic
always @(*)
begin
    next_state = state;
    z = 0;

    case(state)

        // No match
        S0:
        begin
            if(x)
                next_state = S1;
            else
                next_state = S0;
        end

        // Detected 1
        S1:
        begin
            if(x)
                next_state = S1;
            else
                next_state = S2;
        end

        // Detected 10
        S2:
        begin
            if(x)
                next_state = S3;
            else
                next_state = S0;
        end

        // Detected 101
        S3:
        begin
            if(x)
            begin
                next_state = S1;   // Overlapping sequence
                z = 1;             // 1011 detected
            end
            else
            begin
                next_state = S2;
                z = 0;
            end
        end

        default:
        begin
            next_state = S0;
            z = 0;
        end

    endcase
end

endmodule

```
## Test bench
```
`timescale 1ns/1ps

module tb_mealy_1011_detector;

reg clk;
reg rst;
reg x;

wire z;

// Instantiate DUT
mealy_1011_detector uut(
    .clk(clk),
    .rst(rst),
    .x(x),
    .z(z)
);

// Clock Generation
always #5 clk = ~clk;

initial
begin
    clk = 0;
    rst = 1;
    x = 0;

    #10 rst = 0;

    // Input sequence : 1011
    #10 x = 1;
    #10 x = 0;
    #10 x = 1;
    #10 x = 1;   // z = 1

    // Another overlapping sequence
    #10 x = 0;
    #10 x = 1;
    #10 x = 1;   // z = 1

    #20 $finish;
end

initial
begin
    $monitor("Time=%0t  X=%b  State=%b  Z=%b",
              $time, x, uut.state, z);
end

endmodule

```

## output Waveform
<img width="1353" height="773" alt="image" src="https://github.com/user-attachments/assets/94e28688-75c4-4f97-a480-a891ba7ceca4" />



# Moore 1011
## write verilog code for ROM using $random
```
module moore_1011_detector(
    input clk,
    input rst,
    input x,
    output reg z
);

reg [2:0] state, next_state;

parameter S0 = 3'b000,
          S1 = 3'b001,
          S2 = 3'b010,
          S3 = 3'b011,
          S4 = 3'b100;

// State Register
always @(posedge clk or posedge rst)
begin
    if(rst)
        state <= S0;
    else
        state <= next_state;
end
// Next State Logic
always @(*)
begin
    case(state)

        S0: if(x) next_state = S1;
            else next_state = S0;

        S1: if(x) next_state = S1;
            else next_state = S2;

        S2: if(x) next_state = S3;
            else next_state = S0;

        S3: if(x) next_state = S4;
            else next_state = S2;

        S4: if(x) next_state = S1;
            else next_state = S2;

        default: next_state = S0;

    endcase
end
// Moore Output Logic
always @(*)
begin
    case(state)
        S4: z = 1;
        default: z = 0;
    endcase
end

endmodule

```

## Test bench
```
module tb_moore_1011_detector;

reg clk, rst, x;
wire z;

moore_1011_detector uut(
    .clk(clk),
    .rst(rst),
    .x(x),
    .z(z)
);

always #5 clk = ~clk;

initial
begin
    clk = 0;
    rst = 1;
    x = 0;

    #10 rst = 0;

 // Input sequence: 1 0 1 1
    #10 x = 1;
    #10 x = 0;
    #10 x = 1;
    #10 x = 1;   // Sequence detected

    #20;

    // Another overlapping sequence
    x = 0;
    #10 x = 1;
    #10 x = 1;

    #20 $finish;
end

initial
$monitor("Time=%0t  X=%b  State=%b  Z=%b", $time, x, uut.state, z);

endmodule

```

## Output Waveform
<img width="1359" height="766" alt="image" src="https://github.com/user-attachments/assets/e7baf3c8-f374-40cb-b0c4-b2af04a27dac" />


# Conclusion 
The Mealy and Moore state machine for sequence 1011 was designed and successfully simulated using Verilog HDL. The testbench verified both the write and read functionalities by simulating the sequence operations and observing the output waveforms.

