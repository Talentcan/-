# 快速寻找质数
首先最初的想法是穷举，遍历每一个数，看它是不是素数
```
int countPrimes(int n) {
    int count = 0;
    for (int i = 2; i < n; i++)
        if (isPrim(i)) count++;
    return count;
}

// 判断整数 n 是否是素数
boolean isPrime(int n) {
    for (int i = 2; i < n; i++)
        if (n % i == 0)
            // 有其他整除因子
            return false;
    return true;
}
```
这个方法的效率是很差的，时间复杂度是O(n^2)，其中可以用一个小的优化，在循环中，循环结束的判断不必到n，只用到sqrt(n)就行。即一个数在[2,sqrt(n)]中没有可以整除的因子，那么在[sqrt(n),n]中也一定找不到可以整除的因子，就可以直接判定为素数。这样时间复杂度就降到了O(sqrt(n))。

## 高效实现
可以思考，首先从第一个素数2开始，那么2的倍数4,6,8,10...就不可能是素数；下一个素数3,3的倍数6,9,12,15...也就不可能是素数。
写出代码
```
int countPrimes(int n) {
    boolean[] isPrim = new boolean[n];
    // 将数组都初始化为 true
    Arrays.fill(isPrim, true);

    for (int i = 2; i < n; i++) 
        if (isPrim[i]) 
            // i 的倍数不可能是素数了
            for (int j = 2 * i; j < n; j += i) 
                    isPrim[j] = false;

    int count = 0;
    for (int i = 2; i < n; i++)
        if (isPrim[i]) count++;

    return count;
}
```
其中一些地方可以优化，首先就是循环结束的条件，也不用到n，可以像上面一样只到sqrt(n)。其次在循环体内部也存在计算的冗余，比如12，在i=2时，2*6;在i=3时，3*4；在i=4时，4*3；同一个数会被多个因子重复计算。所以可以优化一下，计算不再从j=2*i开始，而是从j=i*i开始。
代码
```
int countPrimes(int n) {
    boolean[] isPrim = new boolean[n];
    Arrays.fill(isPrim, true);
    for (int i = 2; i * i < n; i++) 
        if (isPrim[i]) 
            for (int j = i * i; j < n; j += i) 
                isPrim[j] = false;

    int count = 0;
    for (int i = 2; i < n; i++)
        if (isPrim[i]) count++;

    return count;
}
```
这个高效寻找素数的算法叫做 Sieve of Eratosthenes（埃氏筛）。


