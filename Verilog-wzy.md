## 数值系统
0：低电平/False
1：高电平/True
x/X：电平未知
z/Z：高阻态（接近于没有输出）

## 数字表示
```verilog
### <bits>'<radix><value>

bits：二进制位宽
radix：进制，十进制（d/D）、十六进制（h/H）、二进制（b/B）、八进制（o/O）
value：实际数值（可以加入下划线提高可读性）
	如：-4'hf=-4'd15=-15; 9'o210=9'b010_001_000='b0_1000_1000=136
	* 十六进制中不分大小写
```

## 标识符与变量

```verilog

wire
	声明线网型数据，默认初始值为z。verilog的默认数据类型
	本质为导线
reg
	声明在always语句内部进行赋值操作的信号。一般对应一种存储单元，默认初始值为x。
	*在always语句内部被赋值的信号，都应该被定义成reg

声明变量时，一般用如下格式：
	wire/reg [width-1:0] (<var_name>,...) ;
		width-x:位宽,0:从0开始
		如：wire [4:0] my_vec代表一个位宽为5的向量my_vec，范围是0-4
		数组索引从高到低！
		my_vec[0] // 表示最低位
		my_vec[3:2]// 表示第四、三位
		my_vec[4]// 表示最高位，值为 1'b0

也可以声明数组：
	wire/reg [width-1:0] <var_name> [0:width-1];
		reg [7:0] my_regs [0:31];
			//声明了一个数组，该数组由 32 个位宽为 8 的 reg 型变量组成。
```

### 运算符

#### 算数运算符
	+、-、*、/、%

#### 关系运算符
	>、<、>=、<=、==、!=、===（全等）、!==（非全等）
	运算结果固定为 0（False）或 1（True），是一个 1bit 的值。
	如果某一个操作数中*有一位为 x 或 z*，则前六种关系运算的结果为 x。
	全等比较则对为 x 或 z 的位也进行比较，只有两个操作数完全一致时，其结果才是 1

#### 逻辑运算符
	&&：与，等价于A*B
	||：或，等价于A+B
	！：非，取反
	如果一个操作数不为 0，则在逻辑运算时它等价于逻辑 1
	如果一个操作数等于 0，则它运算时等价于逻辑 0
	如果它任意一位为 x 或 z，则它等价于 x

#### 按位运算符
	~ 按位非
	& 按位与
	| 按位或
	^ 按位异或：不同为1
	~^或^~ 按位同或：不同为0

#### 归约运算符
	对一个数的每一位做处理，输出1bit结果

#### 移位运算符
	<<:逻辑左移，右边低位补0
	>>：逻辑右移，左边高位补0
	<<<：算数左移，右边低位补0
	>>>：算数右移，左边高位补符号位

#### 拼接运算符
	{表达式 1，表达式 2,....,表达式 N}
	与拼接操作经常一起使用的是重复操作，其一般格式是：重复次数{表达式}
	a[7:4]={a[0], a[1] ,a[2], a[3]};
		（a[7]=a[0],a[6]=a[1],a[5]=a[2],a[4]=a[3])
	b[31:0]={{24{a[7]}}, a[7:0]};

### Verilog语句

#### 连续赋值：assign
```verilog
assign LHS=RHS;//LHS必须是wire型

	实例：
	wire Cout,A,B;//define
	assign Cout=A&B;

	(等价于)
	wire A,B;
	wire Cout=A&B;
**只要 RHS 表达式的操作数有事件发生（值的变化）时，RHS 就会立刻重新计算，同时赋值给 LHS**
```

