================================
8.4 CAN总线接口--软件编程及应用
================================

本节将使用前一节的CAN节点硬件接口示例来了解CAN总线的软件接口和编程应用。首先运行一个示例程序演示CAN总线通讯的效果，
然后分析CAN接口库的框架和接口的用法，初步了解CAN总线接口的配置、消息收发机制和软件编程应用。

运行CAN总线接口示例程序的硬件如图8.19所示。这是BlueFi的一种扩展板，图8.19(A)是该扩展板的实物照片，图8.19(B)是硬件功能结构和引脚资源占用说明。
该扩展板不仅由一个CAN总线接口，还带有一个NBIoT模块用于实现3G和4G蜂窝网的数据通讯，这部分蜂窝网通讯接口将在后续的内容中用到。
这个扩展板适合多种领域的物联网(IoT)应用，所以称这个扩展板为“IoT模块”。

.. image:: ../_static/images/c8/canbus_two_bluefis_iot_module.jpg
  :scale: 25%
  :align: center

图8.19  BlueFi的一种扩展板——IoT模块

IoT模块使用BlueFi的P13~P15等3个引脚分别作为SPI接口的SCK、MISO和MOSI信号，P16作为SPI的NSS片选信号，P12引脚作为MCP2515的中断请求信号。
也就是说IoT模块占用BlueFi的金手指上的5个扩展连接引脚。我们至少需要2个IoT模块和2个BlueFi来运行本节的示例程序，
如果你打算使用相似的硬件功能单元来运行示例程序，请务必根据硬件接口和资源占用情况修改本节的示例代码以及所用的接口库代码。

我们首先按照图8.19(A)所示的连接方法将两个插有BlueFi的IoT扩展板的CAN总线连接起来。请注意“CAN_H”与“CAN_L”两个信号不能交叉。
然后下载本节使用到的所有源文件和示例程序。

为了便于测试，请先删除“../Documents/Arduino/libraries/BlueFi”文件夹中的全部文件，然后下载下面的压缩文件包，
并解压到“../Documents/Arduino/libraries/BlueFi”文件夹中，

. :download:`本节内容所用到的BlueFi的BSP源文件 <../_static/dl_files/bluefi_ch8_4/BlueFi_bsp_ch8_4.zip>`

再下载下面的压缩包，即“CAN_MCP2515.zip”文件，然后将该压缩包解压到“../Documents/Arduino/libraries/”文件夹中，

. :download:`本节内容所用到的BlueFi的BSP源文件 <../_static/dl_files/bluefi_ch8_4/CAN_MCP2515.zip>`

接着下载下面的压缩包，即“bluefi_nrf52840.zip”文件，然后将该压缩包解压到RF52的Arduino安装路径，
即“../Arduino/packages/adafruit/hardware/nrf52/0.21.0/variants/”文件夹，并覆盖该文件夹中已有的“bluefi_nrf52840”文件夹，

. :download:`本节内容所用到的BlueFi的BSP源文件 <../_static/dl_files/bluefi_ch8_4/bluefi_nrf52840.zip>`

上述3个压缩包分别是BlueFi的BSP文件、CAN总线接口库文件(使用MCP2515)和BlueFi的兼容Arduino的引脚定义文件，
直接使用这些源文件可以快速搭建BlueFi和IoT扩展板的CAN总线通讯测试环境，当然你也可以自行修改这些源码以及对应的示例程序实现其他功能。

上述的软硬件准备工作完毕后，我们现在使用Arduino IDE可以打开“../Documents/Arduino/libraries/CAN_MCP2515/examples/transceiver_bluefi/transceiver_bluefi.ino”源文件，
并将BlueFi和IoT模块与电脑USB端口连接好，点击编译并下载，我们即可将第一个示例程序下载到两个BlueFi的FlashROM中。

如果你不打算编译和修改示例程序，可以直接将“../Documents/Arduino/libraries/CAN_MCP2515/examples/transceiver_bluefi/transceiver_bluefi.uf2”文件拖放到BLUEFFIBOOT磁盘即可。
还记得如何把连接到电脑的BlueFi映射成名称为“BLUEFIBOOT”的磁盘吗？(用USB数据线激昂BlueFi和电脑连接好之后，双击BlueFi的复位按钮，
BlueFi的5颗彩灯全部亮绿色同时电脑的资源管理器中将出现BLUEFIBOOT磁盘)。

