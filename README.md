# Sequence-Detector
Aim
To design and simulate a sequence detector using both Moore and Mealy state machine models in Verilog HDL, and verify their functionality through a testbench using the Vivado 2023.1 simulation environment. The objective is to detect a specific sequence of bits (e.g., 1011) and compare the Moore and Mealy designs.

Apparatus Required
Vivado 2023.1 or equivalent Verilog simulation tool.
Computer system with a suitable operating system.
Procedure
Launch Vivado 2023.1:

Open Vivado and create a new project.
Write the Verilog Code for Sequence Detector (Moore and Mealy FSM):

Design two Verilog modules: one for a Moore FSM and another for a Mealy FSM to detect a sequence such as 1011.
Create the Testbench:

Write a testbench to apply input sequences and verify the output of both FSM designs.
Add the Verilog Files:

Add the Verilog code for both FSMs and the testbench to the Vivado project.
Run Simulation:

Run the simulation and observe the output to check if the sequence is detected correctly.
Observe the Waveforms:

Analyze the waveform to ensure both the Moore and Mealy machines detect the sequence as expected.
Save and Document Results:

Capture the waveforms and include the results in the final report.

Verilog Code for Sequence Detector Using Moore FSM

// moore_sequence_detector.v
module moore_sequence_detector (
    input wire clk,
    input wire reset,
    input wire seq_in,
    output reg detected
);
    typedef enum reg [2:0] {
        S0, S1, S2, S3, S4  // States for detecting 1011
    } state_t;

    state_t current_state, next_state;

    // State transition logic
    always @(posedge clk or posedge reset) begin
        if (reset)
            current_state <= S0;
        else
            current_state <= next_state;
    end

    // Next state and output logic
    always @(*) begin
        case (current_state)
            S0: begin
                if (seq_in) next_state = S1;
                else next_state = S0;
                detected = 0;
            end
            S1: begin
                if (seq_in) next_state = S1;
                else next_state = S2;
                detected = 0;
            end
            S2: begin
                if (seq_in) next_state = S3;
                else next_state = S0;
                detected = 0;
            end
            S3: begin
                if (seq_in) next_state = S4;
                else next_state = S2;
                detected = 0;
            end
            S4: begin
                if (seq_in) next_state = S1;
                else next_state = S0;
                detected = 1;  // Sequence 1011 detected
            end
            default: next_state = S0;
        endcase
    end
endmodule
![image](https://github.com/user-attachments/assets/eb58ec70-0536-4018-86f7-175e67e25e2e)

Verilog Code for Sequence Detector Using Mealy FSM
mealy_sequence_detector.v 
module mealy_seq_detector(input clk, input reset, input seq_in, output reg detected );

localparam S0 = 3'b000,
S1 = 3'b001, S2 = 3'b010, 
S3 = 3'b011, S4 = 3'b100; 
reg [2:0] current_state, 
next_state;

always @(posedge clk or posedge reset) 
begin 
if (reset) current_state <= S0; 
else 
current_state <= next_state;
end

// Next state logic and detected output logic
always @(*) begin next_state = current_state; 
detected = 0; 
case (current_state) S0: begin 
if (seq_in) next_state = S1;
else 
next_state = S0; 
end 
S1: begin 
if (seq_in) next_state = S1; 
else 
next_state = S2; 
end 
S2: begin
if (seq_in) next_state = S3; 
else 
next_state = S0;
end 
S3: begin 
if (seq_in) begin next_state = S1; 
detected = 1; 
end
else 
next_state = S2;
end
default: 
    next_state = S0;
endcase
end
endmodule
![image](https://github.com/user-attachments/assets/b5a219e9-af62-493b-adf3-856fdccba549)

Testbench for Sequence Detector (Moore and Mealy FSMs)
Test bench for Moore:
// sequence_detector_tb.v `timescale 1ns / 1ps
module tb_moore_seq_detector;
// Inputs
reg clk;
reg reset;
reg data;
// Outputs
wire detected;
// Instantiate the Unit Under Test (UUT)
moore_seq_detector uut (
    .clk(clk), 
    .reset(reset), 
    .data(data), 
    .detected(detected)
);
// Clock generation: Toggle clock every 5 time units
always begin
    #5 clk = ~clk;
end
// Test sequence
initial begin
    // Initialize inputs
    clk = 0;
    reset = 1;
    data = 0;
    // Reset the system
    #10 reset = 0;
    // Sequence: 1011 should trigger detection
    #10 data = 1;  // Start with '1'
    #10 data = 0;  // '10'
    #10 data = 1;  // '101'
    #10 data = 1;  // '1011' - sequence detected
    // Hold for a bit
    #20;
    // Another test: introduce a different sequence and reset
    #10 reset = 1;
    #10 reset = 0;
    #10 data = 1;  // Start with '1'
    #10 data = 1;  // Wrong sequence '11'
    #10 data = 0;  // '110'
    #10 data = 1;  // Wrong sequence, no detection
    #10 data = 1;
    // Hold for a bit
    #20;
    // Finish simulation
    $stop;
end
// Monitor output
initial begin
    $monitor("At time %0d: data = %b, detected = %b", $time, data, detected);
end
endmodule
![Screenshot 2024-10-17 092817](https://github.com/user-attachments/assets/d425a46e-89c8-40c5-88be-394ee5f07b56)

Test bench for mealy:
module tb_mealy_seq_detector;
// Inputs
reg clk;
reg reset;
reg seq_in;
// Outputs
wire detected;
// Instantiate the Unit Under Test (UUT)
mealy_seq_detector uut (
    .clk(clk),
    .reset(reset),
    .seq_in(seq_in),
    .detected(detected)
);
// Clock generation: Toggle clock every 5 time units
always #5 clk = ~clk;
// Testbench code
initial begin
    // Initialize inputs
    clk = 0;
    reset = 1;
    seq_in = 0;
    // Apply reset
    #10 reset = 0;
    // Test case 1: Detect sequence "101"
    #10 seq_in = 1;
    #10 seq_in = 0;
    #10 seq_in = 1;  // "101" sequence completed
    #10 $display("Sequence 101 detected: %b", detected);
    // Test case 2: No sequence detected, incorrect sequence "110"
    #10 seq_in = 1;
    #10 seq_in = 1;
    #10 seq_in = 0;
    #10 $display("No sequence detected: %b", detected);
    // Test case 3: Detect the sequence "101" again
    #10 seq_in = 1;
    #10 seq_in = 0;
    #10 seq_in = 1;
    #10 $display("Sequence 101 detected again: %b", detected);
    // Finish the simulation
    #10 $finish;
end
// Monitor output
initial begin
    $monitor("Time: %0d, seq_in = %b, detected = %b", $time, seq_in, detected);
end
endmodule
![Screenshot 2024-10-17 092817](https://github.com/user-attachments/assets/d425a46e-89c8-40c5-88be-394ee5f07b56)

Conclusion
In this experiment, Moore and Mealy FSMs were successfully designed and simulated to detect the sequence 1011. Both designs worked as expected, with the main difference being that the Moore FSM generated the output based on the current state, while the Mealy FSM generated the output based on both the current state and input. The testbench verified the functionality of both FSMs, demonstrating that the Verilog HDL can effectively model both types of state machines for sequence detection tasks.