#### 过程赋值：always、initial
```verilog
用于对reg型变量赋值
always和initial不可嵌套，彼此同时执行。
always会一直循环，initial只执行一次

always引入了敏感变量的概念
	always @(敏感变量列表) 过程语句
		// 每当 a 或 b 的值发生变化时就执行内部的语句
		always @(a or b) begin
		    [过程语句]
		end
%% 	允许使用符号 `*` 在敏感列表中表示缺省，编译器会根据 always 块内部的内容自动识别敏感变量 %%
	reg Cout;
	wire A, B;
	always @(*) begin
	    Cout = A & B;
	end
	Verilog 还支持通过使用 `posedge` 和 `negedge` 关键字将电平变化作为敏感变量。
	其中 `posedge` 对应上升沿，`negedge` 对应下降沿。
	例如：下面的代码仅在 clk 从低电平（0）变为高电平（1）时触发。
	reg Cout;
	wire A, B, clk;
	always @(posedge clk) begin
	    Cout <= A & B;
	end
```

#### if-else
	只能在always内使用，**if-else 是二路选择器，不是 C 语言的表达式**。
```verilog
reg O; 
always @(*) begin 
	if (S0) O <= I[1]; 
	else O <= I[0]; 
end
```

#### case
```verilog
module Mux4To1( 
	input [3:0] I, 
	input [1:0] S, 
	output reg O 
); 
	always @(*) begin 
		case (S) 
			2'b00: O <= I[0]; 
			2'b01: O <= I[1]; 
			2'b10: O <= I[2]; 
			2'b11: O <= I[3]; 
		endcase 
	end 
	
endmodule
```


> [!warning] WARNING
> if 语句要有完整的 if-else
case 语句要遍历所有的 case 硬件码，或者提供 default


> [!NOTE] <=非阻塞赋值
> ```verilog
> module example ( 
> 	input clk, 
> 	input [3:0] I, 
> 	output reg [3:0] O
>  ); 
> 	 always @(posedge clk) begin 
> 		 O <= I[0]; // 非阻塞赋值 
> 	 end 
> endmodule
> 时钟周期结束后才会更新，不同于=（阻塞赋值）即时更新
```
```

#### generate语句
```verilog
wire [3:0] a [3:0];
wire [3:0] b [3:0];

genvar i;//定义仅用于generate语句内部的变量
generate
    for(i=0;i<4;i=i+1)begin
        assign b[i] = a[i];
    end
endgenerate
```

#### integer变量
32位的变量，等于32bit的reg
16位变量：short，64位变量：longint
```verilog
integer i;
initial begin
    ...
    for(i=0;i<8;i=i+1)begin
        data=i[3:0];
        #5;
    end
    ...
end
```

### 参数化编程
#### 宏变量参数
方便修改
```verilog
`define LEN 4
wire [3:0] a [`LEN-1:0];
wire [3:0] b [`LEN-1:0];

genvar i;
generate
    for(i=0;i<`LEN;i=i+1)begin
        assign b[i] = a[i];
    end
endgenerate
```

#### localparam本地参数
类似const
只能定义在模块内部，仅对本模块有用
```verilog
localparam LEN=4;
wire [3:0] a [LEN-1:0];
wire [3:0] b [LEN-1:0];

genvar i;
generate
    for(i=0;i<LEN;i=i+1)begin
        assign b[i] = a[i];
    end
endgenerate
```

#### parameter变量
可修改覆盖原来的值
```verilog
module dummy #(
    parameter LEN = 4 //可以是多个，用‘，’隔开
) (
    input ...
    output ...
);
    ...
    wire [3:0] a [LEN-1:0];
    wire [3:0] b [LEN-1:0];

    genvar i;
    generate
        for(i=0;i<LEN;i=i+1)begin
            assign b[i] = a[i];
        end
    endgenerate
    ...
endmodule

dummy dummy1 (
    ...
);// LEN = 4

dummy #(
    .LEN(5)
) dummy2 (
    ...
);
```

### 多路选择器

	包括输入信号 I0,I1,I2,⋯,IN，选择信号 S0,S1,⋯,SlogN，输出信号 O。当输入信号 S={SlogN,⋯,S1,S0}等于 i 时，输出信号 O=Ii。
#### 二路选择器
![[Pasted image 20250226182442.png|182]]
	电路图：
    ![[Pasted image 20250226182503.png|450]]
```verilog
module Mux2To1(
	input I0,
	input I1,
	input S0,
	output O
);
	assign O = S0 ? I1 : I0 ; 
