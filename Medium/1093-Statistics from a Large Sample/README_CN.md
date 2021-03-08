# 1093. 大样本统计
我们对 `0` 到 `255` 之间的整数进行采样，并将结果存储在数组 `count` 中：`count[k]` 就是整数 `k` 的采样个数。

我们以 **浮点数** 数组的形式，分别返回样本的最小值、最大值、平均值、中位数和众数。其中，众数是保证唯一的。

我们先来回顾一下中位数的知识：
* 如果样本中的元素有序，并且元素数量为奇数时，中位数为最中间的那个元素；
* 如果样本中的元素有序，并且元素数量为偶数时，中位数为中间的两个元素的平均值。

#### 示例 1:
<pre>
<strong>输入:</strong> count = [0,1,3,4,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0]
<strong>输出:</strong> [1.00000,3.00000,2.37500,2.50000,3.00000]
</pre>

#### 示例 2:
<pre>
<strong>输入:</strong> count = [0,4,3,2,2,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0]
<strong>输出:</strong> [1.00000,4.00000,2.18182,2.00000,1.00000]
</pre>

#### 提示:
1. `count.length == 256`
2. `1 <= sum(count) <= 10^9`
3. 计数表示的众数是唯一的
4. 答案与真实值误差在 `10^-5` 以内就会被视为正确答案

## 题解 (Rust)

### 1. 题解
```Rust
impl Solution {
    pub fn sample_stats(count: Vec<i32>) -> Vec<f64> {
        vec![
            Self::minimum(&count),
            Self::maximum(&count),
            Self::mean(&count),
            Self::median(&count),
            Self::mode(&count),
        ]
    }

    pub fn minimum(count: &[i32]) -> f64 {
        count
            .iter()
            .enumerate()
            .filter(|(_, c)| **c > 0)
            .next()
            .unwrap()
            .0 as f64
    }

    pub fn maximum(count: &[i32]) -> f64 {
        count
            .iter()
            .enumerate()
            .rev()
            .filter(|(_, c)| **c > 0)
            .next()
            .unwrap()
            .0 as f64
    }

    pub fn mean(count: &[i32]) -> f64 {
        let sum = count
            .iter()
            .enumerate()
            .map(|(k, c)| k as u64 * *c as u64)
            .sum::<u64>();
        let count_sum = count.iter().sum::<i32>();

        sum as f64 / count_sum as f64
    }

    pub fn median(count: &[i32]) -> f64 {
        let mut l = 0;
        let mut r = 255;
        let mut l_count = count[l];
        let mut r_count = count[r];

        while l < r {
            if l_count > r_count {
                l_count -= r_count;
                r -= 1;
                r_count = count[r];
            } else if l_count < r_count {
                r_count -= l_count;
                l += 1;
                l_count = count[l];
            } else {
                l += 1;
                l_count = count[l];
                r -= 1;
                r_count = count[r];
            }
        }

        (l + r) as f64 / 2.
    }

    pub fn mode(count: &[i32]) -> f64 {
        count.iter().enumerate().max_by_key(|(_, c)| *c).unwrap().0 as f64
    }
}
```