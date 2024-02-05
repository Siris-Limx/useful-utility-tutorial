## 最基本的 Verilator testbench

### 头文件

```cpp
#include <verilated.h>
#include <verilated_vcd_c.h>
#include "V<DUT>.h"
#include "V<DUT>___024unit.h"
```

- `<verilated.h>`：Verilator 的头文件。
- `<verilated_vcd_c.h>`：Verilator 的波形文件头文件。
- `V<DUT>.h`：Verilator 生成的模块头文件。
- `V<DUT>___024unit.h`：Verilator 生成的模块内部单元头文件。

### 设置最大仿真时间

```cpp
#define MAX_SIM_TIME 20
vluint64_t sim_time = 0;
```

### 主函数框架

```cpp
int main(int argc, char** argv, char** env) {
    Valu *dut = new V<DUT>;

    Verilated::traceEverOn(true);
    VerilatedVcdC *m_trace = new VerilatedVcdC;
    dut->trace(m_trace, 5);
    m_trace->open("waveform.vcd");

    /* <...> */

    m_trace->close();
    delete dut;
    exit(EXIT_SUCCESS);
}
```

`Valu *dut = new V<DUT>;` 实例化了测试模块。
`m_trace`对象用于记录波形，并通过`dut->trace(m_trace, 5);`将波形记录到`m_trace`对象中。
参数`5`代表了记录5个层级的波形。

### 仿真循环

```cpp
while (sim_time < MAX_SIM_TIME) {
    dut->clk ^= 1;
    dut->eval();
    m_trace->dump(sim_time);
    sim_time++;
}
```

在每次循环中，首先将时钟信号取反，从而产生时钟激励。
`dut->eval();`用于更新模块的状态。
`m_trace->dump(sim_time);`将当前时刻的波形记录到`m_trace`对象中。

## 编译指令

```
$ verilator -Wall --trace -cc <DUT>.sv --exe tb_<DUT>.cpp
```

- `-Wall`：开启所有警告。
- `--trace`：开启波形记录。
- `-cc`：生成 C++ 模块。
- `--exe`：testbench对应的源文件。

```
$ make -C obj_dir -f V<DUT>.mk V<DUT>
```

- `-C`：指定编译目录。
- `-f`：指定编译的Makefile。
- `V<DUT>`：编译目标。

```
$ ./obj_dir/V<DUT>
```

运行可执行文件，生成波形，完成仿真。