endmodule
```

> [!NOTE] ? :
> 输入（S0）为1，判断为真，输出I1，反之I0

#### 四路选择器
![[Pasted image 20250226183255.png|400]]
	电路图：
	![[Pasted image 20250226183349.png|425]]


```verilog
module Mux2To1(
	input I0,
	input I1,
	input S0,
	output O
);
	assign O = I1 & S0 | I0 & ~S0 ;
endmodule

module Mux4To1(
	input [3:0] I,
	input [1:0] S,
	output O
);
	wire [1:0] I_S;

	Mux2To1 mux0(I[0],I[1],S[0],I_S[0]); 
	Mux2To1 mux1(I[2],I[3],S[0],I_S[1]); 
	Mux2To1 mux2(I_S[0],I_S[1],S[1],O);
	
endmodule
```
> [!NOTE] 一维数组
> ```verilog
wire/input...  [a:b] I
> 如： wire [5:7] I;  //I[5], I[6], I[7]
> 	assign a[2:4]=b[7:5];   //a[2] 连接 b[7]，a[3] 连接 b[6]，a[4] 连接 b[5]


> [!NOTE] 二维数组
> ```verilog
> wire [num2 : num1] a [num3 : num4];
> 共有|num4-num3|+1组线路，每组有|num2-num1|根线路
> a[i]表示其中一组线路，a[i:j]表示a[i]-a[j]组线路总和

#### index索引

exp1
```verilog
wire [3:0] I; 
wire [1:0] S;  //S为一个两位宽的数（00，01，10，11），即0-3
wire [1:0] O; 
assign O[0] = I[1]; 
assign O[1] = I[S]; //若S=1，则O[1]=I[1]，以此类推
```

exp2
```verilog
module LUT3( 
	input [2:0] S,//S=0~8
	output O 
); 
	wire [7:0] array; 
	assign array = 8'b10010110; 
	assign O = array[S]; 

endmodule
```


### 变量译码器（decoder）
	译码器（decoder）：多输入多输出组合逻辑电路器件，可分为变量译码和显示译码两类
		广义的译码器是指将二进制编码输入转换为任意的编码输出，且这个转换关系可以不满足单射。
	变量译码器：较少输入→较多输出，常见的如n线-2^n线译码、8421BCD码译码
	显示译码器：将二进制数转换成对应的七段码，一般可分为驱动LED和驱动LCD

### 复合多路选择器
	多路选择器：从单bit中选取单bit输出；复合多路选择器：从多组输入中选取单组输出	
#### 实现方式
##### 多个一位多路选择器组合
```verilog
module Mux4T1_4(
	input [3:0]I0,
	input [3:0]I1,
	input [3:0]I2,
	input [3:0]I3,
	input [1:0]S,
	output [3:0]O
);

	Mux4T1_1({I3[0],I2[0],I1[0],I0[0]},S,O[0]);
	Mux4T1_1({I3[1],I2[1],I1[1],I0[1]},S,O[1]);
	Mux4T1_1({I3[2],I2[2],I1[2],I0[2]},S,O[2]);
	Mux4T1_1({I3[3],I2[3],I1[3],I0[3]},S,O[3]);

endmodule
```

> [!info] { }的作用
> 将里面的单位数据组合成多位数据输入
> - `LEN({ W0, W1, W2, ..., Wn }) = Sum(LEN(W0), LEN(W1), ..., LEN(Wn))`

##### 与或形式
```verilog
module Mux4T1_4(
	input [3:0] I0, 
	input [3:0] I1, 
	input [3:0] I2, 
	input [3:0] I3, 
	input [1:0] S, 
	output reg [3:0] O 
); 
	wire [3:0] sel; 
	assign sel[0] = ~S[0]&~S[1]; 
	assign sel[1] = S[0]&~S[1]; 
	assign sel[2] = ~S[0]&S[1]; 
	assign sel[3] = S[0]&S[1]; 
	assign O = {4{sel[0]}}&I0 | {4{sel[1]}}&I1 | {4{sel[2]}}&I2 | {4{sel[3]}}&I3; 

