#  CSAPP

## lecture2、Bit，Bytes and Integers

补码和无符号数其实是一一对应的

-1（t）——umax（即无符号数的最大数）

-max（t）——tmax+1（正补码表示的最大值+1）

有符号数和unsigned num 进行算数计算（/*-+以及比较）会使得所有的数字都转为无符号类型

符号扩展

![image-20221014192359264](D:\cyy personal\开发学习笔记\fig\image-20221014192359264.png)

Unsigned：0填充

Signed：符号扩展

缩小

保留几位即mod几

## lecture3、Bit，Bytes and Integers

无符号数加法

![image-20221015140237938](D:\cyy personal\开发学习笔记\fig\image-20221015140237938.png)

溢出会直接取模

补码加法（-法同理）

if sum >= 2^(w-1),变为负数

if sum <- 2^(w-1),会变为正数

![image-20221015200335212](D:\cyy personal\开发学习笔记\fig\image-20221015200335212.png)

**乘法指令会比起移位指令会花更长的时间**——编译器在编译乘法时候有时候会解释为左移kbit

除以2也类似

但是，若被除数无法被二整除会被向下取整

当你想要保留数字的符号时，需要使用**算数位移**（移出去的位丢弃，空缺位（vacant bit）用“符号位”来填充，所以一般用在右移运算中。）

现代计算机中除法依旧很慢 need 30 clocks，所以编译器会极力避免这种情况

负数的补码：原码取反+1

对于C语言来说，无符号数，进行右移时候都是执行的逻辑位移，但是对于有符号数，C并没有明确的规定一定要用算数位移。因此，我们对于无符号数和有符号数必须做一个明确的分配而不是暗示。

java中取消了无符号数的存在，只允许了补码的存在，他们位移的改变是引入了>>>认为是逻辑位移>>认为是算数位移

在现实使用中，建议不要使用无符号数（32bit），而是用size_t（64bit）代替。  

在你脑海中，数组就是个巨大的字节数组，from 0 to max-num 

eg：64bit系统中，地址用64位表示，但是实际最大可用为47bit

**字长**（Machine Words）：64位——指针or地址的值是64位

硬件本身并不决定字长，而是和编译器一起确定

Byte 顺序：大多数都是小端序但是在因特网上是大端序列

![image-20221018183310921](D:\cyy personal\开发学习笔记\fig\image-20221018183310921.png)

## Lectrue4、Floating Point

### IEEE802.3

![image-20221022132548472](D:\cyy personal\开发学习笔记\fig\image-20221022132548472.png)

![image-20221022133046400](D:\cyy personal\开发学习笔记\fig\image-20221022133046400.png)

M一般介于1~2之间

单精度 **32bit**：s：1 exp：8bit frac：23bit

double **64bit**：1 、11bit、52bit

Extended precision **80bit**:1、15bit、63or64bit//intel特有

exp全0(**min**,此时无隐含1，阶码是=1-bias，尾数数M = f)or全1(frac =000则是∞，frac  != 0000是NaN)是特殊情况

在此，E=Exp-bias   bias = 2^(k-1)-1

M = 1.xxxxx(2进制)

![image-20221022145343322](D:\cyy personal\开发学习笔记\fig\image-20221022145343322.png)

### 对于浮点数数的操作（舍入）

![image-20221022152054124](D:\cyy personal\开发学习笔记\fig\image-20221022152054124.png)

向0舍入：字面意思

向偶数舍入：<half向下舍入，>half向上舍入，==half向最近的偶数舍入

二进制中：![image-20221022160829440](D:\cyy personal\开发学习笔记\fig\image-20221022160829440.png)

浮点数加法有一个特点就是：不满足结合律，可以交换

强制转换：

float->int:截去小数点后的只保留前的、

double的话会有舍入

## lecture5、Machine-Level Programming 1

一、C->可执行程序

1、C-》汇编程序

2、汇编程序text——》机器语言Byte

3、链接器，静态链接，静态库和程序

4、动态链接，在程序首次运行的时候有些库才链接

二、汇编语言

1、有很多的整数数据类型

1，2，4，8bytes

2、floating point data 4，8，10byte

![image-20221105164121581](D:\cyy personal\开发学习笔记\fig\image-20221105164121581.png)

![image-20221105164152575](D:\cyy personal\开发学习笔记\fig\image-20221105164152575.png)

变量名称在汇编阶段和机器语言阶段会完全消失，会变成寄存器和内存中的某个位置

GDB

可以反汇编（disassemble）

寄存器的名称

![image-20221105165616420](D:\cyy personal\开发学习笔记\fig\image-20221105165616420.png)

使用%r名称时候，你会得到64bit

%e版本时候，32bit

如图中所示，粉色的是特殊的寄存器，这被称为**栈指针**。

Moving Data

```assembly
movq src, Dest:
```

可以操作的类型有

1、立即数 eg：$0x400($就是)

2、寄存器  %rax (%rsp有特殊用处)

3、内存 （ %rax）

![image-20221105173222621](D:\cyy personal\开发学习笔记\fig\image-20221105173222621.png)

%rdi一直会是第一个参数寄存器

%rsi将会是第二个参数寄存器

 ![image-20221106133534080](D:\cyy personal\开发学习笔记\fig\image-20221106133534080.png)

用这些数字原因是索引放缩1~8倍

```assembly
(Rb, Ri)
D(Rb, Ri)
(Rb, Ri, S)
```

```assembly
leaq Src, Dst
```

利用c中的&符号操作，来计算地址

lea：load effective address

src是地址mode 表达

![image-20221106140816918](D:\cyy personal\开发学习笔记\fig\image-20221106140816918.png)

## Lecture6、Machine-Level Programming II:Control

如何控制机器级别指令的执行顺序

1、condition codes

CF：carry flag（无符号数）溢出位

ZF：zero flag

SF：Sign Flag （运算结果最高有效位是1时，置1）

OF：Overflow Flag(有符号数溢出位)

```assembly
cmpq src2, src1//cmpq b,a就是计算a-b
testq b, a //a&b但是不设置dest
```

2、jumping

jx 指令

![image-20221106161128636](D:\cyy personal\开发学习笔记\fig\image-20221106161128636.png)

为什么不能一直用条件move

1、昂贵的计算

两个值都会得到计算

2、有风险的计算

两个值都得到计算时候会得到不想要的效果

3、计算所伴随的副作用



So，条件转移只有在两个计算都相对容易安全无副作用的前提下使用

3、Loop

goto以及jmp指令

4、switch 语句

记得写上break 不然会循环执行下去

switch语句在编译中实际做的事情（并不是一堆ifelse）：

![image-20221106172330179](D:\cyy personal\开发学习笔记\fig\image-20221106172330179.png)

一步到位知道要执行的代码。而非遍历
即下面的goto语句，直接获得索引表进入某些代码块

编译器生成了这些跳转表，至少这些表的框架是由编译器生成的

表的详细信息由汇编程序填写

![image-20221112133054335](D:\cyy personal\开发学习笔记\fig\image-20221112133054335.png)

无论你给的值的范围有多少，编译器都会列出来然后直接作为索引访问该代码块

![image-20221112134221684](D:\cyy personal\开发学习笔记\fig\image-20221112134221684.png)

**Q1**：case的值为负时候，编译器会对值进行偏置（bias）避免出现负索引

**Q2**：若是case只有1和100000两个case（值的范围较大，且稀疏），不会列出所有，而是直接转为if-else又或是二叉树的方式即O（logn）



## Lecture7、Machine-Level Programming III：Procedures

1、如何调用函数

将控制权转移给一个函数并确保能返回到正确位置？

传递数据？

内存分配？局部数据的处理分配与释放？

在oop中，函数应该专注于很小的功能。即只做绝对必要的事情

### Stack

程序用栈来管理过程调用和返回的状态

在栈中，程序传递潜在信息、控制信息和数据，并分配本地数据

![image-20221112143902148](D:\cyy personal\开发学习笔记\fig\image-20221112143902148.png)

栈的起始位置时内存高位，每次在栈上分配更多的空间时，都会通过递减该指针（sp：%rsp）来实现

进去的位置时栈的顶部

#### Push

pushq src(可以来自寄存器、内存or立即数)

先递减栈指针：%rsp - 8

然后执行写操作：在目的地址存储数据

#### Pop从栈中读取数据并存储在目标中

读取%rsp所给地址的数据

%rsp+8即弹出操作

在目标dest存储数据，必须是一个寄存器or立即数

### 传递控制

#### Call

call label（函数标签）

push return address on stack

jump to label

return address：

call 指令执行之后的下一条指令的address 

#### Ret

Pop address from stack

jump to address

###  数据传递

ABI规则要求前6个参数通过特定的寄存器来传递

![image-20221112154459256](D:\cyy personal\开发学习笔记\fig\image-20221112154459256.png)

![image-20221112154506406](D:\cyy personal\开发学习笔记\fig\image-20221112154506406.png)

**warning**：这里的参数都要求是整数类型or指针类型

reason：寄存器访问速度>内存访问速度

### 管理局部数据

#### 栈帧stack frame

栈上用于特定call的每个内存块称为栈帧

对于栈来说只需要在栈中为每个被调用且未返回的过程保留一个栈帧即可

Usually，一个栈帧由两个指针分隔

![image-20221112161347878](D:\cyy personal\开发学习笔记\fig\image-20221112161347878.png)%rbp：基指针//然而基本上不怎么用

当传递超过6个参数以上时，调用这将使用自己的栈帧来存储这些参数

![image-20221112162841012](D:\cyy personal\开发学习笔记\fig\image-20221112162841012.png)

若我们正在使用基指针，必须有办法存储基指针的旧值

还有一些局部状态例如要保存的寄存器，均会存储在栈帧内

![image-20221112171541917](D:\cyy personal\开发学习笔记\fig\image-20221112171541917.png)

所以要地址时，是存在栈内存上的

程序经常在栈上分配比实际需求还要多的空间，原因是要内存地址对齐。

**当一个指令以e开头的寄存器作为目的地时，高32位置0.**

movl比movq少一个字节

#### 寄存器保存习惯

caller：调用别的函数的函数、

callee：被调用的函数

两种方式保存寄存器

一、调用者保存（caller saved）

caller 不应该假设寄存器会一直不变

所以在call之前，save临时值在它自己的栈帧中

二、被调者保存（callee saved）

若特定函数想要改变某寄存器

应该先存储放入栈，且在返回前恢复该值

%rax用于返回值

其余六个用于传递参数（%rdi~%r9）

%r10，%r11用于caller saved

![image-20221113133857491](D:\cyy personal\开发学习笔记\fig\image-20221113133857491.png)

## Lecture8、Machine-Level Programming IV：Data

机器级代码里是没有数组这一高级概念的，而是将其视为字节的集合

### Arrays

#### 一维

![image-20221113141506014](D:\cyy personal\开发学习笔记\fig\image-20221113141506014.png)

#### 多维

在c中数组和指针之间的真正区别是

当在C中声明一个数组时，你既在分配空间，也在创建一个允许在指针运算使用的数组名称

纯纯的c语言基础，先跳过

嵌套数组

![image-20221113155005403](D:\cyy personal\开发学习笔记\fig\image-20221113155005403.png)

看书

## Lecture9、Machine-Level Programming IV：Advanced Topics

### Memory Layout

内存的组织

下图非按比例

![image-20221119141218068](D:\cyy personal\开发学习笔记\fig\image-20221119141218068.png)

#### Stack

一般典型的linux系统，stack区是8MB

若有栈指针指向8MB以外的地址会报段错误

#### Heap

堆用来存放malloc相关的函数申请的变量

这些变量在程序运行的时候会动态变化 

分配时候地址不定

#### Data

#### Text、Shared Libraries

存放的代码，来自于可执行文件

read-only

![image-20221119161956749](D:\cyy personal\开发学习笔记\fig\image-20221119161956749.png)

![image-20221119162024042](D:\cyy personal\开发学习笔记\fig\image-20221119162024042.png)

### Buffer Overflow缓冲区溢出

eg：在存储一条来自于消息的字符串时候，将会出现很多缓冲区溢出的问题，因为无法提前预知大小，所以对于已经分配的缓冲区来说，这个字符串可能会过大。

原因之一是存储字符串但却不检查边界情况的库函数

eg

![image-20221120140918200](D:\cyy personal\开发学习笔记\fig\image-20221120140918200.png)

栈指针下移24B用于buffer

gets函数调用了24B的缓冲区，即使最开始只是声明了4B

![image-20221120142157062](D:\cyy personal\开发学习笔记\fig\image-20221120142157062.png)

当输入超过23B的字符时+上00结尾（\n）就会破坏返回地址的字节表示

但是，报错并不是数组越界，而是在ret时候因为段错误而终止。

#### code injection attacks

常见模式为：用任何我想要的字符填充gets or 其他具有复制功能的函数的缓冲区，而有些字节被编码为可执行代码

#### 系统层面的保护

栈随机化ASLR

每次运行时候，局部变量的地址都会不一样

不可执行代码段

可读可写可执行

栈保护stack canaries

![image-20221120153400453](D:\cyy personal\开发学习笔记\fig\image-20221120153400453.png)

在栈中取出canary的值和特殊区域的进行比较，看是否被侵占

。。。

### Unions

struct会分配所有的内存

而union则是按照占用空间最大的域的大小来分配内存



## Lecture10、Program Optimization

多重层面上的优化

### 泛化有用的优化

代码移动

在循环外预先计算N*i的值

然后在内部一次次使用它

reduction in strength

乘法改加法

改为位移

![image-20221126160736883](D:\cyy personal\开发学习笔记\fig\image-20221126160736883.png)

分享共同子表示

![image-20221126161603649](D:\cyy personal\开发学习笔记\fig\image-20221126161603649.png)

#### 过程调用

减少循环中的函数调用

为什么编译器不会把strlen（）移出内循环？

1、过程可能会有副作用

2、函数对于同一个变量不一定会返回相同的值

warning：
编译器把过程调用当成一个黑盒

不会去优化

#### Memory Matters

不要一遍遍的在内存和寄存器之间来回传送数据

在c中你可以使一个内存数据结构覆盖另一个，当程序的不同部分指向内存中的相同位置时候，这称为别名

编译器必须假设两个存储位置可能相互重叠，所以会一遍遍的写到内存并读回来 

### 探索 指令级别的并行性

特性：无序执行

Cycles per element

代表处理一个元素所花费的时间周期

了解整体性能特征

循环展开 Loop Unrolling

每次循环处理数组的两个元素

![image-20221127160540571](D:\cyy personal\开发学习笔记\fig\image-20221127160540571.png)

由于还是顺序执行依赖的原因So并没有更大的改善

![image-20221127160739543](D:\cyy personal\开发学习笔记\fig\image-20221127160739543.png)

![image-20221127161016230](D:\cyy personal\开发学习笔记\fig\image-20221127161016230.png)

![image-20221127161654287](D:\cyy personal\开发学习笔记\fig\image-20221127161654287.png)

### 用AVX2编程

%xmm寄存器

%ymm寄存器是32B是xmm的两倍

矢量加法

![image-20221203165138119](D:\cyy personal\开发学习笔记\fig\image-20221203165138119.png)

### 分支执行怎么办？（乱序执行）

会先执行某个分支，当执行到后面（乱序）

正确预测，继续执行

错误预测，回到分支点，取出和指令另一个方向上的指令。

conditional mov和conditional jmp是不同的

mov可以在pipline中执行

## Lecture11、The Memory Hierarchy

### 1、存储的技术与趋势

SRAM：Cache

DRAM：主存，frame buffers（帧缓存）

![image-20221204152011085](D:\cyy personal\开发学习笔记\fig\image-20221204152011085.png)

### 2、访问局限性

程序的基本属性——程序的局部性

基本且持久

程序倾向于使用其地址接近or等于最近使用过的数据和指令的那些数据和地址

![image-20221204160955129](D:\cyy personal\开发学习笔记\fig\image-20221204160955129.png)

### 3、存储层次中的缓存

由于局部性原理，程序倾向于访问存储在第k层的数据（作为第k+1层的cache）

## ![image-20221204162748723](D:\cyy personal\开发学习笔记\fig\image-20221204162748723.png)

## Lectrue12、Cache Memories

### 组织方式

![image-20230225144032516](D:\cyy personal\开发学习笔记\fig\image-20230225144032516.png)

每一组（set）都包含E=2^e行（line）

每一行由B=2^b字节的数据块构成

Cashe Size = S * E * B

![image-20230225144457284](D:\cyy personal\开发学习笔记\fig\image-20230225144457284.png)

V:有效位

tag：标记位（帮助搜索块）

Address of word

![image-20230225150440546](D:\cyy personal\开发学习笔记\fig\image-20230225150440546.png)

b：用于确认块中的偏移量

s：无符号整数，

### 查找方式：

1. 用set index locate set（组）若包含此地址的数据字的块存在缓存中 ，它将在set索引表示的集合（set）中

2. 检查标记位（tag），检查该组中的所有行

3. 检查valid位是否置为1

   ​		

### 当每组只有一行时，E=1

直接映射缓存

![image-20230225152905821](D:\cyy personal\开发学习笔记\fig\image-20230225152905821.png)

标记位+有效位匹配时，命中

不足：被迫覆盖一些块——》缓存关联性低

更高的关联性——》高E值

### E路相连高速缓存

![image-20230225160613290](D:\cyy personal\开发学习笔记\fig\image-20230225160613290.png)

会把两个tsg都和地址的tag比较

### write

有多份copies数据存在：l1，l2,l3,主存，外存

1. Write through(immdiately to mem)

2. write Back(替换时才写)

   need a dirty bit

write miss

- Write-allocate（载入到cache中，在cache更新line）good if more write 2 the loc
- no-Write-allocate（直接向内存写）

typical

- write thru + no write allo
- wb+ wa

### 实际系统中的Cache设计

![image-20230225171539522](D:\cyy personal\开发学习笔记\fig\image-20230225171539522.png)

data cache 、instruction cache

Cache表现指标

miss rate、hit time、miss penalty

### 写Cache友好型代码

重复使用（存储在堆栈中的）局部变量是好的

使用全局变量让编译器不知道你在做什么，无法将该变量的引用放于寄存器

逐个元素访问数组是有利的（block1的存在）

![image-20230304163016364](D:\cyy personal\开发学习笔记\fig\image-20230304163016364.png)

内存山

矩阵乘法的优化

- ijk

  ![image-20230304164748846](D:\cyy personal\开发学习笔记\fig\image-20230304164748846.png)

  ![image-20230305152747031](D:\cyy personal\开发学习笔记\fig\image-20230305152747031.png)

  misses per inner loop

  A= 0.25 B=1 c=0 

- jki

  ![image-20230305160324227](D:\cyy personal\开发学习笔记\fig\image-20230305160324227.png)

- kij

  ![image-20230305160458869](D:\cyy personal\开发学习笔记\fig\image-20230305160458869.png)

写操作比读取操作更为灵活

### 用块来改善时间局部性

![image-20230305161725525](D:\cyy personal\开发学习笔记\fig\image-20230305161725525.png)

分块后代码

![image-20230305162131941](D:\cyy personal\开发学习笔记\fig\image-20230305162131941.png)

![image-20230305162225232](D:\cyy personal\开发学习笔记\fig\image-20230305162225232.png)

## Lecture13、Linking

### 静态链接

![image-20230305163014815](D:\cyy personal\开发学习笔记\fig\image-20230305163014815.png)

为什么链接

原因一、模块化

允许你将代码分解为更小的部分

原因二、效率

时间：秩序重写编译一个源文件

空间：可以使用库中的所有函数，但是实际编译时只链接了你所用的那个函数

链接器干什么？

step1

链接器将每一个符号引用与一个符号定义相关联

step2、重定位

合并所有模块，变成一个。o文件

重定位前的目标模块中的函数地址只是它在模块中的偏移量，数据同理（更新为绝对内存位置）

更新所有对于这些符号的引用（新位置）

Detail

重定向目标文件：

.o文件是可重定位目标模块，汇编器的输出，非二进制文件不能直接加载到内存中

可执行文件：a.out文件

包含代码



