================================
第7章 UART接口及其应用
================================

异步串行通讯端口是一种简单的系统之间通讯接口，只需要2个串行数据线即可实现两个系统之间全双工通讯，而且最高波特率可达10Mbps。
UART(通用异步收发器)单元是异步串行通讯接口的可编程的硬件功能单元，他是通用型MCU的一种片上外设单元。
波特率和同步位(包括起始位和停止位)是异步串行通讯的关键概念，是异步通讯的双方保持同步的基本手段。
环境干扰给串行通讯数据线制造出各种噪声，通讯双方的振荡时钟信号不稳定会造成波特率时钟偏差，
异步串行通讯的双方如何在有噪声情况下保持的数据完整性呢？串行数据流如何保持收发双发的同步？
异步串行通讯协议指的是什么？工业应用领域如何改进异步串行通讯的接口？

本章将了解异步串行通讯相关的基础概念，以及UART功能单元的基本结构、工作原理，双机通讯和多机通讯等，并逐一回答这些问题。




.. toctree::
   :maxdepth: 1

   uart.rst
   uart_communication.rst
   uart_protocol.rst
   rs485.rst
   summary.rst
   problems.rst



.. Note::  通讯接口相关的基础概念：

  1. **波特率** (Baudrate)，指每秒能够传输二进制位的个数(使用的单位是bits/s，即bps)，或传输一个二进制位所需要的时间。
  2. **异步串行通讯** (Asynchronous Serial Communication)，指没有同步时钟信号的串行数据传输。异步串行通讯需要发送者和接收者之间使用相同的波特率和同步位信息来保持收发双方的同步。
  3. **USART(通用同步/异步收发器)** (Universal Synchronous Asynchronous Receiver Transmitter)，既支持同步移位寄存器模式又支持异步串行通讯模式的串行通讯接口单元。
  4. **RS232标准** ，美国电子工业联盟(EIA)制订的一种异步串行通讯接口标准，包括接口的电缆、机械和电气等方面的规范，支持硬件流控、软件流控和无流控三种模式，无流控模式仅需要2根串行数据线，硬件流控需要额外的2个信号(RTS和CTS)。RS232标准还支持设备状态握手信号DSR和DTR、线路状态信号DCD和RI。
  5. **RS422标准** ，EIA制订的一种无流控的差分型异步串行通讯接口标准，仅使用4根电缆(2对差分信号)且无须共地线即可实现1.2Km距离的异步串行通讯，最大波特率达10M。相较于RS232标准，RS422采用差分信号能有效抑制共模干扰，适合远距离通讯。
  6. **RS485标准** ，EIA制订的一种半双工的差分型异步串行通讯接口标准，后被通讯工业协会(TIA)修订，也称TIA/EIA-485标准。仅使用1对双绞线作为通讯电缆，通讯距离可达1.2Km，最大波特率可达10M。
  7. **ModBus通讯协议** ，由Schneider于1979年为PLC开发的一种用于主从架构的异步串行通讯协议，后来成为公认为工业界异步串行通讯协议。该协议的从站资源采用寄存器映射的方法，每个从站有惟一ModBus地址用于寻址，主站可根据从站地址、寄存器地址等信息寻址任一功能寄存器。ModBus通讯协议属于应用层协议，他还支持其他类型的物理层网络。