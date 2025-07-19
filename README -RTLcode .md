```verilog
module counter (Clk,rest_n,count);
input wire Clk, rest_n;
output reg [2:0] count;


//setup counter module
always @(posedge Clk or negedge rest_n) begin
    if (rest_n==1'b0) begin
        count<= 3'b000;
    end
    else begin 
        count<= count+1'b1;
    end  
end
    
endmodule
