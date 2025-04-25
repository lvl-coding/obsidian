==shared_pt====r==可以在任何地方都不使用时自动删除相关指针，从而帮助彻底消除内存泄漏和悬空指针的问题
 
shared_ptr读取是线程安全的，因为引用计数是原子变量，但是修改指针指向是不是线程安全的。
 
每个shared_ptr对象在内部指向两个内存位置：

1. 指向对象的指针。
2. 用于控制引用计数数据的指针。

共享所有权如何在参考计数的帮助下工作：

1. 当新的shared_ptr对象与指针关联时，则在其构造函数中，将与此指针关联的引用计数增加1
2. 当任何shared_ptr对象超出作用域时，则在其析构函数中，它将关联指针的引用计数减1。如果引用计数变为0，则表示没有其他shared_ptr对象与此内存关联，在这种情况下，它使用delete函数删除该内存
 
常用操作：  
 

```
   
std
::
shared_ptr
<
int
> 
p1
(
new
 
int
());

   
p1
.
use_count
();


   
std
::
shared_ptr
<
int
> 
p1
 = 
std
::
make_shared
<
int
>();

   
p1
.
reset
();

   
p1
.
reset
(
new
 
int
(
34
));

   
p1
 
=
 
nullptr
;



// 

std
::
shared_ptr
<
int
> 
p3
(
new
 
int
[
12
]);
   // 

// 

std
::
shared_ptr
<
int
[]> 
p3
(
new
 
int
[
12
]);
  // 


// 

void
 
deleter
(
Sample
 
*
 
x
)

{

   
std
::cout << 
"DELETER FUNCTION CALLED
\n
"
;

   
delete[]
 x;

}

// 

std
::
shared_ptr
<
Sample
> 
p3
(
new
 
Sample
[
12
], 
deleter
);



std
::
shared_ptr
<Sample> 
ptr3
;

if
(!
ptr3
)

   
std
::
cout
<<
"Yes, ptr3 is empty"
 
<<
 
std
::
endl
;

if
(
ptr3
 
==
 
NULL
)

   
std
::
cout
<<
"ptr3 is empty"
 
<<
 
std
::
endl
;

if
(
ptr3
 
==
 
nullptr
)

   
std
::
cout
<<
"ptr3 is empty"
 
<<
 
std
::
endl
;





```

std::shared_ptr<int> p1(new int());  
    p1.use_count();  
std::make_shared 使用原子操作一次性为int对象和用于引用计数的数据都分配了内存；而new操作符只是为int分配了内存，将int对象和用于引用计数的数据分两次分配了内存  
    std::shared_ptr<int> p1 = std::make_shared<int>();  
    p1.reset();  
    p1.reset(new int(34));  
    p1 = nullptr;   当 shared_ptr 对象指向数组  
// 需要添加自定义删除器的使用方式  
std::shared_ptr<int> p3(new int[12]);   // 仅用于演示自定义删除器  
// 指向数组的智能指针可以使用这种形式  
std::shared_ptr<int[]> p3(new int[12]);  // 正确使用方式
 
像这样申请的数组，应该调用delete []释放内存，而shared_ptr<int>析构函数中默认delete并不能满足需，可以使用shared_ptr<int[]>形式或者添加自定义删除器。
 
- 给shared_ptr添加自定义删除器

// 自定义删除器  
void deleter(Sample * x)  
{  
    std::cout << "DELETER FUNCTION CALLED\n";  
    delete[] x;  
}  
// 构造函数传递自定义删除器指针  
std::shared_ptr<Sample> p3(new Sample[12], deleter);
 
与普通指针的优缺点  
与普通指针相比，shared_ptr仅提供->，*和==运算符， 没有+，-，++，--，[]等运算符。
 
- NULL检测

当我们创建shared_ptr对象而不分配任何值时，它就是空的；普通指针不分配空间的时候相当于一个野指针，指向垃圾空间，且无法判断指向的是否是有用数据。

- shared_ptr检测空值

std::shared_ptr<Sample> ptr3;  
if(!ptr3)  
    std::cout<<"Yes, ptr3 is empty" << std::endl;  
if(ptr3 == NULL)  
    std::cout<<"ptr3 is empty" << std::endl;  
if(ptr3 == nullptr)  
    std::cout<<"ptr3 is empty" << std::endl;  
注意事项：  
不要使用同一个原始指针构造shared_ptr（假如使用原始指针num创建了p1，又同样方法创建了p3，当p1超出作用域时会调用delete释放num内存，此时num成了悬空指针，当p3超出作用域再次delete的时候就可能会出错。）  
不要用栈中的指针构造 shared_ptr 对象（shared_ptr 默认的构造函数中使用的是delete来删除关联的指针，所以构造的时候也必须使用new出来的堆空间的指针。当 shared_ptr 对象超出作用域调用析构函数delete 指针&x时会出错。）  
建议使用 make_shared  
另外不建议使用get()函数获取 shared_ptr 关联的原始指针，因为如果在 shared_ptr 析构之前手动调用了delete函数，同样会导致类似的错误。