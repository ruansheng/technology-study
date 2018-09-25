### mbstring扩展
注意：mb_strlen函数并不是php的核心函数，只是PHP的一个扩展函数，使用之前要判断是否加在的mbstring扩展模块，在Php.ini文件中可以查看相关配置

### strlen()
```
在strlen计算中，对待一个UTF8的中文字符，处理为3个字节长度，得到的是字符串所占的字节数，
所以在查看一个字符串的长度的时候，strlen并不能得到我们需要的真实值
```

### mb_strlen()
```
mb_strlen的内码选择为UTF-8的时候，则会将中文字符当成一个字符，当mb_strlen的内码选择为gbk的时候，一个中文字符当成1.5个字符来处理来处理
```

### mb_internal_encoding()
```
函数：mb_internal_encoding()会得到当前PHP使用的内部编码
输出: UTF-8
```
