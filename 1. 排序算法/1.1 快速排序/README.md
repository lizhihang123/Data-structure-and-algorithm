# 1.快速排序

## 1.1 快速排序的核心思想

1. 选择一个基准元素
2. 所有比这个基准元素小的元素，放到他的左边
3. 所有比这个基准元素大的元素，放到他的右边。把数组拆成两个部分，叫作分治。
4. 数组拆成的两个部分，按照上面的逻辑，再次进行拆分与排序







## 1.2 快速排序的基准元素怎么选？

1. 选择第一个元素，是最简单的。但是如果有一个完全逆序的数组，第一个数是最大的，就会造成，每一轮只是找了个基准元素，没有进行“分治”，也就是拆分数组，时间复杂度会退化为O(n^2)。
2. 随机选择一个元素作为基准元素，然后开始分治，是一种办法。mid = Math.floor((start + end) / 2),mid就是基准值，这样是选择中间的元素
3. 但是中间的元素也有可能是，最大的元素或者是最小的。这个就是几率问题，因此快排的`平均时间复杂度是O(n logn)`，最坏是`O(n^2)`
4. O(n * logn)是怎么得出来的呢？
   1. 每一轮交换，所有元素都要遍历一次,所以是 `n`
   2. 有多少轮呢？因为是分治，所以有`logn`轮

5. **原地**

   判断是不是原地排序：要看有没有使用额外的数组空间

   快速排序是原地排序

6. **稳定**

   如果存在a[i] = a[j] 排序前，a[i]在a[j]的前面,排序后a[j]在a[i]的前面，就不是稳定的排序

   快速排序不是稳定的排序。比如[4,7,6,5,8,3,1，1,]后面两个1的相对位置会发生改变

   

## 1.3 快速排序写法1

>时间复杂度：O(Nlogn) 为什么两个while 还是Nlogn，因为本质还是两个指针往内部进行遍历，是O(N) 分治的话，就是O(logn) => 合起来就是O(n * logn)
>
>空间：O(n) 递归的复杂度

核心思路：选定一个基准值，利用两个指针进行遍历，比基准值小的在一边，比基准值大的在另外一边

```js
function sortArray(nums) {
    // 1.初始化
    let startIndex = 0
    let endIndex = nums.length - 1
    quickSort(nums, startIndex, endIndex)
    return nums
}

// 分治 + 递归
function quickSort(nums, startIndex, endIndex) {
    // 必须有退出条件
    if (startIndex >= endIndex) {
        return
    }
    // 2.startIndex, endIndex为 0和nums.length时 进行第一波循环 得到变化的第一个基准值
    let mid = partition(nums, startIndex, endIndex)
    // 3.递归当前函数 通过基准值进行切分 注意基准值不再进入排序
    //   递归两次的目的是 分治 
    quickSort(nums, startIndex, mid - 1)
    quickSort(nums, mid + 1, endIndex)
}

// 真正的排序在这里
function partition(nums, startIndex, endIndex) {
    // 4. 存储基准索引的“值”
    let pivot = nums[startIndex]
    let left = startIndex
    let right = endIndex
    let temp //交换变量用
    // 5. 只要left指针比right指针小 就一直进行循环
    while (left < right) {
        // 右边的值比基准值更大 继续while
        while (left < right && nums[right] > pivot) {
            right--
        }
        // 左边的值比基准值更大 继续while
        while (left < right && nums[left] <= pivot) {
            left++
        }
        // 如果说左右指针都停了 就进行元素交换(交换的前提是left<right)
        if (left < right) {
            temp = nums[left]
            nums[left] = nums[right]
            nums[right] = temp
        }
    }
    // 把left指针停留的位置的值赋值给startIndex的位置
    nums[startIndex] = nums[left]
    // left指针的值等于基准值pivot
    nums[left] = pivot
    // left是下一个基准值
    return left
}
console.log(sortArray([4, 1, 2, 3, 5, 6, 8, 7]));
```



## 错误标明版

```js
function sortArray(nums) {
    let startIndex = 0
    // ! nums.length 写成 nums.length 结果就会报错
    let endIndex = nums.length - 1
    quickSort(nums, startIndex, endIndex)
    return nums
}

// 分治 + 递归
function quickSort(nums, startIndex, endIndex) {
    if (startIndex >= endIndex) {
        return
    }
    // !partition(nums, startIndex, endIndex) -> 写成了 partition(startIndex, endIndex)
    let mid = partition(nums, startIndex, endIndex)
    // !partition(nums, 0, mid - 1) 应该是 partition(nums, 0, mid - 1)
    quickSort(nums, startIndex, mid - 1)
    quickSort(nums, mid + 1, endIndex)
}

// 真正的排序在这里
function partition(nums, startIndex, endIndex) {
    let pivot = nums[startIndex]
    let left = startIndex
    let right = endIndex
    while (left < right) {
        // !漏掉了left < right
        while (left < right && nums[right] > pivot) {
            right--
        }
        // !left应该是小于等于pivot 少了等于就报错
        while (left < right && nums[left] <= pivot) {
            left++
        }
        // left可以等于right
        if (left < right) {
            let temp = nums[left]
            nums[left] = nums[right]
            nums[right] = temp
        }
    }
    nums[startIndex] = nums[left]
    nums[left] = pivot
    // !忘记return left的值
    return left
}
```



## 1.4 单边快排

单边快排，sortArray函数和quickSort的函数的写法和前面的是一样的。

唯一不同的是，使用了mark指针来标识，比基准值小的元素。

```js
function sortArray(nums) {
    let startIndex = 0
    let endIndex = nums.length - 1
    quickSort(nums, startIndex, endIndex)
    return nums
}
function quickSort(nums, startIndex, endIndex) {
    if (startIndex >= endIndex) {
        return
    }
    let privotIndex = partition(nums, startIndex, endIndex)
    quickSort(nums, startIndex, privotIndex - 1)
    quickSort(nums, privotIndex + 1, endIndex)
}

function partition(arr, startIndex, endIndex) {
    let mark = startIndex
    let pivot = arr[startIndex]
    // ! i<=endIndex注意要加等于号
    for (let i = startIndex + 1; i <= endIndex; i++) {
        if (arr[i] < pivot) {
            mark++ // mark停在那里的值是一定要比基准值小的
            let p = arr[mark]
            arr[mark] = arr[i]
            arr[i] = p
        }
    }
    // 为什么是下面这样写？
    // arr[mark]最后表示的是所以比基准值小的数的界限
    // 接下来要交换基准值交换完之后左边所有的数都是比基准值小的
    arr[startIndex] = arr[mark]
    arr[mark] = pivot
    return mark
}
console.log(sortArray([4, 7, 3, 5, 6, 2, 8, 1]));
```