两个BlueFi一旦开始运行示例程序，打开与BlueFi连接的电脑上Arduino IDE的串口监视器(即控制台)，然后按下任一BlueFi的A按钮，
我们将会再串口监视器上看到下面的信息：

.. code-block::  C
  :linenos:

  ..
  -------------- Got a message, ID: 0x401
  rData:0x31 0x32 0x33 0x34 0x35 0x36 0x37 0x38 , T:206.000s
  Successed to send a message
  -------------- Got a message, ID: 0x401
  rData:0x31 0x32 0x33 0x34 0x35 0x36 0x37 0x38 , T:206.003s
  Successed to send a message
  -------------- Got a message, ID: 0x401
  rData:0x31 0x32 0x33 0x34 0x35 0x36 0x37 0x38 , T:206.006s
  Successed to send a message
  -------------- Got a message, ID: 0x401
  rData:0x31 0x32 0x33 0x34 0x35 0x36 0x37 0x38 , T:206.009s
  Successed to send a message
  ..
  -------------- Got a message, ID: 0x401
  rData:0x31 0x32 0x33 0x34 0x35 0x36 0x37 0x38 , T:206.999s
  Successed to send a message
  ..

显然这些是3行打印信息的不断重复，但是“T:xxx.xxxs”中的数值是变化的，而且相邻两个重复信息中的这个值相差大约0.003s。
3行重复信息的第1行提示“收到一个CAN消息”且打印该消息的ID；第2行提示收到的CAN消息中的8字节数据“rData:..”和收到该消息时系统的累计运行时间“T:xxx.xxxs”;
第3行提示“成功地发送一个CAN消息”。根据这些提示我们不难发现，两个BlueFi通过IoT模块上的CAN总线连接起来，每3ms收发一对CAN消息。
同时，我们可以观察到BlueFi的红色LED指示灯不断地闪烁。如果我们将连接两个IoT模块的CAN总线插头拔掉一个，BlueFi的红色LED停止闪烁，
再将插头插回去后这个红色LED接着闪烁。拔掉插头则意味着断开两个IoT模块的CAN总线连接。

上述的CAN总线测试可以初步了解CAN总线的容错能力、可靠性和通讯速度。对照上述的运行效果，现在我们来查看示例代码：

.. code-block::  C
  :linenos:

  #include <can_mcp2515.h>
  #include <BlueFi.h>
  MCP_CAN can_bus(16);  // specify CS pin of MCP2515
  unsigned long myid = 0x401;
  unsigned char rlen=0, rbuf[8] = {0x0,0x0,0x0,0x0,0x0,0x0,0x0,0x0};
  unsigned char tcnt=0, tbuf[8] = {0x31,0x32,0x33,0x34,0x35,0x36,0x37,0x38};

  void set_id_filter(void) {
    // set mask, set both the mask to 0x3ff, the MSB of ID may be '0', or '1'
    can_bus.init_Mask(0, 0, 0x3ff); // there are 2 mask in mcp2515, we need to set both of them
    can_bus.init_Mask(1, 0, 0x3ff);
    // set filter, we can receive id from 0x001~0x006 or 0x401~0x406 only
    can_bus.init_Filter(0, 0, 0x001); // there are 6 filter in mcp2515
    can_bus.init_Filter(1, 0, 0x002);
    can_bus.init_Filter(2, 0, 0x003);
    can_bus.init_Filter(3, 0, 0x004);
    can_bus.init_Filter(4, 0, 0x005);
    can_bus.init_Filter(5, 0, 0x006);
  }

  bool a_btn_clicked = false;
  void cbf_a_btn_click(Button2& btn) {
    a_btn_clicked = true;
  }

  void setup() {
    bluefi.begin();
    bluefi.redLED.on();
    bluefi.aButton.setClickHandler(cbf_a_btn_click);
    while (CAN_OK != can_bus.begin(CAN_500KBPS)) {
      Serial.println("CAN BUS FAIL!");
      delay(1000);
    }
    bluefi.redLED.off();
    Serial.println("CAN BUS OK!");
    set_id_filter();
  }

  void loop() {
    static bool rok = false;
    bluefi.aButton.loop();
    if ( (a_btn_clicked) || (rok) ) {
      uint8_t tv = can_bus.sendMsgBuf(myid, 0, sizeof(tbuf), tbuf);
      switch (tv) {
        case CAN_OK:             Serial.println("Successed to send a message"); break;
        case CAN_GETTXBFTIMEOUT: Serial.println("Failed to send [error to get TxBuf]"); break;
        case CAN_SENDMSGTIMEOUT: Serial.println("Failed to send [timeout of sending]"); break;
        default: Serial.println("Failed to send [unknown error]"); break;
      }
      if ( ((tcnt++)>100) && (tv==CAN_OK) ) {
        bluefi.redLED.toggle();
        tcnt = 0;
      }
      a_btn_clicked = false;
      rok = false;
    }
    if( CAN_MSGAVAIL == can_bus.checkReceive() ) {
      can_bus.readMsgBuf(&rlen, rbuf);
      unsigned long rid = can_bus.getCanId();
      String _pstr = "-------------- Got a message, ID: 0x" + String(rid, HEX);
      Serial.println(_pstr);
      _pstr = "Data: ";
      for(int i = 0; i<rlen; i++)
          _pstr += "0x" + String(rbuf[i], HEX) + " ";
      Serial.println(_pstr + "T:" + String(millis()/1000.0, 3) + "s");
      rok = true;
    }
  }

