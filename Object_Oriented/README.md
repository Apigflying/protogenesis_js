# 面向对象

标签（空格分隔）： 面向对象

[TOC]

---





---
##tips
1.对面向对象的理解：使用面向对象的构造函数，创建一类对象，这个类就是用来扩展Object的
### 面向对象的原则：
>在修改函数的this指向的时候，要让他们都指向构造函数
```javascript

Objcet
    |---Array（数组类）每个数组的内容不同，但是都共用数组的方法
    |---Fn 创建一个Fn类，这些对象都有自己的不同属性，或者内容
            但都共用Fn的方法
            多个Fn类的实例之间没有影响，虽然共享方法，但是互不影响
            要注意：虽然Fn创建了一个类，但是它是一个函数，无论用ES5还是ES6的方法，它都是函数
            Object.prototype.toString.call(Fn)->Object Function
            而用它创造的实例，-->Object Object是对象
            

```
## 1. 什么是面向对象
1 . 面向对象就是将全局的变量，变成构造函数构造的对象的属性
将全局函数，变成构造对象的方法

```javascript
/*用面向对象的第一步：
声明一个构造函数*/
function Fn(){
    /*系统内部假想的：*/
   // var this=new Object();
   this.box=document.getElementById("box");
   //将DOM元素挂载到对象下，成为this的属性
            .
            .
        为this挂载属性
            .
            .
    /*系统内部假想的：*/
  //  return this;
}
/*给构造函数创建的实例添加方法*/
Fn.prototype.init=function(){}

var x=new Fn();
/*创建新实例，这个实例就是对象{}，有box属性，有init方法的实例*/

他们都是通过构造函数创建的实例，所以这些实例都是这个构造函数的类
    如：一个数组，它就是Array的类
    Fn就是一个自定义的数据类型
    上面的x就是Fn下面的类
    
    x本身是没有方法的，它通过Fn函数被创建出来，就能够利用Fn下面的方法
    [1,2,3]本身是没有方法的，它通过Array被构造出来，那么它就能够利用Array的方法
    
    用原型就是将这些方法挂载到Fn这个类的下面
    如同：Array的push方法
    用new Array创建的数组都能利用push方法
    同理，用new Fn创建的对象也都能够利用init方法
   
```
![原型图][1]

## 2.new和构造函数的返回值
2 . new是一元运算符，当通过new创建新实例的时候，如果构造函数内没有返回值，那么就是正常的，如果构造函数自定义了一个返回值

>* -[ ] 如果这个返回值是非对象的数据类型，那么构造函数正常运行
>* -[x] 如果构造函数的返回值手动添加了一个对象，那么就返回这个对象
```javascript
    function Fn(){
        return 1;
    }
    var x=new Fn();
    typeof x → Object
  ------------------------
   function Fn(){
        return [123];
    }
    var x=new Fn();
    x  →  [123]
```
## 3.prototype原型
1. 什么是原型？
每创建一个函数，函数的下面都有一个属性叫做prototype（原型），它的值是一个对象Object
2.原型的作用
通过构造函数创建的所有对象，都能够利用这些方法，类似于数组都能利用Array下面的方法

## 4._proto_原型链
1. 什么是原型链？
所有对象（和上面不同，对象都有原型链，函数才有原型，是不同的东西，但是函数也属于Object，所以函数也有原型链，但是对象是没有原型prototype的）都有原型链
2. _proto_原型链的作用
当我们调用一个对象的属性或者方法的时候
>* 1.首先会在该对象自身上找，看这个对象身上是否有这个自定义属性或者方法，如果有，直接调用
>* 2.如果自身没有，会通过\_proto\_去   构造这个对象的**构造函数**身上去查找
>* 3.如果构造函数身上也没有，那么会去构造函数的\_proto_向上找，直到Object，没找到返回undefined，Object的\_proto\_是null
    
