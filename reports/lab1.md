# 实现功能

1. 在`os/src/syscall/process.rs`中对`trace_request`进行match, 按情况返回.
2. 在`os/src/task/task.rs`的`TaskControlBlock`中增加一个数组`task_syscall: [u32; 512]`, 统计每个`syscall`被调用的次数
3. 在`os/src/task/mod.rs`中增加了`add_task_syscall`和`query_task_syscall`函数, 在每次task调用`syscall`时, 增加`task_syscall: [u32; 512]`中对应`syscall`的调用次数.
4. 在`Gemini`的教学下, 修改`os/Makefile`, 增加了`GDB := gdb-multiarch`, 在不用`riscv64-unknown-elf-gdb`的情况下, 对`rcore`进行调试.



# 简答作业

1. 
   - ch2b_bad_instructions.rs中的`sret`触发`Exception::IllegalInstruction`, `sret`在`__restroe`中被使用, 从S态修改`sstatus`返回U态.
   - ch2b_bad_address.rs中zero, 0(zero)触发`Exception::StoreFault`.
   - ch2b_bad_register.rs中的`csrr`在user态访问`sstatus`, 越权触发`Exception::IllegalInstruction`.
2. 
   1. 指向的是由`TaskContext`里保存的指向`TrapContext`的`sp: kstack_ptr`,  . 开始执行第一个task, 或者task调用`syscall`后进入S态, 再从S态返回U态, 需要用到`__restore`.
   2. 从内核栈保存`sstatus`和` sepc `和`sscratch`的位置, 把数据读到`t0~t2`, 然后从`t0~t2`恢复这三个寄存器.
   3. `x2`在`t2`里面, 在上一步已经恢复了. `x4`没被用到, `__alltraps`里没保存`x4`.
   4. `__alltraps`里面交换了`sscratch`和`sp`, `sp`指向的是内核栈, 现在`crs`和x5…x31保存在内核栈中, `L60`就把`sscratch`和`sp`交换回来, 再`sret`.
   5. 发生在最后一行`sret`指令上, `sret`修改了`sstauts`, 返回了U态.
   6. 把`sscratch`中保存的当前task的内核栈地址与`sp`中指向的用户栈交换.
   7. `ecall`触发硬件`trap`, 跳转到`sepc`执行`trap_handler`, 设置`sstatus`到S态.



# 荣誉准则

1. 在完成本次实验的过程（含此前学习的过程）中，我曾分别与 **以下各位** 就（与本次实验相关的）以下方面做过交流，还在代码中对应的位置以注释形式记录了具体的交流对象及内容：

   > ChatGPT, Gemini, 交流了risc-v的汇编指令, 寄存器含义.

2. 此外，我也参考了 **以下资料** ，还在代码中对应的位置以注释形式记录了具体的参考来源及内容：

   > 无

3. 我独立完成了本次实验除以上方面之外的所有工作，包括代码与文档。 我清楚地知道，从以上方面获得的信息在一定程度上降低了实验难度，可能会影响起评分。

4. 我从未使用过他人的代码，不管是原封不动地复制，还是经过了某些等价转换。 我未曾也不会向他人（含此后各届同学）复制或公开我的实验代码，我有义务妥善保管好它们。 我提交至本实验的评测系统的代码，均无意于破坏或妨碍任何计算机系统的正常运转。 我清楚地知道，以上情况均为本课程纪律所禁止，若违反，对应的实验成绩将按“-100”分计。





