# CSR寄存器读写指令实现

要实现的指令是如下六条：

![image-20211026111457461](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211026111457461.png)

各个指令的介绍如下：（可见RISC-V手册P131）

![image-20211026111903314](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211026111903314.png)

需要：CSRs，寄存器堆

![image-20211026113119822](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211026113119822.png)

需要：CSRs，寄存器堆

![image-20211026113843480](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211026113843480.png)

需要：CSRs，寄存器堆

![image-20211026113934337](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211026113934337.png)

需要：CSRs，寄存器堆

![image-20211026114412533](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211026114412533.png)

需要：CSRs，寄存器堆

![image-20211026114257728](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211026114257728.png)



需要：CSRs，寄存器堆

![image-20211026173820638](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211026173820638.png)

![image-20211026174125191](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211026174125191.png)

![image-20211026174313350](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211026174313350.png)

这个我还没看懂

![image-20211026174505261](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211026174505261.png)

![image-20211026174635755](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211026174635755.png)

#### 关于CSR地址

CSR地址是12位的，但有很多区域是保留的，我们要实现的寄存器全集如下:

![image-20211026120639386](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211026120639386.png)

mhartid 是只读的硬件线程号（我理解的就是多核情况下的核号）

![image-20211026121002079](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211026121002079.png)

mstatus 跟踪并控制硬件线程当前的状态，这是一个复杂的寄存器，见P20

![image-20211026141552585](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211026141552585.png)

**mstatush 没有在手册里找到**

mip 是

![image-20211026144047457](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211026144047457.png)

mie 是中断使能寄存器

![image-20211026144756669](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211026144756669.png)

mideleg 是中断授权寄存器，

![image-20211026144942710](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211026144942710.png)

medeleg 是异常授权寄存器

![image-20211026145005528](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211026145005528.png)

mtvec 是存中断向量表基地址的寄存器

![image-20211026150351418](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211026150351418.png)

stvec 是存中断向量表基地址的寄存器

![image-20211026151251512](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211026151251512.png)

mepc 存储发生中断时的pc值，最低位永远是0

![image-20211026151526062](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211026151526062.png)

sepc 存储发生中断时的pc值，最低位永远是0

![image-20211026152054302](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211026152054302.png)

mcause 在发生M级中断时保存引发中断的event

![image-20211026152320073](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211026152320073.png)

scause 在发生S级中断时保存引发中断的event

![image-20211026152507918](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211026152507918.png)

mtval 在中断、异常发生时，往里面写入一些特定的信息

![image-20211026152601868](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211026152601868.png)

stval 在中断、异常发生时，往里面写入一些特定的信息

![image-20211026153422818](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211026153422818.png)



#### 还需要实现的一些feature:

1.（可选）优化代码，在switch内先算出要更新的值后再统一赋值，

2.可访问的csr_num是有限的集合。



