# Ref

-[Jonas course]

(1) : https://github.com/jonasschmedtmann/complete-javascript-course/blob/master/14-OOP/final/script.js

(2) : https://www.udemy.com/course/the-complete-javascript-course/

-[Javascript Tutorial]: https://www.javascripttutorial.net/

## Argument vs. Parameter

<p align="center" width=100%>
    <img width=80% src="https://i.sstatic.net/9lg1H.png">
</p>

### 1. Argument object

- Argument object trong một function là gì. Mình lấy ví dụ:

```javascript
function example() {
  console.log(arguments[0]); // 1
}
example(1, 3);
```

- Nhìn có vẻ giống một array, nhưng nó KHÔNG phải là một instance của Array

```javascript
function example() {
  return arguments.filter((x) => x > 0); //arguments.filter is not a function
}
example(1, 2, 3, 4);
```

- Nó là một `iterable`.
<p align="center" width=100%>
    <img width=80% src="./ex1.png">
</p>

- `Arrow Function` KHÔNG có Argument Object.

### 2. Rest parameter

- Rest parameter `...` phải được xuất hiện ở cuối:

```javascript
function fn(a,...rest, b) {
 // error, nếu chuyển rest xuống cuối thì rest là Array không phải Argument object
}
```

- Gom 1 đóng lại thành một array mới:

```javascript
let [x, y, ...args] = [70, 80, 90, 100];
//args = [90, 100]
```

### 3. Spread operator

- Cũng là `...` nhưng xuất hiện bất kì ở đâu ngoại trừ vị trí của rest(vì nếu ở đó thì gọi là rest rồi).

- Spread trong Array hoặc một iterable object (Map, Set):

```javascript
const odd = [1, 3, 5];
const combined = [2, ...odd, 4, 6];
console.log(combined); //[ 2, 1, 3, 5, 4, 6 ]
```

- Spread trong Object(ES2018):

```javascript
const hien = {
  name: 'hien',
};
const student = {
  ...hien,
  age: 1998
};

console.log(student);
/*
{
     name: 'hien',
     age: 1998
}
/*
```

- Các trường hợp hay dùng:

  - Push element vào array:

    ```javascript
    const a = [1, 2];
    const b = [3, 4];
    b.push(...a); //push từng phần tử
    //khác với
    b.push(a); // push 1 phần tử
    ```

  - Nối mảng

    ```javascript
    const a = [1, 2];
    const b = [3, 4];
    const c = [...a, ...b];
    ```

  - Clone một array mới(chỉ là shallow copy):

    ```javascript
    const a = [1, [2, [3]]];
    const b = [...a];
    // b[1] === a[1]
    ```

  - Clone object mới(chỉ là shallow copy):

    ```javascript
    const hien = {
      name: "hien",
    };
    const student = {
      hien,
      age: 1998,
    };
    const teacher = {
      ...student,
    };
    console.log(teacher);
    //student.hien === hien
    ```

  - Nối object:
    ```javascript
    const hien = {
      name: "hien",
    };
    const student = {
      age: 1998,
    };
    const teacher = {
      ...student,
      ...hien,
    };
    console.log(teacher);
    ```

### 4. Destructuring

- Object:

  > let { property1: alias1, property2: alias2 = defaultIfUndefined} = object != null || {};

  > Thường dùng destructuring trong function. ({a,b}) => a+b

- Array:
  > let [property1: alias1, property2: alias2 = defaultIfUndefined] = array != null || [];
  ```javascript
  const [a, b, c] = [1, 2, 3];
  //let a = 1, b = 2, c = 3;
  ```

## Promise/Async/Await

- Eager giống như construtor của class, chạy mỗi khi khởi tạo object
- Lazy giống như define một function mà không chạy.

### 1. <b>Khởi tạo promise</b>:

- Cách 1:

  ```javascript
  const p1 = new Promise((resolve, reject) => {
    // executor này có lẻ là đặt trong constructor của Promise
    // ... complex logic
    resolve("value");
  });
  ```

- Cách 2:

  ```javascript
  const p2 = Promise.reject("error gi do...");
  ```

- Cách 3: chuyển eager thành lazy

  ```javascript
  const p3 = function (miliSecond) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve("data sau 1 khoang thoi gian");
      }, miliSecond);
    });
  };
  ```

- Cách 4: Async function
  ```javascript
  const p4 = async () => {
    // return Promise.resolve('data')
    return "data";
  };
  ```

### 2. <b> Consume data </b>

