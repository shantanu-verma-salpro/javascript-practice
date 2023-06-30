# JavaScript Code

```javascript
//for static members Object is better
//Objects methods
let p = (...z) => console.log(...z)
let obj = {
    x: 1,
    y: 2
}
// to make hidden properties in object
let s = Symbol("hello")
obj[s] = 1;

//to iterate over object u can use this , it also doesn't iterate over symbol() because that's like a hidden field
for (const [x, y] of Object.entries(obj)) p(x, y);

//to iterate over symbol use Reflect.ownKeys()
for (const x of Reflect.ownKeys(obj)) p(x, obj[x]);

//check this now
let ss = Symbol("h")
let obj1 = {ss: "1"}
console.log(obj1) // use 'ss' as key instead of symbol

//to make that being correctly used , can use []
let obj2 = {[ss]: "1"}
console.log(obj2)

//just like symbol there is an alternative way to hide property in iteration
let obj3 = {}
Object.defineProperty(obj3, "x", {
    value: 1,
    enumerable: false, configurable: true
})
console.log(obj3) //shows empty object because property in not enumerable
for (const [x, y] of Object.entries(obj3)) p(x, y)

//to show them
for (const x of Reflect.ownKeys(obj3)) p(x, obj3[x]);

//for obj3 and obj we can check if property exist
p(s in obj) //true
p('x' in obj3) //true

//adding a method to an object’s prototype is a bad practice, because that property will now be present in for...in loops
obj.__proto__["foo"] = "bar";
console.log(obj)
for (let key in obj) console.log(key) // foo appears

//to make foo disappear because its not its own property but the property it got from prototype
for (let key in obj) if (Object.hasOwn(obj, key)) console.log(key) // foo disappears

//in Object.defineProperty value and enumerable are used for reading purpose , writable and configurable are used for writing purpose
//now in obj property values are deletable and reassignable
// to prevent reassign
delete obj.x
Object.defineProperty(obj, "x", {
    value: 1,
    enumerable: true,
    configurable: true,
    writable: false
})
console.log(obj)
obj.x = 2
console.log(obj) //still shows x value as 1

delete obj.x
console.log(obj) //x got deleted

//configurable false make the key undeletable
Object.defineProperty(obj, "x", {
    value: 1,
    enumerable: true,
    configurable: false,
    writable: false
})

delete obj.x
console.log(obj) //x still there

//js getters and setters
Object.defineProperty(obj, 'age', {
    get: function () {
        return this.y
    },
    set: function (value) {
        this.y = Number(value)
    }
})
obj.age = '32'
console.log(obj) // 32

//another way to have private field is by using class and # fields
class Example {
    #thisIsPrivate;

    constructor(v) {
        this.#thisIsPrivate = v;
    }

    getPrivateValue() {
        return this.#thisIsPrivate;
    }
}

let op = new Example(5);
console.log(op.getPrivateValue())

//getting descriptors
console.log(Object.getOwnPropertyDescriptors(obj))

//when we try to modify or delete property with not using 'use strict' it will fail silently

//properties cant be removed or added only changed -sealed
console.log(Object.isSealed(obj3));
console.log(Object.getOwnPropertyDescriptors(obj3))
Object.seal(obj3);
obj3.y = 2; //cannot add new keys
console.log(Object.isSealed(obj3));
console.log(Object.getOwnPropertyDescriptors(obj3))

//properties can be removed but not added
console.log(Object.getOwnPropertyDescriptors(obj1))
obj1.y = 2;
Object.preventExtensions(obj1);
console.log(Object.getOwnPropertyDescriptors(obj1))
delete obj1.y;
console.log(Object.getOwnPropertyDescriptors(obj1))

//freeze - no further changes allowed
Object.freeze(obj1)
delete obj1[ss]
console.log(Object.getOwnPropertyDescriptors(obj1)) // ss still there

//freeze,seal,preventExtension only works on top level but not in nested objects

//how to remove these from object , by deep clone
let obj4 = {...obj1};
console.log(Object.isExtensible(obj4))
console.log(Object.getOwnPropertyDescriptors(obj4))


//Proxies - can be used to intercept calls to the object
//get proxy - thou descriptors xan be used to intercept calls to properties, but we need to know its name, get proxy get called without knowing its name
const obj5 = {[ss]: 1}
let handler = {
    get: (t, p, r) => {
        return "default";
    }
}

let obj6 = new Proxy(obj5, handler);
console.log(obj6[ss]) //shows default instead of 1

//to hide if property exist we can use has proxy trap
handler = {
    has: (t, p) => {
        return false;
    }
}
obj = null;
obj = {x: 1}
let proxy = new Proxy(obj, handler);
console.log('x' in proxy) //false

//set proxy trap get called
proxy = new Proxy(obj, {
    set: (t, p, v, r) => {
        t[p.toUpperCase()] = v;
    }
})
proxy.y = 2;
console.log(obj);

//function apply proxy
let f = () => "hello";
proxy = new Proxy(f, {
    apply(target, thisArg, argArray) {
        return "h"
    }
})
console.log(proxy())
//if there are operators applied on object like + etc it tried to convert to primitive , we can use Symbol.toPrimitive
proxy[Symbol.toPrimitive] = function (hint) {
    return "this is proxy"
}
console.log(1 + proxy) //shows 1this is proxy

//toString act as fallback
obj = {
    ...obj,
    toString() {
        return "hello";
    }
}
console.log(11 + obj) //return 11hello

//NaN is a special numeric value that is not equal to itself.
console.log(NaN === NaN)

console.log(undefined === undefined) //true

//using querySelector has some degradation in performance , it also return static list
//converting HTMLCollection returned by these selectors are slow to perform oprtaion we can use this

// let x = [].slice.call(document.getElementsByClassName("code-block"))

//Creating object inherit all prototype chains , to create a bare object we can use create method
obj = Object.create(null);
console.log(obj instanceof Object)
//now it loses all predefined things like
// console.log(obj+"") conversion to premitive type not possible
console.log(Object.isPrototypeOf(obj));
obj["y"] = 1;
Object.propertyIsEnumerable.call(obj, 'y'); // false

//Closure
function add(x) {
    return function (y) {
        return x + y;
    }
}

let x = add(1);
console.log(x(2));

//block scope

{
    const y = 4;
}
//console.log(y) error

//hoisting order of function declaration and calling don't matter , declaration are hoisted at top
fn()
function fn(){
    console.log( "hello");
}
// fn1() shows error
let fn1 = ()=>console.log("h")

//comma return last expression
console.log((1,2,3)); //prints 3

// IIFE

(function (x){
    console.log(x);
})("hello") //prints hello

// 2+{}
// '2[object Object]'
// {}+2
// 2

//any unary operator can be used for IIFE
~function(){
    console.log("hi")
}();
!function(){
    console.log("hi")
}();

//classical version
(function(){
    console.log("hi2")
})();

//also this variation
(function(){
    console.log("hi2")
}())

let next2multiple = (function(){
    let x = 2;
    return {
      getNext:()=>{
          x = x+2;
          return x;
      }  ,
      getCurrent:function (){
          return x;
      }
    };
})()
console.log(next2multiple.getNext())
console.log(next2multiple.getNext())

let next2multiple_1 = function(){
    let x = 2;
    return {
        getNext:()=>{
            x = x+2;
            return x;
        }  ,
        getCurrent:function (){
            return x;
        }
    };
}
let nx = new next2multiple_1();
console.log(nx.getNext())
console.log(nx.getNext())

//destructuring

const [,y,m,d] = /^(\d\d\d\d)-(\d\d)-(\d\d)$/.exec("2020-12-10");
console.log(y,m,d);

//Optional parameter
function run({a=1,b=2}={}){
    return a + b;
}
console.log(run());

//multiple parameters
function run1(...args){
    for(const arg of args){
        console.log(arg);
    }
}
run1(1,2,3);

//apply function
console.log(Math.max.apply(Math,[1,5,0,2,8,1]))

let q = [1,2,3]
let w = [4,5,6]
console.log(Array.prototype.push.apply(q,w),q) // appends to q array

// hex , octal and binary,big num
console.log(0x34,0o34,0b10,1234n)

//to other base
console.log((45).toString(16))

//custom base number to decimal
console.log(Number.parseInt(45,8))

//radix is missing or 0. Then radix is set to 16. As a rule, you should never omit the radix.

//Counting leading zeros in an integer number is a critical operation in many DSP algorithms, such as normalization of samples in sound or video processing, as well as in real-time schedulers to quickly find the highest-priority task ready-to-run
console.log(Math.clz32(1))

//multiline template
x = `   hello
            HI
            how are you?\n1
`
console.log(x)

//Repaeting string
console.log(x.repeat(5))

//unicode
//A combining mark is a sequence of two Unicode code points that is displayed as single symbol. The ES6 approach to reversing a string that I have presented here works for non-BMP code points, but not for combining marks. For those, you need a library, e.g. Mathias Bynens’ Esrever.
console.log('\u{1F680}');

//raw string to get \n to \\n like
console.log(String.raw`x\n1`)

// Iterable
x = [1,2,3]
z = x[Symbol.iterator]();
console.log(z.next())

console.log(nx.getNext())
console.log(nx.getNext())




