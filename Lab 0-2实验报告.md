## 实验目的

- 学习 Vivado 的基本使用方法
- 学习Verilog 语言的最基础使用方法
- 学习布尔表达式和对应 Verilog 语言的转换

## 实验步骤
- 安装
- 仿照Logisim导出的文件，将布尔表达式转换为Verilog语言，补全Lab0-3中的文件
		布尔表达式中的每项构成一个and gate，所有and gate一并通过一个or gate
- 在Vivado中下板；分别用Verilator和Vivado仿真

> [!NOTE]
> *值得记录的*：在Vivado中进行综合步骤时失败，无报错，拼尽全力无法战胜，最后在CSDN上找到了原因——我的电脑名字是中文

### 5.1 Verilog练习

src/lab0-3/syn/top.v
```verilog
module AND_GATE_3_INPUTS(
        input input1,
        input input2,
        input input3,
        output result
    );

	assign result = input1 & input2 & input3;
endmodule

module AND_GATE_2_INPUTS(
        input input1,
        input input2,
        output result
    );

	assign result = input1 & input2;
endmodule

module OR_GATE_5_INPUTS(
        input input1,
        input input2,
        input input3,
        input input4,
        input input5,
        output result
    );
    assign result = input1 | input2 | input3 | input4 | input5;
endmodule

  

module top(
  input SW0,
  input SW1,
  input SW2,
  input SW3,
  output LD0
);

  
   wire s0;
   wire s1;
   wire s2;
   wire s3;
   wire s4;
   wire s5;
   wire s6;
   wire s7;
  
   wire s8;

   wire s9;
   wire s10;
   wire s11;
   wire s12;
   wire s13;

   assign s0=SW0;
   assign s1=SW1;
   assign s2=SW2;
   assign s3=SW3;
   assign LD0=s8;

   assign s4=~SW0;
   assign s5=~SW1;
   assign s6=~SW2;
   assign s7=~SW3;

  

AND_GATE_3_INPUTS GATES_1(
   .input1(s5),
   .input2(s6),
   .input3(s7),
   .result(s9)
);

  
AND_GATE_3_INPUTS GATES_2(
   .input1(s4),
   .input2(s2),
   .input3(s5),
   .result(s10)
);
  

AND_GATE_3_INPUTS GATES_3(
   .input1(s4),
   .input2(s1),
   .input3(s3),
   .result(s11)
);
  

AND_GATE_2_INPUTS GATES_4(
   .input1(s2),
   .input2(s3),
   .result(s12)
);


AND_GATE_2_INPUTS GATES_5(
   .input1(s0),
   .input2(s2),
   .result(s13)
);


OR_GATE_5_INPUTS GATES_6(
   .input1(s9),
   .input2(s10),
   .input3(s11),
   .input4(s12),
   .input5(s13),
   .result(s8)
);
endmodule
```

