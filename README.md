# -Student-Volunteer-Time-Management-System-
Projects in school
## 1 前言 ##
在之前的班级当班干部的时候，在学期初要听从团委实践部整理班里工时的情况，然而对工时的统计一切都是靠手写盖章的字条凭证以及一大堆的excel表格来对学院里每个学生的工时进行统计。所造成的问题就是需要大量的人力，而且特别容易出现错误的信息。在学了数据库这门课程后，我对这样的人力浪费感到深深的惋惜，于是便想写这样一个大致的程序更好管理学生工时。

此程序采用C#来编写图形界面以及使用sql server2008存储数据。


## 2 需求分析 ##

### 2.1.1数据流图 ###
2.1.1 顶级数据流图

![](/picture/顶级数据流图.jpg)
 
### 2.1.2 一级数据流图 ###
 
 ![](/picture/一级数据流图.jpg)

### 2.2  系统流程分析 ###
从上述的功能描述中，可以将将其功能分为4个模块，用户验证，学生数据库，社团数据库，活动数据库，在这四个模块中细分及完成各种功能便能完成此管理系统。工时管理系统的关系流程图如图所示。

![](/picture/系统流程示意图.jpg)
 
## 3 系统概要模块设计 ##
通过对工时管理系统的功能分析，可以定义出系统的功能模块图，如图所示。
 
![](/picture/工时管理系统模块示意图.jpg)

 
## 4 数据库概念结构设计 ##
- 一个社团可以举办多次活动，一次活动只能由一个社团举办。
- 一个活动可以有多人参与，一名学生可以参与多场活动。
- 一名学生可以参加多个社团，一个社团由多人组成。
- 一个人参与活动可以获得相应工时。
- 每个人在社团里都有自己的职位，可以是社长，部长，普通成员等。

因此可以得到E-R图如图

 ![](/picture/E-R图.png)

- 关系模式：
- 社团：（**社团编号**，社团名，详细信息）
- 活动：（**活动编号**，活动名称，举办社团编号，活动负责人名字，时间）
- 学生：（**学号**，姓名，性别，年级专业班级，总工时，备注）//总工时是否可以不加？
- 社团人员名单：（**社团编号**，学号，职位，备注）
- 活动人员名单：（**活动编号**，学号，获得工时数，备注）

细节：
每添加一条活动名单时改变学生总工时（触发器解决）

具体解决方法：

 1. 触发器1：新添活动人员名单后（after），修改学生表总工时属性（总工时+=工时）
 2. 触发器2：删除活动人员名单前（before），修改学生表总工时属性（总工时-=工时）
 3. 触发器3：修改活动人员名单的工时属性，修改学生表总工时属性（总工时=总工时-修改前工时+修改后工时）

视图：
 
- 学生工时表：（学号，姓名，总工时）
- 学生参与活动表：（参与活动名称，获得工时数，备注）
- 社团视图（社团名称，社团负责人，详细信息）
- 社团活动视图（活动名称，活动负责人姓名）
- 活动名单（学号，学生姓名，获得工时数，备注）
- 学生参与活动（活动名称，获得工时数，备注）

用户角色：

- 系统管理员：所有权限
- 教师：所有的读取权限
- 社团负责人：更新、读取社团表（仅限隶属社团）、活动表、社团人员名单
- 学生：读取个人信息权限


## 5 数据库逻辑结构设计 ##
根据E-R图，就可以创建以下数据表。

表1 社团表

| 属性     | 类型    | 约束      |
| --      | --     | ----------    |
| 社团编号 | INT     | 主码，自增 |
| 社团名   | CHAR(10)| 唯一      |
| 详细信息 | VARCHAR(100)	 ||

表2活动表

|属性|类型|约束|
|--|--|--|
|活动编号	|INT	|主码，自增|
|活动名称	|VARCHAR(30)	|唯一|
|举办社团编号|INT	|外码|
|时间	|DATE	||

表3学生表

|属性	|类型	|约束|
|--|--|--|
|学号	|VARCHAR(20)	|主码|
|姓名	|CHAR(10)	|非空|
|性别	|CHAR(2)	|CHECK(男或女)|
|年级专业班级	|CHAR(20)	||
|总工时	|INT	|默认值为0|

表4社团人员名单
|属性	|类型	|约束|
|--|--|--|
|社团编号	|INT	|外码|
|学号	|VARCHAR(20)|	外码|
|职位	|CHAR(10)	||
|备注	|VARCHAR(50)||	
|主码|（社团编号，学号）||

表5活动人员名单
|属性	|类型	|约束|
|--|--|--|
|活动编号	|INT	|外码|
|学号	|VARCHAR(20)	|外码|
|获得工时数	|INT	|大于零|
|备注	|VARCHAR(50)||	
|主码   |（活动编号，学号）|