```javascript
p2.catch((error) => {
  console.log(error);
});

const newP = p3()
  .then((data) => {
    console.log(data);
    // Quá trình consume (then(...)) luôn tạo ra 1 promise nếu không return trong then có nghĩa là return undefined.
    // return data + 1 <=> return Promise.resolve(data + 1)
    return data + 1;
  })
  .then((x) => {
    console.log("xxxxx", x);
    // Muốn cho consume tiếp theo nhảy vào catch thì:
    return Promise.reject(new Error("error"));
    // throw new Error('error')
  })
  .catch((error) => {
    console.log(error);
    // Không return có nghĩa là return undefined <=> return Promise.resolve(undefined)
  })
  .then((x) => {
    console.log(x);
    // Cứ thế vô tận và newP là cái return cuối cùng của promise chain.
  });

// Chỉ dùng await trong async function. Mà async function thì return về 1 promise
// await 1 promise sẽ giống với việc .then và gán value của promise trước đó cho quá trình await
// Sử dụng try catch để xử lý resolve reject với async function
function normal() {
  return 1;
}
const aFn = async () => {
  try {
    console.log("async function");
    const data = await normal();
    console.log(data);
  } catch (error) {
    // ... xử lý lỗi
  }
  return 1;
};

// Promise.all([p1,p2,p3]).then(data => console.log(data)) data là []

// Các thứ tự chạy: callstack => microtask queue (callback của then)=> task queue (callback cua setTimeout,...)
// Minh họa https://www.jsv9000.app/
```

## Object and Class

- Hình tròn đại diện cho function, hình vuông đại diện cho Object. Ref: https://www.javascripttutorial.net/javascript-prototype/
  > `obj.constructor` sẽ tìm các properties của nó trước sau đó tìm ở `obj.__proto__`

<p align="center" width=100%>
    <img width=50% src="https://www.javascripttutorial.net/wp-content/uploads/2022/01/JS-prototype-object-with-method.svg">
</p>

- Kế thừa trong javascript bằng prototype:

  > `let teacher = Object.create(person)`

  > `Object.create(object, propertiesObject)` // lấy toàn bộ properties methods của object cho `prototype` object mới

<p align="center" width=100%>
    <img width=30% src="https://www.javascripttutorial.net/wp-content/uploads/2022/01/JavaScript-prototypal-inheritance-example.svg">
</p>

- Con trỏ `this` sẽ references tới object mà hiện tại đang gọi function. Trick lỏ ví dụ x.y thì x là this.

  - Function invocation: `this` sẽ ref tới `global object` với `non-strict` và `undefined` với `strict-mode`
  - Method invocation: `obj.method()`, `this` sẽ trỏ tới `obj` đang gọi method đó. Nếu gán method đó bằng biến khác thì khi gọi biến đó `this` sẽ ref tới `global object`. Bất kể là có gán cho obj khác đi nữa. Dùng `call`, `bind`, `apply`.
  - Contructor invocation: sử dụng từ khóa `new` với một function (không phải `arrow function`) sẽ tạo `this = {}` trong function đó
  - Indirect invocation: sử dụng `call`, `bind`, `apply`.
    - `call(thisArg, arg1, arg2, ...)`: gọi hàm và truyền object như là `this`
    - `bind(thisArg, [arg1,arg2,...])`: giống call
    - `apply(thisArg, arg1, arg2, ...)`: trả về 1 hàm mới.

- Object Property types:

  - Data properties

    ```javascript
    "use strict";
    let obj = {};

    Object.defineProperty(obj, "age", {
      configurable: false, // có thể được redefined hay delete không. Default true
      enumerable: true, // Có thể `for in` key này không. Default true
      writable: true, // value có thể được change không. Default true
      value: "5", // value. Default undefined
    });
    delete obj.age; // Có lỗi do `configurableset` là false
    ```

  - Acessor properties

    ```javascript
    let obj = {};

    Object.defineProperty(obj, "fullName", {
      configurable: false, // có thể được redefined hay delete không. Default true
      enumerable: true, // Có thể `for in` key này không. Default true
      get: function () {
        // khi read value thì get function tự động gọi. Default undefined
        return this.firstName + " " + this.lastName;
      },
      set: function (value) {
        // Khi assign value thì set function tự động gọi.
        if (value && typeof value === "string") {
          this.firstName = value;
        }
        throw "Invalid name";
      },
    });
    ```

  - `Object.getOwnPropertyDescriptor()`: descriptor là Data + Accessor
  - `For...in` loop được cả prototype chain.
  - `obj.hasOwnProperty()` return boolean. Xác định property đó có phải của riêng obj đó không.
  - `Factory function` là function return 1 obj.
  - `obj ?. property` return về undefined thay vì quăng lỗi. Có thể dùng với function để kiểm tra func đó có là undefined hay null.
  - `?? [expression]` nếu giá trị trước đó là null hoặc undefined.

- Class ES6

  <img width=45% src="./assets/constructorfn.png"> ====>
  <img width=46.5% src="./assets/class.png">

  - Class KHÔNG `hoisted`
  - `new class {}`: class expression. Dùng để tạo 1 singleton object
  - `[expression]`: có thể dùng để làm tên key
  - `static funcProperty(){}` hoặc `this.constructor.funcProperty()` để call static function. Static function là function không thuộc về obj mà là của class.
  - Dùng `#` trước filed để đánh dấu private(ES2022). Nếu trình duyệt không hỗ trợ hãy dùng `let` hoặc `var`

- Closure: lưu trữ biến của outer function. Có thể kết hợp với IIFE để gói setTimeout
