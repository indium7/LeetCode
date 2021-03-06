# 1576. 替换所有的问号
给你一个仅包含小写英文字母和 `'?'` 字符的字符串 `s`，请你将所有的 `'?'` 转换为若干小写字母，使最终的字符串不包含任何 **连续重复** 的字符。

注意：你 **不能** 修改非 `'?'` 字符。

题目测试用例保证 **除** `'?'` 字符 **之外**，不存在连续重复的字符。

在完成所有转换（可能无需转换）后返回最终的字符串。如果有多个解决方案，请返回其中任何一个。可以证明，在给定的约束条件下，答案总是存在的。

#### 示例 1:
<pre>
<b>输入:</b> s = "?zs"
<b>输出:</b> "azs"
<b>解释:</b> 该示例共有 25 种解决方案，从 "azs" 到 "yzs" 都是符合题目要求的。只有 "z" 是无效的修改，因为字符串 "zzs" 中有连续重复的两个 'z' 。
</pre>

#### 示例 2:
<pre>
<b>输入:</b> s = "ubv?w"
<b>输出:</b> "ubvaw"
<b>解释:</b> 该示例共有 24 种解决方案，只有替换成 "v" 和 "w" 不符合题目要求。因为 "ubvvw" 和 "ubvww" 都包含连续重复的字符。
</pre>

#### 示例 3:
<pre>
<b>输入:</b> s = "j?qg??b"
<b>输出:</b> "jaqgacb"
</pre>

#### 示例 4:
<pre>
<b>输入:</b> s = "??yw?ipkj?"
<b>输出:</b> "acywaipkja"
</pre>

#### 提示:
* `1 <= s.length <= 100`
* `s` 仅包含小写英文字母和 `'?'` 字符

## 题解 (Rust)

### 1. 题解
```Rust
impl Solution {
    pub fn modify_string(s: String) -> String {
        let mut s = s.into_bytes();

        for i in 0..s.len() {
            if s[i] == b'?' {
                if i == 0 {
                    if s.len() == 1 || s[1] != b'a' {
                        s[0] = b'a';
                    } else {
                        s[0] = b'b';
                    }
                } else {
                    s[i] = s[i - 1] % 26 + b'a';
                    if i + 1 < s.len() && s[i + 1] == s[i] {
                        s[i] = s[i] % 26 + b'a';
                    }
                }
            }
        }

        String::from_utf8(s).unwrap()
    }
}
```
