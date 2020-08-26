===================================
3.5 创建一种兼容Arduino的开源板
===================================

前一节初步了解Arduino开源平台及软件开发的基本流程和基本框架，用Python脚本语言编写嵌入式系统软件主要依赖Python解释器，如果系统预装Python解释器后，
我们的软件开发工作几乎只需要一个文本编辑器软件工具即可，但使用C/C++这样的编译型语言的Arduino开源平台开发软件时，需要的软件工具要多很多。

本节内容需要自己动手实践，一步一步地创建一种兼容Arduino的开源板及其BSP。这个过程不需要任何硬件电路设计和电子电路分析等知识，其中涉及一些硬件接口方面的概念，
我们将在后续的章节中逐步深入掌握，本节不必深究，只需要根据硬件功能单元及其接口的关系结构框图，以及开源社区的相关软件资源创建一个兼容Arduino开源平台的BSP，
并能够在Arduino开源平台开发这个开源板的应用软件。首先来认识本节课使用到的开源板——BlueFi的硬件资源，如图3.23所示。

.. image:: ../_static/images/c3/bluefi_pcb_hardware_resource.jpg
  :scale: 25%
  :align: center

图3.23  BlueFi的硬件资源

BlueFi带有一个与BBC MicroBit开源板完全兼容的40P“金手指”(这是一种特殊的PCB工艺)扩展接口，40P“金手指”的信号定义如图3.24所示。

.. image:: ../_static/images/c3/bluefi_board_40pin_if_signals.jpg
  :scale: 40%
  :align: center

图3.24  BlueFi的40P“金手指”接口信号

BlueFi板上的大多数功能单元的基本功能都是众所周知的，不需要看电路原理图就能了解其中的硬件资源及其I/O的用法，我们使用功能单元的关系结构框图来描述硬件原理，
如图3.25所示。每一个功能单元所用的关键电子元件的型号都已经清楚地列举，如果需要了解某个元件的接口细节和用法，请使用搜索引擎查阅相关资料(Datasheet)。

.. image:: ../_static/images/c3/bluefi_hardware_uints_relation_structure.jpg
  :scale: 25%
  :align: center

图3.25  BlueFi的功能单元及其接口(硬件资源和I/O引脚用法)

BlueFi使用nRF52系列的MCU(Arduino官方支持的)——nRF52840作为主控制器，ESP32(非官方支持的)作为网络协处理器，两者之间采用SPI接口互联，
其中nRF52840的SPI工作在主模式(Master)，ESP32的SPI接口工作在从模式(Slave)。SPI接口的具体细节和编程控制参加第6章。nRF52840片上带有4个独立的
SPI接口功能单元，一个用作TFT-LCD的接口，一个用作扩展片外FlashROM，一个与ESP32连接，预留一个给40P“金手指”拓展接口。




--------------------------

参考文献：
::

.. [1] 

