# 二分查找
二分查找思路简单，但是很细节。

## 二分查找的框架
```
int binarySearch(int[] nums, int target) {
    int left = 0, right = ...;

    while(...) {
        int mid = (right + left) / 2;
        if (nums[mid] == target) {
            ...
        } else if (nums[mid] < target) {
            left = ...
        } else if (nums[mid] > target) {
            right = ...
        }
    }
    return ...;
}
```
分析二分查找的一个技巧是：建议不是只用else，而是把所有的情况都用else if写清楚，这样可以展现所有的细节。

注意在计算mid的时候需要技巧防止溢出。


## 寻找一个数（基本的二分查找）
```
int binarySearch(int[] nums, int target) {
    int left = 0; 
    int right = nums.length - 1; // 注意

    while(left <= right) { // 注意
        int mid = (right + left) / 2;
        if(nums[mid] == target)
            return mid; 
        else if (nums[mid] < target)
            left = mid + 1; // 注意
        else if (nums[mid] > target)
            right = mid - 1; // 注意
        }
    return -1;
}
```
其中：while循环中的条件为<=，不是<。

是因为初始化right的赋值是nums.length-1，相当于两端都能取到，是一个闭区间[left，right]。while（left<=right）的终止条件是left==right+1，写成区间的形式就是[right+1 , right]，如果right==2，在区间中表示为[3,2]，此时搜索的区间为空，所以while循环终止是正确的。如果是while（left<right）的终止条件是left==right，写成区间的形式就是[right , right]，如果right==2，在区间中[2，2]，此时还有一个数没有被遍历，可能会出错。

如果你要写成while(left<right)也可以，加一个补丁就行。
```
while(left < right) {
    // ...
}
return nums[left] == target ? left : -1;
```

## 寻找左侧边界的二分搜索
可以用上面的方法先找到target，然后再向左向右进行线性搜索，这也可以但是很难保证二分查找的对数级复杂度。

代码类似，要注意细节
```
int left_bound(int[] nums, int target) {
    if (nums.length == 0) return -1;
    int left = 0;
    int right = nums.length; 

    while (left < right) { 
        int mid = (left + right) / 2;
        if (nums[mid] == target) {
            right = mid;
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid; 
        }
    }
    return left;
}
```
1.为什么while中是<，而不是<=。

因为是初始化的时候，right=nums.length，最后一个超过界限，取不到。用区间的方式为[left,right)。while(left<right)，此时搜索的区间为[left，left)恰巧为空，可以正确终止。

2.没有返回-1，如果原数组中没有target元素

上面返回的结果，可以理解为：原数组中有left个元素比target小，如：nums={1，2，2，4，5}，target=2，返回值为left=1（为最左边界target的下标），可以看成有1个数小于target（2）。所以，上面代码的返回值（left）的取值范围是[0,nums.length]，可以增加两条语句来控制。
```
while (left < right) {
    //...
}
// target 比所有数都大
if (left == nums.length) return -1;
// 类似之前算法的处理方式
return nums[left] == target ? left : -1;
```





































