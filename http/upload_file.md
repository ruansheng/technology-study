## 上传文件
```
http 文件上传协议定义在RFC 1867 文档中: https://tools.ietf.org/html/rfc1867
```

主要方式
```
1.input 标签的类型增加一个 file 选项；
2.form 表单的 enctype 增加 multipart/form-data 选项；

<form action="http url" method="post" enctype="multipart/form-data">
  <input type="text" name="words"/>
  <input type="file" name="uploadfile1">
  <input type="file" name="uploadfile2">
  <input type="submit">
</form>
```