除了Arduino程序的初始化函数“setup()”和主循环函数“loop()”之外，示例代码还包含“void set_id_filter(void)”子程序用于设置CAN协议控制器——MCP2515的ID滤波器，
以及“void cbf_a_btn_click(Button2& btn)”回调函数。在初始化函数“setup()”的最后一步调用“void set_id_filter(void)”子程序，
在该子程序中分别对MCP2515的两个mask寄存器和6个filter寄存器进行设置，以限制MCP2515仅接收ID为0x001~0x006和0x401~0x406的CAN消息，
关于CAN ID滤波器的mask和filter的用法稍后在解释；在初始化函数“setup()”中已经将“void cbf_a_btn_click(Button2& btn)”子程序注册为BlueFi的A按钮的回调函数，
在程序运行期间当我们按下A按钮时将自动执行该回调函数，这个回调函数仅一行语句，即将变量“a_btn_click”赋值为“true”。

为了更好地理解这个示例程序的代码，对应的程序流程见图8.20。

.. image:: ../_static/images/c8/canbus_software_example_flowchart.jpg
  :scale: 30%
  :align: center

图8.20  CAN总线通讯示例程序流程

可以看出，在示例程序的主循环中首先调用“bluefi.aButton.loop()”更新BlueFi的A按钮的状态，期间如果A按钮被按下则自动执行我们已经注册的回调函数“void cbf_a_btn_click(Button2& btn)”;
然后判断变量“a_btn_clicked”或“rok”是否为“true”，如果“是”则调用“can_bus.sendMsgBuf(myid, 0, sizeof(tbuf), tbuf)”函数发送一个CAN消息(这个CAN消息的ID和内容分别由变量“myid”和“tbuf”指定)，
然后检查是否发送成功，如果发送成功发送且达到100个消息则调用“bluefi.redLED.toggle()”函数切换BlueFi的红色LED状态并清除发送消息的计数器(即变量“tcnt”)；
最后调用“can_bus.checkReceive()”函数并根据其返回值侦测是否接收到CAN消息，如果是则读取这个消息并打印到串口控制台，并将变量“rok”设置为“true”。

根据主循环流程的分析，我们可以看出给两个BlueFi上电或复位后BlueFi的红色LED是熄灭的状态，按下任一A按钮后红色LED开始闪烁，
每个闪烁周期内两个BlueFi已经通过CAN总线收发200对消息。在任一BlueFi上，按下A按钮或收到一个CAN消息则立即发送一个CAN消息，
A按钮就像一个触发开关，只要按下一次BlueFi立即发送一个CAN消息，当另一个BlueFi收到这个消息后立即发送一个消息，如此一来两个BlueFi就像打乒乓球一样“你发我收，我发你收”地重复进行下去，
随着测试时间的延长会不会出现“球丢了”的失误呢？很显然，任一一个消息丢失将会造成某个BlueFi不能接收到消息而终止通讯。

