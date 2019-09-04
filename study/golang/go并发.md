## Go并发

```json
cpu 最小时间单位 纳秒
并行： 借助多核 cpu 实现。			（真 并行）
并发： 
	宏观：用户体验上，程序在并行执行。
	微观：多个计划任务，顺序执行。在飞快的切换。轮换使用 cpu 时间轮片。 		【假 并行】
进程并发：
	程序：编译成功得到的二进制文件。	占用 磁盘空间。	死的	1	1
	进程：运行起来程序。 占用系统资源。（内存）		活的	N	1
进程状态：
	初始态、就绪态、运行态、挂起（阻塞）态、终止（停止）态。
线程并发：
	线程：LWP 轻量级的 进程。最小的执行单位。 —— cpu分配时间轮片的对象。
	进程： 最小的系统资源分配单位。
同步：
	协同步调。规划先后顺序。
线程同步机制：
	互斥锁（互斥量）：	建议锁。 拿到锁以后，才能访问数据，没有拿到锁的线程，阻塞等待。等到拿锁的线程释放锁。
	读写锁：一把锁（读属性、写属性）。 写独占，读共享。 写锁优先级高。
协程并发：
	提高程序执行的效率。
```

### 进程、线程、协程区别：

### 	进程：稳定性强 （最小的系统资源分配单位）
	线程：节省资源 （最小的执行单位）
	协程：效率高

### 例子

	老板生产手机：
	
	生产线 —— 设备、材料、厂房 —— 进程。（资源分配单位）
	工人 —— 线程。	—— 单进程、单线程的 程序。
	50 工人 —— 50 线程。 	——单进程、多线程的 程序。
	10 条生产线 —— 500 工人 —— 。多进程、多线程的 程序。
	利用闲暇时间义务搬砖 —— 协程。—— 多进程、多线程、多协程 程序。

4g内存 是指 可用范围 （32位操作系统） 2^32 字节

![](../img/进程和线程.png)

线程并发访问共享资源

![线程并发访问共享资源](../img/线程并发访问共享资源.png)

## go程序

创建Goroutine程

​	创建于进程中。 直接使用 go 关键字，放置于 函数调用前面，产生一个 go程。 并发。

```go
go func(){ // 这就是一个goroutine

}()

go 函数名()
```

Goroutine的特性：【重点】

​	**主go程结束，子go程随之退出。**

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	go func() {  // 子go程
		for i:=0;i<5;i++ {
			fmt.Printf("子go程序%d\n",i)
			time.Sleep(time.Second)
		}
	}()
    
	fmt.Printf("主go程序") // 主go程结束，子go程随之退出
}
```

## runtime包常用函数 （go程序）

```go
0x00 runtime.Gosched()： 
		出让当前go程所占用的 cpu时间片。当再次获得cpu时，从出让位置继续回复执行。—— 时间片轮转调度算法。
func main() {
	go func(){
		for i:=0;i<120000;i++ {
			fmt.Printf("子go程序%d\n",i)
			// time.Sleep(time.Second)
		}
	}()
	for {
		runtime.Gosched() // 出让当前时间片 使当前go程放弃处理器，以让其它go程运行。它不会挂起当前go程，因此当前go程未来会恢复执行。
		fmt.Println("主")
		// time.Sleep(100*time.Microsecond)
	}
}
0x01 runtime.Goexit()：
		return：	返回当前函数调用到调用者那里去。 return 之前的 defer 注册生效,之后的不生效（未注册所以不生效）。
		Goexit():   结束调用该函数的当前go程。Goexit():之前注册的 defer都生效。
func main() {
	go func(){
		for i:=0;i<120000;i++ {
			fmt.Printf("子go程序%d\n",i)
			runtime.Goexit() //Goexit终止调用它的go程。其它go程不会受影响。Goexit会在终止该go程前执行所有defer的函数。在程序的main go程调用本函数，会终结该go程，而不会让main返回。因为main函数没有返回，程序会继续执行其它的go程。如果所有其它go程都退出了，程序就会崩溃。
			// time.Sleep(time.Second)
		}
	}()

	for {
		;
		// time.Sleep(100*time.Microsecond)
	}
}
0x02 runtime.GOMAXPROCS():
		设置当前 进程使用的最大cpu核数。 返回 上一次调用成功的设置值。 首次调用返回默认值。
