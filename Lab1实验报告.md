# 1  Lab1-1
## 1.1  实验目的
- 进一步学习Logisim 的使用方法，并使用其进行电路设计
- 掌握多路复用器和加法器的内部结构和功能
- 利用原理图所产生的 Verilog 代码进行学习

## 1.2 实验过程

### 1.2.1 四路选择器原理图
![[Pasted image 20250306144715.png|500]]

### 1.2.2  八路选择器代码解释
```verilog
//二路选择器
module Mux2T1_1(
   input I0,
   input I1,
   input S0,
   output O
);
   assign O=S0? I1:I0; //S0为低电平则输出I0，高电平则输出I1
endmodule

//八路选择器
module Mux8T1_1(
   input [7:0] I,
   input [2:0] S,
   output O
);

   wire I01;
   wire I02;

//调用从logisim导出的Mux4T1_1模块
  Mux4T1_1 mux1(
        .I0(I[7]),
        .I1(I[6]),
        .I2(I[5]),
        .I3(I[4]),
        .S(S[1:0]),
        .O(I01)
    ); //处理输入的第一个4bit数据，输出1bit（I01）

  Mux4T1_1 mux2(
        .I0(I[3]),
        .I1(I[2]),
        .I2(I[1]),
        .I3(I[0]),
        .S(S[1:0]),
        .O(I02)
    ); //处理输入的第二个4bit数据，输出1bit（I02）

 Mux2T1_1 mux3(
        .I0(I01),
        .I1(I02),
        .S0(S[2]),
        .O(O)
    ); //根据S[2]的值决定最终输出I01还是I02

//最终可以实现八选一一位多路复用器的功能
endmodule
```

