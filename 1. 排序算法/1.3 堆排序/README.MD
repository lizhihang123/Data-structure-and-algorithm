# 5. 堆排序

目标：

1.知道完全二叉树的概念 知道满二叉树的概念

1.知道二叉堆是什么

2.知道二叉堆的操作：删除 插入 构建 代码如何实现

3.知道堆排序的实现思路 

4.堆排序代码的书写

5.知道堆排序的优化 heapify

6.知道优先队列的概念



>1.二叉堆 ok
>
>2.完全二叉树ok
>
>3.堆排序的代码 删除 插入 ok
>
>但是构建 不ok
>
>4.堆排序的时间复杂度不ok
>
>……



## 1. 基础概念

堆排序一定满足是完全二叉树，一定是最大堆或者是最小堆



**完全二叉树:**

一定是二叉树。树的最后一行，可以不满，但是最后一行的节点一定是靠左边的。倒数第二行开始的节点一定是满的。

区分**满二叉树**

最后一行没有子节点，其余的行都有左右子节点。也就是最后一行的节点都必须是满的

<img src="https://typora-1309613071.cos.ap-shanghai.myqcloud.com/typora/image-20220715081648431.png" alt="image-20220715081648431" style="zoom:50%;" />



**二叉堆**

二叉堆一定是 “完全二叉树”，然后再是“最大堆和最小堆”

最大堆，父节点的值一定比子节点的值要大

最小堆，父节点的值一定比子节点的值要小

