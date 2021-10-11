===========================
7.2 使用UART实现系统间通讯
===========================

我们在前一节中已深入了解异步串行通讯接口及UART等细节，本节将了解如何使用通讯端口实现嵌入式系统之间的通讯。仍从一个示例开始，
该示例的目标是使用多个BlueFi开源板玩一种“数字接龙游戏”。多人玩这种数字游戏时，某个人首先说一个数字，与他相邻的人说加一后的数字，如此加一传递数字，
一轮又一轮地将数字传递下去。实现这个有趣的游戏之前，我们需要稍微了解其背后的原理。

BlueFi开源板40脚金手指拓展接口的P0和P1引脚分别可作为异步串行通讯的RxD(串行数据输入)和TxD(串行数据输出)，
使用鳄鱼夹电线将这两个引脚交叉连接可以实现两个BlueFi之间的串口通讯，如图7.9所示。

.. image:: ../_static/images/c7/two_bluefi_communication_with_uart.jpg
  :scale: 25%
  :align: center

图7.9  两个BlueFi使用串口通讯的连接方法

注意，两个嵌入式系统之间使用串口通讯，两个系统必须共地，即两个BlueFi的GND信号必须连接在一起。此外，所谓交叉连接指的是，
一个BlueFi的TxD引脚与另一个BlueFi的RxD引脚连接。

我们想要实现的数字接龙游戏的准备工作包括，首先使用多根鳄鱼夹电线将所有BlueFi开源板的金手指拓展接口GND引脚连接起来，
然后再使用鳄鱼夹电线将第1个BlueFi的P1引脚与第2个BlueFi的P0引脚连接，第2个BlueFi的P1引脚与第3个BlueFi的P0引脚连接，..，
最后一个BlueFi的P1引脚与第1个BlueFi的P0引脚连接，即多个BlueFi的P0和P1引脚环形级联起来。如图7.10所示。

.. image:: ../_static/images/c7/multi_bluefi_digit_chain_game_connection_line.jpg
  :scale: 20%
  :align: center

图7.10  多个BlueFi使用串口通讯的连接方法

虽然上图仅有3个BlueFi开源板的级联，事实上任意多个BlueFi都是可以用这种方法呈环形级联的。根据这个连接方法，或许你已经明白多个BlueFi实现数字接龙的“数字传递”过程了。
第1个BlueFi将数字(通过TxD引脚)传递给第2个BlueFi(从RxD引脚接收到该数字)，第2个BlueFi将收到的数字加一后(通过TxD引脚)传递给第3个BlueFi(从RxD引脚接收到该数字)，
..，最后一个BlueFi将收到的数字加一后(通过TxD引脚)传递给第1个BlueFi(从RxD引脚接收到该数字)，第1个BlueFi将收到的数字加一后再传递给第2个，如此循环传递即可实现这个游戏。

实现该游戏的程序代码如下：

(uart_digit_chain_game.ino)

.. code-block::  C
  :linenos:

  #include <BlueFi.h>
  const String hintStr = "press Button A to send";
  const String recStr = "Received:";
  bool startSend = false;

  void a_pressed_cb(Button2& btn) {  // callback function to A-Button be pressed
    Serial.println(" A-Button be pressed");
    startSend = true; // send a message to main-loop
  }

  void setup() {
    bluefi.begin(); // include Serial.begin(115200) 
    initialScreen(); // show initial content on the screen
    bluefi.aButton.setPressedHandler(a_pressed_cb); // register a callback function
    Serial1.begin(9600); // P0(RxD) and P1(TxD) are Serial1 signals
  }

  void loop() {
    bluefi.aButton.loop(); // update the state of A-button
    if (Serial1.available() > 0) { // if a digit be received
      int comingIn = Serial1.read(); 
      uint8_t val = (uint8_t)comingIn;
      updateScreen(val);     // update LCD screen 
      Serial1.write(val+1);  // increment this digit and send to next BlueFi
      delay(200);
    } 
    if ( startSend ) { // if A-Button be pressed, then start send 
      startSend = false;
      uint8_t startDigit = 0;
      Serial1.write(startDigit); // send the initial digit
    }
    delay(50);
  }

  void initialScreen(void) {
    bluefi.Lcd.fillScreen(TFT_BLACK); // clear screen
    bluefi.Lcd.setTextColor(TFT_WHITE, TFT_BLACK);
    bluefi.Lcd.setCursor(20, 0, 4);
    bluefi.Lcd.println( "Digit Chain Game"); 
    bluefi.Lcd.setCursor(4, 40, 4);
    bluefi.Lcd.println("with UART (P1-->P0)"); 
    bluefi.Lcd.setCursor(0, 150, 2);
    bluefi.Lcd.println(hintStr); // show hint
  }

  void updateScreen(uint8_t val) {
    static bool first = true; // a static variable is equal to a global variable
    static String digitStr = ""; 
    if (first) { 
      bluefi.Lcd.setTextColor(TFT_BLACK, TFT_BLACK);
      bluefi.Lcd.setCursor(0, 150, 2);
      bluefi.Lcd.println(hintStr); // erase hint
      bluefi.Lcd.setTextColor(TFT_RED, TFT_BLACK);
      bluefi.Lcd.setCursor(20, 100, 4);
      bluefi.Lcd.println(recStr);  // show "Received:"
      first = false; // this code block to execute only once!
    }
    bluefi.Lcd.setTextColor(TFT_BLACK, TFT_BLACK);
    bluefi.Lcd.setCursor(140, 100, 4);
    bluefi.Lcd.println(digitStr); // erase the last digit string 
    bluefi.Lcd.setTextColor(TFT_YELLOW, TFT_BLACK);
    digitStr = String(val);
    bluefi.Lcd.setCursor(140, 100, 4);
    bluefi.Lcd.println(digitStr); // show this digit string
  }

