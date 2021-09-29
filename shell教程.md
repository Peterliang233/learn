### 基本概念

+ shell是一种用C语言编写的程序，他为用户和Linux提供了交互的桥梁，同时我们通过shell可以访问操作系统内核的服务。

### 基本命令

+ 执行shell脚本
  + 首先，我们新建一个后缀为*.sh文件，在文件里面编写好相关的代码，然后我们就可以通过`./shell脚本文件名`或者指定解释器`/bin/sh shell文件名` 执行这个shell脚本文件的程序了。

 + echo

    + 在交互的界面输出一些基本的信息

    + ```shell
      echo "Hello World"
      ```

+ $符号

  + 这个符号在我们的shell脚本文件里面经常被使用到，一般是用来获取我们在shell脚本里面定义的变量。

  + ```shell
    myurl="http://peterliang.top"
    echo $myurl
    ```

+ shell变量命名

  + shell文件的命名方法和C语言的变量的要求基本一直，我们可以类比C语言的相关要求。这里要注意一些脚本命令的空格和不空格的一些地方。

+ 数组

  + 数组的定义就是利用类似`arr=(变量1 变量2 变量3...变量n)`

  + ```shell
    arr=(apple banana pear)
    echo "${arr[0]}"
    ```

+  传递参数

  + 我们可以在执行shell脚本的时候通过命令的形式传递我们的参数。但是这里我们要注意的是这个传递参数是通过空格进行分隔命令的，我们利用数字对参数进行编号，然后在我们的shell脚本里面就可以利用$符号进行引用了。

  + ```shell
    echo "This is a $1"
    echo "This is a $2"
    echo "This is a $3"
    ```

+ 数学运算

  + 原声的bash不支持数学运算，所以我们需要使用`awk`和`expr` 来帮助我们更好的处理数学运算。但是要注意这里使用的是 反引号

  + ```shell
    val=`expr 2 \* 3`
    echo "ans=$val"
    val=`expr 2 + 4`
    echo "ans=$val"
    val=`expr 4 / 2`
    echo "ans=$val"
    val=`expr 5 - 3`
    echo "ans=$val"
    ```

+ 关系运算符

  + 我们可以利用一下的符号来比较两个可比的变量之间的大小，主要有`-ge -eq -ne -gt -lt -le`根据英文单词进行区分即可。
  + if判断

  + ```shell
    a=10
    b=20
    if [ $a -eq $b ]
    then
        echo "equal"
    else
        echo "Not equal"
    fi
    ```

+ 布尔运算符

  + 这一类的运算符包含三种，`! -o -a`，分别表示的是取反，或操作和与操作

+ 逻辑运算符

  + 逻辑与或者逻辑或，`|| &&`

+ 字符串运算操作

  + 主要包括以下的一些操作符号。`=  ！=  -z  -n  $`

+ 文件测试运算符

  + 这一类运算符一般是用来检测Unix文件的各种属性的
  + ` -b file`检查文件是否是块设备的文件
  + `-c file`检查文件是否为字符设备的文件
  + `-d file`检测是否为目录
  + `-f file`检测是否为普通文件
  + `-g file`检测是否设置了SGID位
  + `-k file` 检测是否设置了粘着位
  + `-p file` 检测是否有名管道
  + `-u file`检测文件是否设置了SUID位
  + `-r file`检测文件是否可以读
  + `-w file`检测文件是否可以写
  + `-x file`检测文件是否可以执行
  + `-s file`检测文件是否为空
  + `-e file`检测文件(目录是)否存在

+ 输出日期

  + ```shell
    echo `date`
    ```

+  $printf$命令

  + 对于输出的命令，不仅仅只有前面的`echo`可以实现，还有$printf$，通常我们也可以利用和C语言语法类似的进行处理，我们可以仿照C语言的$printf$的用法，这里支持一些格式化的输出操作。

  + ```shell
    echo "Hello World"
    printf "%-5d %-3d\n" 12 4
    printf "%-s %-f\n" 你好 1.24
    ```

+ case用法

  + ```shell
    num=1
    case $num in
        1) echo "Yes 1"
        ;;
        2) echo "Yes 2"
        ;;
        3) echo "Yes 3"
        ;;
    esac
    ```

+ 与用户进行交互，从终端中允许用户输入一些数据

  + ```shell
    while :
    do
        echo "input:"
        read num
        case $num in
            1|3|5) echo "contine"
            ;;
            *) echo "Yes"
                break
                echo "game over"
            ;;
        esac
    done
    ```

+ 函数

  + 函数分为函数的定义和函数的调用

  + ```shell
    testFun(){
        echo "Hello,This is a function"
    }
    
    testFun
    ```

+ 参数传递

  + 参数的传递和我们通过终端直接获取参数的方法一样，在函数的定义的时候我们不会传输给出参数的格式，而是在函数的调用的时候传递参数进入

  + ```shell
    testFun(){
        echo "参数1：$1"
        echo "参数2: $2"
    }
    
    testFun 4 2
    ```

+ 输出重定向
  + 有的时候我们需要将我们的输入或者一些数据保存到文件里面，这个时候就需要我们使用输出重定向的方法进行处理。
  + 这些重定向的符号一般分为三种。`>` 这种符号就是用新的数据覆盖老的数据。`>>`这种符号一般就是在老的数据的后面追加新的数据