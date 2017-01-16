# 常用
## ssh 测试端口是否通
ssh  134.64.29.31 -p 15002 -v

## telnet 测试端口是否通
telnet 132.35.77.23 8001

## ping 测试地址是否通
ping 132.35.77.23

## 查看进程
ps -ef|grep name

## 查看进程（排除搜索这条命令）
ps -ef|grep -v grep|grep name

## 查看程序执行时间
time program args

例如：time ls

## 查看最近修改的文件
ls -ltr

## a.exp文件
（asia）这是在../../tools/etc/Cmake里配置生成的一个文件

EXP = nm -B -BCpg $^ | awk '{ if ((($$2 == "T") || ($$2 == "D") || ($$2 == "B")) && (substr($$3,1,1) != ".")) { print $$3 } }' | sort -u > a.exp

LD = $(EXP); xlC_r -bE:a.exp -bh:5

主要用于链接


## oracle报错ORA-02019: connection description for remote database not found
原因：连接的数据库中没有这个表或这个表所在的数据库在当前数据库没有dblink(能描述的不准确)
解决方法：在你链接的数据库中建立一个同义词
create or replace synonym dr_le_phone_201610  for dr_le_phone_201610@SHUCRM1C.CRMDB1;

## AIX下的xlC编译器
xlc --- 是AIX下的c编译器

xlC --- 是AIX下默认的c++编译器，我们开发中就是使用的这个编译器

查看xlC的版本号：xlC -qversion

一般xlC编译C++的源代码，xlc编译C的源代码，如果混合C和C++的源代码，则使用xlC编译链接

一般情况下，我们习惯直接使用cc编译程序，实际上AIX的C/C++编译器还提供了其他的一些使用方法，如后缀为_r就有xlc_r、xlC_r，这里我们做一个完整的介绍。
AIX平台上缺省的C++编译器为xlC，而C的编译器有三个：
cc: 这是最习惯用的，扩展模式的C编译器

xlc: ANSI C编译器，使用UNIX的头文件

c89: ANSI C编译器，使用ANSI头文件

具体选择何种编译器，需根据程序的情况来选择，一般我们可以使用cc。另外，还有一些后缀供我们选择，结合以上的基本编译器，给我们提供了更大的选择面，如下：

_r    连接UNIX98标准线程库

_r4   连接POSIX Draft4（DCE）标准线程库

_r7   连接POSIX Draft7标准线程库

128   Double型类型为128位并使用连接相关的库

128_r Double类型为128位并使用U
NIX98线程库

128_r4  Double类型为128为并使用连接POSIX Draft4线程库

128_r7  Double类型为128位并使用连接POSIX Draft7线程库

另外，UNIX98已经兼容涵盖了UNIX95、POSIX标准的内容，所以，如果程序中使用到线程函数，只许增加后缀_r就可以了。举例说，编译连接ANSI C并使用POSIX线程库，可以使用xlc_r编译器，当然也可以使用xlc –lpthread的形式。
这里提到的只是最基本的东西，还有很多方面的内容并没有介绍到，例如如何生成使用动态库，这些更详细的东西需要参考相关的手册，或拨打IBM技术支持电话寻求技术支持

### 共享库使用的编译选项为

－bE:shsub.exp -bM:SRE -bnoentry

### 创建一个动态库：
编译共享库，对于 XL C/C++，必须用选项 -qmkshrobj 替代选项 -shared，如下所示
－qmkshrobj

### 添加编译选项-bstatic或-bdynamic指定其为静态链接或动态链接
