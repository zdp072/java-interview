## get请求和post请求有什么区别？
1. get请求通过浏览器打开；post请求需要通过表单或JS提交。
2. get提交的数据不同浏览器限制不同，一般为2K-8K之间；post提交的数据比较大，大小靠服务器设定值限制。
3. get一般用来做查询，是幂等的；post一般是用来做资源的提交。

## cookie和session的区别
1. cookie数据保存在客户的浏览器上，session数据在服务器上。
2. cookie不是很安全，别人可以分析存放在本地的cookie并进行cookie欺骗，如果考虑安全性使用session。
3. session过多会占用服务器内存，如果考虑到要减轻服务器负担，应该使用cookie。
4. 单个cookie保存的数据不能超过4K，很多浏览器会限制一个站点最多保存20个cookie。