## 5.给构造函数添加原型的方式：
1 . 不建议：
```javascript
逐个添加
function Fn(){};
Fn.prototype.init(){console.log(this)};
Fn.prototype.fnDown(e){};
var n1=new Fn();
n1.init(); →  Fn  {}
n1.__proto__ →  Object{
            init:function(){}
            fnDown:function(){}
            constructor:function Fn()
            __proto__:Objcet
            }
 function Fn()这个函数的prototype是Objcet
                       __proto__是function()这层里显示函数的方法
                       这个function()的__proto__是Objcet
                        也就是最后一层了
            
```
2 . 建议：
```javascript
function Fn(){};

Fn.prototype={
同一添加挂载到对象下
/*由于Fn的原型指向变成了这个对象，所以要将它的指向改变回来*/
    constructor:Fn,
    init:function(){console.log(this)},
    fnDown:function(e){}
}
var n1=new Fn();
n1.init(); →  Fn  {}
n1.__proto__ →  Object{
            init:function(){}
            fnDown:function(){}
            constructor:function Fn()
            __proto__:Objcet
            }
 function Fn()这个函数的prototype是Objcet
    //这里就没有这个了：__proto__是function()这层里显示函数的方法
                       上面的Objcet的__proto__是Objcet
                        也就是最后一层了
```

## 6.包装对象：

> *  string和number以及boolean
>1. 什么是包装对象
当使用原始类型的值的时候（上面三种），在调用对应的属性和方法，内部会自动转成对应的对象，隐式创建的这个对象，就是包装对象
>2. 包装对象的特点：
通过string和number以及boolean直接通过字面量的方式创建基本数据的时候，调用他们的方法，是通过new String、new Number、new Boolean的原型调用他们的方法
```javascript
var str='abc';
str.chatAt(1);
/*str本身是没有这些方法的，当str调用charAt这个方法的时候，是通过String的原型的方法获取到这个charAt方法的*/
特点是：当调用完String后，会立即销毁掉，所以没办法给str添加属性如：
str.a=10;
这里是访问不到str.a的
```

## 7. 实例和原型的方法
1. 实例的方法：hasOwnProperty('');
判断属性是否是构造函数身上的，如果是返回true；
如果不是，那么返回false
特点：
找一层之后，如果不是，就返回false；不会通过原型链向上查找了

2. 原型的方法：constructor
实例.constructor
函数的原型prototype的值是一个对象，初始化会有一个属性为constructor，对应的值是拥有这个原型的构造函数
注：由于prototype的值是可以被修改,
详见：5.给构造函数添加原型的方式
中
>1. 将函数的方法挂载到构造函数的prototype下：Fn.prototype.init=function(){}此时  实例.constructor->Fn
constructor指向没有问题
所以不要拿来做判断
>2. 将函数的方法挂载到一个对象下，
Fn.prototype={init:function(){}}
实例.constructor->Objcet
要将这个值手动改回来，让它指向Fn
即：Fn.prototype={constructor：Fn，init:function(){}}

>- [x] 函数才有prototype
而函数的prototype有两个成员属性：
\__proto__ 和 constructor
constructor属性不是构造函数在创建实例时添加给实例的
例如：

```javascript
function C3(a, b){
    this.p = a + b;
    this.alertP = function(){
        alert(this.p);
    }
}
//我们定义一个函数来覆盖C3原型中的constructor，试图改变属性p的值
function fake(){
    this.p = 100;
}
C3.prototype.constructor = fake; //覆盖C3原型中的constructor
var c3 = new C3(2,3);
c3.alertP();//结果仍然为5

这里虽然手动修改了函数的constructor指向，但是并没有对实例的执行结果造成影响

```
在有类继承的时候，用constructor来获取实例的构造函数，就会出错
如：
```javascript
function f() { this.foo = 1;}
function s() { this.bar = 2; }
s.prototype = new f(); // s继承自f
 
var son = new s(); // 用构造函数s创建一个子类对象
(son.constructor == s); // false
(son.constructor == f); // true

此时son这个实例虽然是被s构造函数创建出来的，但是它的constructor已经指向了f而不是原本的s

```

3. 用for..in..遍历对象的特点：

