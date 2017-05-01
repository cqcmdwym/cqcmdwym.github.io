## code 1
```javascript
'use strict';

var arr = ['red', 'blue', 'green'];

Object.defineProperty(arr, 'last',{
	get:function(){
		return this[this.length-1];
	}
})
var last = arr.last;

console.log(last);//green
```
这个代码的问题是再创建一个数组,就没有last的属性

```javascript
var arr2 = ['one', 'two', 'three'];
console.log(arr2.last);//undefined
```

## code 2(把last属性加在prototype上)
```javascript
'use strict';

var arr = ['red', 'blue', 'green'];

Object.defineProperty(Array.prototype, 'last',{
	get:function(){
		return this[this.length-1];
	}
})
var last = arr.last;
console.log(last);//green
var arr2 = ['one', 'two', 'three'];
console.log(arr2.last);//three
```

## what is prototype
```javascript
'use strict';

var myFunc = function(){}
console.log(myFunc.prototype);//{}

var cat = {name:'Fluffy'};
console.log(cat.prototype);//undefined
console.log(cat.__proto__);//{}Screen Shot 2017-05-01 at 2.27.20 PM.png
```
![Difference](/images/Screen Shot 2017-05-01 at 2.27.20 PM.png)

## Explaination
```javascript
'use strict'

function Cat(name, color){
	this.name = name;
	this.color = color;
}

var fluffy = new Cat('Fluffy','white');

console.log(Cat.prototype);//{}
console.log(fluffy.__proto__);//{}
console.log(Cat.prototype === fluffy.__proto__);//true
```
```javascript
'use strict'

function Cat(name, color){
	this.name = name;
	this.color = color;
}

var fluffy = new Cat('Fluffy','white');
Cat.prototype.age = 3;

console.log(Cat.prototype);//{age:3}
console.log(fluffy.__proto__);//{age:3}
console.log(Cat.prototype === fluffy.__proto__);//true

var muffin = new Cat('Muffin','Brown');
console.log(muffin.__proto__);//{age:3}}
console.log(Cat.prototype === muffin.__proto__);//true
console.log(fluffy.__proto__ === muffin.__proto__);//true
```
## Behind the scenes
```javascript
'use strict'

function Cat(name, color){
	this.name = name;
	this.color = color;
}

Cat.prototype.age = 3;
var fluffy = new Cat('Fluffy','white');
var muffin = new Cat('Muffin','Brown');

console.log(fluffy.age);//3
console.log(muffin.age);//3
```
```javascript
'use strict'

function Cat(name, color){
	this.name = name;
	this.color = color;
}

Cat.prototype.age = 3;
var fluffy = new Cat('Fluffy','white');
var muffin = new Cat('Muffin','Brown');
fluffy.age = 5;

console.log(fluffy.age);//5
console.log(muffin.age);//3
console.log(fluffy.__proto__.age);//3
```
```javascript
'use strict'

function Cat(name, color){
	this.name = name;
	this.color = color;
}

Cat.prototype.age = 3;
var fluffy = new Cat('Fluffy','white');

console.log(fluffy.age);//3
console.log(fluffy.__proto__.age);//3
console.log(Object.keys(fluffy));//['name', 'color']
console.log(fluffy.hasOwnProperty('age'));//false
console.log(fluffy.hasOwnProperty('color'));//true
```
```javascript
'use strict'

function Cat(name, color){
	this.name = name;
	this.color = color;
}

Cat.prototype.age = 3;
var fluffy = new Cat('Fluffy','white');
fluffy.age=4;

console.log(fluffy.age);//4
console.log(fluffy.__proto__.age);//3
console.log(Object.keys(fluffy));//['name', 'color', 'age']
console.log(fluffy.hasOwnProperty('age'));//true
```
![Graphical Overview](/images/Screen Shot 2017-05-01 at 3.01.36 PM.png)

