===========================
3.4 Arduino IDE
===========================

现在的Arduino已是开源(Open Source)的代名词，Arduino是全球最大的电子和编程开源社区。截止目前，Linux是最成功的开源OS，Arduino是最成功的嵌入式软硬件开发平台，
Python是当下最流行的开源编程语言。

Arduino [1]_ 自称使用“Arduino编程语言”。事实上，Arduino使用C/C++语言，并增加一些内部函数。Aduino编程语言的详细说明请参考 [2]_ 页面。
与标准C/C++编程语言相比，Arduino编程语言针对嵌入式系统的应用开发不仅增加一些硬件相关的内部函数、数据类型和常数。

当我们提到Arduino时，大多数情况指的都是“Arduino IDE”和“兼容Arduno IDE的开源板”，前者是一种嵌入式系统软件开发平台，后者是一类嵌入式系统硬件。
目前Arduino社区支持8位的AVR系列MCU(来自Atmel)，以及32位的ARM Cortex-M系列和ESP系列MCU。安装Arduino IDE时，Arduino官方的AVR系列开源板BSP
(板级支持包)软件和AVR的工具链(编译器等)，其他系列开源板的BSP软件和相关工具链可以在运行时安装。具体的安装步骤将在下一节内容中给出，本节我们首先了解
Arduino IDE的基本功能和使用流程，以及兼容Arduino的开源板的一些特性。

--------------------------

Arduino IDE的主界面如图3.16所示，代码编辑区和控制台占据大部分窗口，IDE软件顶部是菜单栏和快捷按钮区。Arduino IDE的控制台和代码编辑区的大小是可以改变的，
将鼠标悬停的两个区域的分界线处，当鼠标符号变为上下箭头时即可拖动两个区域的边界线以改变两个区域的大小。

.. image:: ../_static/images/c3/arduino_ide_ui_windows.jpg
  :scale: 30%
  :align: center

图3.16  Arduino IDE主界面

与专业级IDE相比，Arduino IDE没有项目文件树、寄存器和断点等信息窗口，但是Arduino的软件项目仍支持多个源文件，并采用标签窗口来管理多个源文件。
首次打开Arduino IDE将会看到默认的Arduino程序，如下：

.. code-block::  c
  :linenos:

  void setup() {
    // put your setup code here, to run once:

  }

  void loop() {
    // put your main code here, to run repeatedly:

  }

这个默认的Arduino程序由两个部分组成：初始化部分和主循环部分，如果按照C/C++的习惯，这两个部分正好组成“main()”函数。当系统通电或执行复位之后，
“setup()”，即初始化函数中的程序语句仅执行一次；“loop()”中的程序语句将会循环执行。这显然与标准的C/C++程序有区别，譬如下面的程序代码：

.. code-block::  c
  :linenos:

  void setup() {
    // put your setup code here, to run once:
    pinMode(LED_BUILTIN, OUTPUT);
  }

  void loop() {
    // put your main code here, to run repeatedly:
    digitalWrite(LED_BUILTIN, HIGH);  // turn the LED on (HIGH is the voltage level)
    delay(500);                       // wait for half second
    digitalWrite(LED_BUILTIN, LOW);   // turn the LED off by making the voltage LOW
    delay(500);                       // wait for half second
  }

初始化程序仅一个语句，即设置“LED_BUILTIN”这个可编程I/O引脚为“OUTPUT”模式。主循环中的4个程序语句分别有注释，即设置“LED_BUILTIN”引脚为高电平，
然后延时500ms，再将“LED_BUILTIN”引脚设为低电平，再延时500ms。由于主循环“loop()”内的程序语句被无条件地循环执行，所以这个程序的执行效果是完全可以想象的。

如果你已熟悉C/C++编程语言，上面例子中的“pinMode(,)”、“digitalWrite(,)”和“delay()”函数调用显然都是标准的C/C++语句，这些函数都是Arduino的内部函数，
无需使用“#include <>”这样的预处理指令。除了程序结构、内部函数、硬件相关的变量和常数之外，Arduino与普通C/C++编程并无区别。

