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

14. ### Currying

```javascript
    const add = (x) => {
      function addY(y) {
        return x + y;
      }
    
      return addY;
    };

    const add = (x) => {
      return (y) => {
        return x + y;
      };
    };

    const add = (x) => (y) => x + y;

    curr(5)(4)(2)()
    curr = (x) => (y) => y ? curr(x+y) : x;
    console.log(result);
```

15. ### Array.prototype.filter

```javascript
    Array.prototype.myFilter = function(cb){
        let empArr = [];
        for(let i = 0; i < this.length; i++){
            if(cb(this[i], i, this)) empArr.push(this[i]);
        }
        return empArr;
    }
    
    const arr = [1, 2, 3, 4, 4, 5];
    filter(arr, (item) => item % 2 !== 0);
```

16. ### Array.prototype.reduce

```javascript
    Array.prototype.myReduce = function(cb, initialVal){
        let accumulator = initialVal;
        for(let i = 0; i < this.length; i++){
            accumulator = accumulator ? cb(accumulator, this[i], i, this) : this[i];
        }
        return accumulator;
    }
    
    const arr = [1, 2 ,3 ,4 ,5];
    
    reduce(arr, (acc, item) => acc + item, 0);
```

17. ### Array.prototype.foreach

```javascript
    Array.prototype.myForEach = function(callback){
       for (var i = 0; i < this.length; i++) {
          callback(this[i], i, this);
       }   
    }
    
    const arrData = [0,1,2,3,4,5,6,7,8,9];
    
    arrData.myForEach((element) => {
       console.log(element)
    })
```

18. ### Polyfil for bind

```javascript
    let obj = {
      name: 'Jack',
    };
    
    let myFunc = function (id, city) {
      console.log(`${this.name}, ${id}, ${city}`);  // id will be undefined
    };
    
    // Accepting any number of arguments passed to myBind
    Function.prototype.myBind = function (obj, ...args) {
      let func = this;
      // Accepting arguments passed to newFunc
      return function (...newArgs) {
        func.apply(obj, [...args, ...newArgs]);
      };
    };
    
    let newFunc = myFunc.myBind(obj, 'a_random_id')
    newFunc('New York') // Jack, a_random_id, New York
```

18. ### Object flatten

```javascript
    // flattenObject.ts
    export const flattenObject = (obj:Object, parentKey?:string) => {
      let result = {};
    
      Object.keys(obj).forEach((key) => {
        const value = obj[key];
        const _key = parentKey ? parentKey + '.' + key : key;
        if (typeof value === 'object') {
          result = { ...result, ...flattenObject(value, _key) };
        } else {
          result[_key] = value;
        }
        console.log(`parentKey: "${parentKey}", _key: "${_key}"`);
      });
    
      return result;
    };
```

19. ### Form a largest number out of array

```javascript
    var largestNumber = function(nums) {
        nums.sort((a,b)=>{
            let sa = a.toString()
            let sb = b.toString()
            return parseInt(sa + sb) > parseInt(sb + sa) ? -1 : 1
        })
        if(nums[0] === 0) return '0'
        
        return nums.join('')
    };
```
20. ### Object with function chaining

```javascript
    let calc = {
        ans: 0,
        add: function(x){
            this.ans = this.ans + x;
            return this;
        },
        subtract: function(y){
            this.ans = this.ans - y;
            return this;
        },
        total: function(){
            return this.ans;
        }
    }
    
    const result = calc.add(19).add(5).subtract(4).subtract(5).total();
    console.log(result);
```
20. ### Subarray with sum equal to k

```javascript
    let arr = [1, 4, 2, 6, 7, 20];
    let k = 20;
    let minSub = [];
    let minLen = arr.length;
    
    function subArray() {
      for (let i = 0; i < arr.length; i++) {
        let subSum = 0;
        for (let j = i; j < arr.length; j++) {
          subSum += arr[j];
          if (subSum >= k && j - i + 1 <= minLen) {
            minLen = j - i + 1;
            minSub = arr.slice(i, j + 1);
            break; // Skip unnecessary calculations
          }
        }
      }
    
      console.log(minSub);
    }
    
    subArray();
```
21. ### Sum of maximum subarray using sliding window

```javascript
    let a = [34, 53, 85, 48, 92, 13, 1]
    k = 3;
    
    var twoSum = function(arr, count) {
        let windowArr = [];
        let maxSum = 0;
        let maxSubArr = [];
        if(arr.length){
            for(let i = 0; i < count; i++){
                windowArr.push(arr[i]);
            }   
            maxSum = sum(windowArr);
            for(let j = count; j < arr.length; j++){
                windowArr.shift();
                windowArr.push(arr[j]);
                if(maxSum < sum(windowArr)){
                    maxSubArr =[...windowArr];
                }
            }
            console.log(maxSubArr);
        }
        
    };
    
    const sum = (arr) => arr.reduce((acc, curr) => acc + curr);
```
22. ### Array.prototype.map - `Polyfill for Map`

