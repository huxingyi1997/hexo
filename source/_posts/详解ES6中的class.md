---
title:  详解ES6中的class
date:  2021-05-11 21:30:00
categories: 
- web前端
tags:
- javascript
- class
- 面向对象
---
Javascript的Object模型很独特，和其他语言都不一样，初学者不容易掌握。Javascript是一种基于对象（object-based）的语言，你遇到的所有东西几乎都是对象。但是在ES6之前，它又不是一种真正的面向对象编程（OOP）语言，因为它的语法中没有class（类）。ES6中实现class，这其实是一个语法糖，其底层还是通过 构造函数 去创建的。所以它的绝大部分功能，ES5 都可以做到。新的class写法只是让对象原型的写法更加清晰、更像面向对象编程的语法而已。其实是我在思考怎么去写一个私有不可改变的属性的时候，向大佬讨教了一番，也想着怎么去收集相关知识，为TS进一步学习打好基础，感谢大佬的[详解ES6中的class](https://juejin.cn/post/6844904086089760775)，受益匪浅。
<!-- more -->

## 1.class

class是一个语法糖，其底层还是通过 `构造函数` 去创建的。所以它的绝大部分功能，ES5 都可以做到。新的class写法只是让对象原型的写法更加清晰、更像面向对象编程的语法而已。

```js
function Person(name, age) {
    this.name = name;
    this.age = age;
}
Person.prototype.sayName = function() {
    return this.name;
}

const xiaoming = new Person('小明', 18);
console.log(xiaoming); 
// {name: "小明", age: 18}
```

上面代码用`ES6`的`class`实现，就是下面这样

```
class Person {
    constructor(name, age) {
      this.name = name;
      this.age = age;
    }
  
    sayName() {
      return this.name;
    }
}
const xiaoming = new Person('小明', 18)
console.log(xiaoming);
// { name: '小明', age: 18 }

console.log((typeof Person));
// function
console.log(Person === Person.prototype.constructor);
// true
```

constructor方法，这就是构造方法，this关键字代表实例对象。 类的数据类型就是函数，类本身就指向构造函数。

> 定义类的时候，前面不需要加 function, 而且方法之间不需要逗号分隔，加了会报错。

类的所有方法都定义在类的prototype属性上面。

```
class A {
    constructor() {}
    toString() {}
    toValue() {}
}
// 等同于
function A () {
    // constructor
};
A.prototype.toString = function() {};
A.prototype.toValue = function() {};
```

在类的实例上面调用方法，其实就是调用原型上的方法。

```
let a = new A();
a.constructor === A.prototype.constructor // true
```

### 1.1 constructor 方法

constructor方法是类的默认方法，通过new命令生成对象实例时，自动调用该方法。一个类必须有constructor方法，如果没有显式定义，一个空的constructor方法会被默认添加。

```
class A {
	
}

// 等同于
class A {
  	constructor() {}
}
```

constructor方法默认返回实例对象（即this），完全可以指定返回另外一个对象。

```
class A {
    constructor() {
    	return Object.create(null);
    }
}

console.log((new A()) instanceof A);
// false
```

### 1.2 类的实例

实例的属性除非显式定义在其本身（即定义在this对象上），否则都是定义在原型上（即定义在class上）。

### 1.3 注意事项

#### 1.3.1 class不存在变量提升

```
new A(); // ReferenceError
class A {}
```

因为 ES6 不会把类的声明提升到代码头部。这种规定的原因与继承有关，必须保证子类在父类之后定义。

```
{
    let A = class {};
    class B extends A {};
}
```

上面的代码不会报错，因为 B继承 A的时候，A已经有了定义。但是，如果存在 class提升，上面代码就会报错，因为 class 会被提升到代码头部，而let命令是不提升的，所以导致 B 继承 A 的时候，Foo还没有定义。

#### 1.3.2 this的指向

类的方法内部如果含有this，它默认指向类的实例。但是，必须非常小心，一旦单独使用该方法，很可能报错。



## 2.静态方法static

> 类（class）通过 **static** 关键字定义静态方法。不能在类的实例上调用静态方法，而应该通过类本身调用。这些通常是实用程序方法，例如创建或克隆对象的功能。

### 2.1 语法

```javascript
static methodName() { ... }
```

### 2.2 描述

类相当于实例的原型，所有在类中定义的方法，都会被实例继承。 如果在一个方法前，加上 static 关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就称为"静态方法"。静态方法通常用于创建实用程序函数。

#### 2.2.1 用法

```
class A {
    static classMethod() {
        return 'hello';
    }
}
A.classMethod();
console.log(A.classMethod());
// 'hello'

const a = new A();
a.classMethod();
// TypeError: a.classMethod is not a function
```

`A` 类的`classMethod` 方法前有 `static`关键字，表明这是一个静态方法，可以在 `A` 类上直接调用，而不是在实例上调用 在实例`a`上调用静态方法，会抛出一个错误，表示不存在该方法。

#### 2.2.2 this指向

如果静态方法包含this关键字，这个this指的是类，而不是实例。

```
class A {
    static classMethod() {
    	// this指向A类
    	this.baz();
    }
    // 静态方法
    static baz() {
    	console.log('hello');
    }
    // 普通方法
    baz() {
    	console.log('world');
    }
}
A.classMethod();
// hello
```

静态方法`classMethod`调用了`this.baz`，这里的`this`指的是`A`类，而不是`A`的实例，等同于调用`A.baz`。另外，从这个例子还可以看出，静态方法可以与非静态方法重名。

#### 2.2.3 继承

父类的静态方法，可以被子类继承。

```
class A {
    static classMethod() {
        console.log('hello');
    }
}

class B extends A {}

B.classMethod();
// 'hello'
```

#### 2.2.4 调用方法

从上文this指向中可以分析静态方法调用同一个类中的其他静态方法，可使用this关键字。

非静态方法中，不能直接使用 this关键字来访问静态方法。而是要用类名来调用：`CLASSNAME.STATIC_METHOD_NAME()` ，或者用构造函数的属性来调用该方法： `this.constructor.STATIC_METHOD_NAME()`.

```
class A {
    classMethod() {
    	// 使用A类
    	// A.baz();
    	// 或者使用构造函数
    	this.constructor.baz();
    }
    // 静态方法
    static baz() {
    	console.log('hello');
    }
    // 普通方法
    baz() {
    	console.log('world');
    }
}
let a = new A;
a.classMethod();
// hello
```



## 3.static、public、private和protected

### 3.1 static静态属性

静态属性指的是 Class 本身的属性，即Class.propName，而不是定义在实例对象（this）上的属性。 写法是在实例属性的前面，加上static关键字。

```
class MyClass {
	// static属性
	static myStaticProp = 42;
	
	constructor() {
		console.log(MyClass.myStaticProp);
	}
}
new MyClass; // 42
```

### 3.2 public属性

> 对象的成员都是public成员。任何对象都可以访问，修改，删除这些成员或添加新成员。主要有两种方式来在一个新对象里放置成员: 

#### 3.2.1 在构造函数中使用

> 这种技术通常用来初始化public实例变量。构造函数的“this”变量用来给对象添加成员。

```
function Container(param) {
	this.member = param;
}
let myContainer = new Container('abc');
console.log(myContainer.member);
// abc
```

这样，如果我们构造一个新对象var myContainer = new Container('abc')，则myContainer.member为'abc'

#### 3.2.2 在prototype/内部方法中使用

这种技术通常用来添加public方法。当寻找一个成员并且它不在对象本身里时，则从对象的构造函数的prototype成员里找。 
prototype机制用来做继承。为了添加一个方法到构造函数创建的所有对象里，只需添加到构造函数的prototype: 

```Kotlin
function Container(param) {
	this.member = param;
}
Container.prototype.stamp = function (string) {
    return this.member + string;
}
let myContainer = new Container('abc');
console.log(myContainer.stamp('def'));
// abcdef
```

这样，我们可以调用该方法myContainer.stamp('def')，结果为'abcdef'。 

#### 3.2.3 ES6中的class实现

```
class Container {
	constructor(param) {
		this.member = param;
	}
	stamp (string) {
        return this.member + string;
    }
}
let myContainer = new Container('abc');
console.log(myContainer.stamp('def'));
// abcdef
```

### 3.3 private属性和方法

#### 3.3.1 ES5的private属性

private成员由构造函数产生。普通的var变量和构造函数的参数都称为private成员。 

```
function Container(param) {
    this.member = param;
    // private属性
    var secret = 3;
    var that = this;
}
```

该构造函数创建了3个private实例变量: param，secret和that。它们被添加到对象中，但是不能被外部访问，也不能被该对象自己的 public方法访问。它们只能由private方法访问，类似set,get属性的封装。

```
function People () {
	this.name = 'Yorhom';
	var age = 16;
	// 普通方法调用public属性
	this.getName = function() {
		return this.name;
	}
	// 普通方法调用private属性
	this.getAge = function() {
		return age;
	}
} 

let yorhom = new People();

console.log(yorhom.age);
// undefined

console.log(yorhom.getAge());
// 16
```

#### 3.3.2 ES5的private方法

private方法是构造函数的内部方法。

```
function People () {
	this.name = 'Yorhom';
	var age = 16;
	// 普通方法调用public属性
	this.getName = function() {
		return this.name;
	}
	// 普通方法调用private属性
	this.getAge = function() {
		return age;
	}
	// private方法,构造器中定义的方法，即为私有方法
	function printAge() {
		return 'I\'m ' + age + ' years old.';
	}
	
	// private方法可以内部调用
	console.log(printAge()); 
} 

let yorhom = new People(); // I'm 16 years old.

console.log(yorhom.age);
// undefined

console.log(yorhom.getAge());
// 16

// console.log(yorhom.printAge());
// TypeError: yorhom.printAge is not a function
```

说明：类的构造函数里定义的function，即为私有方法；而在构造函数里用var声明的变量，也相当于是私有变量。(不过类比于c#这类强类型语言中的私有成员概念还是有区别的，比如无法在非构造函数以外的其它方法中调用) 

#### 3.3.3 ES6的private属性和方法

Class私有属性与公共属性的定义方式几乎是一样的，只是需要在属性名称前面添加**#**符号，定义私有属性的时候也可以不用赋值：

```javascript
class People {
    #age;
}
```

引用私有属性也只需要使用**this.#**就好了。

```javascript
class People {
    #age;
    // 普通方法调用private属性
    getAge() {
        // 使用this.#
        return this.#age;
    }
}
```

上文中的例子可以写成

```
class People {
    #age;
    constructor() {
		this.name = 'Yorhom';
		this.#age = 16;
	}
	// 普通方法调用public属性
	getName() {
		return this.name;
	}
    // 普通方法调用private属性
    getAge() {
        // 使用this.#
        return this.#age;
        // 或者直接简化
    }
}
let yorhom = new People();
console.log(yorhom.age); // undefined
console.log(yorhom.getAge()); // 16
```

对于私有属性，我们是不可以直接通过 Class 实例来引用的，这也是私有属性的本来含义。但是有一种情况除外，在 Class 定义中，我们可以引用 Class 实例的私有属性：

```javascript
class Foo {
    #privateValue = 42;
    static getPrivateValue(foo) {
        return foo.#privateValue;
    }
}

Foo.getPrivateValue(new Foo()); // 42
```

其中，**foo**是**Foo**的实例，在 Class 定义中，我们可以通过 foo 来引用私有属性**#privateValue**。

Class 的私有属性是提案[proposal-class-fields](https://github.com/tc39/proposal-class-fields)的一部分，这个提案只关注 Class 的属性，它并没有对 Class 的方法进行任何修改。而 Class 的私有方法是提案[proposal-class-fields](https://github.com/tc39/proposal-class-fields)的一部分。

Class 的私有方法语法改造ES5的private方法如下：

```javascript
class People {
    #age;
    constructor() {
		this.name = 'Yorhom';
		this.#age = 16;
        // private方法可以内部调用
        console.log(this.#printAge());
	}
	// 普通方法调用public属性
	getName() {
		return this.name;
	}
    // 普通方法调用private属性
    getAge() {
        // 使用this.#
        return this.#age;
        // 或者直接简化
    }
	// private方法,构造器中定义的方法，即为私有方法
	#printAge() {
        return 'I\'m ' + this.#age + ' years old.';
    }
}
let yorhom = new People(); // I'm 16 years old.

console.log(yorhom.age);
// undefined

console.log(yorhom.getAge());
// 16

// console.log(yorhom.printAge());
// TypeError: yorhom.printAge is not a function
```

为什么使用**#**符号？可以参考[JavaScript 新语法详解：Class 的私有属性与私有方法](https://www.cnblogs.com/fundebug/p/10754650.html)

很多人都有一个疑问，为什么 JS 不能学习其他语言，使用**private**来定义私有属性和私有方法？为什么要使用奇怪的**#**符号？

很多语言使用 private 来定义私用属性，代码要舒服很多，举例如下：

```javascript
class EnterpriseFoo {
    public bar;
    private baz;
    method() {
        this.bar;
        this.baz;
    }
}
```

对于这些语言属性，私用属性和公共属性的引用方式是相同的，因此他们可以使用 private 来定义私有属性。

但是，对于 JavaScript 来说，我们引用私有属性的时候，我们需要**this.#field**，而不是**this.field**，原因如下：

- 因为我们需要封装私有属性，我们需要允许公共属性与私有属性同名，因此私有属性与公共属性的引用方式必须不一样。这一点我们在前文已经详述。
- 公共属性可以通过**this.field**以及**this['field']**来引用，但是私有属性不能支持**this['field']**这种方式，否则会破坏私有属性的隐私性，示例如下：

```javascript
class Dict {
    #data = 'something_secret';
    add(key, value) {
        this[key] = value;
    }
    get(key) {
        return this[key];
    }
}

new Dict().get("#data"); // 返回私有属性 undefined
```

因此，私有属性与公共属性的引用方式必须不一样，否则会破坏**this['field']**语法。

- 私有属性与公共属性的引用方式一样的话，会导致我们每次都需要去检查属性是公共的还是私有的，这会造成严重的性能问题。

### 3.4 protected

protected可以修饰数据成员，构造方法，方法成员，不能修饰类（此处指外 部类，不考虑内部类）。被protected修饰的成员，能在定义它们的类中，和同包的类中被调用。如果有不同包的类想调用它们，那么这个类必须是定义它们的类 的子类。 

```
// 父类
const _bar = Symbol();
class Foo {
    constructor() {
    	// 是一个私有属性
        this[_bar] = 'hello';
    }
 
    test() {
    	console.log(this[_bar]);
    }
    
    get bar() {
    	return(this[_bar]);
    }
}

// 测试
let foo = new Foo();
console.log(foo._bar); // undefined
console.log(foo.bar); // hello
foo.test(); // hello

// 子类
class Bar extends Foo {
    test2() {
    	console.log(this[_bar]);
    }
}
 
// 测试
new Bar().test2(); // hello
```

此方法并非完美，借助`getOwnPropertySymbols`方法可以取出对象的`Symbol`键值。更多更好的写法推荐[ES6 Class中实现私有属性的几种方法](https://juejin.cn/post/6844903880174600205)



## 4.继承

### 4.1 extends关键字

Class 可以通过extends关键字实现继承

```
class Animal {};
class Cat extends Animal { };
```

上面代码中 定义了一个 Cat 类，该类通过 `extends`关键字，继承了 Animal 类中所有的属性和方法。 但是由于没有部署任何代码，所以这两个类完全一样，等于复制了一个Animal类。 下面，我们在Cat内部加上代码。

```
class Cat extends Animal {
    constructor(name, age, color) {
        // 调用父类的constructor(name, age)
        super(name, age);
        this.color = color;
    }
    toString() {
        return this.color + ' ' + super.toString(); // 调用父类的toString()
    }
}
```

constructor方法和toString方法之中，都出现了super关键字，它在这里表示父类的构造函数，用来新建父类的this对象。

子类必须在 constructor 方法中调用 super 方法，否则新建实例就会报错。 这是因为子类自己的this对象，必须先通过 父类的构造函数完成塑造，得到与父类同样的实例属性和方法，然后再对其进行加工，加上子类自己的实例属性和方法。如果不调用super方法，子类就得不到this对象。

```
class Animal { /* ... */ }

class Cat extends Animal {
    constructor() {
    
    }
}

let cp = new Cat();
// ReferenceError
```

Cat 继承了父类 Animal，但是它的构造函数没有调用super方法，导致新建实例报错。

如果子类没有定义constructor方法，这个方法会被默认添加，代码如下。也就是说，不管有没有显式定义，任何一个子类都有constructor方法。

```
class Cat extends Animal {

}
// 等同于

class Cat extends Animal {
    constructor(...args) {
        super(...args);
    }
}
```

另一个需要注意的地方是，es5 的构造函数在调用父构造函数前可以访问 this, 但 es6 的构造函数在调用父构造函数(即 super)前不能访问 this。

```
class A {
    constructor(x, y) {
        this.x = x;
        this.y = y;
    }
}

class B extends A {
    constructor(x, y, name) {
        this.name = name; // ReferenceError
        super(x, y);
        this.name = name; // 正确
    }
}
```

上面代码中，子类的constructor方法没有调用super之前，就使用this关键字，结果报错，而放在super方法之后就是正确的。

父类的静态方法，也会被子类继承。

```
class A {
    static hello() {
    	console.log('hello world');
    }
}

class B extends A {
	
}

B.hello(); // hello world
```

### 4.2 手写extends的实现

**Object.setPrototypeOf():**

该方法设置一个指定的对象的原型 ( 即, 内部[[Prototype]]属性）到另一个对象或  [`null`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/null)。

语法

```js
Object.setPrototypeOf(obj, proto);
```

参数

obj：要设置原型对象的对象。

proto：该对象的新原型对象或null，否则抛出TypeError异常。

返回值

设置了新的原型对象的对象。

**Object.getPrototypeOf():**

该方法用于获取指定对象的原型对象。

语法

```js
Object.getPrototypeOf(obj);
```

参数

obj：要获取原型对象的对象。

返回值

返回指定对象的原型对象或null。

```
function B(name) {
	this.name = name;
}

function A(name, age) {
	// 1.将A的原型指向B
	Object.setPrototypeOf(A, B);
	// 2.用A的实例作为this调用B,得到继承B之后的实例，这一步相当于调用super
	Object.getPrototypeOf(A).call(this, name);
	// 3.将A原有的属性添加到新实例上
	this.age = age;
	// 4.返回新实例对象
	return this;
}

let a = new A('poetry',22);
console.log(a);
/*
    age: 22
    name: "poetry"
*/
```



### 4.3 super关键字

super这个关键字，既可以当作函数使用，也可以当作对象使用

#### 4.3.1 super作为函数调用

super作为函数调用时，代表父类的构造函数。ES6 要求，子类的构造函数必须执行一次super函数。

```
class A {}

class B extends A {
    constructor() {
    	super();
    }
}
```

子类B的构造函数之中的super()，代表调用父类的构造函数。这是必须的，否则 JavaScript 引擎会报错。

注意，super虽然代表了父类A的构造函数，但是返回的是子类B的实例，即super内部的this指的是B的实例，因此super()在这里相当于A.prototype.constructor.call(this)。

```
class A {
    constructor() {
        // new.target 指向正在执行的函数
        console.log(new.target.name);
    }
}
class B extends A {
    constructor() {
    	super();
    }
}
new A(); // A
new B(); // B
```

在`super()`执行时，它指向的是子类`B`的构造函数，而不是父类A的构造函数。也就是说，`super()`内部的`this`指向的是`B`。

#### 4.3.2 super作为对象调用

**在普通方法中，指向父类的原型对象；** **在静态方法中，指向父类**。

##### super对象在普通函数中调用

```
class A {
    p() {
    	return 2;
    }
}

class B extends A {
    constructor() {
        super();
        console.log(super.p()); // 2
    }
}

let b = new B();
```

上面代码中，子类`B`当中的`super.p()`，就是将`super`当作一个对象使用。这时，`super`在普通方法之中，指向`A.prototype`，所以`super.p()`就相当于`A.prototype.p()`。

这里需要注意，由于super指向父类的原型对象，所以定义在父类实例上的方法或属性，是无法通过super调用的。

```
class A {
    constructor() {
    	this.p = 2;
    }
}

class B extends A {
    get m() {
    	return super.p;
    }
}

let b = new B();
b.m; // undefined
```

上面代码中，p是父类A实例的属性，super.p就引用不到它。

如果属性定义在父类的原型对象上，`super`就可以取到。

```
class A {}
A.prototype.x = 2;

class B extends A {
    constructor() {
        super();
        console.log(super.x); // 2
    }
}

let b = new B();
```

上面代码中，属性x是定义在`A.prototype`上面的，所以`super.x`可以取到它的值。

##### super对象在静态方法中调用

用在静态方法之中，这时super将指向父类，而不是父类的原型对象。

```
class Parent {
    static myMethod(msg) {
    	console.log('static', msg);
    }

    myMethod(msg) {
    	console.log('instance', msg);
    }
}

class Child extends Parent {
    static myMethod(msg) {
    	super.myMethod(msg);
    }

    myMethod(msg) {
    	super.myMethod(msg);
    }
}

Child.myMethod(1); // static 1

const child = new Child();
child.myMethod(2); // instance 2
```

上面代码中，super在静态方法之中指向父类，在普通方法之中指向父类的原型对象。

另外，在子类的静态方法中通过`super`调用父类的方法时，方法内部的`this`指向当前的子类，而不是子类的实例。

```
class A {
    constructor() {
    	this.x = 1;
    }
    static print() {
    	console.log(this.x);
    }
}

class B extends A {
    constructor() {
        super();
        this.x = 2;
    }
    static m() {
    	super.print();
    }
}

// B类的x为3
B.x = 3;
B.m(); // 3
```

上面代码中，静态方法`B.m`里面，`super.print`指向父类的静态方法。这个方法里面的`this`指向的是`B`，而不是`B`的实例。

## 总结

- class是一个语法糖，其底层还是通过 `构造函数` 去创建的。
- 类的所有方法都定义在类的prototype属性上面。
- 静态方法：在方法前加static，表示该方法不会被实例继承，而是直接通过类来调用。
- 静态属性：在属性前加static，指的是 Class 本身的属性，而不是定义在实例对象（this）上的属性。
- es5 的构造函数在调用父构造函数前可以访问 this, 但 es6 的构造函数在调用父构造函数(即 super)前不能访问 this。
- super
  - 作为函数调用，代表父类的构造函数
  - 作为对象调用，在普通方法中，指向父类的原型对象；在静态方法中，指向父类。

## 附加：再来几道题检查一下

### 1. 下面代码输出什么

```
class Person {
    constructor(name) {
    	this.name = name;
    }
}

const member = new Person("John");
console.log(typeof member);
```

答案：object

解析： 类是构造函数的语法糖，如果用构造函数的方式来重写Person类则将是：

```
function Person() {
	this.name = name;
}
```

通过new来调用构造函数，将会生成构造函数Person的实例，对实例执行typeof关键字将返回"object"，上述情况打印出"object"。

### 2. 下面代码输出什么

```
class Chameleon {
    static colorChange(newColor) {
        this.newColor = newColor;
        return this.newColor;
    }

    constructor({ newColor = 'green' } = {}) {
    	this.newColor = newColor;
    }
}

const freddie = new Chameleon({ newColor: 'purple' });
freddie.colorChange('orange');
```

答案：TypeError

解析： colorChange 是一个静态方法。静态方法被设计为只能被创建它们的构造器使用（也就是 Chameleon），并且不能传递给实例。因为 freddie 是一个实例，静态方法不能被实例使用，因此抛出了 TypeError 错误。

### 3.下面代码输出什么

```
class Person {
    constructor() {
    	this.name = "Lydia";
    }
}

Person = class AnotherPerson {
    constructor() {
    	this.name = "Sarah";
    }
}

const member = new Person();
console.log(member.name);
```

答案："Sarah"

解析： 我们可以将类设置为等于其他类/函数构造函数。 在这种情况下，我们将Person设置为AnotherPerson。 这个构造函数的名字是Sarah，所以新的Person实例member上的name属性是Sarah。


