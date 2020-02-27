###基础概念

####1.MMU（memory management unit）
**功能：**
>（1）抽象  
>>将物理地址空间抽象为逻辑（虚拟）地址空间  

>（2）保护  
>>每个进程拥有独立的地址空间  

>（3）共享  
>>可以访问相同地址空间  

>（4）虚拟化  
>>更大的地址空间  

**管理方式：**

>（1）重定位（relocation）  
>>直接使用物理地址，换台机器就需要重定位且一个进程的地址必须是连续的  

>（2）分段（segmentation）   
>>将 逻辑地址空间 划分为 若干个段  
>>每一段的地址必须是连续的  

>（3）分页（paging）  
>>将 物理地址空间 划分为大小相同的基本单位（**页帧**，也叫帧，frame，大小为2^n）  

>>将 逻辑地址空间 划分为大小相同的基本单位（**页面**，也叫页，page）  

>>**页表** 中存储 页面（逻辑地址）到 页帧（物理地址）的映射关系  

>（4）虚拟内存（virtual memory）  
>>使用虚拟地址空间，且基本单位是页  

####2.物理地址
硬件支持的地址空间，起始地址0，最大地址为2^32-1（当有32条地址线时）

####3.逻辑（虚拟）地址
  进程看到的地址，起始地址为0

####4.页表
  在页表中，每个逻辑页面，都有一个页表项
  页表项的组成：**页帧号** 和 **页表项标志**
```shell
页表项标志：
  存在位（resident），表示存在一个页帧与该页面相对应
  修改位（dirty），表示这个页面中的内容已经修改
  引用位（clock/reference），表示是否访问过这个页面中的内容
```
####5.段页式存储
  将 **逻辑地址空间** 划分为若干个段，将每个段划分为若干个大小相等的页

***
###非连续内存分配

####1.连续内存分配的缺点
  分配给程序的物理内存必须连续
  存在外碎片和内碎片（内存利用率低）

####2.三种方式
* 段式
* 页式
* 段页式
***
###x86特权级

####1.有四个特权级：
* ring 0		
内核处于ring 0特权级
* ring 1
* ring 2
* ring 3		
应用程序处于ring 1特权级

####2.实现特权级切换的方式：中断