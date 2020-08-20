============================
第3章 嵌入式系统软件
============================

早期的嵌入式计算机系统软件开发必须使用汇编语言，编写汇编语言程序的效率极低而且很难维护。今天我们可以使用很多种成熟的高级语言开发嵌入式系统软件，
如C/C++、Python、JavaScript、Lua、Go等。在嵌入式软件开发领域C/C++和Python都十分流行，C/C++是编译型语言的代表，Python则是解释型语言的代表，
本章将采用这两种编程语言。在过去的20年间，嵌入式Liunx(即ucLinux)、嵌入式Windows(即Windows CE)几乎被当作标准版本的嵌入式系统软件开发的模版，
事实上99%的嵌入式系统软件根本不用OS或使用FreeRTOS、RTX RTOS等极其小型的OS，本章将分析无OS的和带有OS的嵌入式系统软件开发的基础(分层抽象)架构
和基本开发方法，以及如何使用Python和C/C++语言开发嵌入式系统软件。

动手实战是一种高效率的嵌入式系统学习方法，本章包含一个动手项目：创建一个兼容Arduino开源板和软件开发环境。通过动手实战能够帮助我们理解前三章
的理论内容，还为后续学习过程中的动手验证准备一些工具。



.. toctree::
   :maxdepth: 1

   ec_os_software.rst
   ec_non_os_software.rst
   ec_python.rst
   arduino_ide.rst
   create_arduino_board.rst
   summary.rst
   problems.rst


