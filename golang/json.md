### json
```
JSON全称为Javascript Object Notation，一种数据结构化交互的标准协议，易于阅读与编写，所以在数据交互时广泛使用。
```

### json中的数据类型
```
数字：有十进制和科学记数学两种表示方式。
字符串：使用双引号表示的Unicode字符序列。
布尔：true或者false。
对象：花括号({})括起来的一个或多个键值对(key/value)，用逗号(,)隔开，最后一个键值对后面不能有逗号，键必是双引号("")引起来的字符串，而值则可是作意类型(布尔、数字、对象、数组、字符串)。
数组：中括号([])括号值的集合，这些值可是任意类型(布尔、数字、对象、数组、字符串)。
```

### encoding/json
```
Go语言提供了encoding/json包，用于处理JSON数据的编码与解码。
```

### JSON与GO结合
```
使用encoding/json处理JSON编码与解码时，就必须处理好JSON数据类型与Go语言数据类型的对应关系。
JSON的数字、字符串、布尔等在Go语言中相应内置数据类型一一对应。
JSON的数组则对应Go的数组或Slice(切片)。
JSON的对象则对应Go的struct(结构体)或map。

注意:
编码一个结构体时，结构体中只有首字母大写的成员才会被编码，首字母小写的成员会被忽略，
另外，结构体中字段后面允许使用反引号声明成员的Tag，用于说明成员的元信息。
```

### 编码
```
将Go语言的数据序列化为JSON字符串的操作，称为编码;编码后的结果为一个JSON格式的字符串。

json.Marshal函数:
members := []Member{}
data,_ := json.Marshal(members)
fmt.Printf("%s",data)
json.Marshal实际上只是对json.Encoder的封装,因此使用json.Encoder同样可以编码JSON。

json.Encoder函数:
b := &bytes.Buffer{}
encoder := json.NewEncoder(b)
err := encoder.Encode(members)
fmt.Println(b.String())
```

### 解码
```
将JSON字符串反序列化为Go相对应类型的作品，称为解码。

json.Unmarshal函数:
json.Unmarshal与json.Marshal函数相反，用于解码JSON字符串。
b := bytes.NewBufferString(str)
var members []Member
err := json.Unmarshal(b.Bytes(),&members)
fmt.Println(members)

json.Decoder函数:
b := bytes.NewBufferString(str)
var members []Member
decoder := json.NewDecoder(b)
err = decoder.Decode(&members)
fmt.Println(members)
```