### 1.2.3 Testbench设计
```verilog
module Testbench;
    reg [7:0] I;
    reg [2:0] S;
    wire O;

    initial begin
        I=8'b10101010; //对于一个给定的I
        //遍历所有S的组合
        S=3'b000;
        #5;

        S=3'b001;
        #5;

        S=3'b010;
        #5;

        S=3'b011;
        #5;

        S=3'b100;
        #5;

        S=3'b101;
        #5;

        S=3'b110;
        #5;

        S=3'b111;
        #5;

		$finish;
    end

  

    Mux8T1_1 dut(
        .I(I),
        .S(S),
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

I=8'b10101010时，真值表应为

|  S  |  O  |  对应  |
| :-: | :-: | :--: |
| 000 |  1  | I[7] |
| 001 |  0  | I[6] |
| 010 |  1  | I[5] |
| 011 |  0  | I[4] |
| 100 |  1  | I[3] |
| 101 |  0  | I[2] |
| 110 |  1  | I[1] |
| 111 |  0  | I[0] |
Verilate仿真结果：（与真值表相同）
![[Pasted image 20250313152349.png|600]]


### 1.2.4 Vivado
#### 1.2.4.1  Synthesis
![[Pasted image 20250306153034.png|450]]
#### 1.2.4.2 Implementation
![[Pasted image 20250306153149.png|450]]

#### 1.2.4.3 Simulation
![[Pasted image 20250306154053.png|450]]
#### 1.2.4.4 上板
![[d4afcad6ce5eb1f528d341d15561262.jpg|475]]

## 1.3 思考题

### 1.3.1  Mux2T1_1 是两个 AND，一个 OR 和一个 NOT 组成的简单结构，它是由哪种 decoder 和 AND-OR 结构组成的？

1-to-2 decoder，2×1 AND-OR

### 1.3.2 Mux4T1_1 是如何组成的

2-to-4 decoder，4×1 AND-OR
### 1.3.2 Mux8T1_1 是如何组成的

3-to-8 decoder，8×1 AND-OR

### 1.3.3 Mux2<sup>m</sup>T1_n 是如何构成的

m-to-2<sup>m</sup> decoder，2<sup>m</sup> \*n×n AND-OR

# 2  Lab1-2

## 2.1 实验目的
- 掌握使用 Verilog 语言进行电路设计的方法
- 掌握七段数码管显示译码器的内部电路结构及其设计
- 实现七段数码管十六进制数显示

## 2.2 实验过程

### 2.2.1 译码管设计
```verilog
`timescale 1ns / 1ps

module SegDecoder (
    input wire [3:0] data,
    input wire point,
    input wire LE,

    output wire a,
    output wire b,
    output wire c,
    output wire d,
    output wire e,
    output wire f,
    output wire g,
    output wire p
);
  
  wire wire0, wire1, wire2, wire3;
  wire inv_wire0, inv_wire1, inv_wire2, inv_wire3;
  assign {wire3 ,wire2, wire1, wire0} = data;
  assign {inv_wire0, inv_wire1, inv_wire2, inv_wire3} = {~wire0, ~wire1, ~wire2, ~wire3};
  
  // minterms for number 0-f
  wire [15:0] and_gate;
  assign and_gate[0] = inv_wire0 & inv_wire1 & inv_wire2 & inv_wire3;//0
  assign and_gate[1] = wire0 & inv_wire1 & inv_wire2 & inv_wire3; // 1
  assign and_gate[2] = inv_wire0 & wire1 & inv_wire2 & inv_wire3;//2
  assign and_gate[3] = wire0 & wire1 & inv_wire2 & inv_wire3;//3
  assign and_gate[4] = inv_wire0 & inv_wire1 & wire2 & inv_wire3; // 4
  assign and_gate[5] = wire0 & inv_wire1 & wire2 & inv_wire3;//5
  assign and_gate[6] = inv_wire0 & wire1 & wire2 & inv_wire3;//6
  assign and_gate[7] = wire0 & wire1 & wire2 & inv_wire3;//7
  assign and_gate[8] = inv_wire0 & inv_wire1 & inv_wire2 & wire3;//8
  assign and_gate[9] = wire0 & inv_wire1 & inv_wire2 & wire3;//9
  assign and_gate[10] = inv_wire0 & wire1 & inv_wire2 & wire3;//A
  assign and_gate[11] = wire0 & wire1 & inv_wire2 & wire3; // B
  assign and_gate[12] = inv_wire0 & inv_wire1 & wire2 & wire3;//C
  assign and_gate[13] = wire0 & inv_wire1 & wire2 & wire3; // D
  assign and_gate[14] = inv_wire0 & wire1 & wire2 & wire3;//E
  assign and_gate[15] = wire0 & wire1 & wire2 & wire3;//F
  
  // SegDecoder
  wire a_or, a_result;
  assign a_or = and_gate[1] | and_gate[4] | and_gate[11] | and_gate[13];
  assign a_result = a_or | LE;
  assign a = a_result;

  wire b_or, b_result;
  assign b_or = and_gate[5] | and_gate[6] | and_gate[11] | and_gate[12] | and_gate[14] | and_gate[15];
  assign b_result = b_or | LE;
  assign b = b_result;

  wire c_or, c_result;
  assign c_or = and_gate[2] | and_gate[12] | and_gate[14] | and_gate[15];
  assign c_result = c_or | LE;
  assign c = c_result;

  wire d_or, d_result;
  assign d_or = and_gate[1] | and_gate[4] | and_gate[7] | and_gate[10] | and_gate[15];
  assign d_result = d_or | LE;
  assign d = d_result;

  wire e_or, e_result;
  assign e_or = and_gate[1] | and_gate[3] | and_gate[4] | and_gate[5] | and_gate[7] | and_gate[9];
  assign e_result = e_or | LE;
  assign e = e_result;

  wire f_or, f_result;
  assign f_or = and_gate[1] | and_gate[2] | and_gate[3] | and_gate[7] | and_gate[13];
  assign f_result = f_or | LE;
  assign f = f_result;

  wire g_or, g_result;
  assign g_or = and_gate[0] | and_gate[1] | and_gate[7] | and_gate[12];
  assign g_result = g_or | LE;
  assign g = g_result;
  
  assign p=~point;
 

endmodule
```

### 2.2.2  复合多路选择器及语法分析比较
##### 使用case语法

可读性高，清晰易懂，但若Mux2<sup>m</sup>T1_n 中 m 值较大时，代码量会显著增加
```verilog
module Mux4T1_32(
    input [31:0] I0,
    input [31:0] I1,
    input [31:0] I2,
    input [31:0] I3,
    input [1:0] S,
    output reg [31:0] O
);

    always @(*) begin
        case(S)
            2'b00: O=I0;
            2'b01: O=I1;
            2'b10: O=I2;
            2'b11: O=I3;
            default: O=32'b0;
        endcase
    end
    

endmodule
```

##### 使用if-else语法

结构清晰，但S位宽增加时，嵌套层数显著增加，代码复杂度变高
```verilog
module Mux4T1_32(
    input [31:0] I0,
    input [31:0] I1,
    input [31:0] I2,
    input [31:0] I3,
    input [1:0] S,
    output reg [31:0] O
);

    always @(*) begin
        if (S[1]) begin
            if (S[0]) O = I3;
            else O = I2;
        end else begin
            if (S[0]) O = I1;
            else O = I0;
        end      
    end 

endmodule
```

