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
