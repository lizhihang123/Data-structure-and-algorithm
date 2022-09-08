目录：

1. 冒泡排序是什么
2. 冒泡排序的时间复杂度和空间复杂度
3. 冒泡排序优化1
4. 冒泡排序优化2
5. 鸡尾酒排序
6. 鸡尾酒排序优化1





## 1. 冒泡排序是什么：

概念：每个元素和后一个元素比较 当 `前一个 `大于`后一个` ，就要进行交换。大的元素会交换到后面，小的元素会交换到前面



## 2. 冒泡排序的时间复杂度和空间复杂度

时间：O(N^2) -》因为有两个for循环，外层是多少趟，内层是要从开头走到尾的

空间：O(1) 是原地排序 - 没有创建新的元素

此外，它也是稳定排序。相邻元素的相对位置 不会被破坏



## 3. 冒泡排序写法

```diff
// 1. 交换 
        function swap(arr, i, j) {
            let temp = arr[i]
            arr[i] = arr[j]
            arr[j] = temp
        }
        // 2 排序
        function BubbleSort(nums) {
            let len = nums.length
            // 外面for循环 表示走几趟，走 len-1趟，[5, 3, 2, 1] 走三趟
+外层for循环边界是 len - 1 
+内层for循环是 len - i - 1，这两个总是写错
            for (let i = 0; i < len - 1; i++) {
                // 里面的for循环 表示 每一趟走多少次，长度 - i - 1 而i表示当前是第几趟
                for (let j = 0; j < len - i - 1; j++) {
                    if (nums[j] > nums[j + 1]) {
                        swap(nums, j, j + 1)
                    }
                }
            }
            return nums
        }
        // console.log(BubbleSort([1, 2, 5, 333, 4, 8, 10, 9]));
        // console.log(BubbleSort([5,1,1,2,0,0]));
        console.log(BubbleSort([5, 3, 2, 1]));
```





## 4. 冒泡优化1

有些重复的元素，没有发生变化，但是依然每次都要被遍历。

优化的思路，原本要交换8趟，但是在第4趟，两两元素没有进行交换了，就可以停止不需要进行交换了

1. 外层for循环 每次开始都要让 isSorted改为true
2. 只有交换了，isSorted改为false
3. 判断，如果是true，就break。

```diff
        // 通过一个变量，只要进行了交换，这个变量就是true，否则是false，是true 就return
        function swap(arr, i, j) {
            let temp = arr[i]
            arr[i] = arr[j]
            arr[j] = temp
        }
        // 2 排序
        function BubbleSort(nums) {
            let len = nums.length
            // 外面for循环 表示走几趟，走 len-1趟，[5, 3, 2, 1] 走三趟
            for (let i = 0; i < len - 1; i++) {
+                let isSorted = true
                debugger
                if (i === 3 || i === 4) {
                    debugger
                }
                // 里面的for循环 表示 每一趟走多少次，长度 - i - 1 而i表示当前是第几趟
                for (let j = 0; j < len - i - 1; j++) {
                    if (nums[j] > nums[j + 1]) {
                        swap(nums, j, j + 1)
+                        isSorted = false
                    }
                }
+                if (isSorted) {
                    break
                }
            }
            return nums
        }
        // console.log(BubbleSort([5, 8, 6, 3, 9, 2, 1, 7]));
        console.log(BubbleSort([5, 2, 1, 6, 3, 7, 8, 9]));
```





## 5. 冒泡优化2

