# SimpleDrawer
Interpreter of a simple math function drawing language.

数学函数绘图语言解释器。

## 1. 项目构建
win10系统

使用VS2015 community构建。

## 2. 函数绘图语言语法说明

### 2.1 基本概念
注意：
<font color="red">本语言不区分大小写</font>

#### 2.1.1 数字字面量
本语言仅支持整数、小数形式的输入，均视为double类型，如下两种输入是合法的：
```
num
```
```
num.num
```
其中num是一个数字。
例:
```
1       -- 合法输入
0.1     -- 合法输入
.1      -- 非法输入
```

#### 2.1.2 内置常量
语言内置了两个常量pi和自然对数e，用户可直接使用。

其中pi=3.1415926，e=2.71828。

#### 2.1.3 变量
本语言不支持用户自定义变量，但内置了一个变量'T'用来支持循环绘图语句。
例如：
```
for T from 0 to 2*pi step pi/50 draw (t, t);
```
for语句的具体语法可参考后文。

#### 2.1.4 表达式
本语言支持数字的加、减、乘、除、乘方5种运算以及几个内置数学函数，具体说明如下：

|运算符|含义|示例|示例说明|  
|---|---|---|---|  
|+|加法运算符|1+1|略|  
|-|略|略|略|  
|*|略|略|略|  
|/|略|略|略|  
|**|乘方运算符|2**4|2的4次方|

|函数名|含义|  
|---|---|  
|sqrt(arg)|开平方|  
|sin(arg)|正弦函数|  
|cos(arg)|余弦函数|  
|tan(arg)|正切函数|  
|ln(arg1,arg2)|以arg1为底arg2的对数|  
|exp(arg)|10的arg次方|  

由四则运算、函数调用以及'('和')'可任意组合成数学表达式。

例如：
```
((1-1/(10/7))*Cos(T)+1/(10/7)*Cos(-T*((10/7)-1)), 
+
(1-1/(10/7))*Sin(T)+1/(10/7)*Sin(-T*((10/7)-1)))
```
### 2.2 基本语句
本语言仅支持原点设置、缩放、旋转和循环绘图4种语句，语句间需使用分号`';'`进行分隔。

其中循环绘图语句为主绘图语句，其余皆为参数设置。

参数是全局的，包括原点、缩放倍数、旋转角度三种，任意时刻均可以使用对应的语句改变这几个参数，
从而影响之后的绘图。

各语句介绍如下：
#### 2.2.1 原点设置
通过设置图像的原点即可对图像进行平移，设置原点的语法为：
```
origin is (Expression, Expression)
```
单位为像素。

例：
```
origin is (20, 200);     
```
```
origin is (cos(20), sin(200));
```
#### 2.2.2 缩放
由于本语言的绘图以像素为单位，欲使图像正常显示，需要放大相应的倍数。

缩放语法为：
```
scale is (Expression, Expression);
```
例：
```
scale is (40, 40);										-- 横纵轴各放大40倍
```
#### 2.2.3 旋转
设置图像绘成之后的旋转角度，仅影响本语句之后的绘图，语法为：  
```
rot is Expression;
```
例：
```
rot is 0;											      -- 不旋转
```
```
rot is pi/2;												-- 顺时针旋转90度
```
```
rot is pi;												  -- 逆时针旋转180度
```

#### 2.2.4 循环绘图
循环绘图语句是本语言的核心语句，图像的绘制通过本语句实现，语法为：
```
for T from startExpression to endExpressiong step stepExpression draw (xExpression, yExpression);
```
含义是T从startExpression开始，以stepExpression为步长，直到endExpressing，
循环绘制点(xExpression, yExpression)。

其中xExpression/yExpression是一个常量或者关于t的表达式，表示某一点的横/纵坐标。

例：

```
for T from 0 to 100 step 1 draw (t, t);		          -- 画一条(0,0)到(100,100)的线段
for T from 0 to 2*pi step pi/50 draw (t, sin(t));		-- 画sin(x)在0~2*pi范围内的轨迹
```

