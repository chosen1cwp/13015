# 第二章、程序的转换及机器级表示

---
## 目录

- [一、指令](#考点一指令)
- [二、寄存器传送语言（Register Transfer Language, RTL）](#考点二寄存器传送语言register-transfer-language-rtl)
- [三、指令系统设计](#考点三指令系统设计)
- [四、生成机器代码的过程](#考点四生成机器代码的过程)
- [五、Intel和AT&T格式指令区别](#考点五intel和att格式指令区别)
- [六、数据类型及格式](#考点六数据类型及格式)
- [七、定点寄存器组](#考点七定点寄存器组)
- [八、条件标示](#考点八条件标示)
- [九、定点算术运算指令](#考点九定点算术运算指令)
- [十、寻址方式](#考点十寻址方式)
- [十一、浮点寄存器栈和多媒体扩展寄存器组](#考点十一浮点寄存器栈和多媒体扩展寄存器组)
- [十二、通用数据传送指令](#考点十二通用数据传送指令)
- [十三、地址传送指令](#考点十三地址传送指令)
- [十四、移位指令](#考点十四移位指令)
- [十五、程序执行流控制指令](#考点十五程序执行流控制指令)
- [十六、过程调用](#考点十六过程调用)
- [十七、值传递和地址传递](#考点十七值传递和地址传递)
- [十八、选择语句](#考点十八选择语句)
- [十九、循环语句](#考点十九循环语句)
- [二十、数组](#考点二十数组)
- [二十一、数据的对齐方式](#考点二十一数据的对齐方式)
- [二十二、x86-64的基本特点](#考点二十二x86-64的基本特点)
- [二十三、x86-64的数据传送指令](#考点二十三x86-64的数据传送指令)
- [二十四、x86-64的算术逻辑运算指令](#考点二十四x86-64的算术逻辑运算指令)

---

### 考点一、指令

![](./attachments/chapter03/2dc3eba0-feed-4822-aa84-dfaf0270e4db.png)

**8位R型指令**

```
[ op(4位) | rt(2位)  | rs(2位) ]  
[ 操 作 码 | 目标寄存器 | 源寄存器 ]
```
> <span style="color:red;">**rt**</span>（<span style="color:red;">t</span>arget <span style="color:red;">r</span>egister）：目标寄存器（被操作对象/目标、参与运算 / 有时也作为结果）
> 
> <span style="color:red;">**rs**</span>（<span style="color:red;">s</span>ource <span style="color:red;">r</span>egister）：源寄存器（提供数据、数据来源）

**OP为操作码字段：**

**1)R型指令**  
+ op为 <span style="color:red;">0000</span> 定义为<span style="color:red;">传送</span>（<span style="color:red;">mov</span>）操作  
+ op为 <span style="color:Aqua;">0001</span> 定义为<span style="color:Aqua ;">加</span>（<span style="color:Aqua ;">add</span>）操作

比如：
> 指令 <span style="color:DodgerBlue;">0001</span> <span style="color:red;">00</span><span style="color:Aqua;">01</span> 的功能为R[0]←R[0] <span style="color:DodgerBlue;">+</span> R[1]，表示将0号寄存器和1号寄存器内容相加的结果送到0号寄存器。

**2）M型指令**

+ op为<span style="color:red;">1110</span>定义为<span style="color:red;">取数</span>（<span style="color:red;">load</span>）操作
+ op为<span style="color:Aqua ;">1111</span>定义为<span style="color:Aqua ;">存数</span>（<span style="color:Aqua;">store</span>）操作

比如：
> 指令 <span style="color:red;">1110</span> <span style="color:aqua;">0110</span> 的功能为R[0]←<span style="background-color:yellow;"><span style="color:red;">M</span></span>[0110]，表示将6号主存单元（地址为0110）中的内容取到0号寄存器

### 考点二、寄存器传送语言（Register Transfer Language, <span style="color:red;">RTL</span>）

<span style="color:red;">R[r]</span>：表示寄存器r的内容  
<span style="color:dodgerblue;">M[addr]</span>：表示存储单元addr的内容  
<span style="color:dodgerblue;">M[PC]</span>：表示PC所指存储单元的内容  
M[<span style="color:red;">R[r]</span>]：表示寄存器r的内容所指的存储单元的内容  

* <span style="color:red;">**AT&T**</span>格式指令：<span style="color:red;">源</span>、<span style="color:dodgerblue;">目的</span> <span style="color:red;">**顺序**</span>（<span style="background-color:yellow;">本教材使用</span>）
* <span style="color:dodgerblue;">Intel</span>格式指令：<span style="color:dodgerblue;">目的</span>、<span style="color:red;">源</span>   <span style="color:skyblue;">**逆序**</span>

传送方向用<span style="background-color:yellow;"><span style="color:red;">**←**</span></span>表示，右：传送<span style="color:red;">源</span>，左：传送<span style="color:dodgerblue;">目的</span>

例如：

> 对于<span style="color:red;">AT&T</span>格式指令“mov<span style="color:red;">w 4(%ebp)</span>, <span style="color:dodgerblue;">%ax</span>”，其功能为R[ax]←M[R[ebp]+4]

### 考点三、指令系统设计风格

按指令格式的**复杂度**分以下两种：

1）<span style="color:red;">**CISC**</span>风格指令系统（<span style="color:red;">复杂</span>指令集计算机，Complex Instruction Set Computer）

例如：本书介绍的Intel x86指令系统就是典型的CISC架构

2）<span style="color:dodgerblue;">RISC</span>风格指令系统(<span style="color:dodgerblue;">精简</span>指令集计算机，Reduced Instruction Set Computer)

**特点：**
1. 指令数目少
2. 指令格式规整，采用定长指令字方式
3. 只有Load/Store指令中的数据需要访存
4. 采用大量通用寄存器

指令的<span style="color:red;">操作数</span>有以下三种：

* 1）立即数（<span style="color:red;">I</span>） 例如：movI <span style="background-color:yellow;">$80</span>, -8(%ebp)            # <span style="color:red;">M</span>[R[ebp]-8] ← 80
* 2）通用寄存器（<span style="color:red;">R</span>） 例如：movI $80, -8(<span style="background-color:yellow;">%ebp</span>)   # <span style="color:red;">M</span>[R[ebp]-8] ← 80
* 3）存储单元（<span style="color:red;">S</span>） 例如：movI $80, <span style="background-color:yellow;">-8(%ebp)</span>       # <span style="color:red;">M</span>[R[ebp]-8] ← 80

指令类型可以是：<span style="color:red;">**RR、RS、SI、SS**</span> 等

**习题**

1、简述RISC指令集的主要特点，并写出它的全称。

```
RISC的全称是：精简指令集计算机
RISC指令系统的主要特点：
- 指令数目少
- 指令格式规整，采用定长指令字方式，操作码和操作数地址等字段的长度固定
- 只有load、store指令中的数据需要访存
- 采用大量通用寄存器
```

### 考点四、生成机器代码的过程

![](./attachments/chapter01/hello.c源程序文件到可执行目标文件的转换过程.png)

一个C语言程序转换为可执行目标代码的过程分为以下4个步骤

#### 1）预处理

使用命令：`prog1.c`是C语言程序源文件
`gcc -E prog1.c -o prog1.i`

#### 2）编译

使用命令：生成汇编语言源程序（文本文件）
`gcc -S prog1.i -o prog1.s`
或者可以跳过【预处理】生成prog1.s
`gcc -E prog1.c -o prog1.s`

#### 3)汇编

使用命令：生成可重定位目标程序（二进制）不可读
`gcc -c prog1.s -o prog1.o`

#### 4)链接

使用命令：生成可执行目标程序（二进制）
`gcc prog1.o prog2.o -o prog`

也可以用命令<span style="color:red;">**一步到位**</span>生成最终的可执行文件：

`gcc -O1 prog1.c prog2.c -o prog`

例3.1 在IA-32+Linux平台上，对下列源程序test.c使用GCC命令进行相应的处理，以分别得到与处理后的文件test.i、汇编代码文件test.s和可重定位目标文件test.o。这些输出文件中，那些是可显示的文本文件？那些是不能显示的二进制文件？请给出可显示文本文件的输出结果。

```
// test.c

int add(int i, int j){
    int x = i + j;
    return x;
}
```

解答：
```
可显示的文本文件有：预处理后的文件（test.i） 和 汇编代码文件（test.s）
不可显示的二进制文件：可重定位目标文件 test.o
```

### 考点五、Intel和AT&T格式指令区别

![](./attachments/chapter03/280beb0a-7eaf-4042-bfbb-44573b790466.png)

汇编格式指令为：  
`op src, dst  // op：操作 src：源 dst：目标`  

含义为：  
`dst ← dst op src // 目标 与 源 进行操作 然后送到目标`

**例如：**
```
addl (,%ebx,2),%eax
的含义为：
R[eax] ← R[eax]+M[R[ebx]×2]
```

### 考点六、数据类型及格式

C语言基本数据类型和IA-32操作数类型的对应关系

| C语言声明                                                | Intel操作数类型       | 汇编指令长度后缀 | 存储长度/位 |
|------------------------------------------------------|-----------------------|------------------|-------------|
| <span style="color:red;">(unsigned) char</span>      | 整数/字节             | b                | 8           |
| <span style="color:red;">(unsigned) short </span>    | 整数/字               | w                | 16          |
| <span style="color:red;">(unsigned) int </span>      | 整数/双字             | l                | 32          |
| <span style="color:red;">(unsigned) long int </span> | 整数/双字             | l                | 32          |
| (unsigned) long long int                             | -                     | -                | 2×32        |
| <span style="color:red;">char*</span>                     | 整数/双字             | l                | 32          |
| float                                                | 单精度浮点数          | s                | 32          |
| double                                               | 双精度浮点数          | l                | 64          |
| long double                                          | 扩展精度浮点数        | t                | 80/96       |

### 考点七、定点寄存器组

![](./attachments/chapter03/aa99cdd1-0dad-46fd-aad7-6d2242c029a5.png)

![](./attachments/chapter03/1521c1d2-4233-406b-80c6-8dce6b0cc3c9.png)

<span style="background-color:yellow;">**助记**</span>：<span style="color:red;">2字母16位，3字母32位，末尾</span><span style="color:dodgerblue;">**L**</span>、<span style="color:dodgerblue;">**H**</span><span style="color:red;">都是</span><span style="color:dodgerblue;">**8**</span><span style="color:red;">位</span>

**同理：**
> B<span style="color:dodgerblue;">L</span>、C<span style="color:dodgerblue;">L</span>、D<span style="color:dodgerblue;">L</span> => 低8位  
> B<span style="color:dodgerblue;">H</span>、C<span style="color:dodgerblue;">H</span>、D<span style="color:dodgerblue;">H</span> => 高8位  
> BX、CX、DX => 16位  
> EBX、ECX、EDX => 32位  

IA-32的定点寄存器组分为三大块：

* 一、8个通用寄存器(通用寄存器组前四个存放：操作数，通用寄存器组后四个存放：变址值、指针)
    * <span style="color:red;">EAX</span>：累加器
    * <span style="color:red;">EBX</span>：基址寄存器
    * <span style="color:red;">ECX</span>：计数寄存器
    * <span style="color:red;">EDX</span>：数据寄存器
    * <span style="color:dodgerblue;">ESP</span>：栈指针寄存器
    * <span style="color:dodgerblue;">EBP</span>：基址指针寄存器
    * <span style="color:dodgerblue;">ESI</span> ：源变址寄存器
    * <span style="color:dodgerblue;">EDI</span> ：目标变址寄存器
* 二、2个专用寄存器
    * EIP：指令指针
    * EFLAGS：标志寄存器
* 三、6个段寄存器
    * CS：代码段
    * SS：栈段
    * DS：数据段
    * ES：附加段
    * FS：附加段
    * GS：附加段

> 通用寄存器组-前四个存放：<span style="color:red;">操作数</span>  
> 通用寄存器组-后四个存放：<span style="color:dodgerblue;">变址值、指针</span>

<span style="color:red;">EAX、EBX、ECX、EDX</span> 主要用来存放<span style="color:red;">**操作数**</span>  
<span style="color:dodgerblue;">ESP、EBP、ESI、EDI</span> 主要用来存放<span style="color:dodgerblue;">**变址值**</span>或<span style="color:dodgerblue;">**指针**</span>

### 考点八、条件标示

#### 1)<span style="color:red;">OF</span>(Overflow Flag)：<span style="color:red;">溢出标示</span>

反映<span style="color:red;">带符号数</span>的运算结果是否超过了相应<span style="background-color:purple;">数值范围</span>。

例如：  
> 对于8位带符号整数，其表示范围是-128 ~ +127，如果计算结果超出了这个范围，就发生了溢出，此时OF=1；否则OF=0
> 
> n位的带符号数，其表示范围是：-2ⁿ⁻¹ ~ 2ⁿ⁻¹ -1  
> n位无符号整数，其表示范围是：0 ~ 2ⁿ-1

#### 2)<span style="color:red;">SF</span>(Sign Flag)：<span style="color:red;">符号标示</span>

反映<span style="color:red;">带符号数</span>运算结果的<span style="background-color:purple;">符号</span>。

负数时，SF=1；否则OF=0

#### 3)<span style="color:red;">ZF</span>(Zero Flag)：<span style="color:red;">零标示</span>

反映运算结果<span style="background-color:purple;">是否为0</span>。若结果位0，ZF=1；否则ZF=0

#### 4)<span style="color:red;">CF</span>(Carry Flag)：<span style="color:red;">进/错位标示</span>

反映<span style="color:red;">无符号整数</span>加（减）运算后的<span style="background-color:purple;">进（借）位情况</span>。

有进（借）位则CF=1；否则CF=0

例如：  
> 在二进制加法中，两个1相加，结果为0并向更高位进位1（1+1=10）,CF=1

例3.4、假设R[ax]=FFFAH，R[bx]=FFF0H，则执行Intel格式指令"add ax，bx"后，AX、BX中的内容各是什么？标志CF、OF、ZF、SF各是什么？要求分别将操作数作为无符号整数和带符号整数来解释并验证指令执行结果。

解析：

```
因为Intel格式指令是：先目标 后源，所以Intel格式指令"add ax，bx"中可知：ax是目标，bx是源
Intel格式指令"add ax，bx" = R[ax]←R[ax] + R[bx]。
R[ax] = R[ax] + R[bx]
R[ax] = FFFAH + FFF0H  // H结尾是16进制数

A（10）+ 0     = A，写 A，进位 0
F（15）+ F（15） = 1E（满16进1=》16+14），写 E，进位 1
F（15）+ F（15） + 进位1 = 15 + 15 + 1 = 31 = 1F，写 F，进位 1
F（15）+ F（15） + 进位1 = 15 + 15 + 1 = 31 = 1F，写 F，进位 1

R[ax] = 1FFEAH // 1-进位 不保留

所以执行Intel格式指令"add ax，bx"后，AX中的内容是1FFEAH，X中的内容（不变）还是FFF0H

标志CF是：进/错位标示，无符号整数加减运算后，是否存在进/借位情况
标志OF是：溢出标示，带符号数运算后是否超出了相应的数值范围
标志ZF是：零标示，运算后运算结果是否为零
标志SF是：符号标示，无符号数运算后的运算结果的符号，0-正 1-负

1、无符号整数

R[ax] = FFFAH = 65530（16进制转10进制）
R[bx] = FFF0H = 65520，
R[ax] + R[bx] = 65530 + 65520 = 131050

n位无符号整数的范围：0 ~ 2ⁿ-1

所以，16位无符号数最大值2¹⁶-1=65535
计算结果：131050比65535大，所以计算结果溢出了，
计算结果1FFEAH有进位，所以进错位标示CF=1
计算结果1FFEAH，不是0，所以零标示ZF=0

至于“溢出标示”和“符号标示” 是反映有符号数的计算结果的，所以无值

2、有符号整数（补码表示）

求R[ax] = FFFAH的真值

先转二进制（一拆四）
FFFAH = 1111 1111 1111 1010
符号位是1，则为负数
补码求真值的简便方法：数值部分：各位取反、末位+1
= 1111 1111 1111 1010
= -000 0000 0000 0101
= -000 0000 0000 0101 + 1
= -000 0000 0000 0110
转十进制数
= -（1×2² + 1×2¹ + 0×2⁰）
= -（4 + 2 + 0）
= -6

求R[bx] = FFF0H的真值
先转二进制（一拆四）
FFF0H = 1111 1111 1111 0000
符号位是1，所以是负数-
补码求真值的简便方法：数值部分：各位取反、末位+1
= 1111 1111 1111 0000
= -000 0000 0000 1111
= -000 0000 0000 1111 + 1
= -000 0000 0001 0000
转十进制数
= -（1×2⁴ + 0×2³ + 0×2² + 0×2¹ + 0×2⁰）
= -（16 + 0 + 0 + 0 + 0）
= -16

R[ax] + R[bx] = -6 + (-16) = -22

n位带符号整数的范围：-2ⁿ⁻¹ ~ (2ⁿ⁻¹ - 1)
16位带符号整数的范围就是：-2¹⁵ ~ (2¹⁵ - 1) => -32768 ~ 32767

计算结果-22在范围内，所以没有发生溢出，溢出标示OF=0

计算结果-22是负数，所以符号标示SF=1

计算结果-22不是零，所以零标示ZF=0

进/错位标示是反映无符号整数加减运算后计算结果是否有进借位情况的，所以无效。
```

### 考点九、定点算术运算指令

* 1)<span style="color:red;">加/减</span>运算指令 例如：`ADD(+) SUB(-)`
* 2)<span style="color:red;">增/减</span>运算指令 例如：`INC(++) DEC(--)`
* 3)<span style="color:red;">取负</span>指令  例如：`NED(-)`
* 4)<span style="color:red;">比较</span>运算指令  例如：`CMP(<,<=,>,>=)`
* 5)<span style="color:red;">乘/除</span>运算指令

例如：
```
MUL(*)	无符号整数
IMUL(*)  带符号整数
DIV(/,%)  无符号整数
IDIV(/,%)  无符号整数
```

定点算术运算指令汇总

| 指令 | 显式操作数 | 影响的标志 | 操作数类型 | AT&T指令助记符         | 对应C运算符 |
|------|------------|------------|------------|-------------------|-------------|
| ADD | 2个 | OF、ZF、SF、CF | 无/带符号整数 | addb、 addw、 addl  | ＋ |
| SUB | 2个 | OF、ZF、SF、CF | 无/带符号整数 | subb、subw、subl    | － |
| INC | 1个 | OF、ZF、SF | 无/带符号整数 | incb、incw、incl    | ＋＋ |
| DEC | 1个 | OF、ZF、SF | 无/带符号整数 | decb、decw、decl    | －－ |
| NEG | 1个 | OF、ZF、SF、CF | 无/带符号整数 | negb、negw、negl    | － |
| CMP | 2个 | OF、ZF、SF、CF | 无/带符号整数 | cmpb、cmpw、cmpl    | <,<=,>,>= |
| MUL | 1个 | OF、CF | 无符号整数 | mulb、 mulw、mull   | * |
| IMUL | 1个 | OF、CF | 带符号整数 | imulb、imulw、imull | * |
| IMUL | 2个 | OF、CF | 带符号整数 | imulb、imulw、imull | * |
| IMUL | 3个 | OF、CF | 带符号整数 | imulb、imulw、imull | * |
| DIV | 1个 | 无 | 无符号整数 | divb、divw、divl    | /,% |
| IDIV | 1个 | 无 | 带符号整数 | idivb、idivw、idivl | /,% |

**习题**

1、下列不属于IA-32定点算术运算指令的是（<span style="color:red;">B</span>）

* A：ADD/SUB
* B：<span style="color:red;">IN/OUT</span>
* C：MUL/IMUL
* D：INC/DEC

例3.5、假设R[eax]=0000 00B4H，R[ebx]=0000 0011H，M[0000 00F8H]=0000 00A0H，
请问：
1、执行指令"mulb%bl"后，哪些寄存器的内容会发生变化?与执行"imulb%bl"指令所发生的变化是否一样？为什么？
两条指令得到的CF和OF标志各是什么？请用该例给出的数据验证你的结论。

2、执行指令"imull $-16，(%eax，%ebx，4)，%eax"后哪些寄存器和存储单元发生了变化？乘积的机器数和真值各是多少？

解析：

```
1、
指令"mulb %bl" 是无符号整数 乘法运算
mul：无符号乘法
b：8位（byte）
隐含操作数：
 乘数：BL
 被乘数：AL
结果存放：AX(AH:AL)
指令mulb %bl 
= R[ax] ←R[al] × R[bl] 
= AL × BL → AX

R[eax]=0000 00B4H 可得：
AL = B4H
十六进制数转十进制数：B4H = B × 16¹ + 4 × 16⁰  = 11 × 16 + 4 = 180

R[ebx]=0000 0011H 可得：
BL = 11H
十六进制数转十进制数：11H = 1 × 16¹ + 1 × 16⁰  = 16 + 1 = 17

所以执行指令"mulb %bl"后，内容会发生变化的寄存器是AX

R[ax] = B4H × 11H = 0BF4 // 补0
   B4
×  11
-----
   B4
  B4
------
 0BF4 

R[ax] = 180 × 17 = 3060 // 十进制数

AX = 0BF4
AH = 0B
AL = F4

AH = 0B ≠ 0，高位（AH）≠ 0 → 溢出，所以：CF=1，OF=1

指令"imulb %bl" 是有符号整数 乘法运算
= R[ax] ←R[al] × R[bl] 
= AL × BL → AX

R[eax]=0000 00B4H 可得：
AL = B4H // 一拆四转二进制数
AL = 1011 0100 // 求真值 1-负 so 数值位取反 末位+1
AL = 1100 1011 + 1
AL = 1100 1100
AL = -(1×2⁶ + 1×2³ + 1×2²)
AL = -(64 + 8 + 4)
AL = -(76)

R[ebx]=0000 0011H 可得：
BL = 11H
BL = 0001 0001 // 求真值 0-正 所以 直接转二进制不取反
BL = (1×2⁴ + 1×2⁰)
BL = （16 + 1）
BL = 17

−76×17=−1292
乘积结果被存入 16 位寄存器 AX，并且按照有符号数的补码形式存储，所以需要通过真值得到16位的补码
真值求补码：负数，符号位取1，数值部分：各位取反，末位加1

1292转十六位的二进制数：
0000 0101 0000 1100
1111 1010 1111 0011 // 取反
1111 1010 1111 0011 + 1 // 末位+1
1111 1010 1111 0100 // 四合一 转十六进制

FAF4H

AX = FAF4
AH = FA
AL = F4

CF = OF = 1

执行指令"mulb %bl"和执行"imulb %bl"指令都会修改寄存器AX的内容，但是变化是不一样的，mulb 产生无符号乘积 0BF4H，而 imulb 产生有符号乘积 FAF4H，二者数值不同，所以 EAX 的结果不同。

2、指令"imull $-16，(%eax，%ebx，4)
的功能：R[eax]←(-16) × M[R[eax] + 4×R[ebx]] // -16为立即数

M[R[eax] + 4×R[ebx]]所在的存储单元地址为：
R[eax] + 4×R[ebx]
=B4H + 4×11H
=B4H + 44H
=F8H
=0000 00F8H

题目给出：M[0000 00F8H]=0000 00A0H
M[0000 00F8H]
=0000 00A0H
=（A×16¹ + 0×16⁰）
=(10×16¹ + 0×16⁰)
=（160 + 0）
=160

(-16) × M[R[eax] + 4×R[ebx]]
= （-16） × 160
= -2560

相乘后的运算结果是一个负数 ，所以乘积的符号为负（0-正 1-负）

2560转十六进制数：2560 ÷ 16 = 160 余 0，160 ÷ 16 = 10 余 0，10 ÷ 16 = 0 余 10（A），即 A00
32位：0000 0A00H

对其各位取反 末位+1
0000 0A00H
0000 0000 0000 0000 0000 1010 0000 0000 // 一拆四 转二进制
1111 1111 1111 1111 1111 0101 1111 1111 // 各位取反
1111 1111 1111 1111 1111 0101 1111 1111 + 1 // 末位+1
1111 1111 1111 1111 1111 0110 0000 0000
F F F F F 6 0 0 H// 转换为十六进制数
FFFF F600H

eax = FFFFF600H
机器数：FFFFF600H 真值：-2560

寄存器eax发生了变化，内存不变，ebx不变

```

### 考点十、寻址方式



### 考点十一、浮点寄存器栈和多媒体扩展寄存器组



### 考点十二、通用数据传送指令



### 考点十三、地址传送指令



### 考点十四、移位指令



### 考点十五、程序执行流控制指令




### 考点十六、过程调用



### 考点十七、值传递和地址传递



### 考点十八、选择语句


### 考点十九、循环语句


### 考点二十、数组


### 考点二十一、数据的对齐方式


### 考点二十二、x86-64的基本特点


### 考点二十三、x86-64的数据传送指令


### 考点二十四、x86-64的算术逻辑运算指令


### 总结

