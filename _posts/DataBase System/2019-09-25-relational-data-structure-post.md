---
layout: post
title:  "Database System： Relational Data Structure"
date:   2019-09-25
excerpt: ""
tag:
- Database System
---

本篇内容来自《数据库系统概论（第五版）》（王珊 萨师煊），主要是整理数据库的基本概念，供自己复习查阅。

# 关系数据库

关系数据库就是基于关系模型的数据库系统。关系模型包含三部分：**关系数据结构**、**关系操作集合**、**关系完整性约束**。

## 关系数据结构

关系数据结构只包含一种数据结构——关系，从逻辑结构上看，关系就是一张**二维表**。前面提到，关系模型是基于严格的数学概念形成的数据模型，而关系的基础就是**集合论**。故需要从集合论角度理解关系数据结构的形式化定义。

### 域（domain）

域是一组具有相同数据类型的值的集合。如常见的自然数域N、实数域R。一个域的不同取值的个数称为这个域的**基数**。

### 笛卡尔积（Cartesian product）

笛卡尔积是一种**集合运算**，定义如下：
$$
\begin{aligned}
&对于给定的一组域D_1,D_2,...,D_n，不要求域互异，D_1,D_2,...,D_n的笛卡尔积为\\
&D_1\times D_2\times...\times D_n=\{(d_1,d_2,...,d_n)|d_i\in D_i,i=1,2,...,n\}
\end{aligned}
$$

笛卡尔积运算的结果也是一个域，它的基数等于各域基数的积。

### 关系

#### 定义

$$
\begin{aligned}
&D_1\times D_2\times ...\times D_n的子集叫做在域D_1,D_2,...,D_n上的关系，\\
&表示为R(D_1,D_2,...,D_n)，其中R是关系名，n是关系的度（目）。
\end{aligned}
$$

##### 元组（tuple）

关系中的每个元素是关系的元组，通常用t表示。当n=1时为单元关系，n=2时为二元关系。

##### 候选码（candidate key）

若关系中的某一个属性组的值能唯一地标识一个元祖，且该组的真子集无一可以标识，那么就称改组为候选码。

##### 主属性prime attribute

所有候选码的各个属性称为主属性，其余称为非主属性或非码属性。

##### 主码（primary key）

若一个关系有多个候选码（如学生关系的学号和身份证号都可以作为候选码），则选定一个作为主码。

#### 关系类型

三种：

- 基本关系，即最基本的表，实际存储数据。
- 查询表，查询结果对应的表。
- 视图表，是虚表，由基本表或其他视图表导出。

#### 基本关系的性质

- 列**同质（homogeneous）**，即列的分量是同一类型的数据。
- 不同的列可出自同一域。如家庭关系中所有家庭成员都出自“人”这个域。
- 列的次序可交换，故每次添加新属性时可直接插入至最后一列。
- 任两个元组的候选码不能取完全相同的值。
- 行的次序也可交换。
- **分量必须取原子值**，即不允许表嵌套。这也是最基本的规范条件。

### 关系模式（relational schema）

关系的描述称为关系模式，通常表示为R(U,D,DOM,F)。R是关系名，U是该关系的属性名集合，D是所有属性的域的集合，DOM是属性向域的映像（mapping）的集合，F是属性间数据的依赖关系集合。

## 关系操作

关系模型只关心关系操作的能力，对于具体如何实现并不做具体限制，即不同的关系数据库系统可以定义开发不同的语言来实现关系操作。

### 类型

关系模型常用的操作主要是查询和修改。修改包括**插入（insert）**、**删除（delete）**和**修改（update）**。关系的查询能力很强，也是关系操作的最主要部分，包括**选择（select）**、**投影（project）**、**连接（join）**、**除（divide）**、**并（union）**、**差（except）**、**交（intersection）**、笛卡尔积等。其中基本操作为选择、投影、并、差、笛卡尔积，其他操作可以由基本操作导出。

