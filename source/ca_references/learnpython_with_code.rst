============================
1小时学会Python：撸代码法
============================

请不要怀疑！如果已经有代码编程经验，或许只需要半小时就能学会Python。好方法必能提升学习效率。本文档为你提供一种高效的学习Python语言的方法：撸代码法。
这是所有学习编程者的基本方法，只是照着某一本参考书边咬文嚼字边调试书中的参考代码。跟着一本参考书通过撸代码学编程或许会帮助我们更好地理解相关概念，
然而效率确实低了些，谁会记住多少概念呢？大多数人依靠验证才能get编程的概念。本文档直接给出一组由浅入深(直到Advanced topics)的、
Python的主要知识点和关键概念全覆盖的示例代码，只要你有些许的代码编程知识，一小时内将本文档的代码撸一遍，掌握Python编程语言绝非难事儿。

当然，开工之前需要一点准备工作。请自行在电脑上安装好Python 3.x解释器(直接到python.org网站下载并安装)，他自带编辑器和字符控制台；
或者，你已经安装MU编辑器(见“使用BlueFi之前的准备工作”)，将MU编辑器的模式(点击左上角按钮选择)切换为“Python3.x”。

-----------------------------

使用标准Python 3.x解释器或MU编辑器内置的Python3.x解释器，逐行地阅读并执行下面的每一行代码，第一次学习本文档时可以跳过第5～7等三个部分。


