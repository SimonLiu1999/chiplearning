# module_CSR参考手册

发生中断时CSR的行为总结

寄存器的全集为:

a)    305：Mtvec

b)    341：Mepc

c)    342：Mcause

d)    304：Mie

e)    344：Mip

f)    343：Mtval

g)    340：Mscratch

h)    300：Mstatus

i)     302：Mideleg

j)     303：Medeleg

k)    141：Sepc

l)     105：Stvec

m)   142：Scause

n)    140：Sscratch

o)    143：Stval

p)    100：Sstatus

q)    180：Satp

sie sip misa

先考虑进入M态的中断

#### mtvec: 

介绍：使用Direct模式，值其在软件中配置，记录了中断处理程序的入口，发生中断、异常时把pc指向mtvec的值，mevec的后两位取0（mtvec记录的地址必须4字节对齐），mtvec的写入是软件操作的，在发生中断时被用于给pc赋值。

行为：在发生中断时被用于给pc赋值。

#### mepc：

介绍：最低位永远是0，mepc在发生中断/异常时记录当时的pc位置

行为：在发生中断时，mepc = this_pc;

#### mcause:

介绍：发生中断/异常时，mcause被写入一个能指示中断事件的值。最高位指示是中断还是异常，剩下的位指示中断/异常的原因。

![image-20211102114106505](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211102114106505.png)

行为：发生中断、异常时按下表赋值。

![image-20211102143247719](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211102143247719.png)

#### mip：

介绍:中断待处理寄存器，无论中断是否被enable，当有中断请求时，相应bit位置为1

行为：这个寄存器的值由当前中断的状态决定，对程序是只读的。

#### mie:

介绍：中断使能寄存器，只有相应的位被置为1时该中断才会被处理。mie的值可以通过软件来指定

行为：mie和mip都set时处理器进行中断。

#### mtval:

介绍：记录发生中断、异常时的一些信息

行为：发生中断、异常时往里面写入一些信息（具体要实现哪些待定）

#### mscratch:

介绍：专门用于m态中断的一个寄存器。

行为：可以通过CSR指令读写，没什么特殊的。

#### mstatus：

介绍：mstatus跟踪并控制当前硬件线程的状态

![image-20211102171254536](C:\Users\huawei\AppData\Roaming\Typora\typora-user-images\image-20211102171254536.png)

1.MIE、SIE、UIE是全局的中断使能位，当运行在x模式时，若xIE=1,则x级的中断是使能的，更高级别的中断总是使能的，更低级别的中断总不是使能的。

2.xPIE是发生中断前的中断使能位的记录，用于支持嵌套中断。

3.xPP存储了trap之前的特权模式。

When a trap is taken from privilege mode y into privilege mode x, x PIE is set to the value of x IE; x IE is set to 0; and xPP is set to y.

4.MPRV是指定load和store对内存的访问权限的

5.MXR是在访问page-based virtual memory时控制访问权限的。

6.SUM是指定在S模式下对U模式内存的访问权限的

7.TVM指定在S模式下sfence.vma指令和修改satp寄存器能不能用

8.TW指定了WFI指令会不会在特定条件触发异常

9.TSR指定了在S模式能否运行SRET指令

10.FS和XS和SD是用于其它扩展指令的

#### mideleg/medeleg:

介绍：中断异常一般直接交给m模式处理，为了提升效率，一些种类的中断、异常可以交给S模式处理，mideleg/medeleg指定了哪些中断和异常是需要交给S模式处理的。

When a trap is delegated to a less-privileged mode x, the x cause register is written with the trap cause; the x epc register is written with the virtual address of the instruction that took the trap; the x tval register is written with an exception-specific datum; the xPP field of mstatus is written with the active privilege mode at the time of the trap; the x PIE field of mstatus is written with the value of the x IE field at the time of the trap; and the x IE field of mstatus is cleared. The mcause and mepc registers and the MPP and MPIE fields of mstatus are not written.

mideleg/medeleg控制相应中断、异常的比特位就是mcause的值，

行为：当中断异常发生时，若mideleg/medeleg置位，交给s模式处理，若mideleg/medeleg复位，交给m模式处理，一旦交给了s模式处理，相应的s开头的寄存器表现出和m开头的寄存器类似的行为，而m开头的寄存器值保持不变。