===========================
2.8 本章总结
===========================

现代嵌入式系统MCU的微内核种类非常丰富，这与其他类型的计算机系统CPU或MPU的内核种类形成明显区别，桌面计算机系统CPU目前仅有Intel x86和ARM Cortex-A
两种主流体系架构，PMD类计算机系统MPU的体系架构目前几乎只有ARM Cortex-A，但嵌入式系统MCU的微内核体系包含8-bit、16-bit和32-bit等数百种之多。
学习某一种体系架构的嵌入式系统显然不合理，本章中主要了解ARM Cortex-M和RISC-V两种主流体系架构的微内核结构及其半导体实现，以及可重构的、小众的
Tensilica Xtensa内核的结构和采用该内核的SoC(ESP32)内部结构。为满足更高计算性能需要微内核的时钟速度越来越高，但传统片上外设的速度仍很低，
复杂的片上供电系统、时钟树等能够确保高性能内核和外设的低功耗特性。几乎所有的MCU和SoC都遵循“一切皆地址”原则将片上资源全部映射到存储器系统内，
像访问数据存储器一样地访问某些存储地址单元即可实现对片上外设的编程，对整个存储器系统进行功能分区并引入低成本的易用MPU管理各功能区域的权限，
允许CPU直接从片外扩展串行存储器执行程序，与传统的并行接口扩展片外存储器相比不仅极大地减少MCU引脚数目和PCB面积，还有利于降低系统成本。

本章我们初步了解嵌入式系统硬件方面的基础知识、概念和系统实现方法，但是并没有涉及具体的硬件电路设计和调试等技术，在嵌入式系统软件方面仅从MCU的
工作状态和工作模式方面了解顶层软件的组成，下一章我们将学习嵌入式系统软件的基本(分层抽象)架构、开发方法、开发工具和开发环境的搭建等。

----------------------------

**本章拓展阅读**

  - MCU/SoC设计 ( [1]_ 是非常值得推荐的)
  - MCU体系架构 ( [2]_ 和 [3]_ 的前5章最值得推荐)
  - 桌面计算机的体系架构 ( [4]_ 的第3～4章值得推荐)
  - MCU Datasheet (GD32VF103、STM32F401、SAMD51、nRF52840、ESP32等MCU都是本章的示例，建议快速浏览相关Datasheet)


-------------------------

参考文献：
::

.. [1] Joseph Yiu, System-on-Chip Design with ARM Cortex-M processors, ARM Education Media, 2019
.. [2] Joseph Yiu, The Definitive Guide to ARM Cortex-M0 and Cortex-M0+ Processors (2nd-Edition), Elsevier, 2015
.. [3] Joseph Yiu, The Definitive Guide to ARM Cortex-M3 and Cortex-M4 Processors (3rd-Edition), Elsevier, 2013
.. [4] Bryant, R. E., and O'Hallaron D. R., Computer Systems: A Programmer's Perspective, the 3nd Edition, Pearson Education, 2018
