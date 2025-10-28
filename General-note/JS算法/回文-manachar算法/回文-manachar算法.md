---
title: 回文-manachar算法
---
```js
function countSubstrings(s){
	let newStr = "@#"
	for(const char of s){
		newStr += char + "#"
	}
	newStr += "$"
	const n = newStr.length
	const f = new Array(n).fill(0)
	let center = 0, right = 0, count = 0
	for(let i = 1; i< n-1; i++){
		//初始化起点
		if(i <= right){
			f[i] = Math.min(right - i + 1, f[2 * center - i])
			//正常情况直接赋值f[i_mirror]
			//i_mirror = center - (i - center) = 2 * center - i
			//right - i + 1 是为了防止i+f[i_mirror]超出right
		}else{
			f[i] = 1
		}

		//中心扩展检测
		while (newStr[i+f[i]] === newStr[i-f[i]]){
			f[i] ++
		}

		//记录新的中心和半径
		if(i + f[i] - 1 > right){
			center = i
			right = i+f[i] - 1
		}
		count += Math.floor(f[i] / 2)
	}
	return count
}
```