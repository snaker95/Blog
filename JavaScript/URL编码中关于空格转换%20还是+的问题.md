W3C标准规定，当Content-Type为application/x-www-form-urlencoded时，URL中查询参数名和参数值中空格要用加号+替代，所以几乎所有使用该规范的浏览器在表单提交后，URL查询参数中空格都会被编成加号+。而在另一份规范(RFC 2396，定义URI)里, URI里的保留字符都需转义成%HH格式(Section 3.4 Query Component)，因此空格会被编码成%20，加号+本身也作为保留字而被编成%2B，对于某些遵循RFC 2396标准的应用来说，它可能不接受查询字符串中出现加号+，认为它是非法字符。所以一个安全的举措是URL中统一使用%20来编码空格字符。

golang中两种方法都有，QueryEscape会把空格转成+，PathEscape会把空格转成%20



作者：夕阳西下一天又过去了
链接：https://www.jianshu.com/p/2ba7dda583b5


