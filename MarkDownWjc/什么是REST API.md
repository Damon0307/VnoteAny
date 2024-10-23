# 什么是REST API
![其实就是HTTP类型的访问方式 API](vx_images/32634257686511.png =614x)
## 参考链接
[rest-apis](https://www.ibm.com/cn-zh/topics/rest-apis)
[主要是在于通讯形式](https://developer.aliyun.com/article/1269846)

嗯经常听到有人说 啥啥啥的基于REST API 。 RESTful API的感觉很牛逼的样子。 今天了解了一下REST API ，原来很简单。REST api的全称是 Representational State Transfer。 理解起来很拗口 表述状态型 发送 方式
其实就是类HTTP类型的通讯方式

REST API 是一种风格，比如HTTP 中 GET  POST UPDATE  DELTET 就是约定俗成的规范。 就是这种风格

# 符合REST API 风格的设计有以下几个原则

1. C-S 架构。 数据存储在Server 段，Client 只需要使用就行。 两端独立开发，互不影响
2. 无状态， 无状态即客户端每次请求都必须要带上所有必要的信息，服务器不会保存客户端的连接或者其他信息，而是每次按照请求的内容返回所有信息
3. 信息格式的统一 ，基于 JSON 或者XML
4. 统一接口 GET /DELETE/POST....
5. 系统分层 客户端通常无法表明自己是直接还是间接与端服务器进行连接，分层时同样要考虑安全策略
6. 可缓存 AJAX 只刷新部分内容
# 举例

github  和你 正在浏览的网页 都是 RESTful API 风格
![](vx_images/421170913824937.png =570x)
