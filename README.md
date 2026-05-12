# code
module shift_reg (
    input wire clk,           // Clock signal
    input wire reset,         // Active high reset
    input wire [1:0] mode,    // 00: Hold, 01: Shift Right, 10: Shift Left, 11: Parallel Load
    input wire sin,           // Serial Input (for SIPO)
    input wire [3:0] pin,     // Parallel Input (for PISO)
    output reg [3:0] q        // Output (Parallel out)
);

    always @(posedge clk or posedge reset) begin
        if (reset) begin
            q <= 4'b0000;
        end else begin
            case (mode)
                2'b01: q <= {sin, q[3:1]};   // Shift Right
                2'b10: q <= {q[2:0], sin};   // Shift Left
                2'b11: q <= pin;             // Parallel Load (PISO start)
                default: q <= q;             // Hold state
            endcase
        end
    end
    endmodule
# Testbench
module shift_reg_tb;
    reg clk, reset, sin;
    reg [1:0] mode;
    reg [3:0] pin;
    wire [3:0] q;

    // Instantiate the design
    shift_reg uut (clk, reset, mode, sin, pin, q);

    // Clock generation (10ns period)
    always #5 clk = ~clk;

    initial begin
        // Initialize signals
        clk = 0; reset = 1; sin = 0; mode = 0; pin = 0;
        #10 reset = 0;

        // 1. Test Parallel Load (PISO Initial Step)
        mode = 2'b11; pin = 4'b1011; #10;
        
        // 2. Test Shift Right
        mode = 2'b01; sin = 1; #40; // Shift in 1s from the left
        
        // 3. Test Shift Left
        mode = 2'b10; sin = 0; #40; // Shift in 0s from the right
        
        #20 $stop;
    end
    endmodule
<img width="1600" height="486" alt="WhatsApp Image 2026-05-12 at 12 31 23 PM" src="https://github.com/user-attachments/assets/2b0cc485-1849-4243-b202-5095d5984ce0" />
   