## Change a Function's Prototype
```javascript
'use strict'

function Cat(name, color){
	this.name = name;
	this.color = color;
}

Cat.prototype.age = 4;
var fluffy = new Cat('Fluffy','white');
var muffin = new Cat('Muffin','Brown');

Cat.prototype = {age:5};

var snowbell = new Cat('SnowBell', 'white'); //create after changing prototype

console.log(fluffy.age);//4
console.log(muffin.age);//4
console.log(snowbell.age)//5;
console.log(Cat.prototype.age)//5
```
![Changing Prototypes](/images/Screen Shot 2017-05-01 at 3.11.44 PM.png)

## Multiple Levels of Inheritance
```javascript
'use strict'

function Cat(name, color){
	this.name = name;
	this.color = color;
}

Cat.prototype.age = 4;
var fluffy = new Cat('Fluffy','white');
console.log(fluffy.__proto__);//{age:4}
console.log(fluffy.__proto__.__proto__);//{}
console.log(fluffy.__proto__.__proto__.__proto__);//null
```

## Create Own Prototypal Inheritance Chains
```javascript
function Animal(){

}

Animal.prototype.speak = function(){
	console.log('Grunt');
}

function Cat(name, color){
	this.name = name;
	this.color = color;
}
Cat.prototype = Object.create(Animal.prototype);
var fluffy = new Cat('Fluffy','white');
fluffy.speak();//Grunt
```

```javascript
function Animal(voice){
	this.voice = voice || 'Grunt';
}

Animal.prototype.speak = function(){
	console.log(this.voice);
}

function Cat(name, color){
	Animal.call(this, 'Meow');
	this.name = name;
	this.color = color;
}
Cat.prototype = Object.create(Animal.prototype);
var fluffy = new Cat('Fluffy','white');
fluffy.speak();//Meow
console.log(fluffy);//{cat:{color:'white',name:'Fluffy',voice:'Meow'}, __proto__:Animal}
console.log(fluffy instanceof Cat);//true
console.log(fluffy instanceof Animal);//true
```

```javascript
function Animal(voice){
	this.voice = voice || 'Grunt';
}

Animal.prototype.speak = function(){
	console.log(this.voice);
}

function Cat(name, color){
	Animal.call(this, 'Meow');
	this.name = name;
	this.color = color;
}
Cat.prototype = Object.create(Animal.prototype);
Cat.prototype.constructor = Cat;

var fluffy = new Cat('Fluffy','white');
fluffy.speak();//Meow
console.log(fluffy);//{cat:{color:'white',name:'Fluffy',voice:'Meow'}, __proto__:Animal}
console.log(fluffy instanceof Cat);//true
console.log(fluffy instanceof Animal);//true
console.log(fluffy.__proto__);//animal{}
console.log(fluffy.__proto__.__proto__);//Object{}
```


## Create Prototypes wtth Classes
```javascript
'use strict'
class Animal{
	constructor(voice){
		this.voice = voice || 'Grunt';
	}
	speak(){
		console.log(this.voice);
	}
}

class Cat extends Animal{
	constructor(name, color){
		super('Meow');
		this.name = name;
		this.color = color;
	}
}


var fluffy = new Cat('Fluffy','white');
fluffy.speak();//Meow
console.log(fluffy);//{cat:{color:'white',name:'Fluffy',voice:'Meow'}, __proto__:Animal}
console.log(fluffy.constructor);
// class Cat extends Animal{
// 	constructor(name, color){
// 		super('Meow');
// 		this.name = name;
// 		this.color = color;
// 	}
// }
console.log(fluffy.__proto__);//Animal
console.log(fluffy.__proto__.__proto__);
//Object
// 	-constructor:(voice)
// 	-speak:()
// 	-__proto__:Object
console.log(Object.keys(fluffy.__proto__.__proto__));//[]
console.log(fluffy.__proto__.__proto__.hasOwnProperty('speak'));//true
```