```javascript
    Array.prototype.myMap = function(cb){
        let empArr = [];
        for(let i = 0; i < this.length; i++){
            empArr.push(cb(this[i], i, this));
        }
        return empArr;
    }
```
23. ###Best Time to Buy and Sell Stock

```javascript
    var maxProfit = function(prices) {
        let max = 0;
        let l = 0, r=1;
        while( r < prices.length){
            if(prices[l] > prices[r]){
                l = r;
            }
            max = Math.max(max,prices[r] - prices[l]);
            r++
        }
        return max
    };
```
24. ###Longest Substring Without Repeating Characters

```javascript
    var lengthOfLongestSubstring = function (s) {
        let set = new Set();
        let left = 0;
        let maxSize = 0;
    
        if (s.length === 0) return 0;
        if (s.length === 1) return 1;
    
        for (let i = 0; i < s.length; i++) {
    
            while (set.has(s[i])) {
                set.delete(s[left])
                left++;
            }
            set.add(s[i]);
            maxSize = Math.max(maxSize, i - left + 1)
        }
        return maxSize;
    }
```
25. ### Two Sum

```javascript
    var twoSum = function(nums, target) {
        for (let i = 0; i < nums.length; i++){
            for (let k = i + 1; k < nums.length; k++){
                if (nums[k] + nums[i] === target){
                    return [k, i]
                }
            }
        }
    };
    console.log(twoSum([2,7,11,15], 55))
```
27. ### Contains Duplicate

```javascript
    var containsDuplicate = function(nums) {
        let set = new Set(nums);
        return set.size !== nums.length;
    };
```
28. ### Valid Anagram

```javascript
    var isAnagram = (s, t) => {
        const isEqual = s.length === t.length;
        if (!isEqual) return false;
    
        return reorder(s) === reorder(t); /* Time O(N * logN) | Space O(N) */
    };
    
    const reorder = (str) => str
        .split('')                          /* Time O(N)          | Space O(N) */
        .sort((a, b) => a.localeCompare(b)) /* Time O(N * log(N)) | Space O(1 || log(N)) */
        .join(''); 
```
29. ### Valid Palindrome

```javascript
    var isPalindrome = function(s) {
        let newStr = s.toLowerCase().replace(/[^0-9a-z]/g, "");
        let left = 0, right = newStr.length-1;
        
        while(left < right){
            if(newStr[left] !== newStr[right]) return false
            left++
            right--
        }
        return true
    };
```
30. ### Reverse String

```javascript
    var reverseString = function(s) {
        let left = 0
        let right = s.length - 1
        while(left <= right) {
            let temp = s[left]
            s[left] = s[right]
            s[right] = temp
            left++
            right--
        }
        return s;
    };
```
31. ### Remove Duplicates from Sorted List

```javascript
    const deleteDuplicates = function(head){
        let node = head;
        while(node !== null){
            if(node.next !== null){
                if(node.val === node.next.val){
                    node.next = node.next.next
                    continue
                }
            }
            node = node.next
        }
        return head
    };
```
32. ### Fibonacci Number

```javascript
    const fib = (n, a = 0, b = 1) => {
        return n === 0 ? a : fib(n - 1, b, a + b);
    }
```
33. ### Maximum Depth of Binary Tree

```javascript
    /**
     * Definition for a binary tree node.
     * function TreeNode(val, left, right) {
     *     this.val = (val===undefined ? 0 : val)
     *     this.left = (left===undefined ? null : left)
     *     this.right = (right===undefined ? null : right)
     * }
     */
    /**
     * @param {TreeNode} root
     * @return {number}
     */
    var maxDepth = function(root) {
        if(root == null) return 0;
        return 1 + Math.max(maxDepth(root.left), maxDepth(root.right));
    }; 
```
34. ### Length of last word

```javascript
    /**
     * @param {string} s
     * @return {number}
     */
    var lengthOfLastWord = function(s) {
        let arr = s.trim().split(" ");
        return arr[arr.length-1].length;
    };
```
35. ### Top K Frequent Elements

```javascript
    /**
     * @param {number[]} nums
     * @param {number} k
     * @return {number[]}
     */
    var topKFrequent = function(nums, k) {
        let map = {}
        for (let num of nums) {
            if (!map[num]) map[num] = 0
            map[num]++
        }
    
        return [...Object.keys(map)].sort((a,b) => map[b] - map[a]).slice(0,k)
    };
```
36. ### Product of Array except Self