![image-20220715081704258](https://typora-1309613071.cos.ap-shanghai.myqcloud.com/typora/image-20220715081704258.png)







## 2. 二叉堆的索引关系

用数组来存储。

如果当前节点是 i，那么请问，父节点的索引是多少？是 parseInt(i / 2)

如果当前节点是 i,请问，左右子节点的值是多少？

​	左子节点的索引, i * 2【i是父节点的索引】

​	右子节点的索引， i * 2 + 1

![image-20220715080407329](https://typora-1309613071.cos.ap-shanghai.myqcloud.com/typora/image-20220715080407329.png)



## 3.  堆的操作

- 添加
- 删除
- 构建二叉堆



### 添加操作

只能在最底部添加元素，然后和父元素比较。假设是最大堆，如果比子元素大，就进行交换，直到”根元素“为止

![image-20220715081429670](https://typora-1309613071.cos.ap-shanghai.myqcloud.com/typora/image-20220715081429670.png)



### 删除思路

删除元素只能删除堆顶的元素。为什么呢？如果中间的元素能够删除，就会乱套了

1.如果要删除堆顶元素，先要把这个元素和最后一个元素进行交换。

2.然后删掉最后一个元素

3.紧接着，顶部元素开始 “自顶向下“的进行比较。假设是最大堆，父元素要和子元素进行比较，如果子元素更大，就进行交换。

<img src="https://typora-1309613071.cos.ap-shanghai.myqcloud.com/typora/image-20220715081536400.png" alt="image-20220715081536400" style="zoom:50%;" />

<img src="https://typora-1309613071.cos.ap-shanghai.myqcloud.com/typora/image-20220715081613977.png" alt="image-20220715081613977" style="zoom:50%;" />



### 如何把数据放到堆里面去

1.创建一个新的“堆实例“

2.你有一个数组数据，遍历数组，值一个个 **插入**到堆里面去

3.假设数组有10个元素，就要对“堆”实例，进行“**删除元素并且取出元素 放到result结果数组里面去**”

## 4. 编码环节

### 走过的坑

1 为什么要设置数组第一个值是null

```diff
为什么要设置一个null?
    和堆的索引 第一个从1开始有关系
为什么堆的索引要从1开始?
和我们判断父节点 子节点有关系
    如果索引从1开始 每个节点的父节点就是parseInt(i / 2)
    每个节点的左子节点的索引就是 i * 2
    右子节点的索引就是 i * 2 + 1
```



2 为什么添加元素一定是在底部添加

其他位置添加不好 会很混乱



3 维持堆顶状态的的while递归的条件搞错

while (this.data[currentIndex] > this.data[parseInt(currentIndex / 2 )] 



4 删除的思路 先交换索引为1的元素和最后一个 再进行向底下冒泡的过程

```diff
                swap(this.data, 1, this.data.length - 1)
                let result = this.data.pop()
                this.shiftDown(1)
                return result
```



5 删除的while 退出循环的条件搞错 只有当前元素小于子节点的值时，才需要退出

```diff
+         while (
                    // 需要 currentIndex的值 小于 左 或者 右子节点
                    this.data[currentIndex] < this.data[currentIndex * 2] ||
                    this.data[currentIndex] < this.data[currentIndex * 2 + 1]
                ) {
```



### 正式编码

```diff
function swap (arr, index1, index2) {
    let temp = arr[index1] 
    arr[index1] = arr[index2] 
    arr[index2] = temp
}
class Heap {
    constructor() {
        // 为什么要设置一个null?
            // 和堆的索引 第一个从1开始有关系
        // 为什么堆的索引要从1开始?
        // 和我们判断父节点 子节点有关系
            // 如果索引从1开始 每个节点的父节点就是parseInt(i / 2)
            // 每个节点的左子节点的索引就是 i * 2
            // 右子节点的索引就是 i * 2 + 1
+                this.data = [null]
    }

    getData () {
        console.log(this.data)
        return this.data
    }

    // 添加
    insert (num) {
        // 最底部添加元素
+                this.data.push(num)
        // 维护顶部的状态
        this.shiftUp(this.data.length - 1)
    }

    // 维持堆顶状态 插入的元素要往上调整时
    shiftUp (currentIndex) {
            // 递归 while循环 子元素 比父元素小 就交换
            // currentIndex > 1 是为了避免第一个元素和null交换
+                while (this.data[currentIndex] > this.data[parseInt(currentIndex / 2 )] 
        && currentIndex > 1) {
            swap(this.data, currentIndex, parseInt(currentIndex / 2))
            // 改变当前指针的索引 为父元素
            currentIndex = parseInt(currentIndex / 2)
        }
    }


    // 删除
    // 不能任意删除 将顶部和最后一个元素进行交换
        // 最后一个元素此时要进行和子元素比较 和较大的那个进行交换
    // 再删除最后一个元素
    extractMax () {
        swap(this.data, 1, this.data.length - 1)
        let result = this.data.pop()
        this.shiftDown(1)
        return result
    }

    // 维护顶层的元素
    shiftDown (currentIndex) {
+         while (
            // 需要 currentIndex的值 小于 左 或者 右子节点
            this.data[currentIndex] < this.data[currentIndex * 2] ||
            this.data[currentIndex] < this.data[currentIndex * 2 + 1]
        ) {
            // 如果右子节点 小于 左子节点
            if (this.data[currentIndex * 2 + 1]  <  this.data[currentIndex * 2]) {
                swap(this.data, currentIndex, currentIndex * 2)
                currentIndex = currentIndex * 2
            } else if (this.data[currentIndex * 2 + 1] > this.data[currentIndex * 2 ]) {
                swap(this.data, currentIndex, currentIndex * 2 + 1)
                currentIndex = currentIndex * 2 + 1
            }
        }
    }
}
let heapData = new Heap()
heapData.insert(1)
heapData.insert(2)
heapData.insert(3)
heapData.insert(4)
heapData.insert(5)
heapData.getData()
heapData.extractMax()
heapData.getData()
```



## 5. 堆排序的正式编码

核心技巧：

```diff
            1. 堆的数据结构 
            2. 不断的把传入的数组里面的值放入堆
               借此生成了最大堆
               堆顶都是最大的元素
            3. 调用删除 取出最大堆的值
               unshift放入数组的前面部分
               输出这个值
```

踩过的坑

```diff
产生了一个死循环：
	if (this.data[currentIndex * 2 + 1]  <  this.data[currentIndex * 2]) {
        swap(this.data, currentIndex, currentIndex * 2)
        currentIndex = currentIndex * 2
+     } else if (this.data[currentIndex * 2 + 1] > this.data[currentIndex * 2]) {
        swap(this.data, currentIndex, currentIndex * 2 + 1)
        currentIndex = currentIndex * 2 + 1
    }
```



正式编码

```diff
function swap (arr, index1, index2) {
            let temp = arr[index1] 
            arr[index1] = arr[index2] 
            arr[index2] = temp
        }
        class Heap {
            constructor() {
                // 为什么要设置一个null?
                //     和堆的索引 第一个从1开始有关系
                // 为什么堆的索引要从1开始?
                // 和我们判断父节点 子节点有关系
                //     如果索引从1开始 每个节点的父节点就是parseInt(i / 2)
                //     每个节点的左子节点的索引就是 i * 2
                //     右子节点的索引就是 i * 2 + 1
                this.data = [null]
            }

            getData () {
                console.log(this.data)
                return this.data
            }

            // 添加
            insert (num) {
                // 最底部添加元素
                this.data.push(num)
                // 维护顶部的状态
                this.shiftUp(this.data.length - 1)
            }

            // 维持堆顶状态 插入的元素要往上调整时
            shiftUp (currentIndex) {
                    // 递归 while循环 子元素 比父元素小 就交换
                    // currentIndex > 1 是为了避免第一个元素和null交换
                while (this.data[currentIndex] > this.data[parseInt(currentIndex / 2 )] 
                && currentIndex > 1) {
                    swap(this.data, currentIndex, parseInt(currentIndex / 2))
                    // 改变当前指针的索引 为父元素
                    currentIndex = parseInt(currentIndex / 2)
                }
            }


            // 删除
            // 不能任意删除 将顶部和最后一个元素进行交换
                // 最后一个元素此时要进行和子元素比较 和较大的那个进行交换
            // 再删除最后一个元素
            extractMax () {
                swap(this.data, 1, this.data.length - 1)
                let result = this.data.pop()
                this.shiftDown(1)
                return result
            }

            // 维护顶层的元素
            shiftDown (currentIndex) {
                while (
                    // 需要 currentIndex的值 小于 左 或者 右子节点
                    this.data[currentIndex] < this.data[currentIndex * 2] ||
                    this.data[currentIndex] < this.data[currentIndex * 2 + 1]
                ) {
                    // 如果右子节点 小于 左子节点
                    if (this.data[currentIndex * 2 + 1]  <  this.data[currentIndex * 2]) {
                        swap(this.data, currentIndex, currentIndex * 2)
                        currentIndex = currentIndex * 2
+                    } else if (this.data[currentIndex * 2 + 1] > this.data[currentIndex * 2]) {
                        swap(this.data, currentIndex, currentIndex * 2 + 1)
                        currentIndex = currentIndex * 2 + 1
                    }
                }
            }
        }
        let initArr = [1, 2, 99, 10, 12, 87]
        function HeapSort (arr) {
            // 1. 堆的数据结构 
            // 2. 不断的把传入的数组里面的值放入堆
            //    借此生成了最大堆
            //    堆顶都是最大的元素
            // 3. 调用删除 取出最大堆的值
            //    unshift放入数组的前面部分
            //    输出这个值
            let heapData = new Heap()
            let resultArr = []
            arr.forEach(item => {
                heapData.insert(item)
            })
            for (let i = 0; i < arr.length; i++) {
+                let result = heapData.extractMax()
                resultArr.unshift(result)
            }
            return resultArr
        }
        console.log(HeapSort(initArr));
```



## 6. heapify()

```js
1 找到数组的 最后一个非叶子节点 最后一个非叶子节点 => parseInt( (数组的长度 - 1) / 2 )
3 为什么要从最后一个非叶子节点出发 因为叶子节点都只有一个节点 都可以理解为是最大堆 因此从非叶子节点触发
  而最后一个非叶子节点 的前面所有的节点 都需要调整 
4 判断此时是向上还是向下去调整 
  是向下 为什么 因此此时堆顶的元素是有问题的 要往下挪? 是上面的元素要往下 => 调用shiftDown即可
5 heapify(arr)的时间复杂度是O(n)
heapify的性能比一个一个遍历去求时间复杂度要高很多
```

![image-20220716100853774](https://typora-1309613071.cos.ap-shanghai.myqcloud.com/typora/image-20220716100853774.png)







## 7. 优先队列的概念