func main() {
	n := runtime.GOMAXPROCS(8) //设置当前 进程使用的最大cpu核数。 返回 上一次调用成功的设置值。 首次调用返回默认值。
	fmt.Println(n)
	return
	for {
		go fmt.Print(0)
		fmt.Print(1)
	}
}
0x03 runtime.GC(): 
		GC执行一次垃圾回收。
```

## Channel 是一种数据类型。对应一个“管道”（通道FIFO）

channel 定义`make(chan 传递的数据类型,容量) 容量等于0是无缓冲区channel 大于0是有缓冲区channel`

`make(chan int) or make(chan string,1)`

channel有两个端：

​	一端：写端（传入端）   ` chan <-`
​	另一端： 读端（传出端）`<- chan`
​	要求：读端和写端必须同时满足条件，才能在`chan`上进行数据流动。否则，**谁操作谁阻塞**

```go
var channel = make(chan int)
func printer(s string) {
	for _,ch := range s {
		fmt.Printf("%c",ch)
		time.Sleep(1000*time.Millisecond)
	}
}

func person1() {
	printer("hello")
	channel <- 8 // 写
}

func person2() {
	<- channel // 读
	printer("world")
}

func main() {
	go person1()
	go person2()
	for {
		;
	}
}
```

**channel同步，数据传递**

​	写端：	 `ch <- "hehe"`	。写端写数据，读端不在读。阻塞
​	读端：	`str := <- ch`	。读端读数据， 同时写端不在写，读端阻塞。
​	`len(ch) `： channel 中剩余未读取数据个数。 cap（ch）： 通道的容量。

**无缓冲channel： —— 同步通信**

​	创建： `ch := make(chan int)  或 make(chan int, 0)`
​	通道容量为0，` len = 0 `。 不能存储数据。
​	channel 应用于 两个go程中。	一个读，另一个写。  
​	具备同步的能力。  读、写同步。（打电话）

**有缓冲channel：—— 异步通信**

​	创建： `ch := make(chan int, 5)`
​	通道容量为非0。`len(ch)` ： channel 中剩余未读取数据个数。 cap（ch）： 通道的容量。
​	channel 应用于 两个go程中。一个读，另一个写。  
​	缓冲区可以进行数据存储。存储至 容量上限，阻塞。 具备 异步 能力，不需同时操作channel缓冲区（发短信）

**关闭channel： 无、有缓冲**

```go
确定不再相对端发送、接收数据。关闭channel。 使用 close(ch) 关闭channel
对端可以判断 channel 是否关闭：
	if num， ok := <-ch ;  ok == true {}
	如果对端已经关闭， ok --> false . num无数据。
	如果对端没有关闭， ok --> true . num保存读到的数据。
可以使用 range 替代 ok：
	for num := range ch {		// ch 不能替换为 <-ch
	}
总结:
	1. 数据不发送完，不应该关闭。
	2. 已经关闭的channel， 不能再向其写数据。 报错：panic: send on closed channel
	3. 写端已经关闭channel， 可以从中读取数据。
			读无缓冲channel： 读到0 。 —— 说明：写端关闭。
			读有缓冲channel： 如果缓s冲区内有数据，先读数据。读完数据后，可以继续读。 读到 0

func main() {
	ch := make(chan int)
	go func(){
		for i:=0;i<10;i++ {
			ch <- i
			// fmt.Printf("子go程序%d\n",i)
		}
		close(ch) //关闭channel 不能再写入
		
	}()
	// fmt.Println(cap(ch))
	// for {
	// 	if num,ok := <- ch;ok {
	// 		fmt.Println("asd",num)
	// 	} else {
	// 		fmt.Println("关闭后",<- ch)
	// 		break;
	// 	}
	// }
	for num := range ch {
		fmt.Println("asd",num)
	}
}
```

**单向channel**

```go
默认的channel 是双向的。 var ch chan int	ch = make(chan int)
单向写channel:	var sendCh chan <-	 int	sendCh = make(chan <- int)	不能读操作
单向读channel:	var  recvCh  <- chan int	recvCh = make(<-chan int)
转换：
	1. 双向channel 可以 隐式转换为 任意一种单向channel 
		sendCh  = ch
	2. 单向 channel 不能转换为 双向 channel
		ch = sendCh/recvCh   error！！！
传参： 传【引用】