.. code-block::  python
  :linenos:

  # 从这里开始，这是一个注释语句。单行的注释是以“#”号开始

  """ 多行的注释语句
      可以使用3个双引号(")包括所有注释行
	"""

  ''' 多行的注释语句
      还可以使用3个单引号(')包括所有注释行
  '''

  ####################################################
  ## 1. 简单的数据类型和运算符
  ####################################################

  # 一个数字
  3  # => 3

  # 数学表达式
  1 + 1  # => 2
  8 - 1  # => 7
  10 * 2  # => 20
  35 / 5  # => 7.0 (自动给出浮点型的商)
  11 / 4  # => 2.75 
  11 / 3  # => 3.6666666666666665 (这是个无理数，精度足够高)
  # 整除，商是整数，采用向下圆整
  5 // 2  # => 2

  # 无论是正数或负数，整除的商都是向下圆整
  -5 // 2  # => -3
  5 // 3     # => 1
  -5 // 3  # => -2
  # 浮点数也支持整除，整数型商仍使用浮点型
  5.6 // 3.3 # => 1.0 
  -5.2 // 3.0 # => -2.0

  # 在Python 2.x中，导入“division” 模块可保持与Python 3.x一致的除法运算
  11/4   # 2  (Python 2.x)
  from __future__ import division
  11/4    # => 2.75  
  11//4   # => 2 

	# 注意，从Python 3.x开始，除法运算的结果取整数和浮点数
	# 仅取决于使用的“//”(整数结果)和“/”(浮点数结果)运算符

  # 取余运算
  7 % 3 # => 1

  # 求幂运算
  2 ** 4 # => 16

  # 使用括号指定强制的运算优先级
  (1 + 3) * 2  # => 8

  # 布尔运算
  # 注意：“and”和“or”运算符是区分大小写的
  # 注意：“True”和“False”两个常数也是区分大小写的
  True and False #=> False
  False or True  #=> True

  # 整数用于布尔运算(回想数值的二进制表示形式)
  0 and 2 #=> 0 (按位“与”运算的结果，非布尔型)
  -5 or 0 #=> -5 (按位“或”运算的结果，非布尔型)
  0 == False #=> True
  2 == True  #=> False
  1 == True  #=> True

  # 用“not”取反
  not True  # => False
  not False  # => True

  # 使用“==”判断是否相等
  1 == 1  # => True
  2 == 1  # => False

  # 使用“!=”判断是否不相等
  1 != 1  # => False
  2 != 1  # => True

  # 更多比较运算
  1 < 10  # => True
  1 > 10  # => False
  2 <= 2  # => True
  2 >= 2  # => True

  # 比较也可以写成链状！
  1 < 2 < 3  # => True
  2 < 3 < 2  # => False

  # 使用 " 或 ' 创建字符串 (单引号和双引号必须成对)
  "This is a string."
  'This is also a string.'

  # 字符串相加(拼接)！
  "Hello " + "world!"  # => "Hello world!"
  # 没有 + 号的字符串拼接
  "Hello " "world!"  # => "Hello world!"

  # 字符串多次复制拼接
  "Hello" * 3  # => "HelloHelloHello"

  # 一个字符串也可以当作一种字符列表(稍后就会遇到列表型数据集)
  "This is a string"[0]  # => 'T'

  # 使用“len(str)”函数确定一个字符串的长度
  len("This is a string")  # => 16

  # 使用“%”格式化字符串
  # 尽管字符串的“%”运算符在Python 3.1及以后的版本中被废弃，但了解它怎么用仍是有益的
  x = 'apple'
  y = 'lemon'
  z = "The items in the basket are %s and %s" % (x,y)

  # “format”是一种更新的格式化字符串的方法，代替“%”的方法
  # 在Python 3.x中，这是首选的方法
  "{} is a {}".format("This", "placeholder")
  "{0} can be {1}".format("strings", "formatted")
  # 如果不想数数，你可以使用关键词，关键词的顺序不重要
  "{who} wants to eat {food}".format(who="Bob", food="lasagna")

  # None是一个对象
  None  # => None

  # 禁止使用“==”来判断某个对象是否为None
  # 使用“is”代替“==”
  "etc" is None  # => False
  None is None  # => True

  # “is”操作符用来判断对象的合法性
  # 用“is”处理简单数值不是很有用，但处理对象十分有用

  # 任何一个对象都可以用作布尔型
  # 下面的这些值是False：
  #    - None
  #    - 所有数值型类型的0 (e.g., 0, 0L, 0.0, 0j)
  #    - 空的序列 (e.g., '', (), [])
  #    - 空的容器 (e.g., {}, set())
  #    - 符合某些条件的用户自定义类的实例
  # 详情见：https://docs.python.org/2/reference/datamodel.html#object.__nonzero__
  # 除了下面两行之外，其他所有都为True（使用bool()函数返回True）
  bool(0)  # => False
  bool("")  # => False


  ####################################################
  ## 2. 变量和集合
  ####################################################

  # Python的print方法(输出字符串到字符控制台)
  print("I'm Python. Nice to meet you!") # => I'm Python. Nice to meet you!

  # 从字符控制台获取输入数据的简单方法
  input_string_var = raw_input("Enter some data: ") # 返回一个String型的值
  input_var = input("Enter some data: ") # 返回一个Int型的值
  # 警告：使用input()方法必须要谨慎
  # 注意：在Python 3，原input()被弃用，并将原raw_input()改名为input()

  # 给变量赋值之前没有必要先声明该变量
  some_var = 5   # 变量赋值
  some_var  # => 5

  # 访问之前没有声明的变量会引起异常
  # 前往“流程控制”学习如何处理异常
  some_other_var  # 引起一个变量名错误

  # “if”可用在表达式中，实现C语言中的三目运算
	# “(测试条)? 条件成立时的值:条件不成立时的值”
  "baidu!" if 3 > 2 else "google!"  # => "baidu!"

  # 列表(存储序列)
  li = []
  # 可以使用预先填充的列表
  other_li = [4, 5, 6]

  # 使用“append”方法向列表尾项添加列表项
  li.append(1)    # li is now [1]
  li.append(2)    # li is now [1, 2]
  li.append(4)    # li is now [1, 2, 4]
  li.append(3)    # li is now [1, 2, 4, 3]
  # 通过“pop”移除列表尾项
  li.pop()        # => 3 and li is now [1, 2, 4]
  # 再将其加入进来
  li.append(3)    # li is now [1, 2, 4, 3] again.

  # 访问列表中的某一项就像使用数组一样
  li[0]  # => 1
  # 使用“=”为已经存在的索引项赋于新的值
  li[0] = 42
  li[0]  # => 42
  li[0] = 1  # 注意，现在再重新赋于原始值
  # 查看最后一个元素(列表的尾项)，这与访问数组有区别！
  li[-1]  # => 3

  # 越界访问会报IndexError
  li[4]  # 引起一个IndexError

  # 通过列表切片获取列表中的部分内容，即获取列表的片段
  li[1:3]  # => [2, 4]
  # 省略结尾
  li[2:]  # => [4, 3]
  # 省略开头
  li[:3]  # => [1, 2, 4]
  # 隔两个步长访问
  li[::2]   # =>[1, 4]
  # 列表反转
  li[::-1]   # => [3, 4, 2, 1]
  # 使用li[开始：结束：步长]来实现不同的切分

  # 使用“del”删除指定位置的元素
  del li[2]   # li is now [1, 2, 3]

  # 两个列表相加(合并列表)
  li + other_li   # => [1, 2, 3, 4, 5, 6]
  # 注意：li和other_list中的数据是没有被修改的

  # 使用“extend()”方法来扩展列表
  li.extend(other_li)   # Now li is [1, 2, 3, 4, 5, 6]

  # 删除第一个被找到对应值的元素
  li.remove(2)  # li is now [1, 3, 4, 5, 6]
  li.remove(2)  # 引起一个ValueError，因为2已经不在li中了

  # 在指定的位置插入一个元素
  li.insert(1, 2)  # li is now [1, 2, 3, 4, 5, 6] again

  # 找到对应值的位置
  li.index(2)  # => 1
  li.index(7)  # 引起一个ValueError，因为7不在li中

  # 使用“in”查看列表中是否存在该元素
  1 in li   # => True

  # 使用“len()”方法获取列表的长度
  len(li)   # => 6

  # 元组(Tuples)像列表，但是它是不可变的
  tup = (1, 2, 3)
  tup[0]   # => 1
  tup[0] = 3  # 引起一个TypeError

  # 你可以对元组做下面所有列表的操作
  len(tup)   # => 3
  tup + (4, 5, 6)   # => (1, 2, 3, 4, 5, 6)
  tup[:2]   # => (1, 2)
  2 in tup   # => True

  # 你可以将元组(或列表)中的数据取到变量中
  a, b, c = (1, 2, 3)     # a is now 1, b is now 2 and c is now 3
  d, e, f = 4, 5, 6       # you can leave out the parentheses
  # 不使用“()”也可以创建元组
  g = 4, 5, 6             # => (4, 5, 6)
  # 简单的交换两个变量的值
  e, d = d, e     # d is now 5 and e is now 4

  # 字典(dict)用来存储映射型信息，即{key:value}型
  empty_dict = {}
  # 这是一个预先定义的字典
  filled_dict = {"one": 1, "two": 2, "three": 3}

  # 使用“[]”查看字典中的值
  filled_dict["one"]   # => 1

  # 使用“keys()”方法获得一个字典中所有键的列表
  filled_dict.keys()   # => ["three", "two", "one"]
  # 注意：字典中键的排序是无规律的
  # 你的结果可能和这个列表不完全相同

  # 使用“values()”方法获得一个字典中所有值的列表
  filled_dict.values()   # => [3, 2, 1]
  # 注意：值的排序同上面键的排序

  # 使用“in”查看指定的键是否在字典中
  "one" in filled_dict   # => True
  1 in filled_dict   # => False

  # 试图访问一个不存在的键时会引起KeyError
  filled_dict["four"]   # 引起一个KeyError

  # 使用“get()”方法获取指定键的值但避免产生KeyError
  filled_dict.get("one")   # => 1
  filled_dict.get("four")   # => None
  # 这个get方法，当查找的键不存在时，返回给定默认值
  filled_dict.get("one", 4)   # => 1
  filled_dict.get("four", 4)   # => 4
  # 记住filled_dict.get("four") 仍然会返回None
  # （get方法不会在字典中设置值）

  # 可以使用与列表一样的方法设置指定键的值
  filled_dict["four"] = 4  # now, filled_dict["four"] => 4

  # “setdefault()”方法只有在字典中不存在指定键的时候才插入
  filled_dict.setdefault("five", 5)  # filled_dict["five"] is set to 5
  filled_dict.setdefault("five", 6)  # filled_dict["five"] is still 5

  # 集合(set)是无序的无重复元素的序列
  empty_set = set()
  # 使用一批数值初始化一个集合
  some_set = set([1, 2, 2, 3, 4])   # some_set is now set([1, 2, 3, 4])

  # 无序是常态，即使看起来像排好序的
  another_set = set([4, 3, 2, 2, 1])  # another_set is now set([1, 2, 3, 4])

  # 从Python 2.7开始，允许用“{}”来声明一个集合
  filled_set = {1, 2, 2, 3, 4}   # => {1, 2, 3, 4}

  # 向集合中添加一个元素
  filled_set.add(5)   # filled_set is now {1, 2, 3, 4, 5}

  # 两集合间使用“&”做交集运算
  other_set = {3, 4, 5, 6}
  filled_set & other_set   # => {3, 4, 5}

  # 两集合间使用“|”做并集运算
  filled_set | other_set   # => {1, 2, 3, 4, 5, 6}

  # 两集合间使用“-”做差集运算
  {1, 2, 3, 4} - {2, 3, 5}   # => {1, 4}

  # 两集合间使用“^”做对称差分运算(交集的补集)
  {1, 2, 3, 4} ^ {2, 3, 5}  # => {1, 4, 5}

  # 检查右边的集合是否是左边的子集
  {1, 2} >= {1, 2, 3} # => False

  # 检查左边的集合是否是右边的子集
  {1, 2} <= {1, 2, 3} # => True

  # 使用“in”检查指定的元素是否属于集合中的元素
  2 in filled_set   # => True
  10 in filled_set   # => False


	####################################################
	## 3. 流程控制
	####################################################

  # 让我们来创建一个变量
  some_var = 5

  # 下面是一些if语句。“:”和程序行缩进是保持Python程序块的关键!
  #	  print( "some_var is smaller than 10." )缩进一个 Tab键
  if some_var > 10:
    print( "some_var is totally bigger than 10." )
  elif some_var < 10:    # 这个elif代码块是可选的
    print( "some_var is smaller than 10." )
  else:           # 这个同样是可选的
    print( "some_var is indeed 10." )


  """
  使用“for”循环遍历列表
  prints:
    dog is a mammal
    cat is a mammal
    mouse is a mammal
  """
  for animal in ["dog", "cat", "mouse"]:
    # 你可以使用“{0}”占位符来插入字符串（详情见上）
    print( "{0} is a mammal".format(animal) )

  """
  “range(number)”返回一个从0到number-1的连续整数列表
  prints:
    0
    1
    2
    3
  """
  for i in range(4):
    print( i )

  """
  “range(number1, number2)”返回一个从number1到number2-1的连续整数列表
  prints:
    4
    5
    6
    7
  """
  for i in range(4, 8):
    print( i )

  """
  “range(number1, number2, steplength)”返回一个从number1开始到number2为止的
  固定间隔的整数列表(number2肯定不会包含在列表中)，间隔/步长为steplength
  prints:
    4
    6
  """
  for i in range(4, 8, 2):
    print( i )

  """
  While可以一直循环到条件不成立
  prints:
    0
    1
    2
    3
  """
  x = 0
  while x < 4:
    print( x )
    x += 1  # 这是“x = x + 1”的速写形式

  # 通过try/except代码段来处理异常

  # 请在Python2.6以后可以使用：
  try:
    # 使用“raise”抛出一个异常
    raise IndexError("This is an index error")
  except IndexError as e:
    pass    # Pass就是一个空语句(占位语句)。通常需要你在这里处理异常
  except (TypeError, NameError):
    pass    # 如果有必要，同时处理多种异常
  else:   # 这是try/except代码段可选的条件。必须要跟在所有的except语句之后
    print( "All good!" )  # 仅仅在try代码中没有出现异常时才执行
  finally: #  在所有语句执行完毕之后执行
    print( "We can clean up resources here" )

  # 使用with语句代替try/finally语句清空资源
  with open("myfile.txt") as f:
    for line in f:
      print( line )


  ####################################################
  ## 4. 函数
  ####################################################

  # 使用“def”来创建一个函数
  # 注意，函数是一个程序块，使用“:”和缩进保持程序块
  def add(x, y):
    print( "x is {0} and y is {1}".format(x, y) )
    return x + y    # 通过return语句返回结果

  # 调用带有多个参数的函数，多个参数按定义函数时的参数顺序传递
  add(5, 6)   # => prints out "x is 5 and y is 6" and returns 11

  # 另一种调用带有参数的函数，通过关键字传递参数，参数的顺序不重要了
  add(y=6, x=5)   # Keyword arguments can arrive in any order.

  # 定义一个能够接受参数个数可变的函数，使用“*”将参数解释成元组类型
  def varargs(*args):
    return args

  varargs(1, 2, 3)   # => (1, 2, 3)

  # 定义一个能够接受参数个数可变的函数，使用“*”将参数解释成字典类型
  def keyword_args(**kwargs):
    return kwargs

  # 让我们看看调用这种函数时会发生什么
  keyword_args(big="foot", loch="ness")   # => {"big": "foot", "loch": "ness"}

  # 当然，如果你喜欢还可以同时使用它俩
  def all_the_args(*args, **kwargs):
    print( args )
    print( kwargs )
  """
  all_the_args(1, 2, a=3, b=4) prints:
    (1, 2)
    {"a": 3, "b": 4}
  """

  # 当你调用函数的时候，你可以选择参数，使用“*”和“**”传递不同类型的参数
  args = (1, 2, 3, 4)
  kwargs = {"a": 3, "b": 4}
  all_the_args(*args)   # equivalent to foo(1, 2, 3, 4)
  all_the_args(**kwargs)   # equivalent to foo(a=3, b=4)
  all_the_args(*args, **kwargs)   # equivalent to foo(1, 2, 3, 4, a=3, b=4)

  def pass_all_the_args(*args, **kwargs):
    all_the_args(*args, **kwargs)
    print( varargs(*args) )
    print( keyword_args(**kwargs) )

  # 函数的作用范围
  x = 5  # the "x" is a global variable

  def set_x(num):
    # 函数当前的变量x和全局的变量x是不同的
    x = num # => 43
    print( x )# => 43

  def set_global_x(num):
    global x
    print( x )# => 5
    x = num # 全局的变量x现在变成了6
    print( x )# => 6

  set_x(43)
  set_global_x(6)

  # Python的第一类函数(第一类对象)
  def create_adder(x):
    def adder(y):
      return x + y
    return adder

  add_10 = create_adder(10) # Python函数是一种对象，可以赋值为一个变量
  add_10(3)   # => 13

  # 当然也有匿名函数(lambda函数)
  (lambda x: x > 2)(3)   # => True
  (lambda x, y: x ** 2 + y ** 2)(2, 1) # => 5

  # Python也有很多内建的高阶函数
  map(add_10, [1, 2, 3])   # => [11, 12, 13]
  map(max, [1, 2, 3], [4, 2, 1])   # => [4, 2, 3]

  filter(lambda x: x > 5, [3, 4, 5, 6, 7])   # => [6, 7]

  # 我们可以使用列表构造出漂亮的map和滤波器
  [add_10(i) for i in [1, 2, 3]]  # => [11, 12, 13]
  [x for x in [3, 4, 5, 6, 7] if x > 5]   # => [6, 7]

  # 还可以构造出集合和字典
  {x for x in 'abcddeef' if x in 'abc'}  # => {'a', 'b', 'c'}
  {x: x ** 2 for x in range(5)}  # => {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}


  ####################################################
  ## 5. 类
  ####################################################

  # 我们从object中派生出一个子类
  class Human(object):

    # 一个类的属性。它被这个类的所有实例所共享
    species = "H. sapiens"

    # 基本初始化，当这个类被实例化时将自动调用
    # 注意前后的双下划线表示对象或者属性由Python使用，但是它处在用户控制的命名空间中
    # 你不应该这样创建自己的名字
    def __init__(self, name):
      # 声明变量 _name，并将 name 参数赋值给该变量
      self._name = name

      # 初始化属性
      self._age = 0

    # 实例的一个方法。所有的方法都将“self”作为第一个参数
    def say(self, msg):
      return "{0}: {1}".format(self._name, msg)

    # 一个类中的方法被所有的实例对象所共享
    # 它们通过第一个参数所属的类被其类所调用
    @classmethod
    def get_species(cls):
      return cls.species

    # 一个静态的方法，不通过一个类或者一个实例所调用
    @staticmethod
    def grunt():
      return "*grunt*"

    # “property”就像是getter方法
    # 它将返回方法age的只读的属性
    @property
    def age(self):
      return self._age

    # 下面的方法是对属性赋值
    @age.setter
    def age(self, value):
      self._age = value

    # 下面的方法是将属性删除掉
    @age.deleter
    def age(self):
      del self._age

  # 实例化一个类
  i = Human(name="Ian")
  print( i.say("hi") )    # prints out "Ian: hi"

  j = Human("Joel")
  print( j.say("hello") ) # prints out "Joel: hello"

  # 调用类中的方法
  i.get_species()   # => "H. sapiens"

  # 修改共享的属性
  Human.species = "H. neanderthalensis"
  i.get_species()   # => "H. neanderthalensis"
  j.get_species()   # => "H. neanderthalensis"

  # 调用静态方法
  Human.grunt()   # => "*grunt*"

  # 更新/设置属性值
  i.age = 42

  # 获取属性值
  i.age # => 42

  # 删除属性
  del i.age
  i.age  # => raises an AttributeError


  ####################################################
  ## 6. 模块
  ####################################################

  # 导入模块
  import math

  print( math.sqrt(16) ) # => 4.0

  # 从一个模块中导入指定的函数/方法
  from math import ceil, floor

  print( ceil(3.7) ) # => 4.0
  print( floor(3.7) )  # => 3.0

  # 从一个模块中导入所有的函数
  # 警告：这种方法是不建议的，建议使用 import moduleName
  from math import *

  # 下面的方式可以缩短模块名称
  import math as m

  math.sqrt(16) == m.sqrt(16)   # => True
  # 测试这些函数的等价性
  from math import sqrt

  math.sqrt == m.sqrt == sqrt  # => True

  # Python的模块(module)仅仅是普通的python文件。你可以编写自己的Python模块然后导入它们
  # Python模块的名称和文件名称必须相同

  # 使用“dir(module)”查找一个模块中的方法和属性
  import math

  dir(math)

  # 如果有一个Python脚本文件，名叫“math.py”，正好在当前执行的Python脚本程序文件夹中
  # 当前文件夹中的这个“math.py”文件将替代Python内建的"math"模块被加载
  # 这是因为，本地文件夹内的模块优先级高于内建的库/模块


  ####################################################
  ## 7. 高级(生成器和装饰器)
  ####################################################


  # 生成器(Generators)
  # 一个生成器能够按要求“产生”值，而不是事先存储的值

  # 下面的方法( **不是** 生成器)将翻倍每一个值并存储到“double_arr”中
  # 对于一个大的迭代器，需要很大的存储空间！
  def double_numbers(iterable):
    double_arr = []
    for i in iterable:
      double_arr.append(i + i)
    return double_arr

  # 运行下面代码，我们将首先翻倍每一个值，然后返回所有值并根据条件来判断
  for value in double_numbers(range(1000000)):  # `test_non_generator`
    print( value )
    if value > 5:
      break

  # 现在我们用生成器代替“产生”翻倍值，但这是根据需要进行的
  def double_numbers_generator(iterable):
    for i in iterable:
      yield i + i

  # 现在运行前面相同的代码，但这次使用生成器，随着程序逻辑的需要以迭代的形式翻倍数值
  # 当满足“value > 5”条件时，循环终止，这样的程序执行不需要所有翻倍后的数值(速度更快！内存更少！)
  for value in double_numbers_generator(xrange(1000000)):  # `test_generator`
    print( value )
    if value > 5:
      break

  # 顺便提及，你是否注意到“test_non_generator”这一行的“range()”函数和“test_generator”这一行的“xrange()”?
  # 如果说“double_numbers_generator”是“double_numbers”的生成器版本，关键是“xrange()”是“range()”的生成器版本
  # “range(1000000)”返回列表“[0, 1, .., 999999]”，但“xrange(1000000)”仅仅是一个0～999999之间数值的生成器
  # 只会根据我们程序要求/随着迭代返回其中的单个值
  # 
  # 就像创建一个列表一样地创建一个生成器 (values **是** 一个生成器)
  values = (-x for x in [1, 2, 3, 4, 5])
  for x in values:
    print( x )  # prints -1 -2 -3 -4 -5 to console/terminal

  # 值得注意，Python3.x弃用“xrange()”，使用“range()”代替Python2.x中的“xrange()”
  # 换句话说，Python3.x中的“range()”已经是生成器！

  # 完全可以将一个生成器直接转换成一个列表
  values = (-x for x in [1, 2, 3, 4, 5])
  gen_to_list = list(values)
  print( gen_to_list )  # => [-1, -2, -3, -4, -5]

  # 装饰器(Decorators)
  # 装饰器是一种高阶函数，他是修改其他函数的功能的函数
  # 简单的用法示例：“add_apples”装饰器将“Apple”元素添加到“fruits”列表中，通过“get_fruits”函数返回他
  def add_apples(func):
    def get_fruits():
      fruits = func()
      fruits.append('Apple')
      return fruits
    return get_fruits

  @add_apples
  def get_fruits():
      return ['Banana', 'Mango', 'Orange']

  # Prints out the list of fruits with 'Apple' element in it:
  # Banana, Mango, Orange, Apple
  print( ', '.join(get_fruits()) )

  # in this example "beg" wraps "say"
  # "beg" will call "say". If "say_please" is True then it will change the returned
  # message
  from functools import wraps

  def beg(target_function):
    @wraps(target_function)
    def wrapper(*args, **kwargs):
      msg, say_please = target_function(*args, **kwargs)
      if say_please:
        return "{} {}".format( msg, "Please! I am poor :(" )   
      return msg
    return wrapper

  @beg
  def say(say_please=False):
    msg = "Can you buy me a beer?"
    return msg, say_please

  print( say() ) # Can you buy me a beer?
  print( say(say_please=True) ) # Can you buy me a beer? Please! I am poor :(


如果已经认真地执行到这里(允许跳过第5～7部分)，祝贺你已经学会Python！数值及其运算、字符串、变量和函数，这些基本概念在Python语言中有哪些特殊之处？
列表、元组(只读型列表)、字典、集合等数据结构还在哪些编程语言中用到？Python使用“:”和行缩进的形式来组织程序块，你熟悉的编程语言分别都是如何组织程序块的呢？
导入模块的思想是Python特有的吗？其他编程语言如何导入模块呢？再多找一些能够与你所熟悉的代码编程语言形成对比的概念，更好地加深理解和掌握。

如果你觉得把这些代码撸一遍不能掌握Python的话，那就多撸几遍吧！
