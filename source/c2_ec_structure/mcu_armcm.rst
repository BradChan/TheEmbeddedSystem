===========================
2.1 ARM体系
===========================

据ARM官方 [1]_ 公布的数据说“累计出货ARM体系架构的芯片超过1600亿颗”。根据ARM的商业模式(ARM体系架构及相关技术的设计和授权)，这间英国公司
已是全球最大的IP(知识产权)服务商，某些数据看已是行业垄断型企业。全球著名的半导体公司，包括Microchip [2]_、NXP [3]_、ST [4]_、TI [5]_ 
等虽然各自都拥有自主的CPU体系架构仍购买ARM的授权并生产和销售ARM体系架构的微控制器(MCU)和微处理器(MPU)。虽然MIPS和POWER体系架构也有一些使用
授权的半导体公司，相比ARM的授权业务量是微乎其微。ARM为什么如此受捧呢？我们首先需要了解ARM的体系架构。

虽然ARM第一代体系架构发布于1985年(与第一代MIPS体系架构同年发布)，这是最精简的32位CPU，没有Cache(高速缓存)仅使用3万颗晶体管，因此功耗
较低但效能不比Intel 80286差。上世纪90年代初与苹果电脑公司合作开发新的ARM核使得ARM得到快速发展，并上世纪末发布ARM7TDMI、ARM9和ARM11等
微处理器。其中ARM7TDMI是最成功的ARM处理器，支持16位Thumb指令、调试(Debug)接口、64位乘法指令、EmbeddedICE片上断点和观察点，以及低功耗、
高速和高代码密度等正好满足那个时期个人数字助理、数字移动电话等便携设备的需求。2003年之后ARM发布ARMv7-M、ARMv7-R和ARMv7-A三个不同系列的
微内核分别针对嵌入式系统市场、高性能实时处理器市场和应用处理器市场，近几年并针对IoT和AI应用市场发布对应的三个系列的v8版本。

自2004年开始，ARM将其IP产品统称为“ARM Cortex”，并分为M、R、A三个系列，现有产品阵列如图2.1所示。三个在用的主版本v6、v7和v8中，图中仅列
出v6的两种型号：M0和M0+，实际上还有一种面向FPGA(可编程门阵列)设计和实现的IP被称作ARM Cortex-M1。v8相较于v7的主要区别是针对IoT和AI应用
的性能提升和优化。另外，v8的A系列增加64位处理器。


.. image:: ../_static/images/c2/arm_structure_series.jpg
  :scale: 30%
  :align: center

图2.1 ARM Cortex系列CPU的分类和型号


虽然ARM Cortex-M0和M0+两种微内核属于ARMv6版，但发布日期却在ARMv7之后，他们是综合ARMv7的存储器系统和编程模型等技术，支持Thumb-2指令集
并针对功耗做进一步优化之后的v6版体系架构。ARM Cortex-M3(v7-M)于2003年发布，而ARM Cortex-M0(v6)于2009年才发布，ARM Cortex-M0+与
ARM Cortex-M7都是2012年发布的微内核。采用ARM Cortex-M0和M0+微内核的MCU可以说是最便宜的32位MCU，市场价格与8位MCU持平，性能远超8位MCU
且功耗更低。ARM公司使用ARM Cortex-M0和M0+两种微内核再次刷新“最精简的32位处理器”设计，两种微内核的内部功能(组件)框图分别如图2.2和图2.3所示。


.. image:: ../_static/images/c2/arm_cortex-m0_structure.jpg
  :scale: 30%
  :align: center

图2.2 ARM Cortex-M0的内部功能(组件)框图


.. image:: ../_static/images/c2/arm_cortex-m0plus_structure.jpg
  :scale: 30%
  :align: center

图2.3 ARM Cortex-M0+的内部功能(组件)框图


比较图2.2和图2.3不难发现，M0和M0+的区别很小，M0+支持单周期I/O及其接口，并增加软件调试的跟踪接口，而且这些都是可选择性的组件。显然，功能组件方面
M0+完全兼容M0。事实上，ARM Cortex-M0与ARM Cortex-M3的CPU内核都采用3级流水线(Pipeline)，而ARM Cortex-M0+的CPU内核却采用2级流水线，
因此M0+的动态功耗明显低于M0和M3。

