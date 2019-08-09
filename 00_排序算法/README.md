# 概况

<img  src="./images/00_s1.jpg">

> **稳定型**：保证排序前后两个相等的数的相对顺序不变
>    - 稳定：如果a原本在b前面，且a=b，排序之后a仍然在b的前面
>    - 不稳定：如果a原本在b的前面，且a=b，排序之后a可能会出现在b的后面

> **排序方式**
>    - 内部排序：待排序记录存放在计算机随机存储器中（说简单点，就是内存）进行的排序过程
 >   - 外部排序：待排序记录的数量很大，以致于内存不能一次容纳全部记录，所以在排序过程中需要对外存进行访问的排序过程
    
`Swap` 函数（交换数组中某两个位置的值）
```java
static void swap(int[] arr,int i,int j) { 
    int temp = arr[i];
    arr[i] = arr[j]; 
    arr[j] = temp;
}
```
或者
```java
static void swap(int[] arr,int i,int j) {
    arr[i] = arr[i] ^ arr[j];
    arr[j] = arr[i] ^ arr[j];
    arr[i] = arr[i] ^ arr[j];
}
```
## 一、冒泡排序(BubbleSort) 
> 两个数比较大小，较大的数下沉，较小的数冒起来。

<img src="./images/00_s2.gif">

### 平均时间复杂度 O(n<sup>2</sup>)

```java
public static void bubbleSort(int[] arr) {
    if (arr == null || arr.length == 0)
        return;
    //通过与相邻元素的比较和交换来把小的数交换到最前面（动图相反过程）
    //for (int i = 0; i < arr.length - 1; i++) 
        // for (int j = arr.length - 1; j > i; j--)
        //     if (arr[j] < arr[j - 1])
        //         swap(arr, j, j - 1);
    for (int i = 0; i < arr.length; i++) 
        for (int j = 0; j < arr.length - i - 1; j++)
            if (arr[j + 1] < arr[j])
                swap(arr, j, j + 1);
}

```
### 优化方案

**问题**：
```数据的顺序排好之后，冒泡算法仍然会继续进行下一轮的比较，直到 arr.length-1 次，后面的比较没有意义的。```

**方案**：
```设置标志位 flag，如果发生了交换 flag 设置为 true；如果没有交换就设置为 false。这样当一轮比较结束后如果 flag 仍为 false，即：这一轮没有发生交换，说明数据的顺序已经排好，没有必要继续进行下去。```
```java
public static void bubbleSort(int[] arr) {
    if (arr == null || arr.length == 0)
        return;
    boolean flag;
    //通过与相邻元素的比较和交换来把小的数交换到最前面（动图相反过程）
    for (int i = 0; i < arr.length - 1; i++) {
        flag = false;
        for (int j = arr.length - 1; j > i; j--) {
            if (arr[j] < arr[j - 1]) {
                swap(arr, j, j - 1);
                flag = true;
            }
        }
        if (!flag)
            break;
    }
}
```

## 二、选择排序(SelctionSort)
> 首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置，然后，再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。以此类推，直到所有元素均排序完毕。

![SelectionSort](./images/00_s3.gif)

### 平均时间复杂度 O(n<sup>2</sup>)

```java
public static void selectionSort(int[] arr) {
    if (arr == null || arr.length == 0)
        return;
    //只需要比较 n-1 次
    for (int i = 0; i < arr.length - 1; i++) {
        int minIndex = i;
        // 注意从 i+1 开始比较，minIndex 默认为 i,没必要与自身进行比较
        for (int j = i + 1; j < arr.length; j++) {
            if (arr[j] < arr[minIndex])
                minIndex = j;
        }
        //如果 minIndex 不为 i，说明找到了最小值，交换
        if (minIndex != i)
            swap(arr, i, minIndex);
    }
}
```

## 三、插入排序(Insertion Sort)
> 在要排序的一组数中，假定前n-1个数已经排好序，现在将第n个数插到前面的有序数列中，使得这n个数也是排好顺序的。如此反复循环，直到全部排好顺序。

![InsertionSort](./images/00_s4.gif)

### 平均时间复杂度 O(n<sup>2</sup>)

```java
public static void insertSort(int[] arr) {
    if (arr == null || arr.length == 0)
        return;
    for (int i = 1; i < arr.length; i++) {
        int j = i;
        int target = arr[i];
        // 后移 注意 j=i
        while (j > 0 && target < arr[j - 1]) {
            arr[j] = arr[j - 1];
            j--;
        }
        // 插入
        arr[j] = target;
    }
}
```
另一种简便的写法(直接交换值，不需要插入)
```java
 public static void insertSort(int[] arr) {
    if (arr == null || arr.length == 0)
        return;
    for (int i = 0; i < arr.length - 1; i++) {
        for (int j = i; j > 0 && arr[j] < arr[j - 1]; j--)
            swap(arr, j - 1, j);
    }
}
```

## 四. 希尔排序(Shell Sort)
> 在要排序的一组数中，根据某一增量分为若干子序列，并对子序列分别进行插入排序。然后逐渐将增量减小,并重复上述过程。直至增量为1,此时数据序列基本有序,最后进行插入排序。

![ShellSort](./images/00_s5.jpg)


```java
public static void shellSort(int[] arr) {
    int len = arr.length;
    int temp, gap = len / 2;
    while (gap > 0) {
        for (int i = gap; i < len; i++) {
            temp = arr[i];
            // preIndex 表示上一个节点 
            int preIndex = i - gap;
            // 插入排序 （当上节点的值一直比 temp(arr[i]) 大，将上节点整体后移，最后中间空缺值就是为 temp）
            while (preIndex >= 0 && arr[preIndex] > temp) {
                arr[preIndex + gap] = arr[preIndex];
                preIndex -= gap;
            }
            // 注意 preIndex 值的变化
            arr[preIndex + gap] = temp;
        }
        gap /= 2;
    }
}
```