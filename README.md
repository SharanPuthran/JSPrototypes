# Javascript Popular Coding Interview Questions with solutions
List of personally solved Javascript prototypes

### Table of Contents

| No. | Questions |
| --- | --------- |
|1  | [Custom Map](#bubble-sort) |
|2  | [Custom Filter](#selection-sort) |
|3  | [Custom Reduce](#inventor-update) |
|4  | [Custom GroupBy](#inventor-update) |
|5  | [Currying](#inventor-update) |
|6  | [Reverse String](#inventor-update) |
|7  | [Pure Memoization](#inventor-update) |
|8  | [Debouncing](#inventor-update) |
|9  | [Throttling](#inventor-update) |
|10  | [Polyfil for Promises](#inventor-update) |

## Algorithms
    
1. ### Bubble Sorting in JavaScript

    Bubble sort algorithm is an algorithm that sorts the array by comparing two adjacent elements and swaps them if they are not in the intended order. Here order can be anything like increasing order or decreasing order.

    How Bubble-sort works
    We have an unsorted array `array = [1, 4, 2, 8, 345, 123, 43, 32, 5643, 63, 123, 43, 2, 55, 1, 234, 92]` the task is to sort the array using bubble sort. 

    Bubble sort compares the element from index 0 and if the 0th index is less than 1st index then the values get swapped and if the 0th index is less than the 1st index then nothing happens.

    then, the 1st index compares to the 2nd index then the 2nd index compares to the 3rd, and so on…
    
    Time Complexity in worst case: `O(n²)` 
    
    Number of Swaps in worst case: `n - 1`
    
```javascript
    function bubbleSort(array) {
      for(let i = 0; i < array.length; i++){
        for(let j = 0; j < (array.length - i - 1); j++){
          if(array[j] >= array[j + 1]){
            const temp = array[j];
            array[j] = array[j + 1];
            array[j + 1] = temp;
          }
        }
      }
      return array;
    }
    const sortedArray = bubbleSort([1, 4, 2, 8, 345, 123, 43, 32, 5643, 63, 123, 43, 2, 55, 1, 234, 92]);
    console.log(sortedArray) // Output : [ 1, 1, 2, 2, 4, 8, 32, 43, 43, 55, 63, 92, 123, 123, 234, 345, 5643 ]
```

2. ### Custom Map

We will create a new method[mymap] which allows us to `useArray.mymap()`
In order to use `Array.mymap()` we have to have `mymap()`'s definition in `Array.prototype`.
Now we will be able to run `[1,2,3].mymap();` which will return `undefined`.
map is called with function as an argument inside it. `(eg:[1,2].map(function(val, index, arr){})).` So, our `mymap` function should accept a function as an argument.
The function in the argument should be called for each value in the array with 3 arguments:
`The current element`
`Current element's index`
`Entire Array`
`this` refers to the array on which `mymap` is done. this is the array itself.
Finally, we output the result to a new array and return them.


```javascript
    Array.prototype.mymap = function(callback) {
        const resultArray = [];
        for (let index = 0; index < this.length; index++) {
            resultArray.push(callback(this[index], index, this));
        }
        return resultArray;
    }
```

3. ### Flatten Array

```javascript
    function flattenArr(arrToFlatten, depth) {
        return arrToFlatten.reduce((acc, value) => {
          if (value instanceof Array && depth > 0) {
            return acc.concat(flattenArr(value, depth - 1));
          }
          return acc.concat(value);
        }, []);
    }
```

4. ### Memoisation

```javascript
    function memoize(fn) {
      const cache = new Map();
    
      return (...args) => {
        const key = JSON.stringify(args);
    
        if (cache.has(key)) {
          return cache.get(key);
        }
    
        const result = fn(...args);
        cache.set(key, result);
    
        return result;
      };
    }
```

5. ### Debounce
   
```javascript
    function debounce(fn, timeout = 3000) {
      let timer;
    
      return (...args) => {
        clearTimeout(timer);
        timer = setTimeout(() => {
          fn(...args);
        }, timeout);
      };
    }
    
    function log(text) {
      console.log(text);
    }
    
    const debouncedLog = debounce(log);
```

6. ### Throttle

```javascript
    function throttle(fn, time = 3000) {
      let withinFunctionCall = false;
    
      return (...args) => {
        if (withinFunctionCall) {
          return;
        }
    
        withinFunctionCall = true;
    
        setTimeout(() => {
          fn(...args);
          withinFunctionCall = false;
        }, time);
      };
    }
    
    function log(text) {
      console.log(text);
    }
    
    const throttledLog = throttle(log);
    
    throttledLog('test');
    throttledLog('test');
```

7. ### Promise.prototype.finally()

```javascript
    /**
     * @param {Promise<any>} promise
     * @param {() => void} onFinally
     * @returns {Promise<any>}
     */
    const myFinally = async (promise, onFinally) => {
      try {
        const res = await promise
        await onFinally()
        return res
      } catch (err) {
        await onFinally()
        throw err
      }
    }
```

8. ### Auto-retry Promise on rejection

```javascript
    /**
     * @param {() => Promise<any>} promise
     * @param {number} maximumRetryCount
     * @return {Promise<any>}
     */
    const fetchWithAutoRetry = async (promise, maximumRetryCount) => {
      try {
        return await promise()
      } catch (err) {
        if (maximumRetryCount === 0) {
          return Promise.reject(err)
        }
        return fetchWithAutoRetry(promise, maximumRetryCount - 1)
      }
    }
```

10. ### Implement `Promise.race()`

```javascript
    /**
     * @param {Array<Promise>} promises
     * @return {Promise}
     */
    const race = (promises) => {
      return new Promise((resolve, reject) => {
        promises.forEach(p => p.then(resolve, reject))
      })
    }
```

11. ### Implement `Promise.all()`

```javascript
    /**
     * @param {Array<any>} promises - notice input might have non-Promises
     * @return {Promise<any[]>}
     */
    function all(promises) {
      return new Promise((resolve, reject) => {
        if (!promises.length) {
          resolve([])
        }
    
        const poolResponses = []
        let count = 0
    
        promises.forEach((p, idx) => {
          Promise.resolve(p).then(res => {
            poolResponses[idx] = res
            count++
            if (count === promises.length) {
              resolve(poolResponses)
            }
          }).catch(err => {
            reject(err)
          })
        })
      })
    }
```

12. ### Implement `Promise.allSettled()`

```javascript
    /**
     * @param {Array<any>} promises - notice that input might contains non-promises
     * @return {Promise<Array<{status: 'fulfilled', value: any} | {status: 'rejected', reason: any}>>}
     */
    const allSettled = async (promises) => {
      if (!promises.length) {
        return []
      }
    
      const poolResponses = []
      let counter = 0
    
      for (let i = 0; i < promises.length; i++) {
        try {
          const res = await promises[i]
          poolResponses[i] = {
            status: 'fulfilled',
            value: res
          }
        } catch (err) {
          poolResponses[i] = {
            status: 'rejected',
            reason: err
          }
        } finally {
          counter++
          if (counter === promises.length) {
            return poolResponses
          }
        }
      }
    }
```

13. ### Promise with async support

```javascript
    class Promise {
        constructor(handler) {
            this.status = "pending";
            this.onFulfilledCallbacks = [];
            this.onRejectedCallbacks = [];
    
            const resolve = value => {
                if (this.status === "pending") {
                    this.status = "fulfilled";
                    this.value = value;
                    this.onFulfilledCallbacks.forEach(fn => fn(value));
                }
            };
    
            const reject = value => {
                if (this.status === "pending") {
                    this.status = "rejected";
                    this.value = value;
                    this.onRejectedCallbacks.forEach(fn => fn(value));
                }
            };
    
            try {
                handler(resolve, reject);
            } catch (err) {
                reject(err);
            }
        }
    
        then(onFulfilled, onRejected) {
            if (this.status === "pending") {
                this.onFulfilledCallbacks.push(onFulfilled);
                this.onRejectedCallbacks.push(onRejected);
            }
    
            if (this.status === "fulfilled") {
                onFulfilled(this.value);
            }
    
            if (this.status === "rejected") {
                onRejected(this.value);
            }
        }
    }
    
    // testing code
    const p3 = new Promise((resolve, reject) => {
        setTimeout(() => resolve('resolved!'), 1000);
    });
    p3.then((res) => {
        console.log(res);
    }, (err) => {
        console.log(err);
    });
    
    // ' resolved!'

```


```javascript

```


```javascript

```


```javascript

```


```javascript

```


```javascript

```


```javascript

```


```javascript

```


```javascript

```


```javascript

```


```javascript

```


```javascript

```


```javascript

```


```javascript

```


```javascript

```


```javascript

```


```javascript

```


```javascript

```


```javascript

```


```javascript

```


```javascript

```


```javascript

```


```javascript

```


```javascript

```

```javascript

```





   **[⬆ Back to Top](#table-of-contents)**
