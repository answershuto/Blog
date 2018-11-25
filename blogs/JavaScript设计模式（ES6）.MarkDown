# JavsScript design pattern (ES6)

[●工厂模式](#factory)

[●单例模式](#singleInstance)

[●观察者模式](#observer)

---

<span id="factory"></span>

## 工厂模式

```javascript
// ES6 factory

/** 

 * @abstract class Animal

 */

class Animal {
	constructor(){
		if (new.target === Animal) {
			throw new Error('Animal class is a abstract class');
		};
	}

	call(){
		throw new Error('function call is not implemented');
	}

	eat(){
		throw new Error('function eat is not implemented');
	}
}
```


```javascript
/** 

 * @class Cat

 * @extends Animal

 */

class Cat extends Animal {
	constructor(){
		super();
		console.log('I am a cat');
	}

	call(){
		console.log('miao miao~');
	}

	eat(){
		console.log('eat fish');
	}
}
```


```javascript
/** 

 * @class Dog

 * @extends Animal

 */

class Dog extends Animal {
	constructor(){
		super();
		console.log('I am a dog');
	}

	call(){
		console.log('wang wang~');
	}

	eat(){
		console.log('eat meat');
	}
}
```


```javascript
/** 

 * @class AnimalFactory

 * factory class

 * AnimalType 

 */

class AnimalFactory {
	constructor(){
		
	}

	static CreatAnimal(strAnimalType = AnimalType.Cat){
		switch(strAnimalType) {
			case AnimalType.Cat:
				return new Cat();
				break;
			case AnimalType.Dog:
				return new Dog();
				break;
		}
	}
}

/**

 * AnimalType

 * @type String

 */
const AnimalType = {
	Cat: 'Cat',
	Dog: 'Dog'
};



(function(){

	/*create a cat*/
	let cat = AnimalFactory.CreatAnimal(AnimalType.Cat);
	cat.call();
	cat.eat();

	/*create a dog*/
	let dog = AnimalFactory.CreatAnimal(AnimalType.Dog);
	dog.call();
	dog.eat();

})();
```


<span id="singleInstance"></span>

## 单例模式


```javascript
// ES6 single instance



/** 

 * @instance class DBoperation

 */
class DBoperation {

	constructor(){
		if (DBoperation.instance === undefined) {
			/*init*/
			this.add = function(){
				console.log('add');
			}
			this.del = function(){
				console.log('del');
			}
			this.mod = function(){
				console.log('mod');
			}
			this.find = function(){
				console.log('find');
			}
			DBoperation.instance = this;
		};

		return DBoperation.instance;
	}
}

DBoperation.instance;



let a = new DBoperation();
let b = new DBoperation();
console.log(a === b)
a.add()
b.add()
```

<span id="observer"></span>

## 观察者模式


```javascript
// ES6 single observer

class Observer {
	constructor(){
		this.objSet = new Set();
	}

	attach(obj){
		this.objSet.add(obj);
	}

	detach(obj){
		this.objSet.delete(obj);
	}

	fire(){
		for(let item of this.objSet){
			item.update && item.update();
		}
	}
}

class Config {
	constructor(name = "config"){
		this.name = name;
	}

	update(){
		console.log('i am '+this.name);
	}
}

let c1 = new Config('cfg1');
let c2 = new Config('cfg2');
let c3 = new Config('cfg3');

let obs = new Observer();

obs.attach(c1);
obs.attach(c2);
obs.attach(c3);

obs.detach(c2);

obs.fire();
```