### 2.3 关键字
本语言有如下关键字，在语言实现中采用查表的方式翻译成对应语义，表的设置如下：

|token名|关键字|字面值|函数指针|  
|---|---|---|---|  
| CONST_ID| "PI"	|3.1415926|NULL|   
| CONST_ID| "E"		|2.71828  |NULL|   
| T	  | "T"		|0.0	  |NULL|   
| FUNC	  | "SIN"	|0.0	  |sin |   
| FUNC	  | "COS"	|0.0	  |cos |   
| FUNC	  | "TAN"	|0.0	  |tan |   
| FUNC	  | "LN"	|0.0	  |log |   
| FUNC	  | "EXP"	|0.0	  |exp |   
| FUNC	  | "SQRT"	|0.0	  |sqrt|   
| ORIGIN  | "ORIGIN"	|0.0	  |NULL|   
| SCALE	  | "SCALE"	|0.0	  |NULL|   
| ROT	  | "ROT"	|0.0	  |NULL|   
| IS	  | "IS"	|0.0	  |NULL|   
| FOR	  | "FOR"	|0.0	  |NULL|   
| FROM	  | "FROM"	|0.0	  |NULL|   
| TO	  | "TO"	|0.0	  |NULL|   
| STEP	  | "STEP"	|0.0	  |NULL|   
| DRAW	  | "DRAW"	|0.0	  |NULL|   

## 3. 源程序示例
### 示例1
```
--------------- 函数f(t)=t的图形
--origin is (100, 300);		-- 设置原点的偏移量
--rot is 0;				-- 设置旋转角度(不旋转)
--scale is (1, 1);			-- 设置横坐标和纵坐标的比例
--for T from 0 to 200 step 1 draw (t, 0);
					-- 横坐标的轨迹（纵坐标为0）
--for T from 0 to 150 step 1 draw (0, -t);
					-- 纵坐标的轨迹（横坐标为0）
--for T from 0 to 120 step 1 draw (t, -t);
					-- 函数f(t)=t的轨迹 
origin is (350, 220);
scale is (50, 50);
for t from 0 to 2*pi step pi/100 draw(cos(t), sin(t));
scale is (100, 100);
for t from 0 to 2*pi step pi/200 draw(cos(t), sin(t));
```
运行结果：

