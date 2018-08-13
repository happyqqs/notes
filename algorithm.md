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

递归实现：

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



非递归实现：

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



### 桶排序



### 基数排序



### 比较



| 算法     | 平均  | 最好  | 最坏  | memory | 稳定性 |
| -------- | ----- | ----- | ----- | ------ | ------ |
| 插入     | n2    | n     | n2    | 1      | 稳定   |
| 选择     | n2    | n2    | n2    | 1      | 稳定   |
| 冒泡     | n2    | n2    | n2    | 1      | 稳定   |
| 希尔     |       | nlogn |       | 1      | 不稳定 |
| 归并     | nlogn | nlogn | nlogn | n      | 稳定   |
| 快速     | nlogn | nlogn | n2    | logn   | 不稳定 |
| 堆       | nlogn | nlogn | nlogn | 1      | 不稳定 |
| 桶排序   |       |       |       |        | 稳定   |
| 基数排序 |       |       |       |        |        |



# 二分查找算法(数字在排序数组中出现的次数)

```c++
class Solution {
public:
    int GetNumberOfK(vector<int> data ,int k) {
        int count = (int)data.size();
        if (count == 0 || data[0] > k || data[count -1] < k) {
            return 0;
        }
        // [low,up) 左闭右开区间
        int low = lowBound(data, k);
        int up = upBound(data, k);
         
        return up-low;
    }
     // 找上届
    int upBound(vector<int> data, int k) {
        int count = (int)data.size();
        if (count == 0 || data[count-1] <= k) {
            return count;
        }
        int l = 0, r = count-1;
        int mid;
        while (l < r) {
            mid = (l+r+1) / 2;
            if (data[mid] > k) {
                r = mid-1;
            } else if (data[mid] == k) {
                l = mid;
            } else {
                l = mid+1;
            }
        }
        return r+1;
    }
    // 找下届
    int lowBound(vector<int> data, int k) {
        int count = (int)data.size();
        if (count == 0 || data[0] > k) {
            return -1;
        }
        int l = 0, r = count-1;
        int mid;
        while (l < r) {
            mid = (l+r-1) / 2;
            if (data[mid] > k) {
                r = mid-1;
            } else if (data[mid] == k) {
                r = mid;
            } else {
                l = mid+1;
            }
        }
        return l;
    }
};
```

# 全排列

### 递归

核心思想：**从第一个数字起每个数分别与它后面的数字交换**

去重：**从第一个数字起每个数分别与它后面非重复出现的数字交换。**

```c++
class Solution {
public:
    vector<vector<int>> res;
    vector<vector<int>> permuteUnique(vector<int>& nums) {
        def(nums, 0, nums.size());
        return res;
    }
    
    void def(vector<int> &nums, int begin, int len) {
        if (begin == len) {
            vector<int> list(len,0);
            for (int i = 0; i < len; i++) {
                list[i] = nums[i];
            }
            res.push_back(list);
            return;
        }
        for (int i = begin; i < len; i++) {
            bool flag = false;
            for (int j = begin; j < i; j++) {
                if (nums[j] == nums[i]) {
                    flag = true;
                }
            }
            if (flag) {
                continue;
            }
            int temp = nums[begin];
            nums[begin] = nums[i];
            nums[i] = temp;
            def(nums, begin+1, len);
            nums[i] = nums[begin];
            nums[begin] = temp;
        }
    }
};
```



### 非递归

要考虑全排列的非递归实现，先来考虑如何计算字符串的下一个排列。如"1234"的下一个排列就是"1243"。只要对字符串反复求出下一个排列，全排列的也就迎刃而解了。

如何计算字符串的下一个排列了？来考虑"926520"这个字符串，我们从后向前找第一双相邻的递增数字，"20"、"52"都是非递增的，"26 "即满足要求，称前一个数字2为替换数，替换数的下标称为替换点，再从后面找一个比替换数大的最小数（这个数必然存在），0、2都不行，5可以，将5和2交换得到"956220"，然后再将替换点后的字符串"6220"颠倒即得到"950226"。

**由后向前找替换数和替换点，然后由后向前找第一个比替换数大的数与替换数交换，最后颠倒替换点后的所有数据。**

