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

3.指针的跳动的和上一个算法的不一样，这里的是left=mid+1，right=mid。

因为我们的搜索区间是[left,right)左闭右开，,所以当nums[mid]被检测后，下一步的搜索区间应该是去掉mid分割成两个区间，即[left,mid)或[mid+1,right)。

4.这个算法怎么搜索到左侧边界

关键在于nums[mid] == target这种情况的处理：
```
if (nums[mid] == target)
        right = mid;
```
代码表示，在找到target时不要立即返回，而是缩小搜索区间的上届right，在区间[left,mid)中继续搜索，就是不断向左收缩，达到锁定左侧边界的目的。

5.可以写一些条件，让right的取值变成nums.length-1，也就是继续使用两边都闭的搜索区间，这样就可以和上面第一种二分查找在某种程度上统一起来。

如果right的初始值为nums.length-1，那while的终止条件应该是left == right+1，就是其中应该使用<=。并且现在是搜索左侧边界，所以left和right的变化也要该改变。由于while的退出条件是left == right+1，所以当target比nums中所有元素都大时，会出现left>=nums.length的情况，即数组的索引下标越界。

更改完善后的代码
```
int left_bound(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    // 搜索区间为 [left, right]
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            // 搜索区间变为 [mid+1, right]
            left = mid + 1;
        } else if (nums[mid] > target) {
            // 搜索区间变为 [left, mid-1]
            right = mid - 1;
        } else if (nums[mid] == target) {
            // 收缩右侧边界
            right = mid - 1;
        }
    }
    // 检查出界情况
    if (left >= nums.length || nums[left] != target)
        return -1;
    return left;
}
```

## 寻找右侧边界的二分查找
类似于寻找左侧边界的算法。
左闭右开的写法
```
int right_bound(int[] nums, int target) {
    if (nums.length == 0) return -1;
    int left = 0, right = nums.length;

    while (left < right) {
        int mid = (left + right) / 2;
        if (nums[mid] == target) {
            left = mid + 1; // 注意
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid;
        }
    }
    return left - 1; // 注意
}
```
修改成闭区间的写法
```
int right_bound(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid - 1;
        } else if (nums[mid] == target) {
            // 这里改成收缩左侧边界即可
            left = mid + 1;
        }
    }
    // 这里改为检查 right 越界的情况
    if (right < 0 || nums[right] != target)
        return -1;
    return right;
}
```
## 总结
1.基本的二分查找
```
因为我们初始化 right = nums.length - 1
所以决定了我们的「搜索区间」是 [left, right]
所以决定了 while (left <= right)
同时也决定了 left = mid+1 和 right = mid-1

因为我们只需找到一个 target 的索引即可
所以当 nums[mid] == target 时可以立即返回
```
2.寻找左侧边界的二分查找
```
因为我们初始化 right = nums.length
所以决定了我们的「搜索区间」是 [left, right)
所以决定了 while (left < right)
同时也决定了 left = mid + 1 和 right = mid

因为我们需找到 target 的最左侧索引
所以当 nums[mid] == target 时不要立即返回
而要收紧右侧边界以锁定左侧边界
```
3.寻找右侧边界的二分查找
```
因为我们初始化 right = nums.length
所以决定了我们的「搜索区间」是 [left, right)
所以决定了 while (left < right)
同时也决定了 left = mid + 1 和 right = mid

因为我们需找到 target 的最右侧索引
所以当 nums[mid] == target 时不要立即返回
而要收紧左侧边界以锁定右侧边界

又因为收紧左侧边界时必须 left = mid + 1
所以最后无论返回 left 还是 right，必须减一
```

三个二分查找的闭区间写法
```
int binary_search(int[] nums, int target) {
    int left = 0, right = nums.length - 1; 
    while(left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid - 1; 
        } else if(nums[mid] == target) {
            // 直接返回
            return mid;
        }
    }
    // 直接返回
    return -1;
}

int left_bound(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid - 1;
        } else if (nums[mid] == target) {
            // 别返回，锁定左侧边界
            right = mid - 1;
        }
    }
    // 最后要检查 left 越界的情况
    if (left >= nums.length || nums[left] != target)
        return -1;
    return left;
}


int right_bound(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid - 1;
        } else if (nums[mid] == target) {
            // 别返回，锁定右侧边界
            left = mid + 1;
        }
    }
    // 最后要检查 right 越界的情况
    if (right < 0 || nums[right] != target)
        return -1;
    return right;
}
```

为二分查找labuladong写了一首诗





