单向channel函数事例

func send(out chan <- int,rand int) {
	out <- rand
}

func read(r <- chan int) {
	n := <- r  
	fmt.Println("read函数读到了",n)
}
func main() {
	ch := make(chan int)
	go func(){
		send(ch,256)
	}()
	read(ch)
}
```

### 定时器

**Timer：创建定时器，指定定时时长，定时到达后。 系统会自动向定时器的成员 C 写 系统当前时间。 （对 `chan` 的写操作）**

```go
type Timer struct {
   C <-chan Time
   r runtimeTimer
}
time.Sleep()
time.NewTimer
time.After

读取 Timer.C 得到 定时后的系统时间。并且完成一次  chan 的 读操作。

time.After() 定时： 	
	指定定时时长，定时到达后。 系统会自动向定时器的成员 写入 系统当前时间。
	返回 可读 chan 。 读取，可获得系统写入时间。

总结： Sleep、NewTimer、After —— time包

定时器的 停止、重置：
	1） 创建定时器 myTimer := time.NewTimer(2 * time.Second)
	2)  停止：myTimer.Stop	—— 将定时器归零。    <-myTimer.C 会阻塞
	3） 重置：myTimer.Reset(time.Second)


例子：

package main

import (
	"time"
	"fmt"
)

/*func main()  {
	fmt.Println("当前时间：", time.Now())
	// 创建定时器
	myTimer := time.NewTimer(time.Second * 2)
	nowTime := <- myTimer.C  // chan 类型
	fmt.Println("现下时间：", nowTime)
}*/

// 3 种定时方法
/*func main()  {
	// 1 . sleep
	time.Sleep(time.Second)

	// 2. Timer.C
	myTimer := time.NewTimer(time.Second * 2)		// 创建定时器， 指定定时时长
	nowTime := <- myTimer.C  						// 定时满，系统自动写入系统时间
	fmt.Println("现下时间：", nowTime)

	// 3 time.After
	fmt.Println("当前时间：", time.Now())
	nowTime2 := <-time.After(time.Second * 2)
	fmt.Println("现下时间：", nowTime2)
}*/

// 定时器的停止和重置
func main()  {

	myTimer := time.NewTimer(time.Second * 10)		// 创建定时器。
	myTimer.Reset(1 * time.Second)		// 重置定时时长为 1
	go func() {
		for {
			<- myTimer.C
			fmt.Println("子go程，定时完毕")
		}
	}()

	//myTimer.Stop()		// 设置定时器停止
	for {
		;
	}
}
```

**周期定时**

```go
type Ticker struct {
	C <-chan Time 
	r runtimeTimer
}

1） 创建周期定时器 myTicker := time.NewTicker(time.Second)
	定时时长到达后，系统会自动向 Ticker 的 C 中写入 系统当前时间。 
	并且，每隔一个定时时长后，循环写入 系统当前时间。 
2） 在 子 go 程中循环读取 C。获取系统写入的时间。 


例子

package main

import (
	"time"
	"fmt"
)

func main()  {

	quit := make(chan bool)		// 创建一个判断是否 终止的channel

	fmt.Println("now:    ", time.Now())
	myTicker := time.NewTicker(time.Second * 2)  // 定义一个周期定时器

	i := 0
	go func() {
		for {
			nowTime := <-myTicker.C
			i++
			fmt.Println("nowTime:", nowTime)
			if i == 3 {
				quit <- true	// 解除 主go程阻塞。
				break // return // runtime.Goexit
			}
		}
	}()

	<-quit		// 子go程 循环获取 <-myTicker.C 期间，一直阻塞
}

```

select：
	作用： 用来监听 channel 上的数据流动方向。 读？写？

	用法： 参考 switch case 语句。 但！case后面必须是IO操作，不可以任意写判别表达式。
	
	注意事项：
		1. 监听的case中，没有满足监听条件，阻塞。
	
		2. 监听的case中，有多个满足监听条件，任选一个执行。
	
		3. 可以使用default来处理所有case都不满足监听条件的状况。 通常不用（会产生忙轮询）
	
		4. select 自身不带有循环机制，需借助外层 for 来循环监听
	
		5. break 跳出 select中的一个case选项 。类似于switch中的用法。

select实现fibonacci数列：

	1  1  2  3  5  8  13  21  34  55   89 
	x = y
	y = x+y	