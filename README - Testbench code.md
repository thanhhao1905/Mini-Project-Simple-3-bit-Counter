```verilog
`timescale 1ps/1ps
module tbcounter;
reg Clk, rest_n;
wire [2:0] count;
integer err_cnt =0;

// setup counter testbench
counter tbcount (Clk,rest_n,count);
initial begin
    Clk=0;
    forever #10 Clk =~Clk; 
end

initial begin
    rest_n= 1'b0;
    #100 rest_n= 1'b1;
    #500; 
    
    if (err_cnt==0) begin
            $display("\n==========");
            $display("Status: Test pass");
            $display("==========\n");
         end else begin
            $display("\n==========");
            $display("Status: False with %d errors",err_cnt);
            $display("==========\n");
         end
    
    $finish;
end

initial begin
    $monitor("time= %0t, Clk=%b, rest_n=%b, count=%d", $time, Clk, rest_n, count);
end


//Checker to check all transit
initial begin
    integer exp_count;
    exp_count =0;
    forever begin
        if (rest_n == 1'b0) begin
            wait (rest_n == 1'b1);
        end else begin
        @(posedge Clk); #1;
        exp_count = exp_count +1;
        end

        if (count !== exp_count) begin
             $display("[CHECKER]: Error RTL actual count: %0d, Expected count: %0d", count, exp_count);
                err_cnt= err_cnt+1;
        end else begin
             $display("[CHECKER]: Matching RTL actual count: %0d, Expected count: %0d", count, exp_count);
        end
         if (exp_count == 7) begin
            exp_count = -1;
        end
    end
end


// show wave form
initial begin
    $dumpfile("dump.vcd"); 
    $dumpvars;
 end
endmodule