##### 使用index索引语法

简洁，适用于输入数量较多的多路选择器
```verilog
module Mux4T1_32(
    input [31:0] I0,
    input [31:0] I1,
    input [31:0] I2,
    input [31:0] I3,
    input [1:0] S,
    output [31:0] O
);

    wire [31:0] I [3:0];
    assign I[0] = I0;
    assign I[1] = I1;
    assign I[2] = I2;
    assign I[3] = I3;

    assign O = I[S];

endmodule
```

##### 使用 ？：语法

简洁，但可读性较差，且S位宽增加时，嵌套层次显著增加，代码复杂度高
```verilog
module Mux4T1_32(
    input [31:0] I0,
    input [31:0] I1,
    input [31:0] I2,
    input [31:0] I3,
    input [1:0] S,
    output [31:0] O
);
    assign O=S[1] ? (S[0] ? I3 : I2) : (S[0] ? I1 : I0);
    //S[0]在I3 I2中选一个，I1 I0中选一个，S[1]在选出的两个中选一个
endmodule
```

##### 与或形式

直接映射到硬件电路，但代码较冗长，可读性差
```verilog
module Mux4T1_32(
    input [31:0] I0,
    input [31:0] I1,
    input [31:0] I2,
    input [31:0] I3,
    input [1:0] S,
    output reg [31:0] O
);

    wire [3:0] sel;
    assign sel[0] = ~S[0]&~S[1];
    assign sel[1] = S[0]&~S[1];
    assign sel[2] = ~S[0]&S[1];
    assign sel[3] = S[0]&S[1];
    assign O = {4{sel[0]}}&I0 | {4{sel[1]}}&I1 | {4{sel[2]}}&I2 | {4{sel[3]}}&I3;
    
endmodule
```

##### 多个一位多路选择器组合

简洁，结构清晰，易修改
```verilog
module Mux4T1_32(
    input [31:0] I0,
    input [31:0] I1,
    input [31:0] I2,
    input [31:0] I3,
    input [1:0] S,
    output [31:0] reg O
);

	integer i=0;
	
	always @(*)begin
		for(i=0;i<32;i=i+1)begin
			Mux4T1_1({I3[i], I2[i], I1[i], I0[i]}, S, O[i]);
		end;
    end

endmodule
```
### 2.2.3  综合实现数码管

#### Verilator仿真
![[Pasted image 20250310183733.png|450]]

#### Vivado

##### Synthesis
![[Pasted image 20250310190236.png|475]]

##### Implementation
![[Pasted image 20250310190425.png|475]]

##### Simulation
![[Pasted image 20250310184530.png|450]]
##### 上板
![[b8b2cce979b2c0cc62416257017add4.jpg|300]]


### 2.2.4  展开for语句为初始化序列

```verilog
//for版本
for(i=0;i<8;i=i+1)begin
    data=i[3:0];
    #5;
    end

point=1'b0;

for(i=8;i<16;i=i+1)begin
    data=i[3:0];
    #5;
    end
```

```verilog
//展开
data=4'b0000;
#5;
data=4'b0001;
#5;
data=4'b0010;
#5;
data=4'b0011;
#5;
data=4'b0100;
#5;
data=4'b0101;
#5;
data=4'b0110;
#5;
data=4'b0111;
#5;

point=1'b0;

data=4'b1000;
#5;
data=4'b1001;
#5;
data=4'b1010;
#5;
data=4'b1011;
#5;
data=4'b1100;
#5;
data=4'b1101;
#5;
data=4'b1110;
#5;
data=4'b1111;
#5;
```

对`for`语句的理解：用于生成重复逻辑，处理数组等，在`testbench.v` 中的作用为间隔相等时间，等步长地改变测试值
### 2.2.5 各种多路选择器写法比较

|  特性  | case | if-else | index | ? : | 与或  | 多个一位多路选择器 |
| :--: | :--: | :-----: | :---: | :-: | :-: | :-------: |
| 可读性  |  高   |    中    |   中   |  低  |  低  |     中     |
| 简洁性  |  中   |    中    |   高   |  高  |  中  |     中     |
| 扩展性  |  低   |    低    |   高   |  低  |  中  |     高     |
| 适用场景 | 小规模  |   小规模   |  大规模  | 小规模 | 小规模 |   硬件设计    |
最喜欢的：`case`语法(理由：可读性高，易于检查，书写逻辑清晰)