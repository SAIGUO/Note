## Before
一直以为自己编程规范做的不错，结果做了作者的编程测试，得分竟然将将50分！要好好学习一下了，特别是内存管理部分！！！

## 一、程序版式
1. 函数前后加空行
2. 一行代码只做一件事
3. if、while...不省略{}
4. 尽可能在定义变量的同时初始化该变量（就近原则）
5. 二元操作符前后加空格，一元操作符前后不加
6. 表达式比较长的 for 语句和 if 语句，为了紧凑起见可以适当地去掉一些空格
7. 代码行不要过长，在低优先级操作符处拆分，操作符放在新行之首
8. *、&修饰符尽量靠近变量名

## 二、表达式和基本语句
1. 不可将布尔变量直接与TRUE、FALSE 或者 1、0进行比较，直接写if(flag)
2. 应当将指针变量用“ == ”或“ ！= ”与 NULL 比较。if (p) 容易让人误解 p 是布尔变量
3. 在多重循环中，如果有可能，应当将最长的循环放在最内层，最短的循环放在最外层，以减少 CPU 跨切循环层的次数
4. 如果循环体内存在逻辑判断，并且循环次数 N 非常大，宜将逻辑判断移到循环体的外面
5. 不可在 for 循环体内修改循环变量
6. 建议 for 语句的循环控制变量的取值采用“半开半闭区间”写法，0 <= i < N
7. switch 是多分支选择语句，每个 case 语句的结尾要加 break, 最后一定要加 default 分支

## 三、常量
1. 可以用#define、const定义常量
> - const常量有数据类型，编译器会做安全检查，宏常量没有数据类型，编译器只做字符替换
> - const常量支持调试，宏常量不支持
2. 不能在类声明中初始化 const 数据成员， const 数据成员的初始化只能在类构造函数的初始化表中进行

## 四、函数设计
1. 参数命名要恰当，顺序要合理，一般将目的参数放前，源参数放后
2. 如果参数是指针，且仅做输入使用，应在类型前加const
3. 如果输入参数以值传递的方式传递对象，则宜改用 “ const & ” 方式来传递，省去临时对象的构造和析构过程，提高效率
4. 有时候函数原本不需要返回值，但为了增加灵活性如支持链式表达，可以附加返回值，如字符串拷贝函数 strcpy 
5. 引用规则：a.被创建的同时必须被初始化；b.不能有NULL引用；c.引用被初始化后将不能被改变

## 五、内存管理
### 1. 常见内存错误
- 内存分配错误却使用了它

使用内存之前，检查指针是否为NULL。

- 内存分配成功，但未初始化

如数组，缺少初始化，使用缺省值可能导致引用初值错误，因此一定要赋初值。

- 内存分配成功，且已初始化，但操作越过了内存边界

常见于数据下标操作，for循环语句中循环次数。

- 忘记释放内存，造成内存泄漏

动态内存的申请与释放一定要配对。

- 释放了的内存却继续使用

a.对象调用过于复杂，不清楚某个对象是否已经释放了内存

b.return语句写错，不能返回指向栈内存的指针或者引用 

c.使用free或者delete释放内存后，没有将指针设置为NULL，产生野指针

### 2. 指针与数组
数组名对应着（而不是指向）一块内存，其地址与容量在生命期内保持不变，只有数组的内容可以改变。
指针可以随时指向任意类型的内存块。
```
char a[] = “hello”;
a[0] = ‘X’; // 数组内容可变
char *p = “world”; // 注意 p 指向常量字符串
p[0] = ‘X’; // 该语句企图修改常量字符串的内容，编译器不能发现该错误，会导致运行错误
```
上述代码中，不能对数组名进行直接复制与比较。应该用标准库函数 strcpy 进行复制，用 strcmp 进行比较。

当数组作为函数的参数进行传递时，该数组自动退化为同类型的指针。

### 3. 动态内存的释放
局部变量指针会随着函数调用结束自动消亡，但其所指内存未必释放
- 指针消亡，并不表示其所指向内存会自动释放

- 内存被释放，并不表示指针会消亡或者成了NULL指针

