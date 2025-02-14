---
title: JS字符串方法
---
## .padStart（length,string）

`padStart` 是 JavaScript 中一个非常有用的方法，通常用于字符串操作。它的主要功能是通过在字符串的起始位置填充指定的字符，使字符串达到指定的长度。例如：


```javascript
let str = "5";
let paddedStr = str.padStart(3, '0');
console.log(paddedStr);  // 输出 "005"
```

在这个例子中，`padStart(3, '0')` 表示将字符串 `str` 填充到长度为 3，如果原始字符串长度不足，则在前面添加字符 `'0'` 进行补齐。于是字符串 `"5"` 就变成了 `"005"`。

这个方法在处理诸如日期格式化、数字格式化等场景时非常有用。

## .toString(number)
`toString(2)` 是 JavaScript 中的一个方法，用于将数字转换为二进制字符串表示。其中，`2` 代表将数字转换为二进制。这个方法可以应用于任何数字类型变量。

例如：

```javascript
let number = 10;
let binaryStr = number.toString(2);
console.log(binaryStr);  // 输出 "1010"
```

在这个例子中，`number` 的值是 `10`，调用 `number.toString(2)` 会将其转换为二进制字符串 `"1010"`。

这个方法的参数实际上可以是任意进制，比如：

- `number.toString(10)` 将数字转换为十进制字符串。
    
- `number.toString(16)` 将数字转换为十六进制字符串。