```javascript
    /**
     * @param {number[]} nums
     * @return {number[]}
     */
    function productExceptSelf(nums) {
        const result = [];
        let prefix = 1;
        let postfix = 1;
        
        for (let i = 0; i < nums.length; i++) {
            result[i] = prefix;
            prefix *= nums[i];
        }
        for (let i = nums.length - 2; i >= 0; i--) {
            postfix *= nums[i + 1];
            result[i] *= postfix;
        }
        
        return result;
    };
```

37. ### Valid Paranthesis

```javascript
    var isValid = function(s) {
        // Initialize stack to store the closing brackets expected...
        let stack = [];
        // Traverse each charater in input string...
        for (let idx = 0; idx < s.length; idx++) {
            // If open parentheses are present, push it to stack...
            if (s[idx] == '{') {
                stack.push('}');
            } else if (s[idx] == '[') {
                stack.push(']');
            } else if (s[idx] == '(') {
                stack.push(')');
            }
            // If a close bracket is found, check that it matches the last stored open bracket
            else if (stack.pop() !== s[idx]) {
                return false;
            }
        }
        return !stack.length;
    };
```

38. ### Remove Duplicates from Sorted Array

```javascript
   var removeDuplicates = function(nums) {
        if(nums.length === 0) return 0;
        let i = 0;
    
        for(let j = 1; j < nums.length; j++){
            if(nums[i] !== nums[j]){
                i++;
                nums[i] = nums[j];
            }
        }
        return i+1;
    };
```
39. ### Second Largest of an Array

```javascript
   var removeDuplicates = function(nums) {
        if(nums.length === 0) return 0;
        let i = 0;
    
        for(let j = 1; j < nums.length; j++){
            if(nums[i] !== nums[j]){
                i++;
                nums[i] = nums[j];
            }
        }
        return i+1;
    };
```

40. ### Search for a value in a sorted integer array using Binary Search

```javascript
    function binarySearch(arr, target) {
      let left = 0;
      let right = arr.length - 1;
    
      while (left <= right) {
        const mid = Math.floor((left + right) / 2);
        const midValue = arr[mid];
    
        if (midValue === target) {
          return mid;
        } else if (midValue < target) {
          left = mid + 1;
        } else {
          right = mid - 1;
        }
      }
    
      return -1;
    }
```

41. ### Egg drop puzzle with 2 eggs and N floors

```javascript
    /** https://leetcode.com/problems/egg-drop-with-2-eggs-and-n-floors/
     * @param {number} n
     * @return {number}
     */
    var twoEggDrop = function(n) {
      // Writing down strategy on example 2 we can observe following pattern:
      // Drop at floor:   9   22    34    45    55    64    72    79    85    90    94    97    99    100
      // Diff from prev:      13    12    11    10    9     8     7     6     5     4     3     2     1
      
      // So we have hypothesis algorithm
      // That is, `n` minus `d` until `result(n)` is smaller than `d`, where `d` start at 1 and increment by 1 for each iteration. 
      // If `result(n)` is 0, subtract 1, else return the result
      
      let d = 1;
      
      while (n > d) {
        n -= d;
        d++;
      }
      
      if (n == 0) {
        d--;
      }
      
      return d;
    };
```

42. ### Remove duplicate from Array of Objects

```javascript
    let inputArr = [
      { name: 'abc', age: 21 },
      { name: 'abc', age: 21 },
      { name: 'def', age: 30 }
    ];
    
    let uniqueArr = inputArr.filter((v, i, a) => a.findIndex(v2 => ['name', 'age'].every(k => v2[k] === v[k])) === i);
    
    console.log(uniqueArr);

```

43. ### Print 1 to 5 using var and let using settimeout and closure

```javascript
    // Using Let
    for (var i = 1; i <= 5; i++) {
      (function (j) {
        setTimeout(() => {
          console.log(j);
        },1000);
      })(i);
    }

    // Using Let
    for (let i = 1; i <= 5; i++) {
      setTimeout(() => {
        console.log(i);
      }, 1000);
    }

```
44. ### Currying till n number

```javascript
    const currySum = (acc = 0) => (n) => {
      if (n === undefined) {
        return acc;
      }
      return currySum(acc + n);
    };
    
    // Usage example:
    const sum = currySum();
    
    console.log(sum);         // Outputs: 0
    console.log(sum(1));      // Outputs: 1
    console.log(sum(1)(2));   // Outputs: 3
    console.log(sum(1)(2)(3)); // Outputs: 6
```





   **[⬆ Back to Top](#table-of-contents)**