既然Arduino IDE是面向嵌入式系统应用软件开发，具体支持哪些类型的开源板呢？点击菜单栏中的“工具——>开发板”，将会看当前安装的Arduino IDE所支持的开源板列表。
如图3.17所示。首次安装Arduino IDE时，仅支持AVR系列的开源板(Arduino AVR Boards)。虽然开源板列表中并不能直接看出每一种板所使用的MCU类型，
但Arduino社区习惯上为每一种开源板取一个独特的惟一的名称，使用板名称到Arduino官网或搜索引擎即可查到这个板的具体参数。

.. image:: ../_static/images/c3/arduino_ide_board_manager_board_list.jpg
  :scale: 30%
  :align: center

图3.17  Arduino IDE支持的开源板列表

Arduino IDE还支持其他开源板吗？Arduino官方支持的开源板类型多达近百种，主要包括AVR系列和ARM Cortex-M两个系列。对于官方支持的开源板，
只需要使用菜单栏的“工具——>开发板——>开发板管理器”打开“开发板管理器”窗口，并在这个窗口种浏览或搜索、安装某种开源板的BSP和相关工具链即可，
然后在Arduino IDE中即可使用这些软件工具对该开源板进行软件编程。Arduino的开发板管理器窗口如图3.18所示。

.. image:: ../_static/images/c3/arduino_ide_board_manager_windows.jpg
  :scale: 30%
  :align: center

图3.18  Arduino IDE的开发板管理器

在Arduino开源社区，还有更多种兼容Arduino的开源板并非官方支持的却非常活跃，譬如ESP32。如何在Arduino IDE中使用非官方的开源板呢？Arduino 
IDE对非官方开源板的支持也非常完善，而且与官方支持的开源板一样地使用“开发板管理器”窗口安装BSP和编译器等相关软件包，只要求非官方开源板的开发者
必须自行维护并使用url托管一个JSON格式文件，该文件中指定该开源板所使用的CPU体系架构类别、编译器工具链、下载工具软件包，以及该开源板的BSP等，
这些软件包的下载地址(url)也必须在这个JSON文件中给定。我们只需要将某个非官方支持的开源板的JSON文件的url告知Arduino IDE，具体方法如图3.19所示，
然后再打开“开发板管理器”窗口，在搜索输入框中输入该开发板的名称即可安装该开源板相关的所有软件包。

.. image:: ../_static/images/c3/arduino_ide_unofficial_board_url_manager.jpg
  :scale: 30%
  :align: center

图3.19  为Arduino IDE添加非官方的开源板的JSON描述文件的url

请注意，上图是在macOS系统中使用Arduino IDE，与Windows和Linux系统的情形略有区别。如果我们直接打开并查看某个非官方的开源板的JSON描述文件，
或许有益于理解前述的内容。CPU体系架构类别，指定CPU的指令集；将C/C++程序代码转换成汇编语言程序和机器码必须使用指令集结构体系相关的工具链；
使用什么样的下载软件工具将编译后生成的机器码文件(即固件)下载到嵌入式系统的FlashROM中？有了这个工具软件，我们可以使用Arduino IDE的“编译并下载”
快捷按钮“一键实现”C/C++程序源码转换成机器码并下载到目标板；每一个开源板都有特殊定义的硬件资源，依据分层抽象的编程思想，每一个开源板应该有一套BSP
来封装特定的硬件资源。

显然，每一个开源板的BSP是开发者为使用者所订制的一组软件。在Arduino社区内，兼容Arduino的开源板的BSP必须也是开源的，即一组使用C/C++语言编写的
硬件资源管理和接口程序源文件。







--------------------------

参考文献：
::

.. [1] https://www.arduino.cc/
.. [2] https://www.arduino.cc/reference/en/
.. [3] https://github.com/arduino/Arduino/wiki/Unofficial-list-of-3rd-party-boards-support-urls