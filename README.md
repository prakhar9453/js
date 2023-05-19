# Asynchronous Programming In Javascript

**Asynchronous Programming** helps peices of code to execute in parallel.

**For Example:**

```
function greet() {
    console.log('Hello world');
}
setTimeout(greet, 3000);
console.log('Hello Universe');

//expected output: hello universe => hello world
```
or
```
const promise = new Promise(function(resolve, reject) {
  // not taking our time to do the job
  resolve('Hello world'); // immediately give the result: 123
});

promise.then((val) => console.log(val));
console.log('Hello Universe');

//expected output: hello universe => hello world
```

**Javascript is a single threaded language. But wait.**
#### How is Javascript asynchronous and single-threaded in same time?

The answer is Javascript is single-threaded, but not quite asynchronous. Asynchronous tasks are handled by the environment around Javascript like browser for example. Browser contains Web APIs, Task queue, Microtask queue, and Event loop which handles asychronous tasks.

#### Event loop
Event loop is a running process that watches call stack & queues. If the call stack is empty, it takes first event in Task queue and pushes it to the call stack.

![event-loop](https://res.cloudinary.com/practicaldev/image/fetch/s--fqt0UJmH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_800/https://devtolydiahallie.s3-us-west-1.amazonaws.com/gif2.1.gif)

![event-loop](https://res.cloudinary.com/practicaldev/image/fetch/s--qxI9YF9R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_800/https://devtolydiahallie.s3-us-west-1.amazonaws.com/gif3.1.gif)

![event-loop](https://velog.velcdn.com/images/gtfo/post/03ef68fc-c4bc-4de0-993e-8415d21ed5a2/%E1%84%86%E1%85%A1%E1%84%8B%E1%85%B5%E1%84%8F%E1%85%B3%E1%84%85%E1%85%A9%E1%84%90%E1%85%A2%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3.gif)

### Micro Task Queue VS Macro task Queue

Most asynchronous callbacks like `setTimeout` or `setInterval` goes to Task queue when it is fired. However, in ES6, Mictrotask queue was introduced as a queue that stores callback from the `Promise` object, `MutationObserver`, and etc.

Most importantly, Mictrotask queue has priority to Mactrotask queue. Additonaly, Mictrotask queue continues to push its callback into call stack untill the Mictrotask queue is empty.


### Web API and callback which leads to callback-hell

Common Browser Web API's

    1. APIs that fetch data from the server
    2. Audio and Video APIs
    3.Device APIs enable you to interact with device hardware
    4.Client-side storage APIs enable you to store data on the client-side.
    5. Javascript Engine API
    
**Callback Hell Example**

 Callback hell generally refers to an ineffective way of writing code asynchronously. It is an anti-pattern that is often called the “pyramid of doom”. **Tough Maintenance**

In the example shown below, `getUserData` takes in an argument that is dependent or needs to be extracted from the result produced by `getArticles` which is inside user. The same dependency can be observed with `getAddress` also. This scenario is termed callback hell.
    
```
//assume getArticles, getUserData, getAddress are async function

getArticles(20, (user) => {
  console.log("Fetch articles", user);
  getUserData(user.username, (name) => {
    console.log(name);
    getAddress(name, (item) => {
      console.log(item);
      // this goes on and on...
    }
})
```

## Promise

The Promise object represents the eventual completion (or failure) of an asynchronous operation and its resulting value.

```
 new Promise((resolve, reject) => {
  resolve();
  reject();
});
```

**State of Promise**

1. Pending
2. Fulfilled
3. Rejected

Promise Functions: 

`then` & `catch` 

so above code using promise will look like:

```
getArticles(10)
  .then((user) => getUserName(user.name))
  .then((place) => getAddress(place.city))
  .catch((err) => console.log("Error: ", err.message));
  .then((data) => console.log("Data", data))
  .catch((err) => console.log("Error: ", err.message));
```

Trick: resolve(Promise) will only resolve when inner promise is resolved.

Exercise time: 

[Exercise 1](https://codepen.io/prakhar9453/pen/vYVQEja)
[Exercise 2](https://codepen.io/prakhar9453/pen/WNaYbZV?editors=0011)


## Async / Await

These keywords are introduces in JS, it's implementation is based on getartors and iterators. they are used to write cleaner code in JS.

`async` marks the function as asynchronous and `await` wait at the statement until it is executed, once it is executed it starts execultion code below it.


```
async function showImages() {
  try {
    const article = await getArticle(10);
    const place = await getPlaces(article.name);
    const city = await getCity(place)
    console.log(city);
  } catch (err) {
    console.log("Error: ", err.message);
  }
}

showImages();
```

Common Mistake

```
async function showImages() {
    await someImpureFunction();
}

showImages();
```


### Async / Await Implementation

**Generators:** 
The Generator object is returned by a generator function and it conforms to both the iterable protocol and the iterator protocol.

Generator is a subclass of the hidden Iterator class.

```
    function* generator() {
        yield 1;
        yield 2;
        yield 3;
}

const gen = generator();

console.log(gen.next().value); // 1
console.log(gen.next().value); // 2
console.log(gen.next().value); // 3
```

**Iterators**

iterator Object 
```
    iterface IteratorObject {
        value: any;
        done: boolean;
    }
    
    interface Iterator {
        next: (value: value | undefined) => IteratorOnject;
    }
```


This is a `createData` async function let us see how is implemented using generators and iterators.

```
async function createData(){
    const data = await fetch('https://someApi');
    console.log(data);
}

createData();
```

**Implementation using Generators and Iterators.**
```
function doWhenDataRecieved(){
    returnNextElement.next(value);
}

function *createData(){
    const data = yeild fetch('https://someApi')
    console.log(data);
}

const returnNextElement = createData();
futureData = returnNextElement.next();

futureData.then(doWhenDataRecieved);
```









