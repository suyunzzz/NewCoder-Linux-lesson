# Chapter1-6

## Chapter1-Linux开发环境搭建

### 1、vscode远程ssh连接服务器

1. 获取服务器的IP地址
2. 本地vscode安装插件 `Remote Development`
3. 单击红色圆形，配置vscode

> ![image-20201201214553933](https://gitee.com/suyunzzz/img/raw/master/img/20201201214554.png)
>
> 其中，红色矩形内，分别为ip地址、服务器用户名

4. 这样就成功ssh登录服务器啦

> ![](https://gitee.com/suyunzzz/img/raw/master/img/20201201214832.png)

> ![image-20201201215040894](https://gitee.com/suyunzzz/img/raw/master/img/20201201215040.png)

### 2、解决ssh连接需要每次都需要密码的问题

1. 打开本地cmd，输入`ssh-keygen -t rsa`，会在本地这个地方生成一个新的文件--公钥

   ![image-20201201220207489](https://gitee.com/suyunzzz/img/raw/master/img/20201201220207.png)

2. 复制公钥内容，在服务器端的~/.ssh路径下新建一个文件`authorized_keys`，将公钥的内容放进去。这样每次登录就不需要密码了。

   ![image-20201201220442965](https://gitee.com/suyunzzz/img/raw/master/img/20201201220442.png)

   

## Chapter2-GCC(1)

```bash
// 编译
gcc test.c -o test
```

### 1、工作流程

![image-20201202093739016](https://gitee.com/suyunzzz/img/raw/master/img/20201202093739.png)



### 2、GCC常用参数选项

![image-20201202094554281](https://gitee.com/suyunzzz/img/raw/master/img/20201202094554.png)

![image-20201202094604566](https://gitee.com/suyunzzz/img/raw/master/img/20201202094604.png)







## Chapter4-静态库制作

### 1、如何制作静态库?

![image-20201202094825733](https://gitee.com/suyunzzz/img/raw/master/img/20201202094825.png)

1. 获取.o文件

```bash
// 编译编译不链接
gcc -c add.c mul.c sub.c div.c
```

> 如果有头文件，还需要再编译的时候增加`-I` 参数

2. 打包

```bash
ar rcs libname.a xxx.o xxx.o ...
```



## Chapter5-静态库使用

- 复制

```bash
cp -r calc library ../chapter5
```

> 其中，-r代表递归复制

- 链接静态库，生成可执行文件

```bash
 gcc main.c -o app -Iinclude -lcalc -Llib
```

> 其中，-o指定输出文件名称，-I指定头文件搜索路径，-l指定库名称（注意是库名称，不是库文件名称），-L指定库的搜索路径









## Chapter6-动态库的制作和使用

### 1、制作动态库

![image-20201202101243319](https://gitee.com/suyunzzz/img/raw/master/img/20201202101243.png)

```bash
 gcc -c -fpic add.c mult.c sub.c div.c
 
 gcc -shared *.o -o libcalc.so        
```

### 2、使用动态库链接可执行文件

```bash
$ gcc main.c -o app_shared  -Iinclude -Llib -lcalc
```

- **使用错误**

> `./app_shared`遇到错误：
>
> $ ./app_shared 
> ./app_shared: error while loading shared libraries: libcalc.so: cannot open shared object file: No such file or directory

> **解决**
>
> 对可执行文件`app_shared`指向`ldd app_shared`，得到如下依赖列表：
>
> ```bash
> $ ldd app_shared    
> 	linux-vdso.so.1 =>  (0x00007fffa75ec000)
> 	libcalc.so => not found
> 	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f5b7a5d6000)
> 	/lib64/ld-linux-x86-64.so.2 (0x00007f5b7a9a0000)
> 
> ```
>
> - ldd 命令===list dynamic dependencies
>
> 很明显，没找到我们的.so文件。
>
> - **方法1，添加路径，用户级别配置**
>
> 添加路径即可解决：
>
> ```bash
> $ export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/ubuntu/suyunzzz/cpp/chapter6/library/lib     
> ```
>
> 添加路径后：
>
> ```bash
> $ ldd app_shared                                                                        
> 	linux-vdso.so.1 =>  (0x00007ffffe1e8000)
> 	libcalc.so => /home/ubuntu/suyunzzz/cpp/chapter6/library/lib/libcalc.so (0x00007f1ea8f3b000)
> 	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f1ea8b71000)
> 	/lib64/ld-linux-x86-64.so.2 (0x00007f1ea913d000)
> ```
>
> 执行：
>
> ```bash
> $ ./app_shared
> a = 20, b = 12
> a + b = 32
> a - b = 8
> a * b = 240
> a / b = 1.666667
> ```
>
> - **方法2，修改共享库配置文件/etc/ld.so.conf**
>
> 1. 设置
>
> ```bash
> sudo gedit /etc/ld.so.conf
> ```
>
> 2. 添加库路径
>
> ```conf
> include /etc/ld.so.conf.d/*.conf
> include /usr/local/lib
> /home/ubuntu/suyunzzz/cpp/chapter6/library/lib(新增的)
> ```
>
> 3. 使配置立即生效
>
> ```bash
> sudo ldconfig
> ```
>
> - **方法3，系统级别的配置**
>
> 1. 修改文件
>
> ```bash
> sudo gedit /etc/profile
> ```
>
> 2. source生效
>
> ```bash
> source /etc/profile
> ```

> 参考：https://blog.csdn.net/qc530167365/article/details/91491851

## Chapter7-动态库加载失败的原因

![image-20201202224650061](https://gitee.com/suyunzzz/img/raw/master/img/20201202224650.png)



## chapter8-解决动态库加载失败的问题

- 显示环境变量

```bash
env			// 终端输入env
```



## chapter9-静态库和动态库的对比

![image-20201203092030913](https://gitee.com/suyunzzz/img/raw/master/img/20201203092031.png)

### **1、静态库制作**

![image-20201203092117166](https://gitee.com/suyunzzz/img/raw/master/img/20201203092117.png)

```bash
gcc -c add.c		// 生成add.o目标文件
```

```bash
ar rcs libname.a add.o		// 打包生成libname.a静态库
```



### **2、动态库制作**

![image-20201203092130988](https://gitee.com/suyunzzz/img/raw/master/img/20201203092131.png)

```bash
gcc -c -fpic add.c		// 生成目标文件 add.o
```

```bash
gcc -shared add.o -o libadd.so			// 生成动态库
```

### 3、静态库优缺点

![image-20201203092748368](https://gitee.com/suyunzzz/img/raw/master/img/20201203092748.png)

### 4、动态库优缺点

![image-20201203092900808](https://gitee.com/suyunzzz/img/raw/master/img/20201203092900.png)