src/lab0-3/syn/nexysa7.xdc
```verilog
## IO

set_property PACKAGE_PIN {R15} [get_ports {SW0}]
set_property IOSTANDARD {LVCMOS33} [get_ports {SW0}]

set_property PACKAGE_PIN {M13} [get_ports {SW1}]
set_property IOSTANDARD {LVCMOS33} [get_ports {SW1}]

set_property PACKAGE_PIN {L16} [get_ports {SW2}]
set_property IOSTANDARD {LVCMOS33} [get_ports {SW2}]

set_property PACKAGE_PIN {J15} [get_ports {SW3}]
set_property IOSTANDARD {LVCMOS33} [get_ports {SW3}]

# add IO
set_property PACKAGE_PIN {H17} [get_ports {LD0}]
set_property IOSTANDARD {LVCMOS33} [get_ports {LD0}]
```
src/lab0-3/submit/main.v
```verilog
module AND_GATE_3_INPUTS(
        input input1,
        input input2,
        input input3,
        output result
    );

	assign result = input1 & input2 & input3;
endmodule

module AND_GATE_2_INPUTS(
        input input1,
        input input2,
        output result
    );

	assign result = input1 & input2;
endmodule

module OR_GATE_5_INPUTS(
        input input1,
        input input2,
        input input3,
        input input4,
        input input5,
        output result
    );
    assign result = input1 | input2 | input3 | input4 | input5;
endmodule

  

module top(
  input SW0,
  input SW1,
  input SW2,
  input SW3,
  output LD0
);

  
   wire s0;
   wire s1;
   wire s2;
   wire s3;
   wire s4;
   wire s5;
   wire s6;
   wire s7;
  
   wire s8;

   wire s9;
   wire s10;
   wire s11;
   wire s12;
   wire s13;

   assign s0=I0;
   assign s1=I1;
   assign s2=I2;
   assign s3=I3;
   assign O=s8;

   assign s4=~I0;
   assign s5=~I1;
   assign s6=~I2;
   assign s7=~I3;

  

AND_GATE_3_INPUTS GATES_1(
   .input1(s5),
   .input2(s6),
   .input3(s7),
   .result(s9)
);

  
AND_GATE_3_INPUTS GATES_2(
   .input1(s4),
   .input2(s2),
   .input3(s5),
   .result(s10)
);
  

AND_GATE_3_INPUTS GATES_3(
   .input1(s4),
   .input2(s1),
   .input3(s3),
   .result(s11)
);
  

AND_GATE_2_INPUTS GATES_4(
   .input1(s2),
   .input2(s3),
   .result(s12)
);


AND_GATE_2_INPUTS GATES_5(
   .input1(s0),
   .input2(s2),
   .result(s13)
);


OR_GATE_5_INPUTS GATES_6(
   .input1(s9),
   .input2(s10),
   .input3(s11),
   .input4(s12),
   .input5(s13),
   .result(s8)
);
endmodule
```

上板结果：
![[Pasted image 20250226145205.png]]

### 5.2 仿真练习

src/lab0-3/sim/testbench.v
```verilog
module Testbench;
    reg I0,I1,I2,I3;
    wire O;

    initial begin

        I0=1'b0; I1=1'b0; I2=1'b0; I3=1'b0;
        #5;
        
        I0=1'b1; I1=1'b0; I2=1'b0; I3=1'b0;
        #5;
        
        I0=1'b0; I1=1'b1; I2=1'b0; I3=1'b0;
        #5;

        I0=1'b0; I1=1'b0; I2=1'b1; I3=1'b0;
        #5;

        I0=1'b0; I1=1'b0; I2=1'b0; I3=1'b1;
        #5;
        
        I0=1'b1; I1=1'b1; I2=1'b0; I3=1'b0;
        #5;

        I0=1'b1; I1=1'b0; I2=1'b1; I3=1'b0;
        #5;

        I0=1'b1; I1=1'b0; I2=1'b0; I3=1'b1;
        #5;
        
        I0=1'b0; I1=1'b1; I2=1'b1; I3=1'b0;
        #5;
        
        I0=1'b0; I1=1'b1; I2=1'b0; I3=1'b1;
        #5;

        I0=1'b0; I1=1'b0; I2=1'b1; I3=1'b1;
        #5;

        I0=1'b1; I1=1'b1; I2=1'b1; I3=1'b0;
        #5;

        I0=1'b1; I1=1'b1; I2=1'b0; I3=1'b1;
        #5;

        I0=1'b1; I1=1'b0; I2=1'b1; I3=1'b1;
        #5;

        I0=1'b0; I1=1'b1; I2=1'b1; I3=1'b1;
        #5;

        I0=1'b1; I1=1'b1; I2=1'b1; I3=1'b1;
        #5;

         $finish;
    end

    main dut(
        .I0(I0),
        .I1(I1),
        .I2(I2),
        .I3(I3),
        .O(O)
    );

    `ifdef VERILATE
        initial begin
            $dumpfile({`TOP_DIR,"/Testbench.vcd"});
            $dumpvars(0,dut);
            $dumpon;
        end
    `endif
endmodule
```
#### Verilator仿真

	思路：①列出所有可能的情况，补全testbench文件；②打开WSL，进入src/lab0-3目录，make，make wave；③选中并查看I0、I1、I2、I3、O的波形

![[Pasted image 20250226145622.png]]

#### Vivado仿真

	思路：①Add Sources加入testbench.v，set as top；②SIMULATION——Run Simulation——Run Behavioral Simulation；③查看波形

![[Pasted image 20250226145716.png]]
