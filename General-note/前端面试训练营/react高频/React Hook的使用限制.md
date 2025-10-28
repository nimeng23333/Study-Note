---
title: React Hook的使用限制
---
1.只能在函数组件或自定义hook里使用。
2.不能在条件语句中使用。
3.只能在React函数组件的最顶层使用。
4.useState的值不能直接修改，只能用setState。
5.useEffect的回调函数不能使用异步函数。
6.useContext必须在useContext所对应的Context.Provider里使用