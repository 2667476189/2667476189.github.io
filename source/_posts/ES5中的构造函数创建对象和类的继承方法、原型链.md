---
title: ES5中的构造函数创建对象和类的继承方法、原型链
date: 2020-09-10 10:02:52
tags:
  - JavaScript
---
js中不使用ES6的class来写类，怎样实现继承，new的原理
<!--more-->

## 创建对象的方法

### 1.构造模式

也就是写一个方法，然后内部设定各种this.xxx=arg来进行对象的初始化，类似于Java中的构造函数。使用时new即可

```js
function Person(age,name){
  this.age = age
  this.name = name
  this.sayHi = function(){
    console.log('Hi')
  }
}
let p = new Person(11,'David')
```

缺点：每个函数都单独开了个空间，造成浪费

### 2.原型模式

写一个空方法，在这个空方法的prototype上设置各种变量，new出来的对象共享原型链上的数据，如果要拥有自己的个性化数据直接设置同名成员变量覆盖原型链上的成员，类似Java类中的static修饰的类变量，所有实例共用

```js
function Person () {
}
Person.prototype = {
  age: 11,
  name: 'David',
  sayHi () {
    console.log('Hi')
  },
  constructor: Person
}
let p1 = new Person()//全都使用默认值
let p2 = new Person()
p2.age = 12//直接设定对象上的属性屏蔽原型上的值
```

缺点：全都是共享变量，如果对于类成员是复杂变量的，要进行覆盖不符合一般逻辑

### 3.原型和构造两个模式组合使用

上面两种组合使用，对于一般而言需要共享的，就放在原型链上；对于不需要共享的，就放在方法中

```js
function Person (age,name) {
  this.age = age
  this.name = name
}
Person.prototype = {
  sayHi () {
    console.log('Hi,My name is',this.name)
  },
  constructor: Person
}
let p = new Person(11,'David')
```

缺点：Person.prototype不是在function函数体内部设置的，缺少一定的封装性，使人感到困惑

### 4.动态原型模式

第三种方法的代码阅读优化版，判断一下是否设定原型，第一次构造时设定一次

```js
function Person (age, name) {
  this.age = age
  this.name = name
  if (typeof this.sayHi != 'function') {//仅在第一次调用构造函数时运行一次此代码
    Person.prototype.sayHi = function () {
      console.log('Hi,My name is', this.name)
    }
  }
}
let p = new Person(11, 'David')
```

前四种属于同一类构造方式，属于ES5的最基础构造的构造方式，直接使用第四种集大成的方法即可

### 5.寄生构造函数模式

将创建对象的代码封装进一个函数中，本质上还是工厂模式创建对象，设置后返回，由于根本不涉及this，这个new运算符实际上没有效果加和不加都一样

```js
function Person (age, name) {
  const o = new Object()
  o.age = age
  o.name = name
  o.sayHi = function () {
    console.log('Hi,My name is', this.name)
  }
  return o
}
let p = new Person(11, 'David')
p.sayHi()
```

缺点：不是用this创建的，无法建立函数Person和实例p之间的联系Object.getPrototypeOf(p)===Person.prototype，也就无法利用instanceof来运算实例的类型，一般不推荐使用。（另外其中的成员函数也无法进行共享复用，同样降低了效率）

### 6.稳妥构造函数模式

整个函数内不使用this,外部也不访问内部变量，所有针对内部变量的操作全部通过函数接口来进行，对外形成一个类似类的形式，创建时不需要new运算符

```js
function Person (age, name) {
  const o = new Object()
  let _age = age//不使用this，仅仅利用闭包保存这个变量
  let _name = name
  o.sayHi = function () {
    console.log('Hi,My name is', _name)
  }
  o.setName = function (name) {
    _name = name
  }
  return o
}
let p = Person(11, 'David')
p.setName('Davd')
p.sayHi()
```

缺点：和5一样同样无法使用instaceof，是一种比较特殊的构造对象的方式

## 继承对象方法

### 1.原型链

复杂的原型链模式，其精华在于Son.prototype = new Father()，将Son的原型替换为了一个father的实例。根据instanceof的运算规则，可以判定Son的实例为Son/Father/Object

```js
function Father (age, name) {
  this.age = age
  this.name = name
  if (typeof this.sayHi != 'function') {
    this.prototype.sayHi = function () {
      console.log('Hi,My name is', this.name)
    }
  }
}
function Son (age, name, studentId) {
  this.age = age
  this.name = name
  this.studentId = studentId
}
Son.prototype = new Father()//继承
Son.prototype.sayHi = function () {
  console.log('Hi,i\'m a student,my name is', this.name)
}//重写方法,如果不重写就会按照父类的方法来运行，可以注释后进行测试
Son.prototype.sit = function(){
  console.log('i will sit down')
}//子类新方法
let s = new Son(11,'David',2014556)
s.sayHi()
s.sit()
```

