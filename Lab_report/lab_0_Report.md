## lab_0_Report

在Rust中，程序遇到错误会发生panic，当panic发生时会调用`panic_handler`函数

```rust
#[panic_handler]
fn panic(_info: &PanicInfo) -> ! {
    loop {}
}
```

类型为 `PanicInfo` 的参数包含了 panic 发生的文件名、代码行数和可选的错误信息。这个函数从不返回，所以他被标记为发散函数（Diverging Function）。发散函数的返回类型称作 Never 类型（"never" type），记为 `!`

`eh_personality`，eh是Exception Handing的缩写，是用来标记某函数是用来实现堆栈展开处理功能的语义项。

堆栈展开：当程序出现异常，会从异常点开始沿着调用栈一层层回溯，直到找到某个函数能捕获异常或者终止程序（需要沿着调用栈一层层回溯上去回收每个 caller 中定义的局部变量

运行时系统：指一种把半编译的运行码在目标机器上运行的环境。运行时系统是一种介乎编译(Compile)和解释(Interpret)的运行方式，由编译器（Compiler)首先将源代码编译为一种中间码，在执行时由运行时充当解释器对其解释。该过程以运行时为先决条件，因此系统被称为运行时系统。

以 Rust 语言为例，一个典型的链接了标准库的 Rust 程序会首先跳转到 C 语言运行时环境中的 `crt0`（C Runtime Zero）进入 C 语言运行时环境设置 C 程序运行所需要的环境（如创建堆栈或设置寄存器参数等）。

然后 C 语言运行时环境会跳转到 Rust 运行时环境的入口点（Entry Point）进入 Rust 运行时入口函数继续设置 Rust 运行环境，而这个 Rust 的运行时入口点就是被 `start` 语义项标记的。Rust 运行时环境的入口点结束之后才会调用 `main` 函数进入主程序。

OS内核一般将地址空间放在高地址上，在QEMU模拟的RISC-V中，DRAM内存的物理地址是从0x80000000开始的，大小128M

#### 程序内存布局

- .text 段：代码段，存放汇编代码
- .rodata 段：只读数据段，顾名思义里面存放只读数据，通常是程序中的常量
- .data 段：存放被初始化的可读写数据，通常保存程序中的全局变量
- .bss 段：存放被初始化为 0 的可读写数据，与 .data 段的不同之处在于我们知道它要被初始化为 0，因此在可执行文件中只需记录这个段的大小以及所在位置即可，而不用记录里面的数据，也不会实际占用二进制文件的空间
- Stack：栈，用来存储程序运行过程中的局部变量，以及负责函数调用时的各种机制。它从高地址向低地址增长
- Heap：堆，用来支持程序**运行过程中**内存的**动态分配**，比如说你要读进来一个字符串，在你写程序的时候你也不知道它的长度究竟为多少，于是你只能在运行过程中，知道了字符串的长度之后，再在堆中给这个字符串分配内存

| .text                                              low address |
| ------------------------------------------------------------ |
| .rodata                                                      |
| .data                                                        |
| .bss                                                         |
| Heap                                                         |
| unused                                                       |
| Stack                                              High address |

RISC-V 共有 3 种特权级，分别是 U Mode（User / Application 模式）、S Mode（Supervisor 模式）和 M Mode（Machine 模式）。