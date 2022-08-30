# 1.快速排序

## 1.1 快速排序的核心思想

1. 选择一个基准元素
2. 所有比这个基准元素小的元素，放到他的左边
3. 所有比这个基准元素大的元素，放到他的右边。把数组拆成两个部分，叫作分治。
4. 数组拆成的两个部分，按照上面的逻辑，再次进行拆分与排序，用到递归





## 1.2 快速排序的基准元素怎么选？

1. 选择第一个元素，是最简单的。但是如果有一个完全逆序的数组，第一个数是最大的，就会造成，每一轮只是找了个基准元素，没有进行“分治”，也就是拆分数组，时间复杂度会退化为O(n^2)。
2. 随机选择一个元素作为基准元素，然后开始分治，是一种办法。mid = Math.floor((start + end) / 2),mid就是基准值，这样是选择中间的元素
3. 但是中间的元素也有可能是，最大的元素或者是最小的。这个就是几率问题，因此快排的`平均时间复杂度是O(n logn)`，最坏是`O(n^2)`
4. O(n * logn)是怎么得出来的呢？
   1. 每一轮交换，所有元素都要遍历一次,所以是 `n`
   2. 有多少轮呢？因为是分治，所以有`logn`轮







## 1.3 快速排序写法1

```js
/* 
时间O(nlogn)
空间O(n)
出错：1. 重复的元素 >=
      2. 删除元素代码注意熟悉怒
*/
function quickSort(nums) {
    debugger
    // 1. 判空 其实也是递归的出口
    if (nums.length === 0) {
        return []
    }
    if (nums.length === 1) {
        return nums
    }
    // 2. 基准元素 - 我们取中间值
    let mid = Math.floor(nums.length / 2)
    // 3. 基准值
    let targetNum = nums[mid]
    // 4. 删除这个元素 其实不删也没关系
    nums.splice(mid, 1)
    // 5. 声明数组变量
    let leftArr = []
    let rightArr = []
    // 6. 遍历 比较大小
    for (let i = 0; i < nums.length; i++) {
        // nums[i] 更大 放进右边 更小 放进左边
        if (nums[i] > targetNum) {
            rightArr.push(nums[i])
        }
        if (nums[i] < targetNum) {
            leftArr.push(nums[i])
        }
    }
    // 在这里进行递归
    // 左边递归 + targetNum + 右边递归
    let newArr = quickSort(leftArr).concat(targetNum, quickSort(rightArr))
    return newArr
}
let test3 = [5, 1, 1, 2, 0, 0]
// console.log(sortArray(test1));
// console.log(sortArray(test2));
console.log(quickSort(test3));
```





## 1.4 快速排序写法2 双路快排

>时间复杂度：O(Nlogn) 为什么两个while 还是Nlogn，因为本质还是两个指针往内部进行遍历，是O(N) 分治的话，就是O(logn) => 合起来就是O(n * logn)
>
>空间：O(n) 递归的复杂度

核心思路：

1. partition里面的代码是关键。而快速排序双路快排，就是，两个指针，left和right。
2. 右指针和基准值比，如果右指针的值更小，就要停止。左指针开始，和基准值比，如果更大，就要停止。易错点：arr[left] <= 基准值，可以是等于哦！少了就会出错。 因为left是从基准值开始的，自己等于自己没关系
3. 如果left >= right，不交换，且最外层的循环也会跳出

```js
function sortArray(nums) {
    // debugger
    let startIndex = 0
    let endIndex = nums.length - 1
    quickSort(nums, startIndex, endIndex)
    return nums
}

function quickSort(nums, startIndex, endIndex) {
    // debugger
    if (startIndex > endIndex) {
        return
    }
    let privotIndex = partition(nums, startIndex, endIndex)
    quickSort(nums, startIndex, privotIndex - 1)
    quickSort(nums, privotIndex + 1, endIndex)
}


function partition(nums, startIndex, endIndex) {
    // debugger
    let left = startIndex
    let right = endIndex
    let privt = nums[startIndex]
    while (left < right) {
        while (left < right && nums[right] > privt) {
            right--
        }
        while (left < right && nums[left] <= privt) {
            left++
        }
        if (left < right) {
            let temp = nums[left]
            nums[left] = nums[right]
            nums[right] = temp
        }
    }
    nums[startIndex] = nums[left]
    nums[left] = privt
    return left
}
```