.. Note:: CPU的流水线(Pipeline)技术

  CPU的流水线是一种加速程序运行过程的技术，将一条将指令分解为多步，并让连续多条指令的各步操作重叠，从而实现几条指令并行处理，以加速程序运行。
  保存在指令存储器中程序指令必须经过取指令(Fetch)、指令译码(Decode)和执行指令(Execute)三步，因此3级流水线是最简单的、最易实现的技术方案。
  ARM Cortex-M0和M3都是采用3级流水线技术，指令执行过程为：正在执行第n条指令时，第n+1条指令正好被译码，同时从指令存储器读取第n+2条指令。
  ARM Cortex-M0+的2级流水线技术是如何分解指令的执行步骤呢？如图2.4所示，2级流水线将一条指令的执行拆分为4步：取指令、预译码(Pre-decode)、
  主译码(Main-decode)和执行指令。ARM Cortex-M0+的2级流水线技术是在每一个时钟周期执行两步，从而形成2条指令的并行处理效果。3级以上的流水线
  仍保持3步的指令执行分解，但多级流水线很容易受跳转指令影响：当执行到跳转指令时，流水线上已经预取的和预译码的指令必须先清洗掉再根据跳转指令的
  执行效果重新取指和译码。同时多级流水线需要更多的触发器单元从而增加CPU的动态功耗。

  .. image:: ../_static/images/c2/arm_cortex-m0plus_2s_pipeline.jpg
    :scale: 30%
    :align: center

  图2.4 ARM Cortex-M0+的2级流水线


相较于ARM Cotex-M0/M0+体系架构不大于50MHz的CPU时钟速度，M3和M4的时钟速度可达200MHz，这就意味着适合于M3和M3微内核的存储器和I/O外设
必须具备与之匹配的访问速度。然而，再半导体领域“速度与成本始终保持单调的正比关系”！很多种外设的速度是非常低的，如按钮和继电器切换速度仅仅是
数十毫米。高速CPU和低速外设之间的矛盾如何解决呢？半导体技术研发工程师使用总线桥技术很好地解决这一问题：CPU和存储器之间不仅采用高速总线还
增加Cache(高速缓存)单元，CPU与低速外设之间采用总线桥和低速外设总线，这样的总线桥隔离技术不仅能够保持CPU拥有高速时钟，并保持低速外设的低成本。


.. image:: ../_static/images/c2/arm_cortex-m3_m4_structure.jpg
  :scale: 30%
  :align: center

图2.5  ARM Cortex-M3/M4的内部功能(组件)框图


ARM Cortex-M3和M4微内核的内部功能(组件)如图2.5所示。与图2.3和图2.4相比，M3和M4微内核拥有更丰富的软件调试接口(断点、数据和指令观察点等)，
还具备专用的硬件浮点数处理单元，以及I-Cache(取指令专用的高速缓存)和D-Cache(访问数据存储器专用的高速缓存)单元。

截止2020年中期，ARM Cortex-M系列最高速度的微内核已高达1GHz，足以流畅运行Linux系统！详见NXP [6]_ 的i.MX RT系列微控制器产品页面的相关介绍。
NXP的i.MX RT1xxx微控制器采用ARM Cortex-M7微内核，图2.6给出该内核的简易功能(组件)框图。


.. image:: ../_static/images/c2/arm_cortex-m7_structure.jpg
  :scale: 40%
  :align: center

图2.6  ARM Cortex-M7的内部功能(组件)框图


几乎已接近桌面计算机系统CPU速度的MCU完全归功于先进的半导体工艺制程和紧耦合的存储器(TCM)技术。NXP的i.MX RT1xxx系列MCU采用先进的28nm半导体
制程(其他MCU绝大多数都采用40nm以上工艺制程)，以及NXP独有的TCM技术。相较于ST、Microchip的ARM Cortex-M7微内核的MCU产品，i.MX RT1xxx的
时钟速度超过他们5倍。

------------------------

从低于50MHz到高达1GHz的ARM Cortex-M系列MCU，他们的技术和成本差异非常大，ARM公司提供如此多样性的微内核之原生动力是为了满足多样性的嵌入式
计算机系统。使用时无存在感的穿戴类产品需要极低工作电压和极低功耗的低计算性能的MCU，嵌入式的机器视觉和机器听觉感知力不仅需要低功耗更需要高性能
计算能力和浮点数处理能力。当我们仔细阅读ARM Cortex-A系列微处理器相关的历史演变和性能发展文档之后，一定会发现常规的性能提升和产品迭代，但微
内核的结构体系的变化并不大。但ARM Cortex-M系列微内核之间确存在鸿沟级的差异，究其原因也就是高速CPU、











-------------------------






-------------------------



参考文献：
::

.. [1] www.arm.com
.. [2] www.microchip.com
.. [3] www.nxp.com
.. [4] www.st.com
.. [5] www.ti.com
.. [6] https://www.nxp.com/products/processors-and-microcontrollers/arm-microcontrollers/i-mx-rt-crossover-mcus:IMX-RT-SERIES