上面示例程序看起来代码行较多，如果暂时不考虑最后的两个函数initialScreen()和updateScreen()，实现数字接龙游戏的关键代码是初始化函数setup()中的最后一行代码，
即Serial1.begin(9600)，以及主循环函数loop()中的代码。其中Serial1.begin(9600)用来初始化BlueFi金手指拓展接口的P0和P1两引脚分别为Serial1的RxD和TxD信号，
波特率为9600；主循环中的第一个if程序块以Serial1.available()接口的返回值是否大于0为条件，该接口返回值大于0则意味着Serial1接收到数据，
在这个if程序块中首先从接收缓冲区读取一个接收到的数据并转换为8位无符号整数，然后调用子程序updateScreen()更新LCD显示器，然后将该数值加一后再发送出去。

该示例程序的大多数代码都是用于控制LCD显示器。在initialScreen()函数中将LCD显示器清屏，然后显示初始的文本内容。在updateScreen()函数中，
如果首次执行LCD屏幕刷新则需要将初始化时屏幕上的提示信息擦掉，更新接收到的数值到LCD显示屏上(仍然是先擦掉再显示新的数值)。

此外，示例程序还用到按钮A来启动游戏。BlueFi的按钮控制软件的接口详见第4.1节。本示例的初始化期间为按钮A注册一个“A按钮被按下时”的回调函数，
当A按钮按下时向主循环发送一个消息(一个全局变量startSend=true)。在主循环程序中，调用bluefi.aButton.loop()接口检查按钮A的事件，
并检查是否收到A按钮被按下的回调函数发出的消息，如果收到则通过Serial1发出一个数字。

将上面的程序代码复制-粘贴到Arduino IDE中，编译并逐个地下载到已经连接好的所有BlueFi开源板上。注意，这个示例程序并不依赖电脑USB端口，当程序下载到BlueFi之后，
可以使用任何USB电源给BlueFi开源板供电。当所有BlueFi开源板通上电且按图7.10的示例环形级联好之后，并没有启动游戏，当你按下任一BlueFi开源板的按钮A之后才能启动游戏。

如果你觉得使用Arduino IDE编译和下载程序所耗费的时间太长，可以使用Python脚本程序来实现该游戏。使用Python之前，你必须使用USB数据线将BlueFi与电脑连接好，
并双击复位按钮让BlueFi进入Bootloader状态，将BlueFi的最新版Python解释器固件拖放到BLUEFIBOOT磁盘，等待CIRCUITPY磁盘出现在电脑资源管理器中，
再将下面的Python脚本程序保存到/CIRCUITPY/code.py文件，即可。

.. code-block::  Python
  :linenos:

  import time               # time moudle
  import board, busio       # board and busio modules
  from hiibot_bluefi.basedio import  Button     # Button module
  btn = Button()
  # Initialize UART port:
  uart = busio.UART(
          board.P1, board.P0, baudrate=9600,    # two Pins(TxD, RxD)
          timeout=0.01, receiver_buffer_size=1) # waiting time(s), size of buffer
  outBuf = bytearray(1)     # out buffer, uart.write(bytearray,num)
  print("Press A button to start game")
  while True:
      btn.Update()
      if btn.A_wasPressed : # start our game, send a digit
          outBuf[0] = 0     # any digit: 0~255
          uart.write(outBuf, 1)
          print("Go it!")
      inBuf = uart.read(1)  # return a bytearray type
      if inBuf != b'' :     # is not None
          print(int(inBuf[0]))
          outBuf[0] = (inBuf[0]+1)%256
          uart.write(outBuf, 1)
          time.sleep(0.3)