即使是添加到Objcet原型身上的，也能遍历到，但是无法遍历到系统自带的属性或者方法

4. instanceof判断一个实例是否属于某种类型
instanceof检测一个对象A是不是另一个对象B的实例的原理：
查看对象B的prototype指向的对象是否在对象A的__proto__链上，如果在，返回true，如果不在返回false。但是有一个特殊的情况，当对象B的prototype为null时，将会报错，类似于空指针异常

## 8. 继承
1. 什么是继承？
让子类共用父类（基类）的方法，并且给子类添加方法的时候，不影响父类的原型
2. 为什么继承？
当其他原型共用同一个原型的方法的时候，就需要将他们共用的那个原型，变成一个父类，这样他们就能够通过继承的方式，获得这个父类的原型的属性和方法
```javascript
//这个是父类
    function Person( name,age ){
    	this.name = name;
    	this.age = age;
    }
    //父类的原型方法
    /*扩展父类的原型，就能够实现方法的共享*/
	Person.prototype.say = function (){
		console.log("hello,我的名字是"+this.name);	
	}
	//子类
	function Coder(name,age,money){
	    /*在这里，this指向实例，也是构造的对象*/
		//Person中的this就指向了Coder创建的实例
		//在Person中是通过this添加属性的
		//实际上Person中通过this添加上的属性就是给Coder创建的实例添加的属性
		Person.apply(this,[...arguments]);
        /*用这种方式只能获取父类的（*属性*），不能获得父类的（*方法*）
        */
		this.money = money;
	}
	Coder.prototype.coding = function (){
		console.log( "我会编程" );	
	}
	var c1 = new Coder("leo",30,1000);
	console.log( c1.name )->leo
	console.log( c1.say )->undefined
```
>* 注：上面的继承，只能获得父类的属性，要想得到父类的方法，要将子类的原型指向父类的原型
即：
```javascript
    function Person( name,age ){
    	this.name = name;
    	this.age = age;
    }
	Person.prototype.say = function (){
		console.log("hello,我的名字是"+this.name);	
	}
	//子类
	function Coder(name,age,money){
		Person.call(this,name,age);
		this.money = money;
	}
	Coder.prototype.coding = function (){
		console.log( "我会编程" );	
	}
	Coder.prototype=Person.prototype
	/*让子类的原型指向父类的原型，从而获取父类的方法*/
	
	var c1 = new Coder("leo",30,1000);
	
	console.log( c1.name )->leo
	console.log( c1.say )->function (){console.log("hello,我的名字是"+this.name）;}
	
    此时，子类就能够使用父类的方法了，但是，子类Code实例的指向也被修改了，需要手动修改回来，即：
    Coder.prototype.constructor=Coder
    
    此时父类的原型上也有了coding（子类的方法）
   console.log(Person.prototype) =》》
   Objcet{
        coding:function ()
        run:function ()
        say:function ()
    }
    
```
>* -[ ] 但是上面的方式还有问题，就是由于子类的原型指向了父类的原型，所以，在给子类原型添加方法的时候，父类原型本身也会被动的添加上子类的方法
>* 如：
    var obj1={a:1}(父类)
    var obj2=obj1;(子类)
    obj2.b=100
    obj1->Object {a: 1, b: 100}
由于obj2=obj1保存的是指向，所以会修改obj1的内容

