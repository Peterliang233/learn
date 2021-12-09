+ 什么是内存逃逸，在什么情况下发生的，原理是什么？
  + 每一段程序，每个函数都有自己的内存区域存放自己的局部变量，返回地址等，这些内存会由编译器在栈中分配，栈空间一般是用来存放函数的参数和一些局部变量的。每个函数在运行的时候都会分配一个栈帧，在函数结束后会自动销毁。但是有些变量我们想在函数结束之后依然可以使用，那么就需要将这些变量分配到堆空间里面，这种从栈上逃逸到堆上的现象就成为了内存逃逸。
  + 虽然，在大多数的语言中都有GC机制，GC机制会对分配到堆上的对象自动管理，当某一个对象不可达的时候，它将会被回收并且重复使用。但是我们在程序中会分配大量的内存，这种情况下就要我们尽量减少在堆上分配内存，一种方法就是让栈中的内存尽可能留在栈里面。
  + 这里我们比较一下堆和栈的分配的内存的区别，在栈中的内存，只需要CPU的两个指令就能进行回收和使用，一个是PUSH，另一个是RELEASE。但是堆中的内存是通过自动分配的，不可预知性比较大，而且容易造成内存碎片，堆上的内存只能通过GC进行回收。所以就需要我们对程序进行逃逸分析，提高程序的运行速度。
  + 几种常见的内存逃逸的情况
    + 函数返回局部指针变量
    + interface类型逃逸
    + 闭包产生的逃逸
    + 变量大小不确定及栈空间不足引发逃逸
  + 如何避免逃逸分析
    + 接口类型的方法是动态调度的，不能在编译阶段就确定下来，所有类型结构转换成接口的过程都会涉及到内存逃逸的情况发生，如果对于性能要求比较高的而且访问频次比较高的函数调用，尽可能避免使用接口类型。
    + 减少外部引用，如指针
    + 在切片使用场景中一般实在函数传递的场景，而且切片可能会涉及动态内存分配。
  
+ 如何交替打印数字和字母

  + 我们准备两个无缓冲区的channel来控制goroutine的协作。开两个协程，一个用来打印数字，另一个用来打印字母。首先是用来打印数字的协程先使用，然后往打印字符的channel内传递参数，以此循环打印，最后利用一个wait等待打印结束之后推出即可。

  + 具体的执行步骤是

    + 定义两个bool类型的channel赋值给letter和number
    + 定义一个等待组，阻塞协程等协程执行结束之后才退出。
    + 启动第一个协程，死循环，但是由于select多路复用，number为false的值阻塞，发生协程切换。
    + 等待组原子+1
    + 启动第二个协程，并传入协程空间，死循环去select letter但是由于letter false发生阻塞。
    + 此时number channel推入true值，协程A中的number值获取true，代码执行
    + 执行完A中的协程之后，letter放入bool true。
    + 携程B触发，协程B执行，但是wait不走done，所以程序依然不会退出，再次将ture推入number，以此进行循环操作，知道打印结束。
    + 最后wati.done()被执行，主进程退出。

  + 代码如下

    ```go
    package main
    
    import (
            "fmt"
            "sync"
    )
    
    func main(){
    	letter, number := make(chan bool), make(chan bool)
    	wait := sync.WaitGroup{}
    
    	go func(){
    		i := 1
    		for {
    			select {
    			case <- number:
    				fmt.Print(i)
    				i++
    				fmt.Print(i)
    				i++
    				letter <- true
    			}
    		}
    	}()
    
    	wait.Add(1)
    
    	go func(wait *sync.WaitGroup){
    		i := 'A'
    		for {
    			select {
    			case <- letter:
    				if i >= 'Z'{
    					wait.Done()
    					return
    				}
    				fmt.Print(string(i))
    				i++
    				fmt.Print(string(i))
    				i++
    				number <- true
    			}
    		}
    	}(&wait)
    	number <- true
    	wait.Wait()
    }
    ```

+ 判断一个字符串里面是否具有相同的字符，要求不能使用其他的数据结构。这里我们使用两种方法进行求解

  + 一种是利用strings.Count()来判断一个字符串中另一个字符串出现的次数。

  + 一种是利用strings.LastIndex()来判断一个字符串中另一个字符串最后出现的位置。

  + ```GO
    package main
    
    import (
    	"fmt"
    	"strings"
    )
    
    
    func isUniqueString(s string) bool {
    	if strings.Count(s, "") > 3000 {
    		return false
    	}
    	for index, v := range s {
    		if v > 127 {
    			return false
    		}
    		// if strings.Count(s, string(v)) > 1{
    		// 	return false
    		// }
    		if strings.LastIndex(s, string(v)) != index {
    			return false
    		}
    	}
    
    	return true
    }
    
    func main(){
    	s := "hello"
    	fmt.Println(isUniqueString(s))
    }
    ```

+ channel底层

  + buf，一个循环链表，来缓存数据
  + sendx和recvx是记录buf用来发送数据和接收数据的index
  + lock是一个互斥锁
  + recvq和sendq是用来接收和发送的goroutine抽象出来的结构体的队列，是一个双向的链表。