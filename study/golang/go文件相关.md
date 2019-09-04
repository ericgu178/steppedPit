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

   参数3 一般传6  读取目录时 传`os.ModeDir`

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

4. 写文件

   ​	按字符串写：`WriteString()`  n个写入的字符个数
   
   ```go
   n,err := f.WriteString("123\n") //回车换行 
   ```
   
   ​	按位置写：Seek(): 修改文件的读写指针位置。
   
   ​				参数一：偏移量。 正：向文件尾偏 负：向文件头偏移
   
   ​				参数二：偏移起始位置 `io.SeekStart 文件起始位置，io.SeekCurrent 文件当前位置，io.SeekEnd 文件结尾位置`
   
   ​				返回值：表示从文件起始位置，到当前文件读写指针位置的偏移量。
   
   ```go
   offset,err := f.Seek(-5,io.SeekEnd)
   ```
   
   ​	按字节写：`WriteAt()` 在文件制定偏移位置，写入[]byte,通常搭配Seek函数用
   
   ​				参数一： 代写入的数据
   
   ​				参数二： 偏移量
   
   ​				返回值：实际写入的字节数
   
   ```go
   n,err := f.WriteAt([]byte("1234"),offset) 
   ```
   
5. 读取文件

   按行读取 创建一个带有缓冲区的Reader（读写器）

   ​	`reader := bufio.NewReader(文件指针)`

   从reader的缓冲区中，读取指定长度的数据，数据长度取决于分隔符

   ​	`buf,err := reader.ReadBytes('分隔符') //按照分隔符读取`

   判断到达文件结尾 `err == io.EOF // io.EOF 结尾标记`
   
   ```go
   // Read() 使用  按字节读文件
   // Write() 使用 按字节写文件
   f_r,f_r_err := os.Open("../xxx.txt") 
   buf := make([]byte,4096)
   n,err := f_r.Read(buf) // 读进buf字节切片
   f_w.Write(buf[:n]) //按字节写文件
   ```
   
6. 实例

   ```go
   // 0x00 拷贝文件
   package main
   
   import (
   	"fmt"
   	"os"
   	"bufio"
   	"io"
   )
   
   func main() {
   	f_r,f_r_err := os.Open("../xxx.txt")
   	if f_r_err != nil {
   		fmt.Println("f_r出错了",f_r_err)
   		return
   	}
   	defer f_r.Close()
   
   	f_w,f_w_err := os.Create("../ysy.txt")
   	if f_w_err != nil {
   		fmt.Println("f_w出错了",f_w_err)
   		return
   	}
   	defer f_w.Close()
   	buf := make([]byte,4096) // 一次读取的最大字节数 内存地址最小单位4kb 4096字节
   	for {
   		n,err := f_r.Read(buf) // n 代表成功读到的字节数
   		if err != nil && err == io.EOF{
   			fmt.Println("拷贝完成")
   			return
   		}
   		f_w.Write(buf[:n]) // 每次取到n写入新文件
   	}
   }
   
   // 0x01 历指定目录下的文件
   package main
   
   import (
   	"fmt"
   	"os"
   	"bufio"
   	"io"
       "strings"
   )
   
   func main() {
   	var path string
   	fmt.Println("请输入你想要遍历的目录地址：")
   	fmt.Scan(&path)
   	listFile(path)
   }
   // 递归遍历
   func listFile(path string) {
   	d,err := os.OpenFile(path,os.O_RDONLY,os.ModeDir)
   	if err != nil {
   		fmt.Println("d出错了",err)
   		return
   	}
   	defer d.Close()
   
   	fileinfo,_ := d.Readdir(-1)
   
   	for _,v := range fileinfo {
   		if v.IsDir() {
   			listFile(path + "/" + v.Name())
   		} else {
   			size := v.Size()/1024/1024
   			unit := "Mb"
   			if (size == 0) {
   				size = v.Size()
   				unit = "b"
   			}
                // 配合上面拷贝文件使用
                copy(path + "/" + v.Name(),"../../" + v.Name())
                // 判断后缀
   			// if (strings.HasSuffix(v.Name(),".jpg")) {
   				fmt.Println("文件名:",v.Name(),"\t文件大小",size,unit,"\t最后修改时间",v.ModTime())
   			// }
   		}
   	}
   }
   
   // 0x02 按照指定分割字符 读取 
   
   func main() {
   	f,err := os.OpenFile("../xxx.txt",os.O_RDWR,6)
   	if err != nil {
   		// 报错了输出并终止
   		fmt.Println("err=",err)
   		return
   	}
   	defer f.Close()
   	fmt.Println("success")
   	reader := bufio.NewReader(f)
   	for {
   		buf,err := reader.ReadBytes('\n')
   		if err != nil && err == io.EOF {
   			fmt.Println("文件输出完毕")
   			return
   		} else if err != nil {
   			fmt.Println("buf err=",err)
   			return
   		}
   		fmt.Println(string(buf))
   	}
   }
   
   ```

7. 目录操作

   打开目录 `OpenFile`  已只读，只写，等等打开文件的方式

   ​		参数1 name 文件名 绝对路径 相对路径

   ​		参数2 打开文件权限 ：`O_RDONLV , O_WRONLY, O_RDWR`

   ​		参数3 一般传6  读取目录时 传`os.ModeDir`

   ​		返回值：返回一个可以读写目录的文件指针。

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

