
html的表格由table控制
包含thead、tbody和tfoot
每一行为tr
每一个数值为td（标题栏的为th）
```
<table>
	<thead>
		<tr>
			<th>1列标题</th>
			<th>2列标题</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>1列1行值</td>
			<td>2列1行值</td>
		</tr>
		<tr>
			<td>1列2行值</td>
			<td>2列2行值</td>
		</tr>
	</tbody>
</table>
```
表格的外观可以用html写，但比较丑，还是用css

表格可以装图片进去
表格之间的间隙可以用cellspacing属性

表格可以嵌套但直接用HTML表格嵌套会让结构看起来有点乱

表单 form让用户可以提交信息
form里有两个主要元素 label和input
input有多种属性
![[Pasted image 20230324131713.png]]
![[Pasted image 20230324131727.png]]
![[Pasted image 20230324131748.png]]
![[Pasted image 20230324131848.png]]

datalist与select的区别：datalist是给选项提示，但是可以用户自己填写内容，select只能选择给定的选项

输入的文本等形式可以限制输入的字符数长度，但数字不行，数字可以通过限制范围来实现
如下：
```
<input type="text" required minlength="4" maxlength="10" size="20">
<input type="number"min="1000000000" max="9999999999" size="20">
```

textarea添加一个输入方框
form里的action可以设置提交以后的动作
在input的name属性里可以将用户输入的数据命名