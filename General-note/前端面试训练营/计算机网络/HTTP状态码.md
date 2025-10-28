---
title: HTTP状态码
---
重点：
1.常见状态码

# 2xx 成功

200 OK

204 No Content 正常处理，但没有返回的内容

206 partial content

# 3xx 重定向
浏览器需要执行某些特殊处理

301 Moved Permanently 永久重定向
换域名

302 Found 临时重定向
做活动登录到首页时，进入活动页面
未登录用户访问用户中心重定向到登陆页面
访问404后重定向到首页

303 See Other
表示对应资源有另一个URI，应用GET方法获取
303与302相似，但303明确表示用GET方法。
通常最为PUT或POST操作的返回结果，比如消息确认页面或者上传进度页面。

注意：当301、302、303返回时几乎所有浏览器都会把POST改为GET，并删除报文主体，自动再次发送请求。
301、302禁止将POST变为GET，但实际都会这么做。

304 Not Modified
缓存相关
客户端发送带有条件的请求时，服务器允许请求，但不满足条件。响应不包含主体。
用Get方法，包含if-match、if-modified-since、if-unmodified-since、if-range、if-none-match
不是错误，是告诉客户端有缓存，直接使用缓存数据。

307 Temporary Redirect
临时重定向，与302相似
但307不会将POST变为GET，继续向Location的地址POST内容

# 4xx 客户端错误

400 bad request
报文有语法错误

401 unauthorized
需要有通过HTTP认证的认证信息，已进行过一次请求则表示认证失败

403 forbidden
请求资源的访问别拒绝了

404 not found
在服务器上无法找到请求的资源

405 method not allowed
请求方法虽然能别识别，但禁止使用
服务器总是允许GET和HEAD
可以用OPTIONS查看

# 5xx 服务器错误

500 internal server error
服务器端执行请求发生了错误

502 bad gateway
扮演网关或代理服务器收到从上有服务器的响应是无效的
需要web服务器或代理服务器修复

503 service unavailable
暂时超负荷或停机维护

504 gateway timeout
无法在规定时间获得想要的响应