为啥这个游戏的Python脚本程序很短呢？少了刷新LCD显示屏的代码，因为Python解释器将BlueFi的LCD显示当作字符控制台，
我们需要显示的信息直接用print(info)即可输出到屏幕上，而且自动滚屏显示。

现在可以仔细地测试本示例并观察数字接龙的效果。如果没有观察到连续的数字接龙效果，请首先检查环形级联的连线、共地连线、供电等硬件连接是否可靠牢固；
然后检查鳄鱼夹是否与P0、P1、GND附近的引脚连接上，波特率参数是否一致。

如果你的硬件连线很牢固，上面的数字接龙游戏几乎可以无穷无尽地执行！我们观察到的接龙数字应该是“.., 254, 255, 0, 1, .., 127, 128, .., 255, 0, 1, ..”，
为什么接龙数字被限制在0~255范围呢？Python脚本程序的第20行语句回答这个问题。虽然C程序中并没有明显的判断接收到的数据是否小于255等语句，
但我们使用8位无符号整数作为接收和发送的数据类型，这种类型数据的有效值是多少呢？

现在可以将波特率参数修改为1200、4800、115200或921600等，再试一试。随着波特率的增加，或许我们的数字接龙会失败，包括数字不连续、游戏停止等现象。
为什么提高波特率会产生这样的效果呢？根据前一节的内容即可回答这个问题。

-------------------------

通过前面的示例已了解异步串行通讯的便捷性和可靠性，初步了解异步串行通讯软件的基本接口和工作流程。在工作流程方面，必须先初始化所用的异步串行端口，
调用write()接口即可发送数据，调用read()从接收缓冲区读取数据。异步串行通讯接口的软件到底有哪些接口呢？