endmodule
```

##### ?: 语法
```verilog
module Mux4T1_4(
    input [3:0] I0,
    input [3:0] I1,
    input [3:0] I2,
    input [3:0] I3,
    input [1:0] S,
    output [3:0] O
);

    assign O=S[1] ? (S[0] ? I3 : I2) : (S[0] ? I1 : I0);

endmodule
```

##### index 索引
```verilog
module Mux4T1_4(
    input [3:0] I0,
    input [3:0] I1,
    input [3:0] I2,
    input [3:0] I3,
    input [1:0] S,
    output [3:0] O
);

    wire [3:0] I [3:0];
    assign I[0] = I0;
    assign I[1] = I1;
    assign I[2] = I2;
    assign I[3] = I3;

    assign O = I[S];

endmodule
```

##### if-else 语法
```verilog
module Mux4T1_4(
    input [3:0] I0,
    input [3:0] I1,
    input [3:0] I2,
    input [3:0] I3,
    input [1:0] S,
    output reg [3:0] O
);

    always @(*) begin
        if (S[1]) begin
            if (S[0]) O <= I3;
            else O <= I2;
        end else begin
            if (S[0]) O <= I1;
            else O <= I0;
        end      
    end 

endmodule
```

##### case语法
```verilog
module Mux4T1_4(
    input [3:0] I0,
    input [3:0] I1,
    input [3:0] I2,
    input [3:0] I3,
    input [1:0] S,
    output reg [3:0] O
);

    always @(*) begin
        case (S)
            2'b00 : O <= I0;
            2'b01 : O <= I1;
            2'b10 : O <= I2;
            2'b11 : O <= I3;
        endcase
    end 

endmodule
```

### 七段数码管
	数码管：七段数码管、八段数码管（多一个用于显示小数点的发光二极管单元DP（decimal point）
	七段数码管：共阳极&共阴极，共阳极的七段数码管的正极（或阳极）为八个发光二极管的共有正极，正极接电，负极接地

> [!tip] Nexys A7
>  [Nexys A7](https://digilent.com/reference/programmable-logic/nexys-a7/start) 开发板的七段数码管为**共阳极**数码管，当输入信号为 0 时，对应数码管亮；输入信号为 1 时，对应数码管灭。

![[Pasted image 20250301142425.png|500]]
显示译码的对应关系：
![[Pasted image 20250301142458.png|159]]

### 动态时分复用
	Nexys-A7 开发板有八个七段数码管，所有七段数码管共用 CA、CB ... DP 的输出。此外每个七段数码管有自己专用的 AN 信号线作为使能信号，当 AN=1 时，七段数码管无论 CA、CB ... DP 输入为多少，七段数码管不亮；**当 AN=0 时七段数码管才根据 CA、CB ... DP 的输入亮灭**。


> [!example] 让八个七段数码管分别显示 32'h12345678
> 将一个时间周期平均分为八分
> 
> 在第一分时间内我们将 4'h1 的译码结果输出到七段数码管，然后将第一个七段数码管的 AN 启动，其余七段数码管的 AN 关闭，这样第一个七段数码管会显示数字 1，其余数码管不亮。
> 
> 第二个时间片输出 4'h2 的译码结果，仅使能第二个七段数码管，这样只有第二个七段数码管显示数字 2，其余数码管不亮。
> 
> 以此类推，最后每个数码管都会有八分之一的时间显示自己对应的数据，然后因为视觉残留，最后人眼会感觉八个数码管同时亮起，且分别显示对应的数据。
> 