![test0](https://raw.githubusercontent.com/zhangpc123/SimpleDrawer/master/images/test0.PNG)

### 示例2
```
--------------- 函数f(t)=t的图形
origin is (200, 300);				-- 设置原点的偏移量
rot is pi/6;					-- 设置旋转角度
scale is (200, 100);				-- 设置横坐标和纵坐标的比例
for T from 0 to 200 step 1 draw (t, 0);		-- 横坐标的轨迹
for T from 0 to 180 step 1 draw (0, t);	-- 纵坐标的轨迹
for T from 0 to 150 step 1 draw (t, t);	-- 函数f(t)=t的轨迹
```
运行结果：

![test1](https://raw.githubusercontent.com/zhangpc123/SimpleDrawer/master/images/test1.PNG)

### 示例3
```
--------------- 图形1：
origin is (20, 200);									-- 设置原点的偏移量
rot is 0;												-- 不旋转
scale is (40, 40);										-- 设置比例
for T from 0 to 2*pi step pi/50 draw (t, -sin(t));		-- 画T的轨迹
origin is (20, 240);									-- 下移40单位
for T from 0 to 2*pi step pi/50 draw (t, -sin(t));		-- 画T的轨迹
origin is (20, 280);									-- 再下移40单位
for T from 0 to 2*pi step pi/50 draw (t, -sin(t));		-- 画T的轨迹

--------------- 图形2：
origin is (380, 240);									-- 右移
scale is (80, 80/3);									-- y方向压缩为三分之一
rot is pi/2+0*pi/3 ;									-- 旋转初值设置
for T from -pi to pi step pi/50 draw (cos(t), sin(t));	-- 画T的轨迹
rot is pi/2+2*pi/3;										-- 旋转2/3*pi
for T from -pi to pi step pi/50 draw (cos(t), sin(t));	-- 画T的轨迹
rot is pi/2-2*pi/3;										-- 再旋转2/3*pi
for T from -pi to pi step pi/50 draw (cos(t), sin(t));	-- 画T的轨迹

--------------- 图形3：
origin is(580, 240);									-- 再右移
scale is (80, 80);										-- 恢复原比例
rot is 0;												-- 不旋转
for t from 0 to 2*pi step pi/50 draw(cos(t),sin(t));	-- 画T的轨迹
for t from 0 to Pi*20 step Pi/50 draw					-- 画T的轨迹
   ((1-1/(10/7))*Cos(T)+1/(10/7)*Cos(-T*((10/7)-1)), 
	(1-1/(10/7))*Sin(T)+1/(10/7)*Sin(-T*((10/7)-1)));
```
运行结果：

![test2](https://raw.githubusercontent.com/zhangpc123/SimpleDrawer/master/images/test2.PNG)


### 示例4
```
--------------- 函数复杂度图形：
rot is 0;										-- 不旋转
origin is (50, 400);							-- 设置新原点（距系统默认原点的偏移量）

scale is (2, 1);								-- 设置比例
for T from 0 to 300 step 1 draw (t, 0);			-- 横坐标
for T from 0 to 300 step 1 draw (0, -t);		-- 纵坐标

scale is (2, 1);								-- 设置比例
for T from 0 to 300 step 1 draw (t, -t);		-- 函数f(t)=t的轨迹

scale is (2, 0.1);								-- 设置比例
for T from 0 to 55 step 1 draw (t, -(t*t));		-- 函数f(t)=t*t的轨迹

scale is (10, 5);								-- 设置比例
for T from 0 to 60 step 1 draw (t, -sqrt(t));	-- 函数f(t)=sqrt(t)的轨迹

scale is (20, 0.1);								-- 设置比例
for T from 0 to 8 step 0.1 draw (t, -exp(t));	-- 函数f(t)=exp(t)的轨迹

scale is (2, 20);								-- 设置比例
for T from 0 to 300 step 1 draw (t, -ln(t));	-- 函数f(t)=ln(t)的轨迹

```
运行结果：

![test3](https://raw.githubusercontent.com/zhangpc123/SimpleDrawer/master/images/test3.PNG)

### 示例5
```
origin is (350, 220);					-- 原点位置
rot is 0;						-- 旋转角度为零
scale is (100, 100);					-- 横、纵坐标比例因子
for t from 0 to 2*pi step pi/100 draw(cos(t), sin(t));	-- 画园
scale is (150, 150);  				        -- 横、纵坐标比例因子
for t from 0 to 2*pi step pi/150 draw(cos(t), sin(t));	-- 画园
scale is (200, 200);					-- 横、纵坐标比例因子
for t from 0 to 2*pi step pi/200 draw(cos(t), sin(t));	-- 画圆
```
运行结果：

![test4](https://raw.githubusercontent.com/zhangpc123/SimpleDrawer/master/images/test4.PNG)

### 示例6
```
--------------- 测试f(t)=t*t与f(t)=t**2的图形：
rot is 0;										-- 不旋转
origin is (50, 400);							-- 设置新原点（距系统默认原点的偏移量）
scale is (1, 1);								-- 设置比例

for T from 0 to 300 step 1 draw (t, 0);			-- 横坐标
for T from 0 to 300 step 1 draw (0, -t);		-- 纵坐标

scale is (2, 0.1);								-- 设置比例
for T from 0 to 55 step 1 draw (t, -(t*t));		-- 函数f(t)=t*t的轨迹

origin is (150, 400);							-- 设置新原点（横坐标右移100）
for T from 0 to 55 step 1 draw (t, -(t**2));	-- 函数f(t)=t**2的轨迹

```
运行结果：

![test5](https://raw.githubusercontent.com/zhangpc123/SimpleDrawer/master/images/test5.PNG)
