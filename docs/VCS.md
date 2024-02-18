## 概述

我们所说的VCS，指的是全套“功能验证解决方案”（a full suite of "Functional Verification Solution"）。
另一个容易混淆的概念是VCS-MX，它相比VCS，增加了对VHDL的支持，相应地，处理RTL的步骤也会比VCS多。

### VCS-MX流程

VCS-MX通过以下三步来处理 RTL：

- 分析源文件（analyze）。
- 处理源文件（elaborate）。
- 仿真可执行文件（simulate）。

```
$ vlogan [vlogan_options] file1.v file2.v       // analyze
$ vhdlan [vhdlan_options] file3.vhd file4.vhd   // analyze
$ vcs [compile_options] design_unit             // elaborate
$ simv [run_options]                            // simulate
```

### VCS流程

VCS将前两步合并，简化为了两步。

- 编译源文件（compile）。
- 仿真可执行文件（simulate）。

```
$ vcs [compile_options] Verilog_files           // compile
$ simv [run_options]                            // simulate
```

### 生成波形文件

生成`.fsdb`波形文件需要用到如下4个系统函数：

1. `$fsdbDumpfile(“<filename.fsdb>”)`
2. `$fsdbDumpvars(<levels>,<module_name>)`
3. `$fsdbDumpon`
4. `$fsdbDumpoff`

下面是一个实例：

```verilog
initial begin
    $fsdbDumpfile(“dump.fsdb”);
    $fsdbDumpvars();
    #100;
    $fsdbDumpoff;
    #700;
    $fsdbDumpon;
    #10500;
    $fsdbDumpoff;
end
```