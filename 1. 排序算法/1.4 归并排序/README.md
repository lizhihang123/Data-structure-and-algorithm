## 1.概念与逻辑

概念：归并排序最核心的逻辑就是，把数字先归类，每个类别里面各自排序。然后再把类别组合起来，一边组合一边排序。

时间复杂度:O(nlogn)

空间复杂度：O(n)

是不是原地排序：不是

是不是稳定排序：是稳定的

<img src="https://typora-1309613071.cos.ap-shanghai.myqcloud.com/typora/image-20220919094942239.png" alt="image-20220919094942239" style="zoom:50%;" />

<img src="https://typora-1309613071.cos.ap-shanghai.myqcloud.com/typora/image-20220919095817903.png" alt="image-20220919095817903" style="zoom:50%;" />



判断是不是稳定排序。因为如果是相等，还是把左边的元素先放进结果数组，那么相对位置还是原来的

<img src="https://typora-1309613071.cos.ap-shanghai.myqcloud.com/typora/image-20220919100024293.png" alt="image-20220919100024293" style="zoom:50%;" />

## 2.代码

为了方便阅读，我把下面的代码拆成了3段，分别是3个函数

```js
// 归并排序的思路
// 先归类 -> 每个类别里面排序 -> 再合并类别的进行排序

// 主函数
function sortArray(arr) {
    debugger
    // 1.调用_mergeSort函数
    // 返回
    _mergeSort(arr, 0, arr.length - 1)
    return arr
}
```



```js
// 分类的函数
function _mergeSort(arr, start, end) {
    debugger
    // 递归退出条件
    // 2.求mid值(分组界限)
    // 3.进行递归
    // 4.调用合并排序函数
    if (start >= end) {
        return
    }
    let mid = parseInt((start + end) / 2)
    _mergeSort(arr, start, mid)
    _mergeSort(arr, mid + 1, end)
    _merge(arr, start, mid, end)
}
```



```js
// 排序合并的函数
function _merge(arr, start, mid, end) {
    debugger
    // 5.初始化p1,p2(不同分组的指针),p(新的结果数组的指针),tempArr(新的结果数组)
    // 6.while循环判断p1和p2是否越界，没有越界，就把小的值放进结果数组，有一个越界，就要退出while循环
    // 7.判断p1小于等于mid或者p2仍旧小于等于end，有的话依次放入结果数组
    // 8.遍历结果数组 去替换原始arr数组的值
    let p1 = start,
        p2 = mid + 1,
        p = 0, // 因为结果数组每次都是空的
        tempArr = []
    while ((p1 <= mid) && (p2 <= end)) {
        // 思考，这里能否写等于号呢？
        if (arr[p1] <= arr[p2]) {
            tempArr[p++] = arr[p1++]
        } else {
            tempArr[p++] = arr[p2++]
        }
    }
    while (p1 <= mid) {
        tempArr[p++] = arr[p1++]
    }
    while (p2 <= end) {
        tempArr[p++] = arr[p2++]
    }
    for (let i = 0; i < tempArr.length; i++) {
        arr[i + start] = tempArr[i]
    }
}
let initArr = [3, 1, 6, 5, 7, 2, 9, 8]
console.log(sortArray(initArr));
```

