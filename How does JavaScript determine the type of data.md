

# **How does JavaScript determine the type of data?**

#### Recently, I ran into a problem about getting the data type of a parameter and returning it, so I wanted to talk about how JavaScript determines the data type. This article will share with you 4 ways to determine the type of data, I hope it will be helpful to you.**

#### What data types are available in JavaScript?

**primitive data types**：Undefined、Null、Boolean、Number、String

**reference data types**：objects、arrays、functions

#### Here's a common way to determine the type of data:

### 1. Object.prototype.toString

This method is recommended because it has smaller defects than other methods, and it can accurately determine the type of all data, but it returns [object xxxx], where xxxx is the type of data we need, so we need to process the return value.

```javascript
function checkType (data){
    return Object.prototype.toString.call(data).slice(8, -1)
}
console.log(checkType(123)); // Number
console.log(checkType("123")); // String
console.log(checkType(true)); // Boolean
console.log(checkType(null)); // Null
console.log(checkType(undefined)); // Undefined
console.log(checkType(Symbol(123))); // Symbol
console.log(checkType({})); // Object
console.log(checkType([])); // Array
console.log(checkType(function () {})); // Function
console.log(checkType(new Date())); // Date
console.log(checkType(new RegExp())); // RegExp
```

### 2. typeof

typeof can only detect functions in primitive data types and reference data types other than null

```javascript
console.log(typeof(1))        //number
console.log(typeof('123'))      //string
console.log(typeof(true))       //boolean
console.log(typeof(undefined))  //undefined
console.log(typeof(null))       //object
console.log(typeof(Function))   //function
console.log(typeof([]))         //object
console.log(typeof({}))         //object 
```

### 3. **instanceof & **isPrototypeof

instanceof returns a boolean value that is used to detect whether an object has a prototype property of a constructor in its prototype chain, not the raw data type.

isPrototypeof is basically equivalent to instanceof, which determines whether the object is on the prototype chain of the instance object

```javascript
// instanceof
console.log(123 instanceof Number)      //false
console.log({} instanceof Object)      //true
console.log([] instanceof Array)      //true
console.log(function (){} instanceof Function)     //true

// isPrototypeof
console.log(Number.prototype.isPrototypeOf(123)) //false
console.log(Object.prototype.isPrototypeOf({})) //true
console.log(Array.prototype.isPrototypeOf([])) //true
console.log(Function.prototype.isPrototypeOf(function(){})) //true
```

### 4. Array.isArray

Array.isArray() returns a boolean value that is used to determine whether an object is an array or not.

```javascript
console.log(Array.isArray())              //false
console.log(Array.isArray({}))            //false
console.log(Array.isArray([]))            //true
console.log(Array.isArray(function (){})) //false
```

In general, Object.prototype.toString is suitable for all data types, typeof is more suitable for detecting primitive data types, instanceof && isPrototypeof is more convenient for detecting reference objects, and Array.isArray is for detecting arrays.

Thank you for your time and hope you get something out.