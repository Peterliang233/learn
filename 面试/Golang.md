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
  
+ GPM 模型

  + GPM分别代表了三个角色， 分别是Goroutine,Processer,Machine。
    + Goroutine就是我们常用的用go关键字创建的执行体。它对应着一个结构体g,结构体里面保存了goroutine的堆栈信息。
    + Machine表示操作系统的线程。
    + Processor表示的是处理器，有了它才建立G和M的的联系。
  + Goroutine是go关键词创建的执行单元。也是大家比较熟悉的协程。协程不为操作系统熟知。它由编程语言层面实现，上下文切换不需要经过内核态。而且占用的空间非常小。
  + Goroutine调度相关的数据存储在sched里面，在协程切换，恢复上下问的时候用到。
  + M是操作系统对应的线程，一般被设置为电脑内核数，线程数是CPU的个数的原因是，每个线程分配到一个CPU上就不至于出现线程的上下文切换。保证了操作系统的开销最低。
  + M里面比较重要的东西是g0和curg，g0会深度参与运行时的调度过程，比如goroutine的创建，内存的分配等等。curg代表了协程上执行的goroutine的数量。
  + Processor负责Machine和Goroutine之间的连接。它能提供线程需要的上下文环境。也能分配G到它应该去的线程上面执行。主要负责线程的调度，使得每个G得到合理的利用。
  + Processor的存储结构里面除了性能追踪，垃圾回收，计时器等相关的字段，以及处理器的待运行队列，队列中存储了待执行的Goroutine的列表。

  + 使用过程，在启动了若干个线程之后，会使用若干个对应的处理器Processor，这个时候，如果一个goroutine被创建，在进行函数体地址，参数起始地址和参数长度等信息以及调度相关属性的更新之后，它就要进入一个处理器的等待队列里面等待被执行。如果有开了一个新的协程，那么就继续分配到其余空闲的等待队列里面。这里可以把处理器和对应的线程看作是一个窗口，开始了新的协程就相当来了一个新的要处理事情的人，如果有空闲的窗口就会自动分配到空闲的窗口里面，没有的话就会先放到每个Processor的对应的私有队列里面，如果所有的私有队列都满了，那么就会放到公共队列里面。等待之后空闲的处理器去取用。
  + ![img](https://raw.githubusercontent.com/lifei6671/interview-go/master/images/1718813abb679031.jpg)
  
  + M会先在自己的私有队列里面取用，如果没有的话会去全局队列里面取用，如果还是没有，就会偷取其它队列里面的。如果还是没有，那么P和M就会断开。
  + 如果两个Goroutine正在通过channel通信，那么M不会等待，而是去找其他需要处理的goroutine
  + 如果Goroutine进入了系统调用，那么M也会跟着进入系统调用，此时的P会去寻找其他空闲的M和G。

+ Goroutine调度器(用户态线程)
  + 首先，为什么不是用操作系统自带的线程？
    + 操作系统自带的线程太重了，操作系统的线程是内核态的，在分配内存的使用会预先分配一个比较大的内存栈，但是其实我们一个系统线程不会使用这么多的内存，容易造成不必要的浪费。而且，系统级线程一旦被创建，栈空间的大小一旦被创建和初始化完成就不能改变，这决定了某些特殊的场景里面可能有栈溢出的风险。由于是系统级线程，这就导致线程之间的切换需奥进入内核，而进入内核的消耗比较大。
    + Goroutine则相反，这是用户态线程。线程之间的切换无需进入内核，开销比系统线程小。而且，goroutine一开始分配的空间大小只有2-4KB，而且如果中途不够的话，会自动扩张。这样，就可以避免不必要的空间浪费和栈溢出的风险。
  + 线程和调度器
    + goroutine和操作系统的线程之间实现了一个多对多的线程模型N:M,M个goroutine运行在N个操作系统线程上面，内核负责对这N个线程进行调度，N个线程负责对这M个goroutine进行调度，这其实就是一个二级的线程模型。
  + 调度器数据结构概述
    + goroutine被存储在一个g结构体里面，为了便于工作线程寻找被调度起来运行的goroutine，Go调度器引入了一个schedt结构体。一方面保存调度器的自身的状态信息。另一个方面还拥有一个用来保存goroutine的运行队列。这个队列被成为全局队列。同时每个工作线程还有一个私有的局部队列。
  
+ 如果挑选出一个goroutine出来运行？

  + 在go源码里面，有一个schedule函数，负责决定如何挑选出一个goroutine出来运行。
  + 第一步，从全局队列里面寻找goroutine，设计的是工作线程没经过61次调度就会从全局队列里面选取一个goroutine进行调度，这样确保了全局队列里面的goroutine也可以得到运行。全局队列是所有的工作进程都可以访问的，所以在访问之前应该加锁。
  + 第二步，从本地工作队列里面选取一个goroutine。
  + 第三步，从其他工作队列里面偷取goroutine出来运行，如果上一布步也没有找到运行的goroutine，那么会调用findunnable从其他工作线程偷取。但是在每次偷取的时候会先尝试从全局队列和本地队列寻找需要运行的goroutine。