缺点：虽然在实现继承时Son.prototype = new Father()此时可以像Father内传入一次变量设定父类的成员，但是无法像其他语言那样，在new Son()时设置父类的成员。另外所有的子类共用一个父类的实例，产生共享问题，因此原型链并不常用。

### 2.借用构造函数

利用call换绑this，将父类的成员全部放到自己的this上，可以达成修改父类的成员的方法，利用这种方法继承，父类必须是利用构造方法建立的类

```js
function Father (age, name) {
  this.age = age
  this.name = name
  this.sayHi = function () {
    console.log('Hi,My name is', this.name)
  }
}
function Son (age, name, studentId) {
  Father.call(this, age, name)//把自己的this利用call传过去借助其父类进行构造
  this.studentId = studentId//设定自己的特有变量
  //重写或者新增,因为this.sayHi必定能从原型链上回溯得到，因此无法利用typeof进行共享复用
  //如果就算不重写sayHi，每一个Son的实例，都也会被设置一个sayHi，因此也无法复用
  //但是可以在类外设置this.prototype.sayHi
  this.sayHi = function () {
    console.log('Hi,i\'m a student,my name is', this.name)
  }
}
let s = new Son(11, 'David', 2014556)
s.sayHi()
```

缺点：继承方法没有复用，这种借用导致无法使用instanceof

### 3.组合继承

既使用借用构造也使用原型链，变量由借用构造从父类中取得，方法复用父类的方法

```js
function Father (age, name) {
  this.age = age
  this.name = name
}
Father.prototype.sayHi = function () {
  console.log('Hi,My name is', this.name)
}
function Son (age, name, studentId) {
  Father.call(this, age, name)//继承变量
  this.studentId = studentId//子类和父类区别的变量
}
Son.prototype = new Father()
Son.prototype.constructor = Son//重建子类的原型
Son.prototype.sayHi = function () {//覆盖方法
  console.log('Hi,i\'m a student,my name is', this.name)
}
let s = new Son(11, 'David', 2014556)
s.sayHi()
```

这种方法是最常用的,23这两种方法由于都有借用构造，因此最好父类是使用创建对象方法的第3种组合方法创建的，避免重复调用代码

### 4.原型式继承

这种方法已经被写入ES规范之中，使用Object.create(o)调用，本质上就是根据已有的对象创建一个新的对象，根据同一个实例派生出来的各个对象共享父类的值（尤其是复杂类型），可以在创建时给Object.create()传入第二个参数进行同名属性的覆盖和新属性的设定

```js
let father = {  name: 'David',  age: 11,  sayHi () {    console.log('Hi,my name is', name);  }}let son = Object.create(father, {  name: {    value: 'Davd'  },  sayHi: {//因为派生类没有构造函数，无法复用函数    value: function () {      console.log('Hi,i\'m a student,my name is', this.name)    }  },  studentId: {    value: 2014066  }})son.sayHi()Object原理代码:一个方法，方法内创建一个空函数，用o作这个方法原型，然后new出这个新方法后返回，本质上是一次浅复制

function object(o){
function F(){}
F.prototype = o;
return new F();
}
```

如果原型对象也是new出来的，可以使用instanceof运算符

### 5.寄生式继承

这种继承方法仅仅是对原型式继承的一种过程的封装，和构造函数一样没法复用函数

```js
function F(name,age){
  this.name = name
  this.age = age
  this.sayHi = function(){
    console.log('Hi,my name is', name)
  }
}
let father = new F('David',11)
function Son(father){
  let clone = Object.create(father)
  clone.sayHi = function(){
    console.log('Hi,i\'m a student,my name is', this.name)
  }
  return clone
}
let son = new Son(father)
son.sayHi()
console.log(son instanceof F)
```

### 6.寄生组合式继承

ES5最好的继承方式，能够使用instanceof，没有重复创建原型上的方法，子类的实例能够使用重写或者新增成员

```js
function Father (name, age) {
  this.name = name
  this.age = age
}
Father.prototype.sayHi = function () {
  console.log('Hi,my name is', this.name)
}
function object (o) {
  function F () { }
  F.prototype = o;
  return new F();
}
function Son (name, age, studentId) {//不是直接用Son函数
  Father.call(this, name, age)
  this.studentId = studentId
}
function inheart (Son, Father) {
  let prototype = object(Father.prototype)//创建原型
  prototype.constructor = Son//修复
  Son.prototype = prototype
}
inheart(Son,Father)//运行继承代码，只运行一次
// Son.prototype.sayHi = function () {
//   console.log('Hi,i\'m a student,my name is', this.name)
// }//如果不重写，就是用父类的
let son = new Son('David',11,2014866)
son.sayHi()
console.log(son instanceof Father)
```
