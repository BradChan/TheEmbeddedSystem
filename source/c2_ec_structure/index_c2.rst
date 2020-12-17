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



