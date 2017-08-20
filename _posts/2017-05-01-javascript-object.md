---
layout: default
title: javascript-object
categories: [tech]
tags: [javascript]
---

## case 1
```javascript
'use strict'

function Cat(){
	this.name = 'Fluffy';
	this.color = 'white';
}

var cat = new Cat();
console.log(cat);
```

## case 2
```javascript
function Cat(){
	this.name = 'Fluffy';
	this.color = 'white';
}

var cat = Cat();
console.log(cat);//undefined, 因为Cat未return回去，但是this指向的是global（window）
console.log(window.color);
```