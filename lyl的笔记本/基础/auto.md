auto变量忽视掉初始化表达式的顶层const  
auto&则保留初始化表达式的顶层const或volatile属性  
希望auto推导的是顶层const，则const auto  
当类型不为引用时，auto的推导结果不保留表达式的const属性  
类型为引用时，auto的推导结果保留表达式的const属性
 
auto修饰迭代器会去拷贝容器中的元素  
auto&修饰的迭代器可以修改容器中的元素，如果不希望修改，则使用const auto &  
for (auto& data : vec){  
cout<<data<<endl;  
//do something