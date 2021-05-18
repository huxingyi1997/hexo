---
title:  JavaScript手写ES5继承
date:  2021-04-04 16:20:00
categories: 
- web前端
tags:
- JavaScript
- ES5继承
- 函数手写
---
在面向对象的语言中，继承作为面向对象的三个属性，在ES5中却没有直接比较好的实现方式，虽然红宝书看过了，对书中提到的继承方法津津有味，但面试中要求手写才发现很多都写错了，现场分析脑子一片混乱，也不怕丑我把我错误的代码也晒出来，好好反思，其中也包括Object.create()方法的手写方法，吃一堑涨一智

<!-- more -->

面试题如下，想让student继承person，先写下基本框架

```
function person() {
	this.kind="person";
}
person.prototype.eat = function (food) {
	console.log(this.name+" is eating "+food);
}

function student() {

}
```

先写了一下继承后上述prototype和__proto__的关系

```
student.prototype.__proto__ === person.prototype
```

原型继承

```
student.prototype = new person();
```

缺点：原型是所有子类实例共享的，改变一个其他也会改变。

该打，我现场写没写完全对，写的是

```
student = new person();
```

脑子怎么想的，这样student怎么继承person的实例和共享



构造继承

```
function student( ) {
	person.call(this);
}
```

缺点：不能继承父类原型的方法，函数在构造函数中，每个子类实例不能共享函数，浪费内存。

这个当时倒是完全写对了



组合继承

```
function student() {
	person.call(this);
}

student.prototype = new person();
```

缺点：person的构造函数会多执行了一次

哭了，又没有完全写对，错误同原型继承



原型式继承

```
student.prototype = Object.create(person.prototype);
```

又搞错了

写成了

```
student.prototype = Object.create(person());
```

不知道怎么想的，面试官提示我知不知道Object.create怎么一回事，还口述了手写过程

```
Object.prototype.myCreate(proto) {
    function f() {};
    f.prototype = proto;
    return new f();
}
```

口述的方法倒是对的，就是这样传递的参数是什么，我怎么思考



口述之后我发现传递person()不对劲，又重写了

```
student.prototype = Object.create(person);
```

真是该打



之后是寄生组合继承

```
function student() {
	person.call(this);
}

student.prototype = person.prototype;
// 或者 student.prototype = Object.create(person.prototype);
```

我的错误跟上面的一样，

这种继承方法父类原型和子类原型是同一个对象，无法区分子类真正是由谁构造。



最后当然是最完美的寄生组合优化继承

```
function student() {
	person.call(this);
}

student.prototype = person.prototype;
// 或者 student.prototype = Object.create(person.prototype);
student.prototype.constructor = student;
```

总之错误真多，觉得没脸见人了，手写和口述讲思路难度差距真大