```
#include
 
<iostream>

#include
 
<deque>

#include
 
<unordered_map>

using
 
namespace
 
std
;

class
 
lrcCache

{

   
class
 
node

   {

   
public:

       
int
 
val
;

       
node
* 
pri
, *
next
;

       
node
(
int
 
_val
) :
val
(
_val
) {

           
pri
 = 
NULL
;

           
next
 = 
NULL
;

       }

   };

   
int
 
length
;

   
deque
<
node
*> 
head
;

   
unordered_map
<
int
, 
node
*> 
myMap
;

public:

   
int
 
get
(
int
 
val
) {

       
if
 (
myMap
.
count
(
val
)) {

           
node
* 
cur
 = 
myMap
[
val
]
;

           
head
.
erase
(
cur
);

           
cur
->
pri
->
next
 = 
cur
->
next
;

           
cur
->
next
 = 
head
;

           
head
 = 
cur
;

           
return
 
cur
->
val
;

       }

   }

   
void
 
put
(
int
 
val
) {

       
if
 (
myMap
.
count
(
val
)) {

           
node
* 
cur
 = 
myMap
[
val
]
;

           
cur
->
pri
->
next
 = 
cur
->
next
;

           
cur
->
next
 = 
head
;

           
head
 = 
cur
;

           
return
;

       }

       
else
 {

           
if
 (
head
.
size
() >= 
length
) {

               
head
.
pop_back
();

               
head
.
emplace_front
(
node
(
val
));

           }

       }

   }

};
```

<iostream>  
#include <deque>  
#include <unordered_map>  
using namespace std;  
class lrcCache  
{  
    class node  
    {  
    public:  
        int val;  
        node* pri, *next;  
        node(int _val) :val(_val) {  
            pri = NULL;  
            next = NULL;  
        }  
    };  
    int length;  
    deque<node*> head;  
    unordered_map<int, node*> myMap;  
public:  
    int get(int val) {  
        if (myMap.count(val)) {  
            node* cur = myMap[val];  
            head.erase(cur);  
            cur->pri->next = cur->next;  
            cur->next = head;  
            head = cur;  
            return cur->val;  
        }  
    }  
    void put(int val) {  
        if (myMap.count(val)) {  
            node* cur = myMap[val];  
            cur->pri->next = cur->next;  
            cur->next = head;  
            head = cur;  
            return;  
        }  
        else {  
            if (head.size() >= length) {  
                head.pop_back();  
                head.emplace_front(node(val));  
            }  
        }  
    }  
};