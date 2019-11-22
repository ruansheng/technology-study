## 二分查找
```
package main

import "fmt"

// 二分查找

func BinarySearch(sortedArray []int64, lookingFor int64) int64 {
	var low = 0
	var height = len(sortedArray) - 1
	fmt.Printf("array len:%d\n", len(sortedArray))
	fmt.Printf("look:%d\n\n", lookingFor)

	fmt.Println("================")
	for low <= height {
		var mid = low + (height-low)/2
		midVal := sortedArray[mid]
		fmt.Printf("mid:%d\n", mid)
		fmt.Printf("midVal:%d\n", midVal)
		if midVal == lookingFor {
			fmt.Printf("================\n\n")
			return midVal
		} else if midVal > lookingFor {
			height = mid - 1
			fmt.Printf("height:%d\n", height)
			fmt.Println("================")
		} else {
			low = mid + 1
			fmt.Printf("low:%d\n", low)
			fmt.Printf("================\n\n")
		}
	}
	return -1
}

func main() {
	array1 := []int64{1, 3, 5, 6, 8, 9, 45, 67}
	lookingFor := BinarySearch(array1, 3)
	fmt.Printf("search result:%d\n", lookingFor)
}
```

```
执行输出:
array len:8
look:3

================
mid:3
midVal:6
height:2
================
mid:1
midVal:3
================

search result:3
```