如果你能够修改和重新编译示例代码，可以尝试修改第4行的变量“myid”的赋值，修改为之外的任意值，
该变量的数据类型虽然是32位无符号整型数，根据CAN标准的ID域的有效位数：11位(CAN2.0的标准ID)和29位(扩展ID)，请选择合适的数值。
譬如我们修改“myid=0x409”，保持代码并重新编译和下载示例程序到两个BlueFi，其他保持不变再次测试两个BlueFi之间的CAN总线通讯，
不仅看不到上述的现象(红色LED闪烁，接收到消息的打印信息等)，虽然看到“Successed to send a message”打印信息但怀疑CAN总线并未发送成功。
事实上，按下某个BlueFi的A按钮后一个ID为0x409的CAN消息理解被发送到CAN总线上，另一个IoT模块的MCP2515也能够收到这个消息，
但是我们的示例程序却收不到该消息，主要原因是MCP2515的ID滤波器在起作用：仅接收ID为0x001~0x006和0x401~0x406这12种消息，
0x409显然不在这个有效范围内。这就是说，配置ID滤波器可以让MCP2515帮助我们的程序忽略掉大部分ID的消息，仅接收我们想要的消息，
这可以有效地提升CAN协议控制器和MCU/SoC之间的数据传输效率，也能节约MCU/SoC的时间。

几乎所有的CAN协议控制器都支持ID滤波器功能，而且都采用mask和filter两种配置寄存器，不同的CAN协议控制器的惟一区别是mask和filter寄存器的个数。
如何使用ID滤波器的mask和filter呢？对于11位的ID，可接收的ID为：

  -  ( (maskValue & 0x7FF) | filterValue)
  -  (~(maskValue & 0x7FF) & filterValue)

对于29位的扩展ID，可接收的ID为：

  -  ( (maskValue & 0x1FFF-FFFF) | filterValue)
  -  (~(maskValue & 0x1FFF-FFFF) & filterValue)

对于具有m个mask和n个filter寄存器的CAN协议控制器，需要根据上述两种情况分别计算，可以确定2*m*n个(或类)可接收ID。
在上面示例中，MCP2515具有2个mask寄存器和6个filter寄存器，我们想2个mask寄存器写入同一值0x3FF，向6个filter寄存器分别写入“1~6”，
根据上面的可接收ID的计算规则可确定：

  -  ( (0x3FF & 0x7FF) | [0x001,0x002,0x003,0x004,0x005,0x006]) = [0x401,0x402,0x403,0x404,0x405,0x406]
  -  (~(0x3FF & 0x7FF) & [0x001,0x002,0x003,0x004,0x005,0x006]) = [0x001,0x002,0x003,0x004,0x005,0x006]

这些逻辑运算的结果即可说明“myid=0x409”时示例程序不能呈现最初的效果。

根据可接收ID的计算规则，如果将mask寄存器的每一个位都设置为‘0’时，则只能接收由filter寄存器指定的ID；如果将mask寄存器的每一个位都设置为‘1’时，
则所有ID都是可接收的。

现在我们需要来看一看上面示例中用到的CAN总线接口库，图8.21给出“../Documents/Arduino/libraries/CAN_MCP2515/”库文件夹中的主要文件说明，
除了上面使用过的“transceiver_bluefi”示例程序之外，在“examples”子文件夹中还有其他一些示例程序。这个使用SPI接口扩展的MCP2515的CAN总线接口库的源文件在“src”子文件夹中，
其中包含3个源文件，“mcp2515_dfs.h”指定MCP2515内部寄存器映射关系，“can_mcp2515.cpp”是接口库的源文件，“can_mcp2515.h”是接口库的头文件，
这个库的所有接口都可以在这个头文件中找到。

.. image:: ../_static/images/c8/canbus_software_if_library_files.jpg
  :scale: 25%
  :align: center

图8.21  CAN_MCP2515接口库文件夹的结构和主要源文件

这个CAN总线接口库依然使用分层抽象和封装的思路进行设计，其层次结构如图8.22所示。作为BlueFi一种功能拓展板，板上MCP2515通过SPI接口与BlueFi的主控制器连接，
软件接口库的底层必须使用SPI通讯接口访问MCP2515的内部寄存器，从CAN总线配置到收发CAN消息等操作实际上都是在访问(读/写)MCP2515内部寄存器。
图8.22中也给出了使用MCU/SoC片上CAN协议控制器的情况，此时我们无需使用SPI接口则根据MCU/SoC的相关文档确定CAN协议控制器的寄存器映射直接访问MCU/SoC的相关寄存器即可。
面向用户层的CAN总线接口应包含配置接口、发送CAN消息的接口、接收CAN消息的接口等。

