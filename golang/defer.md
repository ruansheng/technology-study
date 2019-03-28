### defer 的主要特点
```
延迟调用
所在的函数中，它在 return 或 panic 或 执行完毕 后被调用
多个 defer，它们的被调用顺序，为栈的形式。先进后出，先定义的后被调用
```

### 非引用传参给defer调用的函数，且为非闭包函数情况
```
值不会受后面的改变影响
func defer0() {
	a := 3  // a 作为演示的参数
	defer fmt.Println(a) // 非引用传参，非闭包函数中，a 的值 不会 受后面的改变影响
	a = a + 2
}
// 控制台输出 3
```

### 传递引用给defer调用的函数，即使不使用闭包函数
```
值也会受后面的改变影响
func myPrintln(point *int)  {
	fmt.Println(*point) // 输出引用所指向的值
}

func defer1() {
	a := 3
	// &a 是 a 的引用。内存中的形式： 0x .... ---> 3
	defer myPrintln(&a) // 传递引用给函数，即使不使用闭包函数，值 会 受后面的改变影响
	a = a + 2
}
// 控制台输出 5
```

### 传递值给defer调用的函数，且非闭包函数
```
值不会受后面的改变影响
func p(a int)  {
	fmt.Println(a)
}

func defer2() {
	a := 3
	defer p(a) // 传递值给函数，且非闭包函数，值 不会 受后面的改变影响
	a = a + 2
}
// 控制台输出： 3
```

### defer调用闭包函数，且内调用外部非传参进来的变量
```
值会受后面的改变影响
// 闭包函数内，事实是该值的引用
func defer3() {
	a := 3
	defer func() {
		fmt.Println(a) // 闭包函数内调用外部非传参进来的变量，事实是该值的引用，值 会 受后面的改变影响
	}()
	a = a + 2  // 3 + 2 = 5
}
// 控制台输出： 5

// defer4 会抛出数组越界错误。
func defer4() {
	a := []int{1,2,3}
	for i:=0;i<len(a);i++ {
		// 同 defer3 的闭包形式。因为 i 是外部变量，没用通过传参的形式调用。在闭包内，是引用。
		// 值 会 受 ++ 改变影响。导致最终 i 是3， a[3] 越界
		defer func() {
			fmt.Println(a[i])
		}()
	}
}
// 结果：数组越界错误
```

### defer调用闭包函数，若内部使用了传参参数的值
```
使用的是值
func defer5() {
	a := []int{1,2,3}
	for i:=0;i<len(a);i++ {
		// 闭包函数内部使用传参参数的值。内部的值为传参的值。同时引用是不同的
		defer func(index int) {
		        // index 有一个新地址指向它
			fmt.Println(a[index]) // index == i
		}(i)
		// 后进先出，3 2 1
	}
}
// 控制台输出：
//     3
//     2
//     1
```

### defer所调用的非闭包函数，参数如果是函数
```
会按顺序先执行（函数参数）
func calc(index string, a, b int) int {
	ret := a + b
	fmt.Println(index, a, b, ret)
	return ret
}
func defer6()  {
	a := 1
	b := 2
	// calc 充当了函数中的函数参数。即使在 defer 的函数中，它作为函数参数，定义的时候也会首先调用函数进行求值
	// 按照正常的顺序，calc("10", a, b) 首先被调用求值。calc("122", a, b) 排第二被调用
	defer calc("1", a, calc("10", a, b))
	defer calc("12",a, calc("122", a, b))
}
// 控制台输出：
/**
10 1 2 3   // 第一个函数参数
122 1 2 3  // 第二个函数参数
12 1 3 4   // 倒数第一个 calc
1 1 3 4    // 倒数第二个 calc
*/
```

### defer 不影响 return的值
```
无论 defer 内部调用传递的是值还是引用。都不会改变 return 的返回结果。返回值的确定，比 defer 早
func defer7() int {
	a := 2
	defer func() {
		a = a + 2
	}()
	return a
}
// 控制台输出：2

func add(i *int)  {
	*i = *i + 2
}

func defer8() int {
	a := 2
	defer add(&a)
	return a
}
// 控制台输出：2
```

### 闭包函数对 defer 的影响
```
函数中，值传递和引用传递它们的区别是比较简单的，为基础的 C 语言指针知识。

而对于为什么 defer 修饰的闭包函数，如果函数内部不是使用传参的参数时，它所能起到的引用修改作用。原理如下：

a := 2
func() {
    fmt.Println(a)
}()
a = a + 3

// 内存
闭包外：
    1. a 实例化
    2. a地址 ---> 2
闭包内：
    1. a 地址被传递进来
    2. a地址 ---> 2
    3. a = a + 3
    4. 输出 5
```