### 特点

关系操作的对象和结果都是集合。

## 关系完整性

关系模型又三类完整性约束：**实体完整性（entity integrity）**、**参照完整性（referential integrity）**、**用户定义的完整性（user-defined integrity）**。前两者是关系系统自动支持的，后者是具体应用领域的约束。

### 实体完整性

若属性A是基本关系R的一个主属性，则A不能取**空值（null value）**。

### 参照完整性

在定义参照完整性之前，首先要了解**外码**的概念。

#### 外码（foreign key）

设F是基本关系R的一个或一组属性，但不是R的码，K是基本关系S的主码。如果F与K相对应，则称F是R的外码，并称基本关系R为参照关系，S是被参照关系或目标关系。

一个简单的例子：假设学生关系表中有“专业号”属性，专业关系表中的主码即“专业号”，这两个专业号是对应的，这里就可以称“专业号”属性是学生关系的外码。其中，专业关系是被参照关系，学生关系是参照关系。

#### 定义

若属性或属性组F是基本关系的外码，它与基本关系S的主码K相对应，则对于R中每个元组在F上的值必须取空值或等于S中某个元组的主码值。

回到上面的例子，不难理解：如果学生关系的专业号属性取空值，说明该学生还没有分配专业；如果非空，又不是专业关系中元组的主码值，那也就是说，该学生被分配到了一个不存在的专业，这显然是不合适的。

### 用户定义的完整性

用户定义的完整性反映某一具体应用所涉及的数据必须满足的语义要求。如：在学生关系中，学生姓名不能为空。

## 关系代数

关系代数是一种抽象查询语言，用**对关系的运算**表示查询。关系代数的运算对象和运算结果均为关系，它包含两类运算符：传统的集合运算和专门的关系运算。

### 传统的集合运算

传统集合运算是二目运算，包括并、差、交、笛卡尔积，它把关系看成元组的集合，即从行的角度理解关系。

设关系R和关系S具有相同的目数n（即都有n个属性），相应的属性取自同一个域，t是元组变量。定义集合运算如下：

#### 并

关系R、S的并的结果仍是n目关系，其中的元组或来自R或来自S，并运算记作：
$$
R\bigcup S=\{t|t\in R\bigvee t\in S\}
$$

#### 差

关系R、S的差的结果也是n目关系，其中的元组均来自R但不属于S，差运算记作：
$$
R-S=\{t|t\in R\bigwedge t\notin S\}
$$

#### 交

关系R、S的交的结果也是n目关系，其中的元组同时属于R和S，交运算记作：
$$
R\bigcap S=\{t|t\in R\bigwedge t\in S\}
$$
交运算和差运算可以互相转化：
$$
R\bigcap S=R-(R-S)
$$

#### 笛卡尔积（广义）

设关系R、S分别为n、m目关系，则它们笛卡尔积的结果是一个（m+n）列的元组的集合。元组的前n列是关系R的一个元组，后m列是关系S的一个元组。若关系R、S的元组个数分别为r、s，则它们笛卡尔积的结果有r*s个元组。笛卡尔积运算记作：
$$
R\times S=\{\overbrace{t_rt_s}|t_r\in R\bigwedge t_s\in S\}
$$

### 专门的关系运算

专门的关系运算包括选择、投影、连接、除运算等。

#### 记号

为了叙述方便，首先引入几个记号。

##### 分量

$$
\begin{aligned}
&设关系模式为R(A_1,A_2,...,A_n)，设它的一个关系为R。\\
&t\in R表示t是R的一个元组。t[A_i]表示元组t中相应于属性A_i的一个分量。
\end{aligned}
$$

##### 补集

