# 1  Lab1-1
## 1.1  实验目的
- 进一步学习Logisim 的使用方法，并使用其进行电路设计
- 掌握多路复用器和加法器的内部结构和功能
- 利用原理图所产生的 Verilog 代码进行学习

## 1.2 实验过程

### 1.2.1 四路选择器原理图
![[Pasted image 20250306144715.png]]

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
![[Pasted image 20250306151956.png]]

### 1.2.4 Vivado
#### 1.2.4.1  Synthesis
![[Pasted image 20250306153034.png]]
#### 1.2.4.2 Implementation
![[Pasted image 20250306153149.png]]

#### 1.2.4.3 Simulation
![[Pasted image 20250306154053.png]]
#### 1.2.4.4 上板


## 1.3 思考题

### 1.3.1  Mux2T1_1 是两个 AND，一个 OR 和一个 NOT 组成的简单结构，它是由哪种 decoder 和 AND-OR 结构组成的？


### 1.3.2 Mux4T1_1 是如何组成的

由三个Mux2T1_1组成，输入I(4位)，从高到低对应I[3]-I[0], S(2位)，从高到低对应S[1]-S[0]
I[3]-I[2]、I[1]-I[0]在S[1]的控制下分别输出一位
输出的两位数据在S[0]的控制下输出一位。

### 1.3.2 Mux8T1_1 是如何组成的

Mux8T1_1由两个Mux4T1_1，连接一个Mux2T1_1组成
输入I(8位)，从高到低对应I[7]-I[0], S(3位)，从高到低对应S[2]-S[0]
I[7]-I[4]、I[3]-I[0]在S[1]S[0]的控制下分别输出一位
输出的两位数据在S[2]的控制下输出一位。

### 1.3.3 Mux2<sup>m</sup>T1_n 是如何构成的

由两个Mux2<sup>m-1</sup>T1_n，和一个Mux2T1_n构成

# 2  Lab1-2

## 2.1 实验目的
- 掌握使用 Verilog 语言进行电路设计的方法
- 掌握七段数码管显示译码器的内部电路结构及其设计
- 实现七段数码管十六进制数显示

## 2.2 实验过程

### 2.2.1 译码管设计
```verilog
`timescale 1ns / 1ps
module and4(
    input I0,
    input I1,
    input I2,
    input I3,
    output O
);
    assign O=I0 & I1 & I2 & I3;
endmodule

module and3(
    input I0,
    input I1,
    input I2,
    output O
);
    assign O=I0 & I1 & I2;
endmodule

module and2(
    input I0,
    input I1,
    output O
);
    assign O=I0 & I1;
endmodule

module or3(
    input I0,
    input I1,
    input I2,
    output O
);
    assign O=I0 | I1 | I2;
endmodule

module or4(
    input I0,
    input I1,
    input I2,
    input I3,
    output O
);
    assign O=I0 | I1 | I2 | I3;
endmodule

module or2(
    input I0,
    input I1,
    output O
);
    assign O=I0 | I1;
endmodule

  
module SegDecoder (
    input wire [3:0] data,//要显示的十六进制数
    input wire point,//1显示小数点
    input wire LE,//0使能
  
    output wire a,
    output wire b,
    output wire c,
    output wire d,
    output wire e,
    output wire f,
    output wire g,
    output wire p //小数点
);
    wire [20:0] s;
    and4 and4_0(~data[0],~data[1],data[2],data[3],s[0]);
    and4 and4_1(data[0],data[1],data[2],~data[3],s[1]);
    and3 and3_0(~data[1],~data[2],~data[3],s[2]);

    and3 and3_1(data[0],data[1],~data[3],s[3]);
    and3 and3_2(data[1],~data[2],~data[3],s[4]);
    and3 and3_3(data[0],~data[2],~data[3],s[5]);
    and3 and3_4(data[0],~data[1],~data[2],s[6]);
    and3 and3_5(~data[1],data[2],~data[3],s[7]);

    and2 and2_0(data[0],~data[3],s[8]);

    and4 and4_2(data[0],~data[1],data[2],~data[3],s[9]);
    and3 and3_6(data[0],data[1],data[2],s[10]);
    and3 and3_7(data[1],data[2],data[3],s[11]);

    and4 and4_3(~data[0],data[1],~data[2],~data[3],s[12]);

    and3 and3_8(data[0],data[2],data[3],s[13]);
    and3 and3_9(~data[0],data[2],data[3],s[14]);
    and3 and3_10(~data[0],data[1],data[2],s[15]);
  

    and4 and4_4(data[0],~data[1],data[2],~data[3],s[16]);
    and4 and4_5(data[0],data[1],~data[2],data[3],s[17]);
    and4 and4_6(data[0],~data[1],data[2],data[3],s[18]);
    and4 and4_7(~data[0],~data[1],data[2],~data[3],s[19]);
    and4 and4_8(data[0],~data[1],~data[2],~data[3],s[20]);

    wire o0,o1,o2,o3,o4,o5,o6;

    or3 or3_0(s[0],s[1],s[2],o0);
    or4 or4_0(s[3],s[4],s[5],s[18],o1);
    or3 or3_1(s[6],s[7],s[8],o2);
    or4 or4_1(s[9],s[10],s[19],s[20],o3);
    or3 or3_2(s[11],s[12],s[14],o4);
    or4 or4_2(s[13],s[14],s[15],s[16],o5);
    or4 or4_3(s[17],s[18],s[19],s[20],o6);
  
    assign p=~point;
    assign g=LE|o0;
    assign f=LE|o1;
    assign e=LE|o2;
    assign d=LE|o3;
    assign c=LE|o4;
    assign b=LE|o5;
    assign a=LE|o6;

endmodule //SegDecoder
```

#### 2.2.2  复合多路选择器及语法分析比较
##### 使用case语法
	√ 简单易懂
	× 对于Mux2^mT1_n，m值较大时，需要写的例子较多，会比较繁琐
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

#### 2.2.3  综合实现数码管

##### Verilator仿真
![[Pasted image 20250307155007.png]]

##### Vivado

###### Synthesis
![[Pasted image 20250307155325.png]]

###### Implementation
![[Pasted image 20250307155410.png]]

###### Simulation


#### 2.2.4  展开for语句为初始化序列

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

对for语句的理解：间隔单位时间，等步长地改变输入数据的值以查看不同输出结果