.. image:: ../_static/images/c8/canbus_if_software_structure.jpg
  :scale: 25%
  :align: center

图8.22  CAN总线接口库的层次结构

打开“../Documents/Arduino/libraries/CAN_MCP2515/src/can_mcp2515.h”文件，我们可以清晰地看到“MCP_CAN”类的“public”域包含以下接口：

.. code-block::  C
  :linenos:

  MCP_CAN(byte _CS);
  byte begin(byte speedset);
  byte init_Mask(byte num, byte ext, unsigned long ulData);
  byte init_Filter(byte num, byte ext, unsigned long ulData);
  byte sendMsgBuf(unsigned long id, byte ext, byte rtr, byte len, byte *buf);
  byte sendMsgBuf(unsigned long id, byte ext, byte len, byte *buf);
  byte readMsgBuf(byte *len, byte *buf);
  byte readMsgBufID(unsigned long *ID, byte *len, byte *buf);
  byte checkReceive(void);
  byte checkError(void);
  unsigned long getCanId(void);
  byte isRemoteRequest(void);
  byte isExtendedFrame(void);

第1个接口是构造函数，输入参数用于指定SPI接口的NSS片选信号的引脚，在上面示例程序中我们传入的参数为16，即BlueFi的P16引脚与MCP2515的片选信号连接，
从图8.19和前一节的图8.15都可以确定。

第2个接口“byte begin(byte speedset)”是初始化CAN总线的操作，输入参数用于指定CAN总线的波特率，可用的CAN总线波特率共16种，
在“../Documents/Arduino/libraries/CAN_MCP2515/src/mcp2515_dfs.h”文件中的第275～292行定义。调用该接口时将会对MCP2515的某些寄存器(如波特率配置寄存器)进行读写操作，
如果这些读写操作都是成功的则返回“CAN_OK”，否则返回可能的错误原因对应的错误编码值(见“../Documents/Arduino/libraries/CAN_MCP2515/src/mcp2515_dfs.h”文件中的第294～302行的定义)。
在上面示例程序的初始化函数“setup()”中有一个“while”语句来判断CAN总线的初始化是否成功，如果我们未将BlueFi和IoT模块正确连接好并对他们通电，
我们的程序将停留在初始化的这个操作，很显然这属于硬件故障，无法进一步实现CAN总线通讯，程序停留在这一步是正常的设计。

第3个和第4个接口用于配置MCP2515的mask和filter寄存器，即ID滤波器的配置操作接口。每个接口的三个参数分别是寄存器的编号、是否是扩展ID和寄存器值。
MCP2515仅有2个mask寄存器其编号分别为0和1，6个filter寄存器的编号分别为0~5。“是否扩展ID”的参数为0时表示标准ID(即11位)，为1时表示扩展ID(即29位)。

第5个和第6个接口都是发送一个CAN消息的操作，区别是第5个接口可以发送一个远程请求帧(参数“rtr”设为1时)，第6个接口只能发送标准的CAN2.0B消息。
这两个接口的输入参数还有“id”、“ext”、“len”和“*buf”分别指定待发送的CAN消息的ID、是否是扩展ID、数据域的字节个数和数据指针。
根据这两个接口的返回值可以确定发送是否成功，如果发送成功则返回值为“CAN_OK”，否则返回可能的错误原因对应的错误码(见“../Documents/Arduino/libraries/CAN_MCP2515/src/mcp2515_dfs.h”文件中的第294～302行的定义)。

第7个和第8个接口都是用于从MCP2515读取一个接收到的CAN消息，两个接口的输入参数“*len”和“*buf”分别为返回值的两个指针，用来保存接收到的CAN消息的数据域的字节个数和数据，
第8个接口还有一个“*ID”的指针，用来保存接收到的CAN消息的ID值。第7个接口虽然没有ID参数，但可以单独使用第11个接口，即“unsigned long getCanId(void)”单独地获取当前接收到的CAN消息的ID。
第11个接口在不使用CAN消息的ID滤波器时很有意义，当有CAN消息接收到首先读取该消息的ID，如果该ID的消息可以忽略则不必读取消息的数据。

