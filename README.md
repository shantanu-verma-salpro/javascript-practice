# JavaScript Code

```javascript
// for static members Object is better
// Objects methods
let p = (...z) => console.log(...z);
let obj = {
  x: 1,
  y: 2,
};
// to make hidden properties in object
let s = Symbol("hello");
obj[s] = 1;

// to iterate over object u can use this, it also doesn't iterate over symbol() because that's like a hidden field
for (const [x, y] of Object.entries(obj)) p(x, y);

// to iterate over symbol use Reflect.ownKeys()
for (const x of Reflect.ownKeys(obj)) p(x, obj[x]);

// check this now
let ss = Symbol("h");
let obj1 = { ss: "1" };
console.log(obj1); // use 'ss' as key instead of symbol

// to make that being correctly used, can use []
let obj2 = { [ss]: "1" };
console.log(obj2);

// just like symbol there is an alternative way to hide property in iteration
let obj3 = {};
Object.defineProperty(obj3, "x", {
  value: 1,
  enumerable: false,
  configurable: true,
});
console.log(obj3); // shows empty object because property in not enumerable
for (const [x, y] of Object.entries(obj3)) p(x, y);

// to show them
for (const x of Reflect.ownKeys(obj3)) p(x, obj3[x]);

// for obj3 and obj we can check if property exist
p(s in obj); // true
p("x" in obj3); // true

// adding a method to an object’s prototype is a bad practice, because that property will now be present in for...in loops
obj.__proto__["foo"] = "bar";
console.log(obj);
for (let key in obj) console.log(key); // foo appears

// to make foo disappear because its not its own property but the property it got from prototype
for (let key in obj) if (Object.hasOwn(obj, key)) console.log(key); // foo disappears

// in Object.defineProperty value and enumerable are used for reading purpose, writable and configurable are used for writing purpose
// now in obj property values are deletable and reassignable
// to prevent reassign
delete obj.x;
Object.defineProperty(obj, "x", {
  value: 1,
  enumerable: true,
  configurable: true,
  writable: false,
});
console.log(obj);
obj.x = 2;
console.log(obj); // still shows x value as 1

delete obj.x;
console.log(obj); // x got deleted

// configurable false make the key undeletable
Object.defineProperty(obj, "x", {
  value: 1,
  enumerable: true,
  configurable: false,
  writable: false,
});

delete obj.x;
console.log(obj); // x still there

// js getters and setters
Object.defineProperty(obj, "age", {
  get: function () {
    return this.y;
  },
  set: function (value) {
    this.y = Number(value);
  },
});
obj.age = "32";
console.log(obj); // 32

// another way to have private field is by using class and # fields
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
console.log(op.getPrivateValue());

// getting descriptors
console.log(Object.getOwnPropertyDescriptors(obj));

// when we try to modify or delete property with not using 'use strict' it will fail silently

// properties cant be removed or added only changed -sealed
console.log(Object.isSealed(obj3));
console.log(Object.getOwnPropertyDescriptors(obj3));
Object.seal(obj3);
obj3.y = 2; // cannot add new keys
console.log(Object.isSealed(obj3));
console.log(Object.getOwnPropertyDescriptors(obj3));

// properties can be removed but not added
console.log(Object.getOwnPropertyDescriptors(obj1));
obj1.y = 2;
Object.preventExtensions(obj1);
console.log(Object.getOwnPropertyDescriptors(obj1));
delete obj1.y;
console.log(Object.getOwnPropertyDescriptors(obj1));

// freeze - no further changes allowed
Object.freeze(obj1);
delete obj1[ss];
console.log(Object.getOwnPropertyDescriptors(obj1)); // ss still there

// freeze,seal,preventExtension only works on top level but not in nested objects

// how to remove these from object, by deep clone
let obj4 = { ...obj1 };
console.log(Object.isExtensible(obj4));
console.log(Object.getOwnPropertyDescriptors(obj4));