```javascript
接上面的代码：
function Doctor(name,age,color){
	Person.call(this,name,age);
	this.color = color;	
}
    Doctor.prototype=Person.prototype;
	var d = new Doctor("momo",18,"pink");
	可以看出。在这里Doctor的实例也能够获得Code的方法
    console.log( d.prototype );->function (){console.log( "我会编程" );}

```
>* -[x] 所以，需要复制父类的原型，然后再将子类的指针指向被复制的这个对象，这样再给子类添加方法的时候，就不会影响到父类
解决上面的问题有两种方法：
```javascript
第一种：复制一个对象，将父类的属性和方法复制给这个对象
/*使用递归进行深拷贝*/
function clone(obj){
	//obj是数组 生成新数组 obj是对象 生成新对象
	if( typeof obj === "object" ){
		var newObj = (obj instanceof Array) ? [] : {};
		for( var attr in obj ){
			if( obj.hasOwnProperty(attr) ){
				//obj[attr]对应的值是简单类型 直接赋值
				//对应的值是对象：[]或{}，把这对象也复制一份,再给到newObj[attr]
				if( typeof obj[attr] === "object" ){
					//使用递归
					newObj[attr] = clone(obj[attr]);
				}else{
					newObj[attr] = obj[attr];
				}
			}
		}
		return newObj;
	}else{
		return obj;
	}
}

```
```javascript
第二种：将父类的prototype通过另一个函数的实例{}，嫁接到子类上，这样，子类就能够利用父类的方法和属性，而且不会对父类造成影响
function inherit(child,parent){
    /*参数是子类和父类*/
    function O(){};
    /*由于只有函数才有prototype，所以，可以将父类的prototype赋值给o这个媒介函数*/
    O.prototype=parent.prototype;
    /*让O这个函数创造实例，也就是新对象，那么这个新对象也就拥有了父类的所有属性和方法，并且new O新创建的实例对象保存的不是父类的指针，而是父类的属性和方法*/
    child.prototype=new O;//将子类的prototype指向这个新的对象
    child.prototype.constructor=child;//修改原型指向
    return  child.prototype;//返回这个对象，后续可以给它添加方法
}
        这种复制的方法虽然能够复制一个对象，但是书写顺序要注意，要等到父类挂载完对象后，才可以访问到父类的方法
```
##8 创建对象的进一步封装
```javascript
function Student(props) {
    this.name = props.name || '匿名'; // 默认值为'匿名'
    this.grade = props.grade || 1; // 默认值为1
}

Student.prototype.hello = function () {
    alert('Hello, ' + this.name + '!');
};

function createStudent(props) {
    return new Student(props || {})
}
/*这个createStudent()函数有几个巨大的优点：一是不需要new来调用，二是参数非常灵活，可以不传，也可以这么传：*/
var xiaoming = createStudent({
    name: '小明'
});

xiaoming.grade; // 1
如果创建的对象有很多属性，我们只需要传递需要的某些属性，剩下的属性可以用默认值。由于参数是一个Object，我们无需记忆参数的顺序。如果恰好从JSON拿到了一个对象，就可以直接创建出xiaoming。
```
```javascript
对上面的函数再进一步封装
function Student(option) {
    this.init(option);//用来初始化
}

Student.prototype={
    contructor:Student,
    init:function(option){//将属性挂载到option下
        this.name=option.name||'匿名',
        this.age=option.age||'20'
    },
    say:function(globalVariable){
        //这个函数接收全局变量，可以在外部被实例调用
        alert(globalVariable+'的名字'+this.name);
    }
}
function createStudent(option) {
    return new Student(option || {})
}
/*这个createStudent()函数有几个巨大的优点：一是不需要new来调用，二是参数非常灵活，可以不传，也可以这么传：*/
var xiaoming = createStudent({
    name: '小明',
    age:'22'
});

```
##9. 用class声明类，用类代替函数原型
1 . 写法的不同
> class是ES6的写法
> 与原型的区别：
```javascript
1. 用prototype的写法：
function Fn(){};
Fn.prototype={
    constructor:Fn,
    init:function(){},
    say:function(){}
}
var x=new Fn;

2. 用class的写法：
class Fn{
    constructor(name,age){
        //当使用new来创建对象，会自动调用这个函数
        /*在这里的name和age就是传入的形参*/
    }
    say(){
        Fn的say方法
    }
    run(){
        Fn的run方法
    }
}
var y=new Fn('name','age');
```

