# 排序算法



```c++
void swap(vector<int>&arr, int i, int j)
{
    int temp = arr[i];
    arr[i] = arr[j];
    arr[j] = temp;
}
```

### 冒泡

基本思想：对相邻的元素进行两两比较，顺序相反则进行交换，这样，每一趟会将最小或最大的元素“浮”到顶端，最终达到完全有序

```c++
void bubbleSort(vector<int> &arr)
{
    int len = (int)arr.size();
    for (int i = 0; i < len; i++) {
        for (int j = i + 1; j < len; j++) {
            if (arr[i] > arr[j]) {
                swap(arr, i, j);
            }
        }
    }
}
```



### 选择

基本思想：每一趟从待排序的数据元素中选择最小（或最大）的一个元素作为首元素，直到所有元素排完为止。在对几乎已经排序的数据操作时，效率高，可以达到线性时间，但是其每次只能将数据移动一位。

```c++
void selectSort(vector<int> &arr)
{
    int len = (int)arr.size();
    for (int i = 0; i < len - 1; i++) {
        int min = i;
        for (int j = i+1; j < len; j++) {
            if (arr[j] < arr[min]) {
                min = j;
            }
        }
        if (min != i) {
            swap(arr, i, min);
        }
    }
}
```

### 插入

基本思想：每一步将一个待排序的记录，插入到前面已经排好序的有序序列中去，直到插完所有元素为止。

```c++
void insertSort(vector<int> &arr)
{
    int len = (int)arr.size();
    for (int i = 1; i < len; i++) {
        int temp = arr[i];
        int j = i;
        for (; j > 0 &&  arr[j] < arr[j-1]; j--) {
            arr[j] = arr[j-1];
        }
        arr[j] = temp;
    }
}
```



### 希尔排序

基本思想：将比较的全部元素分为几个区域来提升插入排序的性能。这样可以让一个元素一次性地朝最终位置前进一大步。然后算法再取越来越小的步长进行排序，算法的最后一步就是普通的插入排序，但是到了这步，需排序的数据几乎是已排好的了，此时插入排序较快

### 快速排序

基本思想：一趟排序中将待排序的序列分割成两组，其中一部分记录的关键字均小于另一部分。然后分别对这两组继续进行排序，以使整个序列有序。

```c++
// 三数中值分割，并将分割元素与最后元素交换，返回分割元素
int median3(vector<int>& arr, int left, int right)
{
    int mid = (left+right)/2;
    if (arr[left] > arr[mid]) {
        swap(arr, left, mid);
    }
    if (arr[left] > arr[right]) {
        swap(arr, left, right);
    }
    if (arr[mid] > arr[right]) {
        swap(arr, mid, right);
    }
    swap(arr, mid, right-1);
    return arr[right-1];
}

void qSort(vector<int> arr, int left, int right)
{
    int i, j, pivot;
    int threshold = 3;
    if (left + threshold <= right) {
        pivot = median3(arr, left, right);
        i = left;
        j = right-1;
        for (; ; ) {
            while (arr[++i] < pivot) { }
            while (arr[--j] > pivot) { }
            if (i < j) {
                swap(arr, i, j);
            } else {
                break;
            }
        }
        // 最后要记得把枢纽值跟arr[i]交换
        swap(arr, i, right - 1);
        qSort(arr, left, i-1);
        qSort(arr, i+1, right);
    } else {
        // 插入排序
        
    }
}
```



### 归并排序



### 堆排序

```c++
void adjustHeap(vector<int>& arr, int i, int len)
{
    int temp = arr[i];
    // 下沉
    for (int k = i*2+1; k < len; i++) {
        if (k+1 < len && arr[k] < arr[k+1]) {//如果左子结点小于右子结点，k指向右子结点
            k++;
        }
        if (arr[k] > temp) { //如果子节点大于父节点，将子节点值赋给父节点（不用进行交换）
            arr[i] = arr[k];
            i = k;
        } else {
            break;
        }
    }
    arr[i] = temp;
}

void heapSort(vector<int>& arr)
{
    int len = (int)arr.size();
    for (int i = len / 2 - 1; i >= 0; i--) {
        //从第一个非叶子结点从下至上，从右至左调整结构
        adjustHeap(arr, i, len);
    }
    for (int j = len-1; j >= 0; j--) {
        swap(arr, 0, j);
        adjustHeap(arr, 0, j);
    }
}
```



### 比较



| 算法 | 平均  | 最好  | 最坏  | memory | 稳定性 |
| ---- | ----- | ----- | ----- | ------ | ------ |
| 插入 | n2    | n     | n2    | 1      | 稳定   |
| 选择 | n2    | n2    | n2    | 1      | 稳定   |
| 冒泡 | n2    | n2    | n2    | 1      | 稳定   |
| 希尔 |       |       |       | 1      | 不稳定 |
| 归并 | nlogn | nlogn | nlogn | n      | 稳定   |
| 快速 | nlogn | nlogn | n2    | logn   | 不稳定 |
| 堆   | nlogn | nlogn | nlogn | 1      | 不稳定 |
|      |       |       |       |        |        |
