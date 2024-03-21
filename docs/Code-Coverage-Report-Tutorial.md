In this tutorial. I'm using 2 files "counter.v" and "counter_tb.v"

Both of them are placed in the Coverage Folder

Counter.v :
```verilog
module counter #(parameter N=8) // parameter to specify bit number, default is 8 bit
(
    output reg [N-1:0] q,
    output max_tick,
    input clk, rst
);

    always @(posedge clk, posedge rst)
        if(rst) //async reset, active high
            q <= 0;
        else
            q <= q + 1;

    assign max_tick = (q == 2**N - 1) ? 1 : 0;
endmodule
```
Counter_tb.v :
```verilog
module counter_tb;
    reg clk,rst;
    wire max_tick;
    wire [3:0] q;

    counter#(4) test(q, max_tick, clk, rst); //4 bit counter

    initial begin
        clk = 0;
        forever #10 clk = ~clk;
    end

    initial begin
        #0	rst = 1;
        #50 	rst = 0;
	#1000000 $stop;
    end

    initial begin
        $monitor($time," rst = %b , q = %b , max_tick = %b",rst,q,max_tick);
    end

endmodule
```

Note : Type the following command in transcript window. To open transcript window in modelsim, go to View>Transcript

1. Place design file and testbench in the same folder.
2. Open modelsim and change directory to that folder. Go to File>Change Directory... Alternatively, using cd command :

```
cd C:/Users/Admin/Documents/Verilog/Coverage
```
Note : You have to using the forward slash "/" or double backward slash "\\"

3. Create new library and map the library. Go to File>New>Library... Alternatively, using the vlib and vmap command :

```
vlib work
vmap work work
```

Where work is the library name. Use whatever you want.

4. Compile the design and testbench with coverage option :

```
vlog -coveropt 3 +cover +acc counter.v
vlog -coveropt 3 +cover +acc counter_tb.v
```

5. Run the simulation for code coverage :

```
vsim -coverage -vopt work.counter_tb -c -do "coverage save -onexit -directive -codeAll report.ucdb; run -all"
```

6. Export the Coverage :

```
vcover report -html report.ucdb
```
In case of any error redo Step-5 and Step-6

7. View your report at covhtmlreport/index.html