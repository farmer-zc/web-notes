# Generator

## 1. Generator 的基本概念

1. Generator 函数是 es6 提供的一种异步编程的方案！
2. Generator 函数是一种可以暂停执行的函数
3. Generator 可以理解为一个状态机，封装了多个内部状态
4. 执行 Generator 函数会返回一个遍历器对象，可依次遍历 Generetor函数内部的每一个状态

## 2. Generator 函数的形式

1. Generator 函数 `function` 关键字和函数名之间有一个 *（星号）
2. 函数体内部使用 `yield` 表达式定义不同的内部状态
3. Generator 函数调用与普通函数一样，但是调用后函数并不执行，返回的也不是运行结果，而是一个指向内部状态的执政对象（也就是遍历器对象 Iterator）
4. 必须调用遍历器对象的 `next` 方法，每次调用 `next` 方法，内部执政就从函数头部或上一次停下来的地方开始执行，知道遇到下一个 `yield` 表达式或 `return` 语句，
5. 每次调用 `next` 方法，就会返回一个有 `value` 和 `done` 两个属性的对象， `value` 表示当前的内部状态的值，即 `yield` 后面那个表达式的值，`done` 是个布尔值，表示是否已经遍历结束
6. 总结：Generator 函数是分段执行的，`yield` 表达式是暂停执行的标记，而 `next` 方法可以恢复执行

```javascript
function* fun () {
    yield 'hello';
    yield 'world';
    return 'ending'
}
let it = fun();  // 并没有执行
it.next();  // { value: 'hello', done: false }
it.next();  // { value: 'world', done: false }
it.next();  // { value: 'ending', done: true }
it.next();  // { value: undefined, done: true }

```

## 3. Generator 函数的执行规则

**Generator 是一种可以暂停执行的函数，`yield` 表达式就是暂停标志**

Generator 函数生成的遍历器对象的 `next` 的执行规则：

1. 第一次调用遇到 `yield` 表达式，就暂停执行后面的操作，并将 `yield` 后面的表达式的值作为返回对象的 `value` 的属性值
2. 下一次调用 `next` 就继续往下执行，直到遇到下一个 `yield`表达式
3. 如果之后没有 `yield` 表达式，就一直运行到 `return` 语句，并将 `return` 语句后面的表达式的值 作为返回对象的 `value` 的属性值，且，返回对象的 `done` 变为true，表示遍历完毕
4. 如果该函数没有`return`语句，则返回的对象的`value`属性值为`undefined`。

## 4. yield 表达式

`yield` 后面的表达式在函数调用的时候不会执行，只有当调用 `next` 方法将指针移到这一句时，才会求值

```javascript
function* add () {
	yield 1 + 1
}
let it = add(); //不会求值
it.next(); // {value: 2, done: false}
it.next(); // {value: undefined, done: true}

```

**`yield` 表达式与 `return` 语句有像是之处也有区别**

1. 相似：都能返回紧跟在语句后面的表达式的值
2. 区别：一个函数能有多个`yield`， 遇到 `yield` 函数暂停，下一次 `next` 从该位置继续向后执行，而 `return` 语句在一个函数里面只能执行一次，只能返回一个值

**Generator 函数可以不用 `yield` 表达式，函数就变成了一个暂缓执行函数，但是 `yield` 必须在Generator 函数中**

```javascript
function* fun() {
	console.log('执行了')
}
let it = fun(); // 不会执行
setTimeout(function() {
	it.next();  // 2秒后执行了
}, 2000)
```

**yield 表达式如果在另一个表达式中，必须得放在圆括号中**

```
function* fun() {
	console.log('hello' + yield ); // 错误的
	console.log('hello' + yield 123 ); // 错误的
	console.log('hello' + (yield) ); // 正确
	console.log('hello' + (yield 123) ); // 正确
}
```

## 5. next 方法

**`yield` 表达式本身没有返回值，或者说总是返回 `undefined`。但是`next`方法可以带一个参数，该参数会被当做==上一个==`yield`表达式的返回值, 所以第一个`next` 方法的参数总是无用的**

