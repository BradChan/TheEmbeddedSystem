============================
第2章 嵌入式系统体系结构
============================


当前，x86、POWER、ARM、RISC-V和MIPS等被全球公认为主流的CPU体系架构，除了专注于桌面计算机、服务器等领域的x86和POWER之外，
随着IoT和AI应用的不断普及，几乎所有的CPU体系架构的开发和维护者都积极部署IoT和AI应用领域，由此形成满足电池供电(极低功耗)的IoT功能产品和
计算优化(高速并行计算)的ML功能产品的不同级别CPU。未来无论从事那个领域的工作，我们都无法绕开CPU体系架构的理解和使用，快速了解多种CPU体系
架构是本章的目的。CPU作为嵌入式计算机系统的“主板”(MCU)的核心组件，我们将以ARM Cortex-M和RISC-V两种体系为代表来了解不同体系架构之间的
区别和统一性，并依此为基础来了解计算机系统的存储模型和编程模型等，以及系统的供电和时钟等必要的组件。

虽然我们主要介绍嵌入式计算机系统的CPU体系架构，本章相关的知识、概念和方法并不局限于此类系统。


.. toctree::
   :maxdepth: 1

   mcu_armcm.rst
   mcu_riscv.rst
   mcu_other.rst
   system_mem.rst
   system_mode.rst
   system_power.rst
   system_clk_reset.rst
   summary.rst
   problems.rst

-------------------------

.. Note::  CPU体系架构相关的基础概念：

  1. **CPU** (Central Processing Unit)，能够执行特定指令(集)并实现数值和逻辑等运算的功能单元。CPU带有并行总线与存储器和I/O连接，能够从(可执行的)存储器取指令(Fetch)、译码(Decode)、执行指令(Excute)，并将执行结果保存到存储器。
  2. **CPU内核** (Core)，由CPU、中断控制器、调试单元和总线等组成的核心单元。
  3. **SoC** (System on Chip)，(芯)片上系统的缩写，由CPU内核、片上存储器(含ROM和RAM)、片上外设(如时钟系统、定时器、UART等)组成，只需要外部电源、振荡器或大容量存储器即可工作。
  4. **MCU** (Micro-Controller Unit)，微控制器单元，由CPU内核、片上存储器(含ROM和RAM)、片上外设(如时钟系统、定时器、UART等)组成，只需要外部电源和振荡器即可工作。
  5. **指令集**，每一种体系架构的CPU对应一组特定的指令(称作指令集)，根据指令的执行结果可以将指令集分为数据传送、堆栈操作、数值计算、逻辑运算、程序跳转等几大类。
  6. **CISC** (Complex Instruction Set Computer)，复杂指令集计算机支持丰富的存储器和I/O的寻址操作，使用单一指令即可对特定存储器和I/O进行读和改写操作。此类CPU的指令集较为庞大。
  7. **RISC** (Reduced Instruction-Set Computer)，精简指令集计算机也称“Load-Store结构”计算机，除了“load”(存储器到寄存器)和“store”(寄存器到存储器)操作外，其他指令仅对寄存器操作。由于存储器的操作模式比较简单，指令集相对较小。

