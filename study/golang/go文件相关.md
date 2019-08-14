## go 文件相关

### 文件相关的操作

包名` "os"`

1. 创建文件 `Create` 	文件不存在创建，存在文件 将文件内容清空  

   ```go
   f,err := os.Create("./xxx.sql")
   if err != nil {
   	fmt.Println("error=",err)
   } else {
   	fmt.Println("创建成功")
   	return 
   	}
   defer f.Close()
   ```

   

2. 打开文件 `Open`       已只读方式打开文件。

   ```go
   f,err := os.Open("./xxx.sql")
   if err != nil {
   	fmt.Println("error=",err)
   	return 
   }
   defer f.Close() // 函数调用完毕之前执行
   // 只读不能写入文件
   _,errors := f.WriteString("333333")
   if errors != nil {
   	fmt.Println("error=",errors)
   	return 
   }
   fmt.Println("打开成功")
   ```

   

3. 打开文件 `OpenFile`  已只读，只写，等等打开文件的方式

   参数1 name 文件名 绝对路径 相对路径

   参数2 打开文件权限 ：`O_RDONLV , O_WRONLY, O_RDWR`

   参数3 一般传6  

   ```go
   // O_RDWR 读写权限
   f,err := os.OpenFile("./xxx.sql",os.O_RDWR,6)
   if err != nil {
   	fmt.Println("error=",err)
   	return 
   }
   defer f.Close() // 函数调用完毕之前执行
   
   // 只读不能写入文件
   _,errors := f.WriteString("333333")
   if errors != nil {
   	fmt.Println("error=",errors)	
       return 
   }
   fmt.Println("打开成功")
   ```

   

### 字符串常用的相关函数

```go
// 字符串按照指定分隔符拆分
ret := strings.Split(string,string)
	str := "a a a"
	rmt := strings.Split(str,"a") // 返回切片string
// 字符串按照空格切分
ret := strings.Fields(string)
	str := "a a a"
	rmt := strings.Fields(str) // 返回切片string
// 判断字符串结束标记 (判断文件名啥的)
ret := strings.HasSuffix(string,string) 
	flag := strings.HasSuffix("test.abvc",".abvc") // 返回值bool类型
// 判断字符串起始标记
ret := strings.HasPrefix(string,string) 
	flag := strings.HasPrefix("test.abvc","test") // 返回值bool类型


```