第9个接口是询问MCP2515是否接收到CAN消息，当调用该接口时的返回值为“CAN_MSGAVAIL”时表示已收到新的CAN消息。在上面的示例程序中我们在主循环中使用这个接口查询是否收到CAN消息，
如果该接口返回值为“CAN_MSGAVAIL”则调用“unsigned long getCanId(void)”接口读取消息的ID并打印到控制台，然后再调用“byte readMsgBuf(byte *len, byte *buf)”获取该消息的数据域的内容。

第10个接口用于查询当前的错误原因，该接口的返回值是错误原因对应的错误码。第11个接口用于查询当前接收到的CAN消息的ID。
第12个接口用于查询当前接收到的CAN消息是否为远程请求帧。第13个接口用于查询当前接收到的CAN消息的ID是否是29位的(即扩展ID)。

使用这12个接口函数，我们可以对CAN总线接口进行初始化和配置(波特率、ID滤波器等)，发送标准ID的或扩展ID的数据帧或远程帧，接收标准ID的或扩展ID的数据帧或远程帧。
当然所有的CAN消息的数据域都是采用C语言的基本数据类型——数组来保存，这个CAN总线接口库也并未使用任何高级的数据结构。

从CAN总线通讯的应用角度，总线上的任一节点需要实现的标准ID的或扩展ID的数据帧的收发，或者向总线上其他节点发送远程请求帧，或者响应其他节点发起的远程请求帧等功能。
使用本节的CAN总线接口库，我们可以设计很多种CAN总线通讯的应用系统。上面示例仅仅是2个节点通讯，如果我们有3个或更多个CAN节点连接在一个CAN总线上，
如何修改上面的示例程序实现这些节点之间的通讯。具体要求如下：

  - 某3-DoF机械手的3个关节马达控制器和主控制器之间采用CAN2.0B总线连接(即4个节点)，并使用标准ID
  - 主控制器产生ID为0x7F0且数据域仅1个字节，该字节为0x0表示关节停止运动、0x01则允许关节根据指令运动
  - 主控制器根据接收到的ID为0x7F1/2/3判断3个关节马达控制器是否与总线连接
  - 当主控制器上A按钮按下时增加3个关节的角位移(增量为某个固定值)并发出消息给关节马达控制器
  - 当主控制器上B按钮按下时减小3个关节的角位移(增量为某个固定值)并发出消息给关节马达控制器
  - 主控制器产生ID为0x3F1且数据域前后各4个字节分别指定关节1的关节角位移和最大角速度
  - 主控制器产生ID为0x3F2且数据域前后各4个字节分别指定关节2的关节角位移和最大角速度
  - 主控制器产生ID为0x3F3且数据域前后各4个字节分别指定关节3的关节角位移和最大角速度
  - 关节1接收到ID为0x7F0的CAN消息时设定为停止、运行状态，并发送ID为0x7F1且空数据域的消息
  - 在运行状态，如果关节1收到ID为0x3F1的消息后，根据数据域的运动参数完成伺服定位后立即发送ID为481且数据域前后4个字节分别指定关节1的故障码和当前实际角位移
  - 关节2接收到ID为0x7F0的CAN消息时设定为停止、运行状态，并发送ID为0x7F2且空数据域的消息
  - 在运行状态，如果关节2收到ID为0x3F1的消息后，根据数据域的运动参数完成伺服定位后立即发送ID为482且数据域前后4个字节分别指定关节2的故障码和当前实际角位移
  - 关节3接收到ID为0x7F0的CAN消息时设定为停止、运行状态，并发送ID为0x7F3且空数据域的消息
  - 在运行状态，如果关节3收到ID为0x3F1的消息后，根据数据域的运动参数完成伺服定位后立即发送ID为483且数据域前后4个字节分别指定关节3的故障码和当前实际角位移

根据这些具体要求(每个CAN总线节点的行为描述)分别定义主控制器和关节马达控制器的软件功能，CAN总线的初始化和正确的配置可以提高节点的通讯效率。
譬如对于关节1的马达控制器，当接收到ID为0x7F0的消息后根据数据域的值确定故障状态，并发送一个ID为0x7F1的空消息；
当收到ID为0x3F1的消息后如果在工作状态则根据参数控制马达运动并在完成后立即发送一个ID为0x481的消息，该消息的数据域来指定故障码和当前实际的角位移。
对于主控制器来说，通过发送ID为0x7F0的消息后侦听ID为0x7Fx(x=1,2,3)消息判断关节控制器的连接是否完好并启动/停止关节；
通过发送ID为0x3Fx(x=1,2,3)消息控制各关节的运动和运动参数，并侦听ID为0x48x(x=1,2,3)消息来判断各关节的执行结果，
如果ID为0x48x(x=1,2,3)消息的数据域中的实际角位移与设定的角位移之间偏差较小则表示该关节运动正常结束，
否则句数据域中的故障码确定故障原因(电机堵转、参数错误等)。

