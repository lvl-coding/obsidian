```
#include
 
<iostream>

#include
 
<future>

#include
 
<thread>

#include
 
<chrono>

int
 
foo
(
int
 
a
, 
int
 
b
) {

   
std
::
this_thread
::
sleep_for
(
std
::
chrono
::
microseconds
(
std
::
abs
(
b
 - 
a
)));

   
std
::
cout
 
<<
 
"thread "
 
<<
 
std
::
this_thread
::
get_id
() 
<<
 
" sleep for "
 
<<
 
std
::
abs
(
b
 - 
a
) 
<<
 
" microseconds"
 
<<
 
std
::
endl
;

   
return
 
std
::
abs
(
b
 - 
a
);

}

int
 
main
(
int
 
argc
, 
char*
 
argv
[])

{

   //construct a packaged_task

   
std
::
packaged_task
<
int
()> 
fo
(
foo
);

   //if fo is valid and exchange foox with fo

   
std
::
packaged_task
<
int
()> 
foox
;

   
if
 (
fo
.
valid
()) {

       
fo
.
swap
(
foox
);

   }

   //get future

   
std
::
future
<
int
> 
f
 = 
foox
.
get_future
();

   //create a thread

   
std
::
thread
(
std
::
move
(
foox
), 
20
, 
30
).
detach
();

   
int
 
val
 = 
f
.
get
();

   
std
::
cout
 
<<
 
val
 
<<
 
std
::
endl
;

   //move

   
std
::
packaged_task
<
int
()> 
bar
([](
int
 
x
) {
return
 
x
 * 
10
; });

   
auto
 
barx
 = 
std
::
move
(
bar
);

   
std
::
future
<
int
> 
f2
 = 
barx
.
get_future
();

   
barx
()
;

   
std
::
cout
 
<<
 
f2
.
get
() 
<<
 
std
::
endl
;

   //reset

   
barx
.
reset
();

   
f2
 
=
 
barx
.
get_future
();

   
std
::
thread
(
std
::
move
(
barx
), 
10
).
join
();

   
std
::
cout
 
<<
 
f2
.
get
() 
<<
 
std
::
endl
;

   
return
 
0
;

}
```

<iostream>  
#include <future>  
#include <thread>  
#include <chrono>  
int foo(int a, int b) {  
    std::this_thread::sleep_for(std::chrono::microseconds(std::abs(b - a)));  
    std::cout << "thread " << std::this_thread::get_id() << " sleep for " << std::abs(b - a) << " microseconds" << std::endl;  
    return std::abs(b - a);  
}  
int main(int argc, char* argv[])  
{  
    //construct a packaged_task  
    std::packaged_task<int()> fo(foo);  
    //if fo is valid and exchange foox with fo  
    std::packaged_task<int()> foox;  
    if (fo.valid()) {  
        fo.swap(foox);  
    }  
    //get future  
    std::future<int> f = foox.get_future();  
    //create a thread  
    std::thread(std::move(foox), 20, 30).detach();  
    int val = f.get();  
    std::cout << val << std::endl;  
    //move  
    std::packaged_task<int()> bar([](int x) {return x * 10; });  
    auto barx = std::move(bar);  
    std::future<int> f2 = barx.get_future();  
    barx();  
    std::cout << f2.get() << std::endl;  
    //reset  
    barx.reset();  
    f2 = barx.get_future();  
    std::thread(std::move(barx), 10).join();  
    std::cout << f2.get() << std::endl;  
    return 0;  
}