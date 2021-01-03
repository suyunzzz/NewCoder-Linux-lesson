# 命名规则

![image-20201213204750341](https://gitee.com/suyunzzz/img/raw/master/img/20201213204750.png)

---

# 工作原理

> 检查更新：以最新的依赖为准
>
> 所有行都是为第一行服务的，如果其他行的`目标`与第一行的`依赖`没有关系，那么不执行该行

![image-20201213211323765](https://gitee.com/suyunzzz/img/raw/master/img/20201213211323.png)



# 变量

![image-20201213211756134](https://gitee.com/suyunzzz/img/raw/master/img/20201213211756.png)

```
$ cat Makefile
src=sub.o add.o mult.c main.c div.c
target=app

${target}:${src}
	${CC} ${src}  -o ${target} 



sub.o:sub.c
	gcc -c sub.c -o sub.o
add.o:add.c
	gcc -c add.c -o add.o
```

# 模式匹配

![image-20201213212731197](https://gitee.com/suyunzzz/img/raw/master/img/20201213212731.png)

```
src=sub.o add.o mult.c main.c div.c
target=app

${target}:${src}
	${CC} ${src}  -o ${target} 



%.o:%.c
	${CC} -c $< -o $@

```

```
$ make
cc -c sub.c -o sub.o
cc -c add.c -o add.o
cc sub.o add.o mult.c main.c div.c  -o app 

```

# 函数

![image-20201213213039596](https://gitee.com/suyunzzz/img/raw/master/img/20201213213039.png)

![image-20201213213552592](https://gitee.com/suyunzzz/img/raw/master/img/20201213213552.png)

```
$ cat Makefile
src=${wildcard ./*.c} # sub.o add.o mult.c main.c div.c
objs=${patsubst %.c ,%.o, ${src}}
target=app

${target}:${objs}
	${CC} ${objs}  -o ${target} 



%.o:%.c
	${CC} -c $< -o $@
```