```c++
class Solution {
public:
    
    vector<vector<int>> permuteUnique(vector<int>& nums) {
        vector<vector<int>> res;
        int len = nums.size();
        if (len == 0) {
            return res;
        }
        sort(nums.begin(), nums.end());
        do {
            vector<int> list(len, 0);
            for (int i = 0; i < len; i++) {
                list[i] = nums[i];
            }
            res.push_back(list);
        }while(nextPermutation(nums, len));
        return res;
    }
    
    void swapStr(vector<int> &s, int i, int j) {
        int tmp = s[i];
        s[i] = s[j];
        s[j] = tmp;
    }
    
    void reverse(vector<int> &s,int from, int to) {
        while(from < to) {
            int tmp = s[from];
            s[from]= s[to];
            s[to] = tmp;
            from ++;
            to --;
        }
    }
    
    bool nextPermutation(vector<int> &s, int n) {
        int i, j, find;
        i = n-1;
        while (i != 0)
        {
            j = i;
            --i;
            if (s[i] < s[j])
            {
                find = n-1;
                while (s[find] <= s[i])
                    --find;
                swapStr(s, find, i);
                reverse(s, j, n-1);
                return true;
            }
        }
        reverse(s, i, n-1);
        return false;
    }

};
```



# 无向图连通块个数

### DFS

利用DFS来做，思路是给每个节点都有个flag标记其是否被访问过，对于一个未访问过的节点，我们将结果自增1，因为这肯定是一个新的连通区域，然后我们通过邻接链表来遍历与其相邻的节点，并将他们都标记成已访问过，遍历完所有的连通节点后我们继续寻找下一个未访问过的节点，以此类推直至所有的节点都被访问过了，那么此时我们也就求出来了连通区域的个数。

```c++
class Solution {
public:
    int countComponents(int n, vector<pair<int, int> >& edges) {
        int res = 0;
        vector<vector<int> > g(n);
        vector<bool> v(n, false);
        for (auto a : edges) {
            g[a.first].push_back(a.second);
            g[a.second].push_back(a.first);
        }
        for (int i = 0; i < n; ++i) {
            if (!v[i]) {
                ++res;
                dfs(g, v, i);
            }
        }
        return res;
    }
    void dfs(vector<vector<int> > &g, vector<bool> &v, int i) {
        if (v[i]) return;
        v[i] = true;
        for (int j = 0; j < g[i].size(); ++j) {
            dfs(g, v, g[i][j]);
        }
    }
};
```



### 并查集

```c++
class Solution {
public:
    int countComponents(int n, vector<pair<int, int> >& edges) {
        int res = n;
        vector<int> root(n);
        for (int i = 0; i < n; ++i) root[i] = i;
        for (auto a : edges) {
            int x = find(root, a.first), y = find(root, a.second);
            if (x != y) {
                --res;
                root[y] = x;
            }
        }
        return res;
    }
    int find(vector<int> &root, int i) {
        while (root[i] != i) i = root[i];
        return i;
    }
};
```



# 二叉树的非递归遍历

### 前序

根据前序遍历访问的顺序，优先访问根结点，然后再分别访问左孩子和右孩子。即对于任一结点，其可看做是根结点，因此可以直接访问，访问完之后，若其左孩子不为空，按相同规则访问它的左子树；当访问其左子树时，再访问它的右子树。因此其处理过程如下：

​     对于任一结点P：

​     1)访问结点P，并将结点P入栈;

​     2)判断结点P的左孩子是否为空，若为空，则取栈顶结点并进行出栈操作，并将栈顶结点的右孩子置为当前的结点P，循环至1);若不为空，则将P的左孩子置为当前的结点P;

​     3)直到P为NULL并且栈为空，则遍历结束。

```c++
void preOrder(TreeNode* pRoot)
{
    stack<TreeNode *> tmp;
    TreeNode *node = pRoot;
    while (node != nullptr || !tmp.empty()) {
        while (node!=nullptr) {
            tmp.push(node);
            cout<<node->val<<endl;
            node = node->left;
        }
        
        if (!tmp.empty()) {
            node = tmp.top();
            tmp.pop();
            node = node->right;
        }
    }
}
```



### 中序

