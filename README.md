### 每个对象都有toString和 valueOf，什么情况下先触发哪个？

#### toString() 返回一个表示该对象的字符串

##### 当对象表示为文本值或以期望的字符串方式被引用时，toString方法被自动调用。
toString特殊情况
```
var obj = {}
console.log(obj.toString()) // [object object]
console.log(toString.call(new Date)) // [object Date]
console.log(toString.call(new String)) // [object String]
console.log(toString.call(Math)) // [object Math]
console.log(toString.call(window)) // [object Window]
console.log(toString.call(undefined)) // [object undefined]
console.log(toString.call(null)) // [object null]
```
其他类型的toString
```
var arr = [1,2,3,4]
console.log(arr.toString()) // "1,2,3,4"
var str = '1234'
console.log(str.toString()) // "1234"
var fn = function () { console.log('fn') }
console.log(fn.toString()) // "function () { console.log('fn') }"
```

##### 可以自定义一个toString方法覆盖原来的方法 
```
obj.toString = function () { return ‘a new toString’}
console.log(obj+’ hello’) // a new toString hello
```


#### valueOf() 表示指定对象的原始值(数值、字符串和布尔值)

##### 返回对象本身
```
var obj = {}
obj.valueOf() // {}
var x = [1,2,3]
x.valueOf() // [1,2,3]
var y = function(){console.log('valueOf')}
y.valueOf() // function(){console.log('valueof')}
var z = 123
z.valueOf() // 123
```

##### 可以自定义一个valueOf方法覆盖原本的，类同toString

##### valueOf比toString优先级更高
```
var object = {
  toString:function(){
    console.log('toString')
    return Object.prototype.toString.call(this)
  },
  valueOf:function(){
    console.log('valueOf')
    return Object.prototype.valueOf.call(this)
  }
}
alert(object) // 控制台:toString 弹出[object object] 控制台:valueOf
alert(+object) // NaN 控制台:toString
alert(object=={}) // false
alert(object==={}) // false
alert(object=='test') // 弹出false 控制台valueOf toString
alert(object==='test') // false
```

```
function fn () {
  return 20;
}
console.log(fn + 10)
```
返回结果：
```
function fn () {
  return 20;
}10
```

```
console.log(fn + 'hello')
```
结果：
```
function fn () {
  return 20;
}hello
```

```
fn.toString = function (){
  return 10;
}
console.log(fn + 10) // 20
console.log(fn + 'hello') //10hello
fn.valueOf = function () {
  return 5;
}
console.log(fn + 10) //15
console.log(fn + 'hello') //5hello
```


### toString与valueOf什么情况下先执行：

##### 如果没有重新定义valueOf和toString，其隐式转换会调用默认的toString()方法。如果重新定义了，那么其转换会按照定义的来。其中，valueOf比toString优先级更高。
```
var obj = {}
console.log(obj+10) // [object object]10
console.log(obj+'hello') // [object object]hello
var arr = [1,2,3,5]
console.log(arr+20) // 1,2,3,520
console.log(arr+'hello') // 1,2,3,5hello
var date = new Date(0)
console.log(date.toString()) //Thu Jan 01 1970 08:00:00 GMT+0800 (中国标准时间) 
console.log(date.valueOf()) //0
console.log(date+1) //Thu Jan 01 1970 08:00:00 GMT+0800 (中国标准时间)1
```

### 特殊情况：alert,[x].join(‘’)等这类特殊的表达，均调用toString()，须当特例记住
```
var x = {
  toString: function () {
    return 'foo'
  },
  valueOf: function () {
    return 42
  }
}
alert(x) //弹出foo
console.log('x='+x) //x=42
console.log(x+'=x') //42=x
console.log(x+'1') //421
console.log(x+1) //43
console.log(['x+', x].join('')) // x+foo
```




### valueOf相关的面试题

#### 1.如何使if(v == 2 && v == 4 && v == 8)

```
const v = {
	value: 2,
	valueOf: function () {
		return Math.pow(2, v.value++)
}
}
const equ = (v==2 && v==4 && v==8)
console.log(equ); //true
```


#### 2.The question is : can (a==1 && a==2 && a=3) ever evaluate to true? The answer is Yes.
```
const a = {
	currentVal: 0,
	valueOf: function () {
		return this.currentVal += 1
}
}
const equality = (a==1&&a==2&&a=3)
console.log(equality) // true
```


##### 解析：隐式类型转换，在对象上执行类型转换时，JavaScript首先尝试调用该对象的valueOf()方法。 

Object.prototype.valueOf() 每个对象都有valueOf方法，返回正在调用的对象。
```
const b = {num: 0}
console.log(b.valueOf()) //返回对象本身:{num: 0}
console.log(typeof b.valueOf()) // 使用typeof验证返回的对象:object
```

重新定义valueOf方法返回调用它的值
```
b.valueOf = function () {
	return this.num;
}
console.log(b.valueOf); //0
console.log(typeof b.valueOf()) //number
console.log(b.num == b.valueOf()) //true
```
此时执行
```
b == 0 
// true
```

参考资料：
https://codeburst.io/javascript-can-a-1-a-2-a-3-ever-evaluate-to-true-aca13ff4462d