2 . 继承方式的不同
```javascript
1. 原型的继承
通过复制父类的原型的方法和属性，让子类的prototype指向被复制的这个拥有父类原型属性和方法的实例，从而让子类拥有父类的属性和方法
2. 用class方法在继承的时候
    class Fn(){
        constructor:(){
            
        }
    };
    class Fn1 extends Fn{
/*通过extends让Fn1来继承Fn的属性和方法*/
        consturctor(){
            super();
            /*需要用super()这个方法初始化一下this，否则在这里是找不到this的。相当于沿用了父类的this，继续给这个this添加属性或者方法*/
            //如果不用super会报错
        }
    }
```
3 . 给函数添加静态方法
```javascript
    /*函数的静态方法*/
    function abc(){};
    abc.x=function(){};
    /*在class内添加静态方法*/
    class abc(){
        static x(){}
    }
    abc.x →  fucntion x(){}
```

4 . 组件方式编写程序
什么是组件？
>组件包括三部分：
1. html结构
2. 行为逻辑
3. 接收数据或执行模板

由于接收的数据对象要放到模板当中，所以，在使用模板的时候，要复制一份这个模板，以防在函数内改变模板的内容
```javascript
this.newOptions = {
		title: '这是一个弹框',
		content: '这是默认的内容',
		okValue: '确定',
		cancelValue: '取消'
					}
	// 通过assign方法合并对象
	//前面的是默认的对象，后面的是传入的对象
	assign方法类似于数组的concat方法，不同的是concat会将两个数组的每一项都合并
	而assign在调用的时候，如果两个对象的attr有冲突，即attr的名字相同，会用后面对象的attr覆盖前面的attr
	Object.assign(this.newOptions,options);
```

##10.面向对象中的继承，新理解2018-3-11
js根据继承的内容的不同，有多种继承方式：

####1.子类继承父类构造函数内定义的属性：
```javascript
//父类
function P(){
    this.a=123;
}
P.prototype.c = 345;
//子类
function C(){
    P.call(this);
    this.b=234;
}
var c = new C();
/*
    c:
    {
        a:123,
        b:234
    }
    注：这里的c只拥有父类P构造函数内定义的属性，而没有prototype中的c的属性
*/
```
####2.子类继承父类原型上的属性
```javascript
C.prototype = Object.create(P.prototype);
C.prototype.constructor = C;
C.prototype.method = '...';
```
**注**：Object.create的作用
```javascript
/*
    Object.create(null);
    参数必传，否则会报错
    使用Object.create总会返回一个空对象，这个对象没有任何属性，但是这个对象的原型，就是传入的参数
*/
var obj = Object.create({
    a:123;
})
/*
    obj:
    {}
        __proto__:{
            a:123;
        }
*/
obj.a //123
```
如果没有Object.create
可以用：
```javascript
if(typeof Object.create!=='function'){
    Object.create = function (obj){
        function F(){};
        F.prototype = obj;
        return new F();
    }
}
```
####3.继承父类的属性和方法
```javascript
//父类
function Parent(){
    this.name="person";
};
Parent.prototype.pName = '这是Parent'
Parent.prototype.showName = function(){
    console.log(this.name);
    return this.name;
};
//子类
function Child(){
    Parent.call(this,arguments);//继承父类构造函数的属性
    this.age = 16;
};
Child.prototype.cName = '这是child';
Child.prototype.showAge = function(){
    console.log(this.age);
    return this.age;
};

inherit(Child,Parent);

//子类继承父类原型的属性和方法
function inherit(child,parent){
    function Middle(){};
    Middle.prototype = Object.assign(parent.prototype,child.prototype);
    child.prototype = new Middle();
    child.prototype.constructor = child;
    return child.prototype;
}

var childs = new Child();
/*
    childs
    {
        age:16,
        name:'preson',
        __proto__:{
            pName:'这是Parent',
            cName:'这是child',
            showName(){},
            showAge(){}
        }
    }
        
*/

```
>子类在继承父类的时候，如果不合并子类的原型（即：不用Object.assign合并），那么inherit调用的地方要在给child原型添加属性之前
否则，子类在继承父类之前添加的属性会被inherit覆盖掉




  [1]: http://osjykr1v3.bkt.clouddn.com/FjH0-4AFIe3VA3aaJQCiEKQsoIcw