```c++
void inOrder(TreeNode* pRoot)
{
    stack<TreeNode *> tmp;
    TreeNode *node = pRoot;
    while (node != nullptr || !tmp.empty()) {
        while (node!=nullptr) {
            tmp.push(node);
            node = node->left;
        }
        
        if (!tmp.empty()) {
            node = tmp.top();
            tmp.pop();
            cout<<node->val<<endl;
            node = node->right;
        }
    }
}
```



### 后序

思路1

对于任一结点P，将其入栈，然后沿其左子树一直往下搜索，直到搜索到没有左孩子的结点，此时该结点出现在栈顶，但是此时不能将其出栈并访问，因此其右孩子还为被访问。所以接下来按照相同的规则对其右子树进行相同的处理，当访问完其右孩子时，该结点又出现在栈顶，此时可以将其出栈并访问。这样就保证了正确的访问顺序。可以看出，在这个过程中，每个结点都两次出现在栈顶，只有在第二次出现在栈顶时，才能访问它。因此需要多设置一个变量标识该结点是否是第一次出现在栈顶

```c++
void postOrder(TreeNode* pRoot)
{
    stack<TreeNode *> s;
    TreeNode *p = pRoot;
    TreeNode *temp;
    map<TreeNode*, bool> flags;
    while(p!=NULL||!s.empty()) {
        while(p!=NULL) {
            s.push(p);
            flags[p] = true;
            p=p->left;
        }
        if(!s.empty()) {
            temp=s.top();
            s.pop();
            if(flags[temp] == true)     //表示是第一次出现在栈顶
            {
                flags[temp]=false;
                s.push(temp);
                p=temp->right;
            } else {
                cout<<temp->val<<endl;
                p=NULL;
            }
        }
    }
}

```



思路2

要保证根结点在左孩子和右孩子访问之后才能访问，因此对于任一结点P，先将其入栈。如果P不存在左孩子和右孩子，则可以直接访问它；或者P存在左孩子或者右孩子，但是其左孩子和右孩子都已被访问过了，则同样可以直接访问该结点。若非上述两种情况，则将P的右孩子和左孩子依次入栈，这样就保证了每次取栈顶元素的时候，左孩子在右孩子前面被访问，左孩子和右孩子都在根结点前面被访问。

```c++
void postOrder(TreeNode* pRoot)
{
    stack<TreeNode *> tmp;
    if (pRoot != nullptr) {
        tmp.push(pRoot);
    }
    TreeNode *pre = nullptr;
    while (!tmp.empty()) {
        TreeNode *p = tmp.top();
        if ((p->left == nullptr && p->right == nullptr) || (pre != nullptr && (pre == p->left || pre == p->right))) {
            cout<<p->val<<endl;
            pre = p;
            tmp.pop();
        } else {
            if (p->right != nullptr) {
                tmp.push(p->right);
            }
            if (p->left != nullptr) {
                tmp.push(p->left);
            }
        }
    }
}
```





# 滑动窗口大小

用一个两端开口的队列，保存有可能是滑动窗口最大值的下标。在存入一个数字的下标之前，首先判断队列里已有数字是否小于待存入的数字，如果小于那么这些数字已经不可能是滑动窗口的最大值，因为依次从尾部删除。同时，如果队列头部的数字已经从窗口里滑出，那么滑出的数字下边也要从队列的头部删除。

```c++
class Solution {
public:
    vector<int> maxInWindows(const vector<int>& num, unsigned int size)
    {
        vector<int> res;
        int len = (int)num.size();
        if (len <= 0 || size <= 0 || len < size) {
            return res;
        }
        if (len == 1) {
            res.push_back(num[0]);
            return res;
        }
        deque<int> win;
        for (int i = 0; i < size; i++) {
            while (!win.empty() && num[i] >= num[win.back()]) {
                win.pop_back();
            }
            win.push_back(i);
        }
        for (int i = size; i < len; i++) {
            res.push_back(num[win.front()]);
            while (!win.empty() && num[i] >= num[win.back()]) {
                win.pop_back();
            }
            if (!win.empty() && win.front() <= (i-size)) {
                win.pop_front();
            }
            win.push_back(i);
        }
        res.push_back(num[win.front()]);
        return res;
    }
};
```