$$
\begin{aligned}
&若A=\{A_{i1},A_{i2},...,A_{ik}\}，其中，A_{i1},A_{i2},...,A_{ik}是A_1,A_2,...,A_n中的一部分,\\
&则A称为属性组。t[A]=(t[A_{i1}],t[A_{i2}],...,t[A_{ik}])表示元组t在属性组A上各个分量的集合，\\
&\overline{A}表示\{A_1,A_2,...,A_n\}中去掉\{A_{i1},A_{i2},...,A_{ik}\}后剩余的属性组。
\end{aligned}
$$

##### 元组的连接

$$
\begin{aligned}
&设R、S分别为n、m目关系，t_r\in R，t_s\in S，\overbrace{t_rt_s}称为元组的连接。\\
&它是一个m+n列的元组，前n个分量是R的一个n元组，后m个分量是S的一个m元组。
\end{aligned}
$$

##### 象集（images set）

给定一个关系R(X,Z)，X、Z为属性组。当t[X]=x时，x在R中的象集定义为：
$$
Z_x=\{t[Z]|t\in R,t[X]=x\}
$$
它表示的是属性X对应取值为x的元组，对应的属性Z的取值的集合。

#### 选择

选择运算从行的角度进行运算，从关系R中选取符合条件（使给定逻辑表达式为真）的元组，记作：
$$
\sigma_F(R)=\{t|t\in R\bigwedge F(t)=true\}，其中F(t)为逻辑表达式
$$
例：
$$
\sigma_{Sdept='IS'}(Student)
$$
其中，Sdept表示专业属性，IS为信息专业，Student是学生关系对应的表，上式表示选择所有专业为信息专业的学生。

#### 投影

投影从列的角度进行运算。关系R上的投影指从R中选择若干属性组组成新的关系，记作：
$$
\prod{}_A(R)=\{t[A]|t\in R\}
$$
例：
$$
\prod{}_{Sname,Sdept}(Student)
$$
其中，Sname是学生姓名，上式表示查询学生姓名和所在的专业。

需要注意的是，虽然投影操作是针对列的，但有可能在消除某些属性组之后，余下的元组内容可能会出现重复，这时就需要把重复组一并消除。如：当只查询学生表的专业时，显然学生所在的专业有大量重复，这时每种专业只能保留一个元组。

#### 连接

连接是从两个关系的笛卡尔积中选取属性间满足一定条件的元组，记作：
$$
R\bowtie_{A\theta B}S=\{\overbrace{t_rt_s}|t_r\in R\bigwedge t_s\in S\bigwedge t_r[A]\theta t_s[B]\}
$$
其中，A、B分别是R、S上列数相同且可比的属性组，θ是比较运算符。上式的含义是：从R和S的笛卡尔积R*S中选取R关系在A属性组上的值与S关系在B属性组上的值满足比较关系θ的元组。

##### 等值连接

当θ为=时称为等值连接。

##### 自然连接

自然连接是一种特殊的等值连接，它同时从行和列的角度进行运算，要求两个关系中进行比较的分量必须是同名的属性组，并且在结果中把重复的列去掉。

#### 除运算

设关系T是关系R除以关系S的结果，则T包含所有在R但不在S的属性及其值，且T的元组与S的元组的所有组合都在R中。即有：
$$
R\div S=T，S\times T \subseteq R
$$
关系除法可以通过象集定义：给定关系R(X,Y)和S(Y,Z)，其中X、Y、Z为属性组。R中的Y与S中的Y的属性名可以不同，但必须出自同一域。此时，R与S的除运算结果只包含一个属性X，设运算结果为关系P(X)，P是R中满足下列条件的元组在X属性组上的投影：元组在X上分量值x的象集Y_x包含S在Y上投影的集合，记作：
$$
R\div S=\{t_r[X]|t_r\in R\bigwedge \prod{}_Y(S)\subseteq Y_x\}
$$
除运算也同时从行和列的角度进行运算。

## 关系演算

关系演算是以数理逻辑中的谓词演算为基础的。按谓词变元可以把关系演算分为元组关系演算和域关系演算。主要了解元组关系演算。

### 元组关系演算语言ALPHA