```diff
// 优化的思路2：
// 进一步优化：不仅仅是从走8趟 -》 走4趟
// 且 如果有些元素已经是有序的了，就不需要再进行比较了
function swap(arr, i, j) {
    let temp = arr[i]
    arr[i] = arr[j]
    arr[j] = temp
}
// 2 排序
function BubbleSort(nums) {
    let len = nums.length
    // 外面for循环 表示走几趟，走 len-1趟，[5, 3, 2, 1] 走三趟
    let sortBorder = nums.length - 1
    let lastExchangeIndex = 0
    for (let i = 0; i < len - 1; i++) {
        let isSorted = true
        debugger
        // 里面的for循环 表示 每一趟走多少次，长度 - i - 1 而i表示当前是第几趟
        for (let j = 0; j < sortBorder; j++) {
            debugger
            if (nums[j] > nums[j + 1]) {
                swap(nums, j, j + 1)
                isSorted = false
                // 更新为最后一次交换元素的位置
                // 也就是说 如果是 5, 4, 3, 6, 7, 8, 9 => j此时就是3，下一次遍历的范围就是 0, 3
                lastExchangeIndex = j
            }
        }
        // 请注意 边界更新 要等到 “内层for循环结束再去进行”
        sortBorder = lastExchangeIndex
        if (isSorted) {
            break
        }
    }
    return nums
}
// console.log(BubbleSort([5, 8, 6, 3, 9, 2, 1, 7]));
// console.log(BubbleSort([5, 2, 1, 6, 3, 7, 8, 9]));
console.log(BubbleSort([3, 4, 2, 1, 5, 6, 7, 8]));
```







## 6. 鸡尾酒排序

从左到右进行一轮冒泡，从右到左再进行一轮冒泡；

和单纯的冒泡排序的区别是什么呢？

```diff
		function swap(arr, m, n) {
            let temp = arr[m]
            arr[m] = arr[n]
            arr[n] = temp
        }
        function sort(array) {
            // debugger
            let mid = Math.floor(array.length / 2)
            let len = array.length
            // 控制循环的回合数
            for (let i = 0; i < mid; i++) {
                let isSorted = true
                // 从左向右边循环
                for (let j = i; j < len - i - 1; j++) {
                    // debugger
                    if (array[j] > array[j + 1]) {
                        swap(array, j, j + 1)
                        isSorted = false
                    }
                }
                if (isSorted) {
                    break
                }

                isSorted = true
                // 从右边向左边循环
                for (let j = len - i - 1; j > i; j--) {
                    // debugger
                    if (array[j] < array[j - 1]) {
                        swap(array, j, j - 1)
                        isSorted = false
                    }
                }
                if (isSorted) {
                    break
                }
            }
            return array
        }
        let arr = []
        for (let i = 2; i < 1000000; i++) {
            arr.push(i)
        }
        console.time('排序')
        console.log(sort([...arr, 1]));
        console.timeEnd('排序')
```





## 7. 鸡尾酒排序优化1

```diff
// 适合大部分数据都是有序的数组
        // 可以对“有序部分”进行优化
        function swap(arr, m, n) {
            let temp = arr[m]
            arr[m] = arr[n]
            arr[n] = temp
        }
        function sort(array) {
            // debugger
            let mid = Math.floor(array.length / 2)
            let len = array.length
+            let leftBorder = 0
+            let rightBorder = array.length - 1
+            let leftLast = 0
+            let rightLast = 0
            // 控制循环的回合数
            for (let i = 0; i < mid; i++) {
                // debugger
                let isSorted = true
                // 从左向右边循环
+                for (let j = 0; j < rightBorder; j++) {
                    // debugger
                    if (array[j] > array[j + 1]) {
                        swap(array, j, j + 1)
                        isSorted = false
+                        rightLast = j
                    }
                }
+                rightBorder = rightLast
                if (isSorted) {
                    break
                }

                isSorted = true
                // 从右边向左边循环
+                for (let j = len - i - 1; j > leftBorder; j--) {
                    // debugger
                    if (array[j] < array[j - 1]) {
                        swap(array, j, j - 1)
                        isSorted = false
+                        leftLast = j
                    }
                }
+                leftBorder = leftLast

                if (isSorted) {
                    break
                }
            }
            return array
        }
        let arr = []
        for (let i = 2; i < 1000000; i++) {
            arr.push(i)
        }
        console.time('排序')
        console.log(sort([...arr, 1]));
        console.timeEnd('排序')
```