**next方法会返回一个有value和done两个属性的对象，done属性是个布尔值表示当前遍历器是否遍历完毕，当done为false时value属性的值是当前yield语句后面表达式的结果，当done为true时next的value属性的值为return 语句后面表达式的结果，没有return则为undefined**

```javascript
function* fun() {
	console.log('我是前面的')
	let a = yield 1+1;   // 这个表达式本身没有返回值,默认是undefined
	console.log('我是：' + a)
    let b = yield 2+2;   // 这个表达式本身没有返回值，返回的是第3次执行next方法的参数
	console.log('我是：' + b)
	let c = yield 3+3;	 // 这个表达式本身没有返回值，返回的是第4次执行next方法的参数
	console.log('我是：' + c)
}
let it = fun()
it.next('第1个next'); // console：我是前面的   next的返回：{value: 2, done: false}
it.next('第2个next'); // console(a)：我是第2个next； next的返回：{value: 4, done: false}
it.next('第3个next'); // console(b)：我是第3个next； next的返回：{value: 6, done: false}
it.next('第4个next'); // console(c)：我是第4个next； next的返回：{vlaue: undefined, done: true}

```

**通过next可以在 Generator 函数开始运行之后，继续向函数体内部注入值，即可以在函数运行的不同阶段，从外部注入不同的值，从而得到不同的结果**

```javascript
function* fun(x){
 	// 这里乘的是 yield 的返回值（即next方法的参数），而不是后面表达式（x+1）的结果
 	// （x+1）的结果是赋值给 next 方法的返回对象的 vlaue 属性的
	var y = 2 * (yield x+1); 
	console.log(y)
	// 这里的 z 是等于 yield 返回值，而不是后面（y-3）的结果
	var z = yield y - 3
	console.log(z)
	return x + y + z
}
var it = fun(5); // 不执行
it.next(); // next的返回：{value: 6, done: false} 注意此处Y并不等于 2 * 6 要看下一个next的参数
it.next(4); // next的返回：{value:5, done: false} 此时y = 2*4 所以value的值为8-3,同理x != 5
it.next(3); // {value:16, done: true} 此时z=3   return 为 5+8+3
```

## 6. for  of  循环

**`for...of`循环可以自动遍历 Generator 函数运行时生成的`Iterator`对象，且此时不再需要调用`next`方法。**

```javascript
function* foo() {
  yield 1;
  yield 2;
  yield 3;
  return 6;
}

for (let v of foo()) {
  console.log(v);
}
// 1 2 3 4 5
```

**扩展运算符（`...`）、解构赋值和`Array.from`方法内部调用的,都可以将 Generator 函数返回的 Iterator 对象作为参数**

```
function* numbers () {
  yield 1
  yield 2
}

// 扩展运算符
[...numbers()] // [1, 2]

// Array.from 方法
Array.from(numbers()) // [1, 2]

// 解构赋值
let [x, y] = numbers();
x // 1
y // 2

// for...of 循环
for (let n of numbers()) {
  console.log(n)
}
// 1
// 2
```

## 7. 其他方法

### 7.1 Generator.prototype.throw()

Generator函数返回的遍历器对象，都有一个throw方法，可以在方法体外抛出错误，然后在generator函数体内捕获（这里可以利用 Promise 去在请求api失败的时候 reject（error），就可以被捕获）

```javascript
function* foo () {
	try {
		yield 'hello';
		yield 'world';
	} catch (e){
		console.log(e)
	}
}
var it = foo();
it.next();  // next的返回：{value: "hello", done:false}
it.throw('出错了'); // console：出错了  返回值：{value: undefined, done: true}

```

### 7.2 Generator.prototype.return()

Generator 函数返回的遍历器对象，还有一个`return`方法，可以返回给定的值，并且终结遍历 Generator 函数。

```javascript
function* gen() {
  yield 1;
  yield 2;
  yield 3;
}

var g = gen();

g.next()        // { value: 1, done: false }
g.return('foo') // { value: "foo", done: true }
g.next()        // { value: undefined, done: true }
```



































