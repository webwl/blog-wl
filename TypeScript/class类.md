- [JS中的类](#js中的类)
- [实例与静态](#实例与静态)
  - [方法](#方法)
  - [属性](#属性)
    - [实例属性](#实例属性)
    - [静态属性](#静态属性)
- [修饰符](#修饰符)
  - [public](#public)
  - [private](#private)
  - [protected](#protected)
- [super](#super)


# JS中的类
`ES6`中的类和传统面向对象中的类看起来很像，但是实际上，还是有很多的差别的，因为`ES6`中提供的`class`只是一个语法糖，都是可以还原为`ES5`的内容的。

做个对比
```
// ES5
function Point(x, y) {
  this.x = x;
  this.y = y;
}

Point.prototype.toString = function () {
  return '(' + this.x + ', ' + this.y + ')';
};

// ES6
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }
}
```
ES6 的类，完全可以看作构造函数的另一种写法。

# 实例与静态
## 方法
> 实例方法：可以被继承 ,并且可以被实例调用的方法

> 静态方法：可以被继承，不能被实例调用，只能被类调用的方法

在`class`中的方法，相当于是挂载在原型上的，所以是可以被继承的。

如何使方法变为静态方法，使实例无法调用？通过`static`关键字
```
class Foo {
  static classMethod() {
    return 'hello';
  }
}

Foo.classMethod() // 'hello'

var foo = new Foo();
foo.classMethod()
// TypeError: foo.classMethod is not a function
```
==注意：静态方法可以与实例方法重名==

## 属性
> 实例属性：可以继承，实例可使用

> 静态属性：可以继承，实例无法使用

### 实例属性
实例属性的两种写法

1：定义在constructor内
```
class IncreasingCounter {
  constructor() {
    this._count = 0;
  }
}
```
2：定义在类的最顶层
```
class IncreasingCounter {
  _count = 0;
}
```
### 静态属性
静态属性的两种写法

1: 在类外部去定义
```
class Foo {}
Foo.prop = 1;
```
2：在类内部顶层通过static去定义
```
class Foo {
  static prop = 1;
}
```
第二种还是提案，TS实现了可以使用，js应该还不行

# 修饰符
## public
修饰的属性或方法是公有的，可以在任何地方被访问到，默认所有的属性和方法都是。并且是可以存取的。
```
class Animal {
  public name;
  public constructor(name) {
    this.name = name;
  }
}

let a = new Animal('Jack');
console.log(a.name); // Jack
a.name = 'Tom';
console.log(a.name); // Tom
```
## private
修饰的属性或方法是私有的，不能在声明它的类的外部访问。无法在类实例化后进行读取与操作。
```
class Animal {
  private name;
  public constructor(name) {
    this.name = name;
  }
}

let a = new Animal('Jack');
console.log(a.name);
a.name = 'Tom';

// index.ts(9,13): error TS2341: Property 'name' is private and only accessible within class 'Animal'.
// index.ts(10,1): error TS2341: Property 'name' is private and only accessible within class 'Animal'
```
## protected
修饰的属性或方法是受保护的，无法在类实例化后进行读取与操作，区别是它在子类中是允许被访问的
```
class Animal {
  public name;
  protected constructor(name) {
    this.name = name;
  }
}
class Cat extends Animal {
  constructor(name) {
    super(name);
  }
}

let a = new Animal('Jack');
// index.ts(13,9): TS2674: Constructor of class 'Animal' is protected and only accessible within the class declaration.
```
==注意：需要注意一点的是，当构造函数修饰为 protected 时，该类只允许被继承，不能实例化==

# super
一直很纠结，super究竟是用来干嘛的，总结一下就是，纠结这里的，一定是原型链基础不牢靠的。看文档的工程中，脑海里也不能自动理顺关系。

看个E6入门的示例
```
class ColorPoint extends Point {
  constructor(x, y, color) {
    super(x, y); // 调用父类的constructor(x, y)
    this.color = color;
  }

  toString() {
    return this.color + ' ' + super.toString(); // 调用父类的toString()
  }
}
```
之前纠结的点，为什么要在constructor中调用super,super()中的参数为什么和constructor()的参数不一样，什么时候应该多一点什么时候应该少一点？（别笑）

先解释一下这一点。
```
class animal {
 constructor(name, age) {
    this.name = name;
    this.name = age;
  }
}

class cat extends animal {
  constructor(name,age,color) {
    super(name,age);
    this.color = color;
  }
}
```
看上面应该就很清楚了，调用super是为了调用父类的构造函数，在 constructor 中必须调用 super 方法，因为子类没有自己的 this 对象，而是继承父类的 this 对象，然后对其进行加工。

白话版本
super()的调用，相当于把父类的构造函数拼到自己身上来，然后自己再去添加自己特有的属性
```
class cat extends animal {
  constructor(name,age,color) {
    super(name,age);
    this.color = color;
  }
}

// 大概就可以理解成这样了
class cat extends animal {
  constructor(name,age,color) {
    // super(name,age);
    this.name = name;
    this.age = age;
    this.color = color;
  }
}

```
这里this指向，是指向子类的实例。`animal.prototype.constructor.call(this)`