### 4. 野指针
产生原因：

a. 指针变量没有被初始化；

b. 指针p被free或delete掉后，没有设置为NULL，让人误以为p是个合法的指针；

c. 指针操作超越了变量的作用范围。

### 5. malloc、free 与 new、delete
malloc 与 free 是 C++/C 语言的**标准库函数**， new/delete 是 C++的**运算符**。它们都可用于申请动态内存和释放内存。

对于非内部数据类型的**对象**而言，在创建的同时要自动执行构造函数，在消亡之前要自动执行析构函数。由于malloc/free 是库函数而不是运算符，不在编译器控制权限之内，不能够把执行构造函数和析构函数的任务强加于 malloc/free。

因此 C++语言需要一个能完成动态内存分配和初始化工作的运算符 new，以及一个能完成清理与释放内存工作的运算符 delete。

> 如果 p 是 NULL 指针，那么 free 对 p 无论操作多少次都不会出问题。如果 p 不是 NULL 指针，那么 free 对 p 连续操作两次就会导致程序运行错误。
如果用 new 创建对象数组，那么只能使用对象的无参数构造函数。例如

> `Obj *objects = new Obj[100]; // 创建 100 个动态对象`

> 不能写成

> `Obj *objects = new Obj[100](1);// 创建 100 个动态对象的同时赋初值 1`

> 在用 delete 释放对象数组时，留意不要丢了符号‘ []’。例如

> `delete []objects; // 正确的用法`

> `delete objects; // 错误的用法`

> 后者相当于 delete objects[0]，漏掉了另外 99 个对象。

### 6. 内存耗尽的处理办法
a. 判断指针是否为 NULL，如果是则马上用 return 语句终止本函数;

b. 判断指针是否为 NULL，如果是则马上用 exit(1) 终止整个程序的运行；

c. 为 new 和 malloc 设置异常处理函数。

## 六、C++函数的高级特性

### 1. 函数重载
类的构造函数需要重载：因为C++规定，构造函数与类同名，构造函数只能有一个名字，如果想用不同的方法创建对象就只能用重载机制实现。

只能通过参数数量及类型区分重载函数，返回值不行。 因为编译器根据参数为每个重载函数产生不同的内部标识符。

C++程序要调用已经被编译后的 C 函数：

假设某个 C 函数的声明如下：`void foo(int x, int y);`

该函数被 C 编译器编译后在库中的名字是_foo ，而C++编译器则会得到_foo_int_int这样的名字，因此C++程序不能直接调用C编译后的函数，通过在函数或者类最外层添加“ extern C ”解决。这是告诉C++编译器，函数foo是个 C 连接，应该在库中找_foo， 而不是_foo_int_int。

全局函数和类的成员函数同名不算重载，因为函数的作用域不同。为了区别，要在全局函数被调用时应加‘::’ 标志

### 2.成员函数的重载、覆盖、隐藏
- 重载特性

相同的范围；函数名字相同；参数不同；virtual 可有可无。

- 覆盖特性

不同的范围（基类与派生类）；函数名字相同；参数相同；基类函数必须有 virtual 关键字。

- 隐藏规则（隐藏指派生类函数屏蔽了与其同名的基类函数）

a. 如果派生类函数与基类相同，但参数不同，不管有无 virtual，基类函数被隐藏；

b. 如果派生类函数与基类相同，参数也相同，但基类没有 virtual，基类函数被隐藏。  

### 3. 内联函数
C++支持内联，目的是提高函数的执行效率。

C程序用宏代码提高执行效率。预处理器用复制宏代码的方式替代函数调用，缺点是易出错，且无法操作类的私有数据成员。

C++调用内联函数时，编译器首先会检查调用是否正确，如果正确，内联函数代码会直接替代调用函数，省去函数调用的开销。

关键字inline必须与函数定义放在一起，仅放在函数的声明前起不到作用。所以说，inline 是一种“用于实现的关键字”，而不是一种“用于声明的关键字”。

定义在类声明之中的成员函数将自动地成为内联函数。