元组关系演算以元组-变量作为谓词变元的基本对象，ALPHA是典型的元组关系演算语言，主要有GET、PUT、HOLD、UPDATE、DELETE、DROP六种操作语句。语句的基本格式为：
$$
操作语句\space\space 工作空间名(表达式):操作条件
$$

#### 检索操作

用GET可以实现一般的查询；还可以结合RANGE语句来说明元组变量，简化关系名。同时，在操作条件使用量词时，必须使用元祖变量描述；还可以用蕴含表达式检索信息。

1. 查询所有学生的数据。
   $$
   GET\space W(Student)
   $$

2. 查询信息系中年龄小于20岁的学生的学号和年龄。
   $$
   GET\space W(Student.Sno,Student.Sage):Student.Sdept='IS'\bigwedge Student.Sage<20
   $$

3. 查询信息系年龄最大的三个学生的学号和年龄，结果按年龄降序排列。
   $$
   GET\space W(3)(Student.Sno,Student.Sage):Student.Sdept='IS'\space DOWN\space Student.Sage
   $$

4. 查询选修2号课程的学生名字。
   $$
   \begin{aligned}
   &RANGE\space SC\space X\\
   &GET\space W(Student.Sname):\exists X(X.Sno=Student.Sno\bigwedge X.Cno='2')
   \end{aligned}
   $$

5. 查询不选1号课程的学生名字。
   $$
   \begin{aligned}
   &RANGE\space SC\space SCX\\
   &GET\space W(Student.Sname):\forall SCX(SCX.Sno\ne Student.Sno\bigvee SCX.Cno\ne '1')
   \end{aligned}
   $$

6. 查询最少选修了学号为201215122的学生所选课程的学生学号。
   $$
   \begin{aligned}
   RANGE\space &Course\space CX\\
   &SC\space SCX \\
   &SC\space SCY \\
   GET\space W&(Student.Sno):\forall CX(\exists SCX(SCX.Sno='201215122'\bigwedge SCX.Cno=CX.Cno)\\
   &\Rightarrow \exists SCY(SCY.Sno=Student.Sno\bigwedge SCY.Cno=CX.Cno))
   \end{aligned}
   $$




#### 更新操作

##### 修改操作

修改操作用UPDATE实现：

1. 用HOLD语句把待修改的元组从数据库提取到工作空间；
2. 用宿主语言修改工作空间中元组的属性值；
3. 用UPDATE语句把修改后的元组送回数据库。

**其中，HOLD是带有并发控制的GET语句。**且在ALPHA语言中，不允许修改关系主码，若有需要，只能先删除再添加。

例：学号为201215127的学生从计算机科学系转到信息系。
$$
\begin{aligned}
&HOLD\space W(Student.Sno,Student.Sdept):Student.Sno='201215127'\\
&MOVE\space 'IS'\space TO\space W.Sdept\\
&UPDATE\space W
\end{aligned}
$$

##### 插入操作

插入操作用PUT实现：

1. 用宿主语言在工作空间建立新元组；
2. 用PUT语句把该元组存入指定关系中。

例：学校新开设了一门课程“计算机组织与结构”，课程号8，学分2，直接先行课为6号。
$$
\begin{aligned}
&MOVE\space '8'\space TO\space W.Cno\\
&MOVE\space '计算机组织结构'\space TO\space W.Cname\\
&MOVE\space '6'\space TO\space W.Cpno\\
&MOVE\space '2'\space TO\space W.Ccredit\\
&PUT\space W(Course)
\end{aligned}
$$

##### 删除操作

删除操作用DELETE实现：

1. 用HOLD语句把待删除的元组从数据库读取到工作空间中；
2. 用DELETE语句删除该元组。

例：学生201215230因故退学。
$$
\begin{aligned}
&HOLD\space W(Student):Student.Sno='201215230'\\
&DELETE\space W
\end{aligned}
$$