-------------------------



-------------------------

当我们使用几个BlueFi和IoT模块将上面问题模拟解决之后，相信你一定能够发现CAN总线的更多的应用场景，虽然我们在基于CAN总线的底层操作接口来解决这些问题破费周章。
我们在本章第一节已经提到，CAN总线的国际标准ISO 11898仅仅是低层网络标准，截止目前我们也仅仅遵循这些标准的一部分了解CAN2.0A/B的相关软硬件接口。
那么CAN总线的高层网络标准又是什么样的呢？目前有很多种CAN总线的高层网络标准应用于不同的领域，譬如CANOpen、DeviceNet和SAE J1939等标准。
这些高层网络标准都是基于低层网络标准，且仅仅是为了统一用户层的通讯接口，以确保所有兼容CANOpen等同类高层网络标准的网络节点能够相互通讯，
这样的兼容性很容易在同一个行业内实施，并为行业的产品制造商和供应商带来很多益处。

.. image:: ../_static/images/c8/canbus_canopen_protocol.jpg
  :scale: 30%
  :align: center

图8.23  CANOpen协议栈和协议帧 vs CAN总线低层网络协议和消息

我们在图8.23中简要地给出CANOpen协议栈和协议栈与CAN总线低层网络协议和消息之间的关系。兼容CANOpen协议的设备上的全部资源都采用我们熟悉的“寄存器映射”机制，
为了最大化复杂设备上的资源，CANOpen协议将全部资源映射为16位的索引(Index)和8位子索引(Sub-index)，每个资源的值最大可占用32位(即4字节)，
即允许每个CANOpen节点上资源多达2^24个。在CANOpen协议中将每个资源称作对象，所有资源的映射关系称作对象字典。很显然，对象字典可以是一种表格文件形式，
也可以是保存在ROM中的常数表(只读的对象)、RAM或EEPROM中的可变对象(可读的且可写的对象)，CANOpen协议接口只是对这些对象进行读/写操作。

CANOpen协议要求CAN总线上的每个节点都有惟一的识别码(ID)，而且节点识别码本身也是一种对象。而且兼容CANOpen协议的设备节点分为主节点和从节点两类，
主节点可以发起网络管理帧，包括对所有从节点的启动、停止、暂停、继续等操作指令，但从节点无需应答。主节点也可以使用从节点的惟一识别码发起一对一的问答型通讯，
常用操作就是读取或设置某个节点上的对象的值，这种操作的协议帧的ID由4位命令码和7位从节点识别码组成(在CANOpen协议中称作COB-ID)，
8字节数据域中首个字节是命令码(包括读/写单字节/双字/四字节等6种操作的命令)，第2和3字节是对象的索引，第4字节是对象的子索引，其余的4个字节则是对象的值，
对于读操作来说这4个字节都是0。虽然CANOpen协议帧包含更多个信息域，但他们仍包含在标准CAN数据帧的ID和数据域中。此外，ACANOpen协议不支持远程请求帧。

CANOpen协议并不涉及CAN总线通讯的硬件和传输控制，仅仅是对兼容CANOpen设备上的资源使用对象及其字典进行管理，主节点使用CANOpen协议帧来访问从节点上的对象，
如果我们将前面的3-DoF机械臂的主控制器和关节马达控制器设计成兼容CANOpen协议的节点，那么解决上面的问题会变得更容易。
我们将每个关节马达控制器的惟一识别码(譬如分别设置为1~3)、节点上关节角位移的设定值和实际值、节点上关节角速度的设定值和实际值等等都设计成对象，
并指定每个对象的索引和子索引值，那么主控制器通过写“关节角位移的设定值”，关节马达控制器根据角位移的设定值和实际值之间偏差等参数来控制关节马达的运动。

CANOpen等高层网络协议标准的目标是提升行业内设备之间的兼容性和互联能力，还能大大地简化用户层应用程序的开发。
这些CAN总线的高层协议标准可以通过互联网搜索引擎查阅到，限于篇幅不再赘述。

-------------------------

参考文献：
::

  [1] 

