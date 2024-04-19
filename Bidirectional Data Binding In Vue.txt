### What is bidirectional data binding ？ 

VUE is an MVVM framework, bidirectional data binding is when the data changes, the view will also change, and when the view changes, the data will also change synchronously.

The Vue framework has been upgraded from Vue2 to Vue3 with many changes, one of which is bidirectional data binding.

vue2 bidirectional data binding is implemented through the **Object.defineProperty()** function, and vue3 is implemented through **Proxy**.

#### Let's take a look at the differences between the two.

##### Object.defineProperty()

The Object.defineProperty() function can define the property-related descriptors of the object, and the set and get functions play a crucial role in completing the bidirectional data binding of data.



**Basic usage**

```javascript
var obj = {
  foo: 'foo'
}

Object.defineProperty(obj, 'foo', {
  get: function () {
    console.log('read obj.foo');
  }, 
  set: function (newVal) {
    console.log('current value', newVal);
  }
});

obj.foo; // read obj.foo
obj.foo = 'name';
```



**Use Object.defineProperty() to implement a simple bidirectional data binding**



```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>forvue</title>
</head>
<body>
  <input type="text" id="textInput">
  input：<span id="textSpan"></span>
  <script>
    var obj = {},
        textInput = document.querySelector('#textInput'),
        textSpan = document.querySelector('#textSpan');

    Object.defineProperty(obj, 'foo', {
      set: function (newValue) {
        textInput.value = newValue;
        textSpan.innerHTML = newValue;
      }
    });

    textInput.addEventListener('keyup', function (e) {
        obj.foo = e.target.value;
    });

  </script>
</body>
</html>
```

Let's give it a try

![img](https://cdn.nlark.com/yuque/0/2024/png/40374407/1713531671175-94d8d03d-af59-4981-8fc1-d3102f3ab0ff.png)



**shortcomings:**

1. It is not possible to listen to the array, and the method of the array is rewritten (push, pop, shift, unshift, etc.). Perform bidirectional binding and data listening operations on this issue
2. Poor efficiency, mainly because of a one-time recursive operation on multi-layer data, if the data is large or very deep, the performance is very poor
3. Due to limitations, it is not possible to listen to the new/deleted data, so use $set in vue2.0 to add it manually

##### Proxy

Proxy is a special object in ES6 that is used to create proxy objects, which allows us to intercept and customize the actions of the target object, such as property access, assignment, function calls, etc. Proxy provides a mechanism to set an interceptor on the target object to intercept operations on the target object.



**Basic usage**

```javascript
const target = {...... }

const handler = {
  get(target, property, receiver){
    //Intercept read of attributes
  },
  set(target, property, value, receiver){
    //Intercept set of attributes
  }
}

const proxy = new Proxy(target, handler);
```



**Use Proxy to implement a simple bidirectional data binding**



```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>forvue</title>
</head>

<body>
    <input type="text" id="textInput">
    input：<span id="textSpan"></span>
    <script>
        var obj = {
        },
            textInput = document.querySelector('#textInput'),
            textSpan = document.querySelector('#textSpan');

        const handler = {
            get(target, property, receiver) {
                console.log(`read property: ${property}`);
                return target[property];
            },
            set(target, property, value, receiver) {
                console.log(`set property：${property}，new value：${value}`);
                target[property] = value;
                if (property == 'foo') {
                    textInput.value = value;
                    textSpan.innerHTML = value;
                }
                return true;
            }
        };
        const proxy = new Proxy(obj, handler);
        textInput.addEventListener('keyup', function (e) {
            proxy.foo = e.target.value;
        });
    </script>
</body>
</html>
```

Let's give it a try

![img](https://cdn.nlark.com/yuque/0/2024/png/40374407/1713531520584-aa9465cd-7250-419b-b210-1d5b1bd978bc.png)

**shortcomings:**

1. The proxy will start multiple set/get responses
   Solution:
   (1) Using something similar to debounce, optimize it so that its value responds once
   (2) (Solution) in vue 3.0), determine whether the key is the target's own property, and whether the value is equal to the target[key], which can avoid redundant set/get operations
2. Proxy can only proxy one layer, and cannot listen in depth
   (1) Use deep recursion to listen to each layer. The clever use of Reflect.get() returns the properties of the object's inner structure (the next layer), determines whether the next layer is still an object, and uses a deep recursive operation. But it has a big impact on performance
   (2) Use weakMap, use two weakMaps to save the original data and the responsive data. When accessing data, it will look up from the saved data, and if it is not proxyed, it will be reproxyed



#### summary

In general, es6 Proxy is used to implement bidirectional data binding, which solves many bugs hidden by bidirectional data binding in vue 2.0 and uses Object.defineProperty to solve problems, and the performance has also been improved