> 慎用内联：内联以代码膨胀（复制）为代价，省去了函数调用的开销，从而提高了执行效率。如果执行函数体内代码的时间，相比于函数调用的开销较大，效率的收获会很小。且内联函数的调用都要复制代码，使程序总代码量增多，消耗内存。

不适合使用内联的情况：

a. 如果函数体内的代码比较长，使用内联将导致内存消耗代价较高;

b. 如果函数体内出现循环，那么执行函数体内代码的时间要比函数调用的开销大。

## 七、类的构造、析构、赋值函数
C++编译器会自动为一个类产生四个缺省函数：无参构造、拷贝构造、析构、赋值函数。

### 1. 构造函数的初始化表
使用规则：

a. 如果类之间存在继承关系，派生类必须在初始化列表中调用基类构造函数；
```
class A
{
  A(int x); // A 的构造函数
};
class B : public A
{
  B(int x, int y);// B 的构造函数
};
B::B(int x, int y) : A(x) // 在初始化表里调用 A 的构造函数
{
}
```

b. 类的 const 常量只能在初始化表里被初始化，因为它不能在函数体内用赋值的方式来初始化;

c. 类的数据成员的初始化可以采用初始化表或函数体内赋值两种方式，这两种方式的效率不完全相同。非内部数据类型的**成员对象**应当采用第一种方式初始化，以获取更高的效率（对内部数据类型的数据成员来说两种没有区别）。
> 如对类A的类B类型的成员对象进行两种方式的初始化、赋值时，初始化列表方式只会调用类B的拷贝构造函数；在函数体内赋值时，首先调用类B的无参构造函数创建一个类B对象，在调用类B的赋值函数为该成员对象赋值。

### 2. 构造与析构次序
构造构造从类的最根层开始，先调用基类构造，再调用成员函数构造。析构顺序与构造次序相反。

成员对象初始化的次序只由成员对象在类中声明的次序决定，这是因为类的声明是唯一的。

### 3. 拷贝构造、赋值函数
不主动编写拷贝构造函数和赋值函数，编译器将以“位拷贝”的方式自动生成缺省的函数。倘若类中含有**指针变量**，那么这两个缺省的函数就隐含了错误。

**拷贝构造函数是在对象被创建时调用的，而赋值函数只能被已经存在了的对象调用。**

### 4. 在派生类中实现类的基本函数
基类的构造函数、析构函数、赋值函数都不能被派生类继承。

编写基本函数注意：

a. 派生类的构造函数应在其初始化表里调用基类的构造函数;

b. 基类与派生类的析构函数应该为虚（保证当基类指针指向派生类对象时，调用基类的虚函数时可以同时析构掉派生类对象）;
 
c. 在编写派生类的赋值函数时，注意不要忘记对基类的数据成员重新赋值;

## 八、其他编程经验

### 1. const的使用

#### 1.1 修饰函数参数
- 对于非内部数据类型的输入参数，应该将“值传递”的方式改为“ const 引用传递”，目的是提高效率。例如将 void Func(A a) 改为 void Func(const A &a)。
- 对于内部数据类型的输入参数，不要将“值传递”的方式改为“ const 引用传递”。否则既达不到提高效率的目的，又降低了函数的可理解性。例如 void Func(int x) 不应该改为 void Func(const int &x)。

#### 1.2 修饰函数的返回值
- 如果给以“指针传递”方式的函数返回值加 const 修饰，那么函数返回值（即指针）的内容不能被修改；
- 如果函数返回值采用“值传递方式”，由于函数会把返回值复制到外部临时的存储单元中，加 const 修饰没有任何价值。

#### 1.3 const成员函数
任何不会修改数据成员的函数都应该声明为 const 类型。（ const 关键字只能放在函数声明的尾部）

### 2. 编程建议
- 变量（指针、数组）被创建之后应当及时把它们初始化，以防止把未被初始化的变量当成右值使用；
- 当心变量的初值、缺省值错误，或者精度不够；
- 尽量使用显式的数据类型转换（让人们知道发生了什么事），避免让编译器轻悄悄地进行隐式的数据类型转换；
- 正常值应当用输出参数获得，而错误标志用 return 语句返回。
