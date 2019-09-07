## 原始类型有哪些？

boolean 
null 
undefined 
number 
symbol 
string

原始类型不能调用方法，但如果这个原型类型本身就是string的话例外：
如'1'.toString(),这时候'1'就已经被强制转换成了string类型，也就是对象类型，所以可以调用toString函数，string类型是不可改变的，无论你在sting类型上调用任何方法，都不会对值有改变

## 对象类型和原始类型的不同之处？

原始类型存储的是值，对象类型存储的是地址（指针）
当创建一个对象类型的时候，计算机会在内存中帮我们开辟一个空间来存放值，但需要找到这个空间，这个空间就拥有一个地址（指针）

## typeof是否能正确判断类型？instanceof能正确判断对象的原理是什么？

typeof除了原始类型来说，除了null都可以显示正确的类型，所以typeof并不能准确判断变量到底是什么类型

typeof可以正确判断除了null以外的原始类型，因为bug的缘故，null变成了object

typeof [] // 'object'
typeof {} // 'object'
typeof console.log // 'function'

instanceof的内部机制是通过原型链判断的