在Arduino开源平台，Serial接口是所有兼容Arduino平台的开源板内置的 [1]_ 。标准的Arduino Serial类接口包括：

  1. **Serial/Serial1/Serial2** 异步串行通讯端口名，根据开源板MCU所支持的UART端口个数确定，BlueFi支持2个串口，即Serial和Serial1两个，其中Serial用于USB-UART桥接。上面示例中使用Serial1
  2. **begin(bps)/begin(bps, onf)** 配置接口，指定波特率(bps)，以及数据位个数、奇偶校验位、停止位个数等。bps的有效值包括1200、2400、4800、9600、19200、115200等，有效的配置参数取值包括SERIAL_8N1、SERIAL_8E1、SERIAL_8O1等。不指定配置参数时，默认取值为SERIAL_8N1，即8个数据位、无校验位、1个停止位
  3. **end()** 禁止串口，释放串口资源，尤其TxD和RxD两个引脚可用作其他功能接口
  4. **available()** 串口接收缓冲区中可读的字节个数，调用read()前使用该接口查询可读的数据个数
  5. **availableForWrite()** 串口发送缓冲区中可写的字节个数，调用write()前使用该接口查询可写的数据个数以避免write()操作的阻塞时间
  6. **read()** 从串口接收缓冲区读取一个字节数据。请注意，read()接口的返回值类型是int16_t型，而非char或uin8_t等类型。当读取失败时，该接口返回值为-1
  7. **write(val)/write(str)/write(buf, len)** 向串口缓冲区写入一个单字节的数值/字符串(String型)/数组和长度，返回值是写成功的字节个数
  8. **print(val,format)/print(str)** 从串口输出数值(或按指定格式输出)/字符串，该接口与write()接口具有较大区别，print(val)首先将val转换为ASCII再发送，write(val)则直接发送原始val的8个二进制位
  9. **println(val,format)/println(str)** 先执行print()接口然后发送“\r\n”两个字符。'\r'，即回车字符，ASCII值为0x0D；'\n'，即换行符，ASCII值为0x0A
  10. **flush()** 等待串口发送缓冲区的数据发送完毕。无返回值，该接口是阻塞型的
  11. **find(target)/find(target, len)** 从串口接收缓冲区查找目标数据/(指定长度的)数据子串，返回值为true则表示已找到目标数据，否则未找到
  12. **findUntil(target, terminal)** 从串口接收缓冲区查找目标数据和终止符，返回值为true则表示已经找到目标数据和终止符，否则未找到
  13. **setTimeout(timeMS)** 设置数据接收的等待时间(ms为单位)，timeMS是uint32_t型
  14. **parseInt()/parseInt(lookahead)/parseInt(lookahead, skip)/** 从串口接收缓冲区中解析下一个有效的整数，返回值类型为int32_t。其中lookahead和skip是指定忽略的整数
  15. **parseFloat()/parseFloat(lookahead)/parseFloat(lookahead，skip)** ，充串口缓冲区中解析下一个有效的浮点数
  16. **peek()** 查看串口缓冲区中的下一个待读的数据，返回值为-1(如果缓冲区为空)或一个字节数据。注意，peek()与read()的返回值是相同的，但调用read()接口读取一个数据后，该数据则从接收缓冲区移除，peek()接口则不会移除
  17. **readBytes(buf, len)** 从串口接收缓冲区读取len个数据到数组buf中，返回值为实际读取的数据个数(不大于len)
  18. **readBytesUntil(character, buf, len)** 从串口接收缓冲区读取len个数据到数组buf中，如果遇到指定的字符character则停止读，返回值为实际读取的数据个数(不大于len)
  19. **readString()** 以字符串形式读取串口接收缓冲区中的数据，返回值为一个字符串(String型)，如果接收缓冲区为空则返回空字符串
  20. **readStringUntil(terminator)** 以字符串形式读取串口接收缓冲区中的数据，遇到指定终止符时立即返回，返回值为一个字符串(String型)，如果接收缓冲区为空则返回空字符串
  21. **serialEvent()** 串口事件的回调函数，当available()不小于1时被自动调用，在这个回调函数内调用read()读取数据
  
BlueFi开源板的Python解释器的异步串行接口是“busio”的子类，名叫UART。使用USB数据线将BlueFi与电脑连接，并打开MU编辑器，点击“串口”打开字符控制台，
将鼠标点击字符控制台区，按下“ctrol+c”键终止当前正在执行的脚本程序，强制让BlueFi解释器进入REPL状态，在“>>>”提示符后输入以下程序语句，
即可查询到“busio.UART”子类的接口：

.. code-block::  Python
  :linenos:

  >>> import busio
  >>> dir(busio.UART)
  ['__class__', '__enter__', '__exit__', '__name__', 'Parity', 'baudrate', 'deinit', 
  'in_waiting', 'read', 'readinto', 'readline', 'reset_input_buffer', 'timeout', 'write']
  >>> 

REPL执行“dir(class_name)”命令时，采用列表的形式将名为“class_name”类/模块所有的接口列举出来。
BlueFi开源板的Python解释器的“busio.UART”子类接口中，baudrate、in_waiting、timeout是接口的三种属性，分别返回当前所用的波特率、
接收缓冲区中可读的数据字节数、当前所用的超时参数(以秒为单位的浮点数)；Parity是UART的子类，定义UART接口的奇偶校验类型，仅有两个有效值：
busio.UART.Parity.ODD和busio.UART.Parity.EVEN；其他都是接口函数，简要说明如下：

  1. **UART(tx: board.Px, rx: board.Px, baudrate = 9600, bits = 8, parity = None, stop = 1, timeout = 1.0, receiver_buffer_size = 64)** , UART类实例化接口，除了两个引脚之外，其他配置参数均有缺省值，未指定的参数采用缺省值
  2. **deinit()**, 禁用UART并释放该硬件单元的资源，尤其引脚资源可以做其他I/O使用
  3. **read()/read(num)** , 从串口接收缓冲区读取(指定个数的)字符，如果指定个数num则至多读取num个字节，返回值是一个字节型数据列表
  4. **readinto(buf)** , 从串口接收缓冲区读取数据到buf中，buf必须是一个字节数组(bytearray)，返回值是读取并储存在buf中的字节个数
  5. **readline()** , 从串口接收缓冲区读取数据，遇到换行符(0x0D)则停止读，返回值是一个字节型数据列表
  6. **reset_input_buffer()** , 清空串口接收缓冲区内，如果接收缓冲区是非空的，相当于丢弃已接收到的数据
  7. **write(buf)** , 发送字节数组(bytearray)buf中的数据，返回值是已发送的数据个数

注意，Python解释器中的“print()”函数并不属于UART类的接口，而是Python解释器自带的一种向字符控制台输出字符串的专用接口，
BlueFi开源板Python解释器使用USB-串口桥接通道实现print()、REPL等接口。此外，UART子类还支持硬件流控信号(RTS和CTS)，
以及RS485模式(支持发送和接收使能/传输方向控制信号)等，关于RS485通讯接口将在第7.4节介绍。

当我们了解这些软件的接口之后，再回头去看上面采用两种语言所编写的多个BlueFi玩“数字接龙游戏”代码，不仅容易理解，还会发现很多接口并未使用到。
上面的示例仅使用UART功能单元收发单个数字，即每次发送一个字节的数据，而实际的异步串口通讯应用中每次需要传输更多数据。譬如，为了测量一个密闭环境中的温湿度，
将一个BlueFi放置在密闭环境，另一个BlueFi放置在外面便于我们观察环境变化情况，我们可以使用3根信号线将两个开源板共地并将P0和P1交叉连接，
密闭环境内的BlueFi软件实施温湿度测量(参考第4.2节)，然后将浮点型的温度和湿度数据传输给外部的BlueFi显示。异步串行通讯接口传输的数据是以字节为单位，
如何传输浮点数呢？至少有两种方案。其一是直接传输温度和湿度的原始二进制表示的数据，由于单精度浮点的是32位宽(即4个字节组成)，即传输8个字节；
其二是先将浮点型的温度和湿度分别转换成字符串，然后再传输。很显然，两中传输方案的区别是所传输的数据的表示形式，以及数据流格式。数据流格式指的是，
数据流的那几个字节是温度数据，那些是湿度数据。传输数据的表示形式之间有稍许区别，原始二进制形式的数据流最短，字符形式的数据流的可读性很高。
事实上，温度和湿度数据的传输方案远不止这两种。但无论采用那种方案，两个BlueFi的软件设计工程师必须先约定好数据流中的数据表示形式和格式，
并按约定的方案编写发送、接收和解析数据的程序才能实现期望的任务。

通讯双方约定的数据流表示形式和格式等被统称为通讯协议(Protocol)。异步串行通讯接口协议包括，波特率、字节单元的格式(数据位个数、校验位、停止位个数等)，
数据流固定的头字符(Head Character)、数据流校验字(Check Word)、终止符(Terminator)，以及数据流的表示形式和格式。

异步串行通讯接口协议的架构示例请参考图7.11。

.. image:: ../_static/images/c7/uart_communicaton_protocol_structure_0.jpg
  :scale: 20%
  :align: center

图7.11  异步串行通讯协议的架构

人与人之间的协议、合同等属于法律范畴的概念，通讯协议是计算机系统之间的软件接口规范，通讯双方的任一方不遵循约定的通讯协议就有可能会造成通讯失败。
上图所示的格式化数据流，也称作数据帧(Data Frame)。头字符和终止符是数据帧的同步字节，用于识别一个数据帧的起始位置和终止位置，一般来说这两个特殊字符是固定不变的。
一个数据帧内可能包含多种不同的数据域(Data Field)用于传输复杂的结构化信息。对于变长的数据域，除了必要的域ID之外，还应有数据域长度字，
接收者能够根据域ID、数据域长度(或字节数)等信息准确地确定该域的有效数据。

数据帧中的校验字，以及计算校验字的算法都属于异步串行通讯协议的一部分，接收者使用校验字来确定接收到的数据帧的准确性。

格式化的数据帧及其数据域的定义是所有通讯协议的关键规范，也是可靠地传输数据的基本保障。计算机系统联网使用的TCP/IP协议中包含多种复杂结构数据帧规范，
譬如HTTP、STMP、FTP、TCP和UDP等数据帧的定义，数据帧中的每一个位都有具体的意义(TCP/IP是一种面向位流的协议)才能实现全球计算机系统互联。

在图7.11中，同步位(起始位和停止位)、同步字符(头字符和终止符)等是异步串行通讯协议的关键结构信息，UART功能单元使用同步位信息保持同步收发单个字节，
串口软件则使用同步字符定位数据帧的头和尾。根据本节开始时的示例测试可以知道，仅使用2个信号线和共地线即可实现全双工通讯的异步串行接口在硬件连线、软件编程等方面都非常简单，
虽然随着波特率的增加数据传输发生错误的可能性越高，低波特率的数据传输可靠性还是非常高的。

目前，虽然各种高速的、大数据吞吐量的系统间通讯接口非常多，异步串行通讯接口仍在广泛使用，尤其在嵌入式系统开发和调试、系统固件下载、工业级通讯等领域，
应归功于异步串行通讯接口的简单易用、成本低和可靠性高等特点。


-------------------------

参考文献：
::

  [1] https://www.arduino.cc/reference/en/language/functions/communication/serial/
  [2] 