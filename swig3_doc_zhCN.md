SWIG 官方文档 - 机翻中文人肉修正
原文链接：http://www.swig.org/Doc3.0/SWIGDocumentation.html
- 第一章介绍如何下载安装 swig 跳过；
- 第三章介绍在 windows 下使用 swig 跳过；
- 第十七章开始介绍各种目标语言，只保留了 Lua 语言，其余跳过

2 简介
2.1 SWIG 是什么？
SWIG 是一种软件开发工具，可简化“将不同语言连接到 C 和 C++ 程序”的任务。简而言之，SWIG 是一个编译器，它接受 C/C++ 声明并创建从其他语言（包括 Lua、Perl、Python、Tcl、Ruby、Guile 和 Java）访问这些声明所需的包装器。SWIG 通常不需要修改现有代码，并且通常可以在几分钟内构建一个可用的接口。SWIG 的可能应用包括：
- 为现有 C 程序构建解释接口。
- 快速原型设计和应用程序开发。
- 交互式调试。
- 将遗留软件重新设计或重构为脚本语言组件。
- 制作图形用户界面（例如使用 Tk）。
- 测试 C 库和程序（使用脚本）。
- 为脚本语言构建高性能 C 模块。
- 使 C 编程更有趣（或可以忍受，取决于您的观点）。F
- 打动你的朋友。
- 获得巨额研究经费（虽然显然不适用于作者）。
SWIG 最初旨在使科学家和工程师无需获得软件工程学位即可轻松构建可扩展的科学软件。因此，SWIG 的使用往往有些非正式和临时（例如，SWIG 不需要用户提供正式的接口规范，就像您在专用 IDL 编译器中发现的那样）。虽然这种开发方式并不适合每个项目，但它特别适合小型软件开发；尤其是在科学和工程项目中常见的研发工作。但是，如今众所周知，SWIG 已用于许多大型开源和商业项目。
2.2 为什么要使用 SWIG？
如上一节所述，SWIG 的主要目的是简化将 C/C++ 与其他编程语言集成的任务。然而，为什么会有人想要这样做呢？要回答这个问题，列出 C/C++ 编程的一些优势很有用：
- 对编写编程库的出色支持。
- 高性能（数字运算、数据处理、图形等）。
- 系统编程和系统集成。
- 庞大的用户社区和软件基础。
接下来，我们列举几个C/C++编程的问题
- 编写用户界面相当痛苦（即考虑使用 MFC、X11、GTK 或任何数量的其他库进行编程）。
- 测试很耗时（编译/调试周期）。
- 不重新编译就不容易重新配置或定制。
- 模块化可能很棘手。
- 安全问题（例如缓冲区溢出）。
为了解决这些限制，许多程序员得出的结论是，为不同的任务使用不同的编程语言要容易得多。例如，使用 Python 或 Tcl 等脚本语言编写图形用户界面可能会容易得多（如果需要更多证据，请考虑数百万程序员使用 Visual Basic 等语言的原因）。交互式解释器也可以用作有用的调试和测试工具。Java 等其他语言可能会大大简化编写分布式计算软件的任务。关键是不同的编程语言有不同的优点和缺点。此外，任何编程都极不可能是完美的。因此，通过将语言组合在一起，
从 C/C++ 的角度来看，很多人使用 SWIG 是因为他们想打破传统的单体 C 编程模型，这种模型通常会产生类似这样的程序：
- 做一些有用的事情的函数和变量的集合。
- 一个 main() 的程序启动一切。
- 形成某种用户界面的可怕的黑客集合（但没有人真正想接触）。
将 C/C++ 结合到更高级别的语言中，而不是沿着这条路走下去，通常会导致更模块化的设计、更少的代码、更好的灵活性和更高的程序员生产力。
SWIG 试图使 C/C++ 集成问题尽可能轻松。这使您可以专注于底层 C 程序和使用高级语言接口，而不是让两种语言相互交谈的繁琐和复杂的工作。同时，SWIG 认识到所有应用程序都是不同的。因此，它提供了多种自定义功能，让您可以更改语言绑定的几乎每个方面。这是 SWIG 拥有如此庞大的用户手册的主要原因 ;-)。
2.3 SWIG 示例
说明 SWIG 的最佳方法是使用一个简单的示例。考虑以下 C 代码：
/* File : example.c */

double  My_variable  = 3.0;

/* Compute factorial of n */
int fact(int n) {
  if (n <= 1)
    return 1;
  else
    return n*fact(n-1);
}

/* Compute n mod m */
int my_mod(int n, int m) {
  return(n % m);
}

假设您想从 Tcl 访问这些函数和全局变量 My_variable。首先创建一个 SWIG 接口文件，如下所示（按照惯例，这些文件带有 .i 后缀）：
2.3.1 SWIG 接口文件
/* File : example.i */
%module example
%{
/* Put headers and other declarations here */
extern double My_variable;
extern int    fact(int);
extern int    my_mod(int n, int m);
%}

extern double My_variable;
extern int    fact(int);
extern int    my_mod(int n, int m);

接口文件包含 ANSI C 函数原型和变量声明。在 %module 指令将定义，由SWIG创建的模块的名称。％{％} 块提供了用于插入附加的代码，如 C 头文件或附加的 C 声明，到生成的 C 包装代码的位置。
2.3.2 swig 命令
使用 swig 命令调用 SWIG 。我们可以使用它来构建一个 Tcl 模块（在 Linux 下），如下所示：
unix > swig -tcl example.i
unix > gcc -c -fpic example.c example_wrap.c -I/usr/local/include
unix > gcc -shared example.o example_wrap.o -o example.so
unix > tclsh
% load ./example.so
% fact 4
24
% my_mod 23 7
2
% expr $My_variable + 4.5
7.5
%

该 swig 命令产生一个名为 example_wrap.c 的新文件，其应该与 example.c 一起编译。大多数操作系统和脚本语言现在都支持模块的动态加载。在我们的例子中，我们的 Tcl 模块已经被编译成一个可以加载到 Tcl 的共享库。加载后，Tcl 现在可以访问 SWIG 接口中声明的函数和变量。查看文件example_wrap.c 会发现一团糟。但是，您几乎不需要担心它。
2.3.3 构建 Perl5 模块
现在，让我们把这些函数变成一个 Perl5 模块。在不做任何更改的情况下键入以下内容（针对 Solaris 显示）：
unix > swig -perl5 example.i
unix > gcc -c example.c example_wrap.c \
        -I/usr/local/lib/perl5/sun4-solaris/5.003/CORE
unix > ld -G example.o example_wrap.o -o example.so # This is for Solaris
unix > perl5.003
use example;
print example::fact(4), "\n";
print example::my_mod(23, 7), "\n";
print $example::My_variable + 4.5, "\n";
<ctrl-d>
24
2
7.5
unix >

2.3.4 构建 Python 模块
最后，让我们为 Python 构建一个模块（显示为 Irix）。
unix > swig -python example.i
unix > gcc -c -fpic example.c example_wrap.c -I/usr/local/include/python2.0
unix > gcc -shared example.o example_wrap.o -o _example.so
unix > python
Python 2.0 (#6, Feb 21 2001, 13:29:45)
[GCC egcs-2.91.66 19990314/Linux (egcs-1.1.2 release)] on linux2
Type "copyright", "credits" or "license" for more information.     
>>> import example
>>> example.fact(4)
24
>>> example.my_mod(23, 7)
2
>>> example.cvar.My_variable + 4.5

2.3.5 快捷方法
对于真正懒惰的程序员，人们可能想知道为什么我们需要额外的接口文件。事实证明，您通常可以不用它。例如，您还可以通过在 C 头文件上运行 SWIG 并指定模块名称来构建 Perl5 模块，如下所示
unix > swig -perl5 -module example example.h
unix > gcc -c example.c example_wrap.c \
        -I/usr/local/lib/perl5/sun4-solaris/5.003/CORE
unix > ld -G example.o example_wrap.o -o example.so
unix > perl5.003
use example;
print example::fact(4), "\n";
print example::my_mod(23, 7), "\n";
print $example::My_variable + 4.5, "\n";
<ctrl-d>
24
2
7.5

2.4 支持的 C/C++ 语言特性
SWIG 项目的一个主要目标是，使语言绑定过程极其简单。尽管已经展示了一些简单的示例，但 SWIG 非常有能力支持大多数 C++。一些主要功能包括：
- 完整的 C99 预处理。
- 所有 ANSI C 和 C++ 数据类型。
- 函数、变量和常量。
- 类。
- 单继承和多继承。
- 重载的函数和方法。
- 重载的运算符。
- C++ 模板（包括成员模板、特化和部分特化）。
- 命名空间。
- 可变长度参数。
- C++ 智能指针。
大多数 C++11 也受支持。详细信息在 C++11 部分。
需要强调的是，SWIG 不像几个明显相似的包装器生成工具那样是一个简单的 C++ 词法分析工具。SWIG 不仅可以解析 C++，还实现了完整的 C++ 类型系统，并且能够理解 C++ 语义。SWIG 在充分了解这些信息的情况下生成其包装器。因此，您会发现 SWIG 与包装简单的 C++ 代码一样能够处理令人讨厌的极端情况。事实上，SWIG 能够处理 C++ 代码，这些代码强调了许多 C++ 编译器的极限。
2.5 非侵入式界面构建
当按预期使用时，SWIG 需要对现有 C 或 C++ 代码进行最少（如果有）修改。这使得 SWIG 非常容易与现有包一起使用，并促进软件重用和模块化。通过使 C/C++ 代码独立于高级接口，您可以更改接口并在其他应用程序中重用代码。还可以根据应用程序支持不同类型的接口。
2.6 将 SWIG 合并到构建系统中
SWIG 是一个命令行工具，因此可以合并到任何支持调用外部工具/编译器的构建系统中。SWIG 最常从 Makefile 中调用，但也可以从流行的 IDE（如 Microsoft Visual Studio）中调用。
如果您使用 GNU Autotools ( Autoconf / Automake / Libtool ) 在您的项目中配置 SWIG 使用，则可以使用 SWIG Autoconf 宏。主要宏是 ax_pkg_swig，请参阅 http://www.gnu.org/software/autoconf-archive/ax_pkg_swig.html#ax_pkg_swig 。该 ax_python_devel 宏也对产生 Python 扩展很有帮助。有关此宏和其他 Autoconf 宏的更多信息，请参阅 Autoconf Archive。
一些构建工具对 SWIG 的支持越来越多，例如CMake是一个跨平台、开源的构建管理器，内置对 SWIG 的支持。CMake 可以检测 SWIG 可执行文件和许多用于链接的目标语言库。CMake 知道如何在许多不同的操作系统上构建共享库和可加载模块。这允许轻松的跨平台 SWIG 开发。它还可以生成从 IDE 和 makefile 驱动 SWIG 所需的自定义命令。所有这些都可以通过单个跨平台输入文件来完成。以下示例是用于为 SWIG 接口文件 example.i 创建 python 包装器的 CMake 输入文件：
# This is a CMake example for Python

FIND_PACKAGE(SWIG REQUIRED)
INCLUDE(${SWIG_USE_FILE})

FIND_PACKAGE(PythonLibs)
INCLUDE_DIRECTORIES(${PYTHON_INCLUDE_PATH})

INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR})

SET(CMAKE_SWIG_FLAGS "")

SET_SOURCE_FILES_PROPERTIES(example.i PROPERTIES CPLUSPLUS ON)
SET_SOURCE_FILES_PROPERTIES(example.i PROPERTIES SWIG_FLAGS "-includeall")
SWIG_ADD_MODULE(example python example.i example.cxx)
SWIG_LINK_LIBRARIES(example ${PYTHON_LIBRARIES})

上面的示例将生成本机构建文件，例如 makefile、nmake 文件和 Visual Studio 项目，这些文件将调用 SWIG 并将生成的 C++ 文件编译为 _example.so (UNIX) 或 _example.pyd (Windows)。对于 Windows 上的其他目标语言，通常会生成 dll，而不是 .pyd 文件。
2.7 代码生成
SWIG 旨在生成不需要手动修改的工作代码（实际上，如果您查看了输出，您就可能不想修改它）。您应该认为您的目标语言界面完全由 SWIG 的输入定义，而不是生成的输出文件。虽然这种方法可能会限制硬核 Hack 的灵活性，但它允许其他人忘记低级实现细节。
2.8 SWIG 和自由
不，这不是关于世界政治令人遗憾的状态的特殊部分。然而，知道 SWIG 是用某种关于编程的“哲学”编写的——即程序员很聪明，工具应该远离他们，这可能很有用。正因为如此，你会发现 SWIG 在它让你逍遥法外的事情上非常宽容。事实上，如果危险编程是您的目标，那么您可以使用 SWIG 来超越“用脚射击自己”。另一方面，这种自由可能正是处理复杂和不寻常的 C/C++ 应用程序所需要的。
具有讽刺意味的是，SWIG 提供的自由被一种极其保守的代码生成方法所抵消。从本质上讲，SWIG 试图将最先进的 C++ 代码提炼成一组基于 若ANSI C 编程的定义良好的小型接口构建技术。因此，您会发现几乎每个 C/C++ 编译器都可以轻松编译 SWIG 接口，并且它们可以在任何平台上使用。同样，这是远离程序员的重要部分——任何开发人员最不想做的事情就是花时间调试依赖于不可移植或不可靠编程功能的工具的输出。依赖项通常是不兼容和问题的根源，因此在生成的代码中不使用额外的第三方库。SWIG 通常还会避免生成引入对 C++ 标准模板库 (STL) 的依赖的代码。SWIG 将生成依赖于 C 库的代码。


4 脚本语言
本章简要概述了脚本语言扩展编程以及脚本语言解释器访问 C 和 C++ 代码的机制。
4.1 两种语言的世界观
当使用脚本语言控制 C 程序时，生成的系统往往如下所示：

在该编程模型中，脚本语言解释器用于高级控制，而 C/C++ 程序的底层功能是通过特殊的脚本语言“命令”访问的。如果您曾经尝试编写自己的简单命令解释器，您可能会认为脚本语言方法是一种非常高级的实现。同样，如果您曾经使用过诸如 MATLAB 或 IDL 之类的包，那么它是一个非常相似的模型——解释器执行用户命令和脚本。但是，大部分底层功能都是用 C 或 Fortran 等低级语言编写的。
“同时使用两种语言”这一计算模型非常强大，因为它利用了每种语言的优势。C/C++ 可用于实现最好的性能，以及复杂的系统编程任务。脚本语言则可用于快速原型设计、交互式调试、脚本编写以及对高级数据结构（如关联数组）的访问。
4.2 脚本语言如何与 C 对话？
脚本语言围绕解析器构建，解析器知道如何执行命令和脚本。在这个解析器中，有一种执行命令和访问变量的机制。通常，这用于实现语言的内置功能。但是，通过扩展解释器，通常可以添加新的命令和变量。为此，大多数语言定义了一个特殊的 API 来添加新命令。此外，一个特殊的外部函数接口定义了这些新命令应该如何挂接到解释器中。
通常，当您向脚本解释器添加新命令时，您需要做两件事：
- 首先，您需要编写一个特殊的“包装器”函数，作为解释器和底层 C 函数之间的粘合剂。
- 然后，您需要通过提供有关函数名称、参数等的详细信息，为解释器提供有关包装器的信息。
接下来的几节说明了该过程。
4.2.1 包装函数
假设你有一个像这样的普通 C 函数：
int fact(int n) {
  if (n <= 1)
    return 1;
  else
    return n*fact(n-1);
}

为了从脚本语言访问这个函数，有必要编写一个特殊的“包装器”函数，作为脚本语言和底层 C 函数之间的粘合剂。包装函数必须做三件事：
- 收集函数参数并确保它们有效。
- 调用 C 函数。
- 将返回值转换为脚本语言可识别的形式。
例如， 上面示例中 fact() 函数的 Tcl 包装函数可能如下所示：
int wrap_fact(ClientData clientData, Tcl_Interp *interp, int argc, char *argv[]) {
  int result;
  int arg0;
  if (argc != 2) {
    interp->result = "wrong # args";
    return TCL_ERROR;
  }
  arg0 = atoi(argv[1]);
  result = fact(arg0);
  sprintf(interp->result, "%d", result);
  return TCL_OK;
}

创建包装函数后，最后一步是将新函数告知脚本语言。这通常在加载模块时语言调用的初始化函数中完成。例如，将上述函数添加到 Tcl 解释器需要如下代码：
int Wrap_Init(Tcl_Interp *interp) {
  Tcl_CreateCommand(interp, "fact", wrap_fact, (ClientData) NULL,
                    (Tcl_CmdDeleteProc *) NULL);
  return TCL_OK;
}

执行时，Tcl 现在将有一个名为“ fact ”的新命令，您可以像使用任何其他 Tcl 命令一样使用它。
尽管已经说明了向 Tcl 添加新函数的过程，但该过程对于 Perl 和 Python 几乎相同。两者都需要编写特殊的包装器，并且都需要额外的初始化代码。只是具体细节不同。
4.2.2 变量链接
变量链接是指在脚本语言解释器中将一个 C/C++ 全局变量映射到一个变量的问题。例如，假设您有以下变量：
double Foo = 3.5;

从脚本访问它可能会很好，如下所示（针对 Perl 显示）：
$a = $Foo * 2.3;   # 计算
$Foo = $a + 2.0;   # 赋值

为了提供这种访问，通常使用一对 get/set 函数来操作变量。例如，无论何时读取变量的值，都会调用 “get” 函数。类似地，每当变量的值发生变化时，都会调用 “set” 函数。
在许多语言中，对 get/set 函数的调用可以附加到求值和赋值运算符。因此，计算诸如 $Foo 之类的变量可能会隐式调用 get 函数。类似地，键入$Foo = 4将调用底层 set 函数来更改值。
4.2.3 常量
在许多情况下，C 程序或库可能定义大量常量。例如：
#define RED   0xff0000
#define BLUE  0x0000ff
#define GREEN 0x00ff00

为了使常量可用，它们的值可以存储在脚本语言变量中，例如 $RED、$BLUE 和 $GREEN。几乎所有脚本语言都提供用于创建变量的 C 函数，因此安装常量通常是一项微不足道的实践。
4.2.4 结构和类
尽管脚本语言可以轻松访问简单的函数和变量，但访问 C/C++ 结构和类会带来不同的问题。这是因为结构的实现，很大程度上与数据表示和布局问题有关。此外，某些语言特征很难映射到解释器。例如，C++ 继承在 Perl 接口中意味着什么？
处理结构最直接的技术是实现一组隐藏结构的底层表示的访问器函数。例如，
struct Vector {
  Vector();
  ~Vector();
  double x, y, z;
};

可以转化为以下一组函数：
Vector *new_Vector();
void delete_Vector(Vector *v);
double Vector_x_get(Vector *v);
double Vector_y_get(Vector *v);
double Vector_z_get(Vector *v);
void Vector_x_set(Vector *v, double x);
void Vector_y_set(Vector *v, double y);
void Vector_z_set(Vector *v, double z);

现在，在解释器中，这些函数可以按如下方式使用：
% set v [new_Vector]
% Vector_x_set $v 3.5
% Vector_y_get $v
% delete_Vector $v
% ...

由于访问器函数提供了一种访问对象内部结构的机制，解释器不需要了解有关 Vector 实际表示的任何信息。
4.2.5 代理类
在某些情况下，可以使用低级访问器函数来创建代理类，也称为影子类。代理类是一种特殊类型的对象，它以脚本语言创建，以类似于原始结构的方式访问 C/C++ 类（或结构）（即，它代理真正的 C++ 类）。例如，如果您有以下 C++ 定义：
class Vector {
public:
  Vector();
  ~Vector();
  double x, y, z;
};

代理分类机制将允许您以更自然的方式从解释器访问结构。例如，在 Python 中，您可能想要这样做：
>>> v = Vector()
>>> v.x = 3
>>> v.y = 4
>>> v.z = -13
>>> ...
>>> del v

同样，在 Perl5 中，您可能希望界面像这样工作：
$v = new Vector;
$v->{x} = 3;
$v->{y} = 4;
$v->{z} = -13;

最后，在 Tcl 中：
Vector v
v configure -x 3 -y 4 -z -13

当使用代理类时，实际上有两个对象在工作——一个是脚本语言，一个是底层 C/C++ 对象。操作同等地影响两个对象，并且出于所有实际目的，看起来好像您只是在操作 C/C++ 对象。
4.3 构建脚本语言扩展
在 C/C++ 应用程序中使用脚本语言的最后一步是，将扩展添加到脚本语言本身。有两种主要方法可以做到这一点。首选技术是以动态链接库（Shared Library）的形式构建可动态加载的扩展。或者，您可以重新编译脚本语言解释器并添加您的扩展（静态链接）。
4.3.1 共享库和动态加载
要创建动态链接库或 DLL，您通常需要查看编译器和链接器的手册页。但是，一些常见平台的过程如下所示：
# 为 Solaris 构建一个动态链接库
gcc -fpic -c example.c example_wrap.c -I/usr/local/include
ld -G example.o example_wrap.o -o example.so

# 构建 Linux 动态链接库
gcc -fpic -c example.c example_wrap.c -I/usr/local/include
gcc -shared example.o example_wrap.o -o example.so

要使用您的动态链接库，您只需使用脚本语言中的相应命令（load, import, use 等...）。这将导入您的模块并允许您开始使用它。例如：
% load ./example.so
% fact 4
24
%

使用 C++ 代码时，构建共享库的过程可能更复杂——主要是因为 C++ 模块可能需要额外的代码才能正确运行。在许多机器上，您可以按照上述过程构建共享 C++ 模块，但将链接行更改为以下内容：
c++ -shared example.o example_wrap.o -o example.so

4.3.2 链接动态库
将扩展构建为动态链接库时，您的扩展依赖于您机器上的其他动态链接库的情况并不少见。为了使扩展能够工作，它需要能够在运行时找到所有这些库。否则，您可能会收到如下错误：
>>> import graph
Traceback (innermost last):
  File "<stdin>", line 1, in ?
  File "/home/sci/data1/beazley/graph/graph.py", line 2, in ?
    import graphc
ImportError:  1101:/home/sci/data1/beazley/bin/python: rld: Fatal Error: cannot 
successfully map soname 'libgraph.so' under any of the filenames /usr/lib/libgraph.so:/
lib/libgraph.so:/lib/cmplrs/cc/libgraph.so:/usr/lib/cmplrs/cc/libgraph.so:
>>>

此错误意味着 SWIG 创建的扩展模块依赖于系统无法定位的名为“ libgraph.so ”的动态链接库。要解决此问题，您可以采取一些方法。
- 链接您的扩展并明确告诉链接器所需库的位置。通常，这可以通过一个特殊的链接器标志来完成，例如 -R、-rpath 等。这不是以标准方式实现的，因此请阅读链接器的手册页以了解有关如何设置搜索路径的更多信息共享库。
- 将共享库放在与可执行文件相同的目录中。有时需要这种技术才能在非 Unix 平台上正确操作。
- 在运行 Python 之前，将 UNIX 环境变量 LD_LIBRARY_PATH 设置为共享库所在的目录。虽然这是一个简单的解决方案，但不建议这样做。考虑使用链接器选项设置路径。
4.3.3 静态链接
使用静态链接，您可以使用扩展程序来重新生成脚本语言解释器。该过程通常包括，编译一个简短的主程序，将您的自定义命令添加到语言中并启动解释器。然后将您的程序与库链接，以生成新的脚本语言可执行文件。
尽管所有平台都支持静态链接，但这并不是构建脚本语言扩展的首选技术。事实上，这样做的实际原因很少——您应该考虑使用动态链接库。


5 SWIG 基础知识
本章介绍 SWIG 的基本操作、其输入文件的结构以及它如何处理标准 ANSI C 声明。C++ 支持将在下一章中描述。但是，C++ 程序员仍应阅读本章以了解基础知识。关于每种目标语言的具体细节在后面的章节中描述。
5.1 运行 SWIG
要运行 SWIG，请使用带有选项和文件名的swig命令，如下所示：
swig [options] filename

其中 filename 是 SWIG 接口文件或 C/C++ 头文件。以下是可以使用的 options 子集。还为每种目标语言定义了其他选项。可以通过键入 swig -help 或 swig -<lang> -help 获取语言 <lang> 特定选项的完整列表。
-allegrocl      - Generate ALLEGROCL wrappers
-chicken        - Generate CHICKEN wrappers
-clisp          - Generate CLISP wrappers
-cffi           - Generate CFFI wrappers
-csharp         - Generate C# wrappers
-d              - Generate D wrappers
-go             - Generate Go wrappers
-guile          - Generate Guile wrappers
-java           - Generate Java wrappers
-javascript     - Generate Javascript wrappers
-lua            - Generate Lua wrappers
-modula3        - Generate Modula 3 wrappers
-mzscheme       - Generate Mzscheme wrappers
-ocaml          - Generate Ocaml wrappers
-octave         - Generate Octave wrappers
-perl           - Generate Perl wrappers
-php            - Generate PHP wrappers
-pike           - Generate Pike wrappers
-python         - Generate Python wrappers
-r              - Generate R (aka GNU S) wrappers
-ruby           - Generate Ruby wrappers
-scilab         - Generate Scilab wrappers
-sexp           - Generate Lisp S-Expressions wrappers
-tcl            - Generate Tcl wrappers
-uffi           - Generate Common Lisp / UFFI wrappers
-xml            - Generate XML wrappers

5.1.1 输入格式
作为输入，SWIG 需要一个包含 ANSI C/C++ 声明和特殊 SWIG 指令的文件。通常，这是一个特殊的 SWIG 接口文件，通常用特殊的 .i 或 .swg 后缀表示。在某些情况下，SWIG 可以直接用于原始头文件或源文件。但是，这不是最典型的情况，您可能不想这样做的原因有多种（稍后描述）。
SWIG 接口最常见的格式如下：
%module mymodule 
%{
#include "myheader.h"
%}
// Now list ANSI C/C++ declarations
int foo;
int bar(int x);
...

模块名称是使用特殊的 %module 指令提供的。模块 部分进一步介绍了模块。
一切在 ％{...％} 块中的代码，被简单地逐字复制到由 SWIG 创建成品包装器的文件里。此部分几乎总是用于包含编译生成的包装器代码所需的头文件和其他声明。需要强调的是，如果您仅仅在 SWIG 文件中包含了一段声明，那么该声明不会自动出现在包装代码中——因此，你需要确保在 ％{.. .%} 部分，引用适当的头文件。应该注意的是，包含在 %{ ... %} 中的文本不会被 SWIG 解析或解释。在 SWIG 中， ％{...％} 的语法和语义，类似于解析器生成工具（例如 yacc 或 bison）的输入文件中使用的声明部分。
5.1.2 SWIG 输出
SWIG 的输出是一个 C/C++ 文件，其中包含构建扩展模块所需的所有包装器代码。SWIG 可能会根据目标语言生成一些额外的文件。默认情况下，名称为 file.i 的输入文件被转换为文件 file_wrap.c 或file_wrap.cxx（取决于是否使用了 -c++ 选项)。输出的 C/C++ 文件名可以用 -o 选项更改。有些时候，文件后缀被编译用来判断源文件的语言（C,C++ 等）。因此，你必须使用 -o 选项来改变 SWIG 生成的包装器文件的后缀，如果你想和默认设置不同的话。 例如：
$ swig -c++ -python -o example_wrap.cpp example.i

SWIG 创建的 C/C++ 输出文件，通常包含了为目标脚本语言构建扩展模块所需的一切。SWIG 不是存根编译器，通常也不需要编辑输出文件（如果您查看输出，您可能不想这样做）。为了构建最终的扩展模块，SWIG 输出文件被编译并与 C/C++ 程序的其余部分链接以创建一个动态链接库。
对于许多目标语言，SWIG 还将生成目标语言的代理类文件。这些特定于语言的文件的默认输出目录与生成的 C/C++ 文件的目录相同。这可以使用 -outdir 选项进行修改。例如：
$ swig -c++ -python -outdir pyfiles -o cppfiles/example_wrap.cpp example.i

如果目录 cppfiles 和 pyfiles 存在，则会生成以下内容：
cppfiles/example_wrap.cpp 
pyfiles/example.py

如果使用 -outcurrentdir 选项（不带 -o），则 SWIG 的行为类似于典型的 C/C++ 编译器，默认输出目录即为当前目录。如果没有此选项，默认输出目录是输入文件的路径。如果 -o 和-outcurrentdir一起使用，-outcurrentdir 将 被有效地忽略，因为如果没有被 -outdir 覆盖，语言文件的输出目录与生成的 C/C++ 文件的目录相同。
5.1.3 注释
C 和 C++ 风格的注释可以出现在界面文件中的任何地方。在以前版本的 SWIG 中，注释用于生成文档文件。但是，此功能目前正在修复中，将在以后的 SWIG 版本中重新出现。
5.1.4 C 预处理器
与 C 一样，SWIG 通过 C 预处理器的增强版本预处理所有输入文件。支持所有标准预处理器功能，包括文件包含、条件编译和宏。但是，除非提供了 -includeall 命令行选项，否则会忽略 #include 语句。禁用包含的原因是 SWIG 有时用于处理原始 C 头文件。在这种情况下，您通常只希望扩展模块在提供的头文件中包含函数，而不是该头文件中可能包含的所有内容（即，系统头文件、C 库函数等）。
还应该注意的是，SWIG 预处理器会跳过包含在%{...%}块内的所有文本。此外，预处理器包括许多宏处理增强功能，使其比普通 C 预处理器更强大。这些扩展在“预处理器”一章中描述。
5.1.5 SWIG 指令
SWIG 的大部分操作都由特殊指令控制，这些指令总是以“ % ”开头，以区别于普通的 C 声明。这些指令用于提供 SWIG 提示或以某种方式改变 SWIG 的解析行为。
由于 SWIG 指令不是合法的 C 语法，因此通常不可能将它们包含在头文件中。但是，可以使用条件编译将 SWIG 指令包含在 C 头文件中，如下所示：
/* header.h  --- Some header file */

/* SWIG directives -- only seen if SWIG is running */ 
#ifdef SWIG
%module foo
#endif

SWIG 宏是 SWIG 在解析输入文件时定义的特殊预处理符号。
5.1.6 解析器限制
尽管 SWIG 可以解析大多数 C/C++ 声明，但它不提供完整的 C/C++ 解析器实现。大多数这些限制与非常复杂的类型声明和某些高级 C++ 功能有关。具体来说，目前不支持以下功能：
- 非常规类型声明。例如，SWIG 不支持如下声明（即使这是合法的 C）：
/* Non-conventional placement of storage specifier (extern) */
const int extern Number;

/* Extra declarator grouping */
Matrix (foo);    // A global variable

/* Extra declarator grouping in parameters */
void bar(Spam (Grok)(Doh));

在实践中，很少（如果有）C 程序员真正编写这样的代码，因为这种风格从未在编程书籍中出现过。但是，如果您感到特别困惑，您当然可以 Hack SWIG（尽管您为什么要这样做？）。
- 不建议在 C++ 源文件（.C、.cpp 或 .cxx 文件中的代码）上运行 SWIG。通常的方法是提供 SWIG 头文件来解析 C++ 定义和声明。主要原因是，如果 SWIG 解析了作用域定义或声明（对于 C++ 源文件来说是正常的），它将被忽略，除非之前解析了符号的声明。例如
/* 除非 foo 已经被定义，并且在 foo 内部的 bar 的声明也已经被解析，
否则 bar 不会被包装 */
int foo::bar(int) {
    ... whatever ...
}

- 尚未完全支持 C++ 的某些高级功能，例如嵌套类。有关更多信息，请参阅 C++ 嵌套类 部分。
如果出现解析错误，可以使用条件编译来跳过违规代码。例如：
#ifndef SWIG 
... 一些错误的声明 ... 
#endif

或者，您可以从接口文件中删除有问题的代码。
SWIG 不提供完整的 C++ 解析器实现的原因之一是，它被设计用于处理不完整的规范，并且在处理 C/C++ 数据类型时非常宽松（例如，即使缺少接口、类声明或不透明数据类型，SWIG 也可以生成接口）。不幸的是，这种方法使得实现 C/C++ 解析器的某些部分变得极其困难，因为大多数编译器使用类型信息来帮助解析更复杂的声明（对于真正好奇的人来说，实现中的主要复杂性，是由于 SWIG 解析器不使用单独的 typedef-name 终端符号造成的，如 K&R 第 234 页所述）。
5.2 包装简单的 C 声明
SWIG 通过创建一个接口来包装简单的 C 声明，该接口与声明在 C 程序中的使用方式非常匹配。例如，考虑以下接口文件：
%module example

%inline %{ 
extern double sin(double x); 
extern int strcmp(const char *, const char *); 
extern int Foo; 
%} 
#define STATUS 50 
#define VERSION "1.1"

在这个文件中，有两个函数 sin() 和 strcmp()，一个全局变量 Foo，以及两个常量 STATUS 和VERSION。当 SWIG 创建扩展模块时，这些声明可以分别作为脚本语言函数、变量和常量进行访问。例如，在 Tcl 中：
% sin 3 
5.2335956 
% strcmp Dave Mike 
-1 
% puts $Foo 
42 
% puts $STATUS 
50 
% puts $VERSION 
1.1

或者在 Python 中：
>>> example.sin(3) 
5.2335956 
>>> example.strcmp('Dave', 'Mike') 
-1 
>>> print example.cvar.Foo 
42 
>>> print example.STATUS 
50 
>>> print example.VERSION 
1.1

只要有可能，SWIG 就会创建一个与底层 C/C++ 代码紧密匹配的接口。但是，由于语言、运行时环境和语义之间的细微差别，并非总是可以这样做。接下来的几节描述了这个映射的各个方面。
5.2.1 基本类型处理
为了构建接口，SWIG 必须将 C/C++ 数据类型转换为目标语言中的等效类型。通常，脚本语言提供的原始类型集比 C 更有限。因此，此转换过程涉及一定量的类型强制。
大多数脚本语言提供使用 C 中的 int 或 long 数据类型实现的单个整数类型。以下列表显示了 SWIG 将在目标语言中与整数相互转换的所有 C 数据类型：
int 
short 
long 
unsigned 
signed 
unsigned short 
unsigned long 
unsigned char 
signed char 
bool

当从 C 转换整数值时，会使用强制转换将其转换为目标语言的表示形式。因此，C 中的 16 位 short 可以提升为 32 位整数。当整数在另一个方向转换时，该值被转换回原始的 C 类型。如果该值太大而无法容纳，则会被静默截断。
unsigned char 和 signed char 是被作为小的 8 位整数处理的特例。
通常，char 数据类型被映射为一个单字符 ASCII 字符串。
bool 数据类型强制转换为 0 和 1 的整数值，除非目标语言提供了一种特殊的布尔类型。
处理大整数值时需要小心。大多数脚本语言使用 32 位整数，因此映射 64 位长整数可能会导致截断错误。32 位无符号整数（可能显示为大的负数）可能会出现类似的问题。根据经验，int 数据类型以及 char 和 short 数据类型的所有变体都可以安全使用。对于 unsigned int 和 long 数据类型，在使用 SWIG 包装后，您需要仔细检查程序的正确操作。
尽管 SWIG 解析器支持 long long 数据类型，但并非所有语言模块都支持它。这是因为 long long 通常超过目标语言中可用的整数精度。在某些模块（例如 Tcl 和 Perl5）中，long long 整数被编码为字符串。这允许表示这些数字的全部范围。但是，它不允许在算术表达式中使用 long long 值。还应该注意的是，虽然 long long 是 ISO C99 标准的一部分，但并非所有 C 编译器都普遍支持。在尝试将这种类型与 SWIG 一起使用之前，请确保您使用的是支持 long long 的编译器。
SWIG 识别以下浮点类型：
- float
- double
浮点数与目标语言中浮点数的自然表示相互映射。这几乎总是一个 C double。 SWIG 不支持很少使用的 long double 数据类型。
char 数据类型被映射到一个以 NULL 终止的单字符 ASCII 字符串。在脚本语言中使用时，它显示为一个包含字符值的小字符串。将值转换回 C 时，SWIG 从脚本语言中获取一个字符串，并将第一个字符去掉作为 char 值。因此，如果将值 “foo” 分配给 char 数据类型，它将获得值 “f”。
char* 数据类型会被作为 NULL 结尾的 ASCII 字符串处理。SWIG 将其映射到目标脚本语言中的 8 位字符串。SWIG 将目标语言中的字符串转换为以 NULL 结尾的字符串，然后再将它们传递给 C/C++。这些字符串的默认处理不允许它们嵌入 NULL 字节。因此，char* 数据类型一般不适合传递二进制数据。但是，可以通过定义 SWIG 类型映射来更改此行为。有关这方面的详细信息，请参阅 Typemaps 一章。
目前，SWIG 对 Unicode 和宽字符字符串（C wchar_t类型）提供了有限的支持。某些语言为 wchar_t 提供类型映射，但请记住，这些可能无法跨不同操作系统移植。这是一个微妙的话题，许多程序员对此知之甚少，并且没有以一致的方式跨语言实现。对于那些提供 Unicode 支持的脚本语言，Unicode 字符串通常以 8 位表示形式提供，例如可以映射到 char * 的 UTF-8 类型（在这种情况下 SWIG 接口可能会工作）。如果您包装的程序使用 Unicode，则无法保证目标语言中的 Unicode 字符将使用相同的内部表示（例如，UCS-2 与 UCS-4）。您可能需要编写一些特殊的转换函数。
5.2.2 全局变量
只要有可能，SWIG 就会将 C/C++ 全局变量映射到脚本语言变量中。例如，
%module example
dobule foo;

结果是这样的脚本语言变量：
# Tcl 
set foo [3.5] ;# 设置 foo 为 3.5 
puts $foo ;# 打印 foo 的值

# Python 
cvar.foo = 3.5 # 设置 foo 为 3.5 
print cvar.foo # 打印 foo 的值

# Perl 
$foo = 3.5 ; # 设置 foo 为 3.5 
print $foo, "\n"; # 打印 foo 的值

# Ruby 
Module.foo = 3.5 # 设置 foo 为 3.5 
print Module.foo, "\n" # 打印 foo 的值

每当使用脚本语言变量时，都会访问底层 C 全局变量。尽管 SWIG 尽一切努力使全局变量像脚本语言变量一样工作，但并不总是可行。例如，在 Python 中，必须通过称为 cvar （如上所示）的特殊变量对象访问所有全局变量。在 Ruby 中，变量作为模块的属性被访问。其他语言可能会将变量转换为一对访问器函数。例如，Java 模块生成一对用于操作值的函数 double get_foo() 和 set_foo(double val)。
最后，如果一个全局变量被声明为 const，它只支持只读访问。注意：此行为是 SWIG-1.3 的新行为。早期版本的 SWIG 错误地处理了const 并改为创建了常量。
5.2.3 常量
可以使用 #define、枚举或特殊的 %constant 指令创建常量。以下接口文件显示了一些有效的常量声明：
#define I_CONST 5 // 整数常量
#define PI 3.14159 // 浮点常量
#define S_CONST "hello world" // 字符串常量
#define NEWLINE '\n' // 字符常量

enum boolean {NO=0, YES =1}; 
enum month {JAN, FEB, MAR, APR, MAY, JUN, JUL, AUG, 
             SEP, OCT, NOV, DEC}; 
%constant double BLAH = 42.37; 
#define PI_4 PI/4 
#define FLAGS 0x04 | 0x08 | 0x40

在 #define 声明中，常量的类型由语法推断。例如，假设带有小数点的数字是浮点数。此外，SWIG 必须能够完全解析 #define 中使用的所有符号，以便实际创建常量。这个限制是必要的，因为 #define  还用于定义绝对不打算成为脚本语言接口一部分的预处理器宏。例如：
#define EXTERN extern 

EXTERN void foo();

在这种情况下，您可能不想创建一个名为 EXTERN 的常量（值是多少？）。通常，SWIG 不会为宏创建常量，除非该值可以完全由预处理器确定。例如，在上面的例子中，声明
#define PI_4 PI/4

定义一个常数，因为 PI 已经被定义为一个常数并且值是已知的。然而，出于同样的保守原因，即使是带有简单强制转换的常量也将被忽略，例如
#define F_CONST (double) 5 // 带强制转换的浮点常量

允许使用常量表达式，但 SWIG 不计算它们。相反，它将它们传递给输出文件，并让 C 编译器执行最终评估（但是，SWIG 确实执行了有限形式的类型检查）。
对于枚举，将原始枚举定义包含在接口文件中的某处（在头文件中或在 %{ %} 块中）至关重要。SWIG 仅将枚举转换为将常量添加到脚本语言所需的代码。它需要原始枚举声明才能获得 C 编译器分配的正确枚举值。
所述 ％constant 指令用于更精确地创建对应于不同的 C 数据类型常数。尽管对于简单值通常不需要它，但在使用指针和其他更复杂的数据类型时它更有用。通常，%constant 仅在您想要向脚本语言接口添加未在原始头文件中定义的常量时使用。
5.3 指针和复杂对象
大多数 C 程序操作数组、结构和其他类型的对象。本节讨论这些数据类型的处理。
5.3.1 简单指针
指向原始 C 数据类型的指针，例如
int *
double ***
char **

SWIG 完全支持。SWIG 没有尝试将指向的数据转换为脚本表示，而是简单地将指针本身编码为包含指针实际值和类型标签的表示。因此，上述指针的 SWIG 表示（在 Tcl 中）可能如下所示：
_10081012_p_int
_1008e124_ppp_double
_f8ac_pp_char

NULL 指针由字符串“NULL”或用类型信息编码的值 0 表示。
SWIG 将所有指针视为不透明对象。因此，指针可以由函数返回并根据需要传递给其他 C 函数。出于所有实际目的，脚本语言接口的工作方式与您在 C 程序中使用指针的方式完全相同。唯一的区别是没有解引用的机制，因为这需要目标语言了解底层对象的内存布局。
永远不应直接操作指针值的脚本语言表示。尽管显示的值看起来像十六进制地址，但使用的数字可能与实际机器地址不同（例如，在小端机器上，数字可能以相反的顺序出现）。此外，SWIG 通常不会将指针映射到高级对象，例如关联数组或列表（例如，将 int * 转换为整数列表）。SWIG 不这样做的原因有几个：
- C 声明中没有足够的信息来将指针正确映射到更高级别的构造中。例如，一个int * 可能确实是一个整数数组，但如果它包含一千万个元素，将其转换为列表对象可能是一个坏主意。
- SWIG 不知道与指针关联的底层语义。例如，一个int * 可能根本不是一个数组——也许它是一个输出值！
- 通过以一致的方式处理所有指针，SWIG 的实现被大大简化并且不易出错。
5.3.2 运行时指针类型检查
通过允许从脚本语言操作指针，扩展模块有效地绕过了 C/C++ 编译器中的编译时类型检查。为了防止错误，类型签名被编码到所有指针值中，并用于执行运行时类型检查。这种类型检查过程是 SWIG 的一个组成部分，如果不使用类型映射（在后面的章节中描述），就不能禁用或修改这个机制。
与 C 一样，void * 匹配任何类型的指针。此外，可以将 NULL 指针传递给任何希望接收指针的函数。尽管这有可能导致崩溃， 但有时也会将 NULL 指针用作标记值或表示缺失/空值。因此，SWIG 将 NULL 指针检查留给应用程序。
5.3.3 派生类型、结构和类
对于其他所有内容（结构、类、数组等），SWIG 应用了一个非常简单的规则：
其他一切都是一个指针 
换句话说，SWIG 通过引用来操作其他所有内容。这个模型是有道理的，因为大多数 C/C++ 程序大量使用指针，而 SWIG 可以使用已经存在的类型检查指针机制来处理指向基本数据类型的指针。
虽然这听起来可能很复杂，但实际上非常简单。假设你有一个这样的接口文件：
%module fileio
FILE *fopen(char *, char *);
int fclose(FILE *);
unsigned fread(void *ptr, unsigned size, unsigned nobj, FILE *);
unsigned fwrite(void *ptr, unsigned size, unsigned nobj, FILE *);
void *malloc(int nbytes);
void free(void *);

在这个文件中，SWIG 不知道 FILE 是什么，但由于它被用作指针，所以它是什么并不重要。如果将此模块包装到 Python 中，则可以像预期的那样使用这些函数：
# 复制一个文件 
def filecopy(source, target):
  f1 = fopen(source, "r")
  f2 = fopen(target, "w")
  buffer = malloc(8192)
  nbytes = fread(buffer, 8192, 1, f1)
  while (nbytes > 0):
    fwrite(buffer, 8192, 1, f2)
          nbytes = fread(buffer, 8192, 1, f1)
  free(buffer)

在这种情况下，f1、f2 和 buffer 都是包含 C 指针的不透明对象。它们包含什么值并不重要——我们的程序在没有这些知识的情况下也能正常工作。
5.3.4 未定义的数据类型
当 SWIG 遇到未声明的数据类型时，它会自动假定它是一个结构或类。例如，假设以下函数出现在 SWIG 输入文件中：
void matrix_multiply(Matrix *a, Matrix *b, Matrix *c);

SWIG 不知道“Matrix”是什么。然而，它显然是一个指向某物的指针，因此 SWIG 使用其通用指针处理代码生成一个包装器。
与 C 或 C++ 不同，SWIG 实际上并不关心 Matrix 先前是否已在接口文件中定义。这允许 SWIG 仅根据部分或有限的信息生成接口。在某些情况下，您可能并不关心 Matrix 到底是什么，只要您可以在脚本语言界面中向周围传递一个不透明的引用。
需要提及的一个重要细节是，当存在未指定的类型名称时，SWIG 会很乐意为接口生成包装器。但是，所有未指定的类型在内部都被处理为指向结构或类的指针！例如，考虑以下声明：
void foo(size_t num);

如果未声明 size_t，SWIG 会生成包装器，期望接收一种 size_t *（此映射将在稍后介绍）。因此，脚本界面的行为可能会很奇怪。例如：
foo(40);
TypeError: expected a _p_size_t.

解决此问题的唯一方法是确保使用 typedef 正确声明类型名称。
5.3.5 类型定义
与 C 一样，typedef 可用于在 SWIG 中定义新的类型名称。例如：
typedef unsigned int size_t;

出现在 SWIG 接口中的 typedef 定义不会传播到生成的包装器代码。因此，它们要么需要在包含的头文件中定义，要么像这样放置在声明部分中：
%{
/* 包含在生成的包装文件中 */
typedef unsigned int size_t;
%}
/* 告诉 SWIG 这件事 */
typedef unsigned int size_t;

或者
%inline %{
typedef unsigned int size_t;
%}

在某些情况下，您可能能够包含其他头文件来收集类型信息。例如：
%module example
%import "sys/types.h"

在这种情况下，您可以按如下方式运行 SWIG：
$ swig -I/usr/include -includeall example.i

需要注意的是，您的成本在这里会有很大差异。众所周知，系统头文件非常复杂，并且可能依赖于各种非标准的 C 编码扩展（例如 GCC 的特殊指令）。除非您准确指定正确的包含目录和预处理器符号，否则这可能无法正常工作（您必须进行试验）。
SWIG 跟踪 typedef 声明并使用此信息进行运行时类型检查。例如，如果您使用上述 typedef 并具有以下函数声明：
void foo(unsigned int *ptr);

相应的包装函数将接受 unsigned int * 或 size_t * 类型的参数。
5.4 其他实用性
到目前为止，本章介绍了将 SWIG 用于简单接口所需了解的几乎所有内容。但是，某些 C 程序使用的习惯用法更难映射到脚本语言接口。本节描述了其中一些问题。
5.4.1 按值传递结构
有时，C 函数采用按值传递的结构参数。例如，考虑以下函数：
double dot_product(Vector a, Vector b);

为了解决这个问题，SWIG 通过创建一个等效于以下内容的包装器将函数转换为使用指针：
double wrap_dot_product(Vector *a, Vector *b) {
    Vector x = *a;
    Vector y = *b;
    return dot_product(x, y);
}

在目标语言中，dot_product() 函数现在接受指向 Vectors 而不是 Vectors 的指针。大多数情况下，这种转换是透明的，因此您可能不会注意到。
5.4.2 按值返回
按值返回结构或类数据类型的 C 函数更难处理。考虑以下函数：
Vector cross_product(Vector v1, Vector v2);

这个函数想要返回Vector，但 SWIG 只真正支持指针。结果，SWIG 创建了一个这样的包装器：
Vector *wrap_cross_product(Vector *v1, Vector *v2) {
        Vector x = *v1;
        Vector y = *v2;
        Vector *result;
        result = (Vector *) malloc(sizeof(Vector));
        *(result) = cross(x, y);
        return result;
}

或者如果 SWIG 是使用-c++选项运行的：
Vector *wrap_cross(Vector *v1, Vector *v2) {
        Vector x = *v1;
        Vector y = *v2;
        Vector *result = new Vector(cross(x, y)); // Uses default copy constructor
        return result;
}

在这两种情况下，SWIG 都会分配一个新对象并返回对它的引用。当不再使用返回的对象时，由用户决定是否将其删除。显然，如果您不知道隐式内存分配并且不采取措施释放结果，这将泄漏内存。也就是说，应该注意的是，某些语言模块现在可以自动跟踪新创建的对象并为您回收内存。有关更多详细信息，请参阅每个语言模块的文档。
还应该注意的是，C++ 中按值传递/返回的处理有一些特殊情况。例如，如果 Vector 未定义默认构造函数，则上述代码片段无法正常工作。SWIG 和 C++ 部分提供了有关此案例的更多信息。
5.4.3 链接到结构变量
当遇到涉及结构的全局变量或类成员时，SWIG 将它们作为指针处理。例如像这样的全局变量
Vector unit_i;

被映射到一对底层的 set/get 函数，如下所示：
Vector *unit_i_get() {
  return &unit_i;
}
void unit_i_set(Vector *value) {
  unit_i = *value;
}

再次需要谨慎。以这种方式创建的全局变量将在目标脚本语言中显示为指针。释放或销毁这样的指针将是一个非常糟糕的主意。此外，C++ 类必须提供正确定义的复制构造函数，以便赋值正常工作。
5.4.4 链接到 char *
当出现 char * 类型的全局变量时，SWIG 使用 malloc() 或 new 为新值分配内存。具体来说，如果你有一个这样的变量
char *foo;

SWIG 生成以下代码：
/* C mode */
void foo_set(char *value) {
  if (foo) free(foo);
  foo = (char *) malloc(strlen(value)+1);
  strcpy(foo, value);
}

/* C++ mode.  When -c++ option is used */
void foo_set(char *value) {
  if (foo) delete [] foo;
  foo = new char[strlen(value)+1];
  strcpy(foo, value);
}

如果这不是您想要的行为，请考虑使用 %immutable 指令将变量设为只读。或者，您可以编写一个简短的辅助函数来完全按照您的需要设置值。例如：
%inline %{
  void set_foo(char *value) {
    strncpy(foo, value, 50);
  }
%}

注意：如果您编写这样的辅助函数，则必须将其作为目标脚本语言的函数调用（它不像变量那样工作）。例如，在 Python 中，您必须编写：
>>> set_foo("Hello World")

char * 变量的一个常见错误是链接到这样声明的变量：
char *VERSION = "1.0";

在这种情况下，变量将是可读的，但任何更改值的尝试都会导致分段或一般保护错误。这是因为当当前分配给变量的字符串文字值未使用 malloc() 或 new 分配时，SWIG 试图使用 free 或 delete 释放旧值。要修复此行为，您可以将变量标记为只读，编写一个类型映射（如第 6 章所述），或编写一个特殊的 set 函数，如图所示。另一种选择是将变量声明为数组：
char VERSION[64] = "1.0";

当声明类型为 const char * 的变量时，SWIG 仍会生成用于设置和获取值的函数。但是，默认行为并没有释放以前的内容（从而可能导致内存泄漏）。实际上，在包装此类变量时，您可能会收到如下警告消息：
example.i:20. Typemap warning. Setting const char * variable may leak memory

这种行为的原因是 const char * 变量通常用于指向字符串文字。例如：
const char *foo = "Hello World\n";

因此，在这样的指针上调用 free() 是一个非常糟糕的主意。另一方面，将指针更改为指向某个其他值是合法的。在设置这种类型的变量时，SWIG 会分配一个新字符串（使用 malloc 或 new）并将指针更改为指向新值。但是，重复修改值会导致内存泄漏，因为旧值没有被释放。
5.4.5 数组
SWIG 完全支持数组，但它们始终作为指针处理，而不是将它们映射到目标语言中的特殊数组对象或列表。因此，以下声明：
int foobar(int a[40]);
void grok(char *argv[]);
void transpose(double a[20][20]);

被处理，就好像它们真的是这样声明的：
int foobar(int *a);
void grok(char **argv);
void transpose(double (*a)[20]);

与 C 一样，SWIG 不执行数组边界检查。由用户确保指针指向适当分配的内存区域。
多维数组被转换为指向少一维数组的指针。例如：
int [10]; // 映射到 int *
int [10][20]; // 映射到 int (*)[20]
int [10][20][30]; // 映射到 int (*)[20][30]

这是需要注意的重要事项是，在C型系统中，多维数组一个[] [] 是 不等价于单个指针 *a 或一个双指针如 **a。相反，使用指向数组的指针（如上所示），其中指针的实际值是数组的起始内存位置。强烈建议读者在将它们与 SWIG 一起使用之前，先从 C 教科书上掸掉灰尘并重新阅读有关数组的部分。
支持数组变量，但默认为只读。例如：
int a[100][200];

在这种情况下，读取变量 'a' 会返回一个 int (*)[200] 类型的指针，该指针指向数组 &a[0][0] 的第一个元素。尝试修改“a”会导致错误。这是因为 SWIG 不知道如何将数据从目标语言复制到数组中。要解决此限制，您可能需要编写一些简单的辅助函数，如下所示：
%inline %{
void a_set(int i, int j, int val) {
  a[i][j] = val;
}
int a_get(int i, int j) {
  return a[i][j];
}
%}

要动态创建各种大小和形状的数组，在界面中编写一些辅助函数可能会很有用。例如：
// Some array helpers
%inline %{
  /* Create any sort of [size] array */
  int *int_array(int size) {
    return (int *) malloc(size*sizeof(int));
  }
  /* Create a two-dimension array [size][10] */
  int (*int_array_10(int size))[10] {
    return (int (*)[10]) malloc(size*10*sizeof(int));
  }
%}

字符数组由 SWIG 作为特殊情况处理。在这种情况下，目标语言中的字符串可以存储在数组中。例如，如果您有这样的声明，
char pathname[256];

SWIG 生成用于获取和设置值的函数，这些函数等效于以下代码：
char *pathname_get() {
  return pathname;
}
void pathname_set(char *value) {
  strncpy(pathname, value, 256);
}

在目标语言中，该值可以像普通变量一样设置。
5.4.6 创建只读变量
可以使用 %immutable 指令创建只读变量，如下所示：
// File : interface.i

int a;       // Can read/write
%immutable;
int b, c, d;   // Read only variables
%mutable;
double x, y;  // read/write

%immutable 指令会启动只读模式，直到使用 %mutable 指令明确禁用。作为像这样关闭和打开只读模式的替代方法，还可以将单个声明标记为不可变的。例如：
%immutable x;                   // Make x read-only
...
double x;                       // Read-only (from earlier %immutable directive)
double y;                       // Read-write
...

 %mutable 和 %immutable 指令实际上是通过 ％feature 指令 这样定义的：
#define %immutable %feature("immutable")
#define %mutable %feature("immutable", "")

如果除了一两个变量以外，您想让所有包装的变量都只读，采用这种方法可能更容易：
%immutable;                     // Make all variables read-only
%feature("immutable", "0") x;   // except, make x read/write
...
double x;
double y;
double z;
...

当声明被声明为const时，也会创建只读变量。例如：
const int foo; /* 只读变量 */
char * const version="1.0"; /* 只读变量 */

兼容性说明：只读访问常常通过一对指令 %readonly 和 %readwrite 控制。尽管这些指令仍然有效，但它们会生成警告消息。只需将指令更改为 %immutable; 和 %mutable; 就可以使警告静音。不要忘记额外的分号！
5.4.7 重命名和忽略声明
5.4.7.1 特定标识符的简单重命名
通常，C 声明的名称在该声明被包装到目标语言中时使用。但是，这可能会与脚本语言中的关键字或现有函数产生冲突。要解决名称冲突，您可以使用 %rename 指令，如下所示：
// interface.i

%rename(my_print) print;
extern void print(const char *);

%rename(foo) a_really_long_and_annoying_name;
extern int a_really_long_and_annoying_name;

SWIG 仍然调用正确的 C 函数，但在这种情况下，函数 print() 在目标语言中将实际被称为“my_print()”。
%rename 指令的位置是任意的，只要它出现在要重命名的声明之前。一种常见的技术是编写用于包装头文件的代码，如下所示：
// interface.i

%rename(my_print) print;
%rename(foo) a_really_long_and_annoying_name;

%include "header.h"

%rename 将重命名操作应用于所有以后出现的名称。重命名适用于函数、变量、类和结构名称、成员函数和成员数据。例如，如果您有两个 C++ 类，所有类都有一个名为“print”（这是 Python 中的关键字）的成员函数，您可以通过指定将它们全部重命名为“ouput”：
%rename(output) print; // Rename all `print' functions to `output'

SWIG 通常不会执行任何检查以查看它包装的函数是否已在目标脚本语言中定义。但是，如果您对命名空间和模块的使用很小心，通常可以避免这些问题。
与 %rename 密切相关的是 %ignore 指令。%ignore 指示 SWIG 忽略与给定标识符匹配的声明。例如：
%ignore print;         // Ignore all declarations named print
%ignore MYMACRO;       // Ignore a macro
...
#define MYMACRO 123
void print(const char *);
...

任何与 %ignore 匹配的函数、变量等都不会被包装，因此在目标语言中将不可用。%ignore 的一个常见用法是，有选择地从头文件中删除某些声明，而不必向头文件添加条件编译。但是，应该强调的是，这仅适用于简单的声明。如果您需要删除一整段有问题的代码，则应改用 SWIG 预处理器。
兼容性说明：旧版本的 SWIG为重命名声明提供了一个特殊的 %name 指令。例如：
%name(output) extern void print(const char *);

该指令仍受支持，但已弃用，应该避免使用。在 ％rename 指令是功能更强大，更好的支持包装器的原始头文件信息。
5.4.7.2 高级重命名支持
虽然为特定声明编写 %rename 很简单，但有时需要将相同的重命名规则应用于 SWIG 输入中的许多（也许是所有）标识符。例如，可能需要对目标语言中的所有名称进行一些转换以更好地遵循其命名约定，例如为所有包装函数添加特定前缀。为每个函数单独执行此操作是不切实际的，因此如果未指定要重命名的标识符的名称，则 SWIG 支持将重命名规则应用于所有声明：
%rename("myprefix_%s") ""; // print -> myprefix_print

这也表明 %rename 的参数不必是文字字符串，而是可以是类似 printf() 的格式字符串。在最简单的形式中，“%s” 被替换为原始声明的名称，如上所示。然而，这并不总是足够的，并且 SWIG 提供了对常用格式字符串语法的扩展，以允许将（SWIG 定义的）函数应用于参数。例如，要将所有 C 函数do_something_long() 包装为更像 Java 的 doSomethingLong()，您可以使用 “小写字母”扩展格式说明符，如下所示：
%rename("%(lowercamelcase)s") ""; // foo_bar -> fooBar; FooBar -> fooBar

一些函数可以被参数化，例如 “strip” 从它的参数中去除提供的前缀。前缀被指定为格式字符串的一部分，在函数名称后跟一个冒号：
%rename("%(strip:[wx])s") ""; // wxHello -> Hello; FooBar -> FooBar

下表总结了当前定义的所有函数，并附有应用每个函数的示例。请注意，其中一些有两个名称，一个更短，一个更具描述性，但是这两个函数在其他方面是等效的：
无法复制加载中的内容
上述所有的命令（不包括的最普遍的功能 command 这个，在原则上更强大，但一般应避免使用，因为性能方面的考虑）都是 regex 命令。以下是其使用的更多示例：
// Strip the wx prefix from all identifiers except those starting with wxEVT
%rename("%(regex:/wx(?!EVT)(.*)/\\1/)s") ""; // wxSomeWidget -> SomeWidget
                                             // wxEVT_PAINT -> wxEVT_PAINT

// Apply a rule for renaming the enum elements to avoid the common prefixes
// which are redundant in C#/Java
%rename("%(regex:/^([A-Z][a-z]+)+_(.*)/\\2/)s", %$isenumitem) ""; // Colour_Red -> Red

// Remove all "Set/Get" prefixes.
%rename("%(regex:/^(Set|Get)(.*)/\\2/)s") ""; // SetValue -> Value
                                              // GetValue -> Value

和以前一样，上面关于 %rename 的所有内容 也适用于 %ignore。实际上，后者只是前者的一个特例，忽略标识符与将其重命名为特殊的 “$ignore” 值相同。所以下面的片段
%ignore print;

和
%rename("$ignore") print;

完全等效，并且 %rename 可用于使用前面描述的匹配可能性，选择性地忽略多个声明。
5.4.7.3 限制全局重命名规则
如前几节所述，可以重命名单个声明或一次将重命名规则应用于所有声明。然而，在实践中，后者很少适用，因为一般规则总是有一些例外。为了处理它们，可以使用后续匹配参数来限制未命名的 %rename 的范围。它们可以应用于 SWIG 与其输入中出现的声明关联的任何属性。例如：
%rename("foo", match$name="bar") "";

可以用来达到和更简单的一样的效果
%rename("foo") bar;

所以它本身并不是很有趣。但是 match 也可以应用于声明类型，例如 match="class" 将匹配限制为仅类声明有效（在 C++ 中），而 match="enumitem" 将其限制为枚举元素。SWIG 还为此类匹配表达式提供了方便的宏，例如
%rename("%(title)s", %$isenumitem) "";

将大写所有枚举元素的名称，但不会改变其他声明的大小写。类似地，可以使用 %$isclass、 %$isfunction、%$isconstructor、%$isunion、 %$istemplate 和 %$isvariable。许多其他检查是可能的，本文档并不详尽，请参阅 swig.swg 中的 “%rename predicates” 部分以获取支持的匹配表达式的完整列表。
除了字面上匹配某些字符串与 match 之外， 您还可以使用 regexmatch 或 notregexmatch 将字符串与正则表达式匹配。例如，要忽略所有以“Old”作为后缀的函数，您可以使用
%rename("$ignore", regexmatch$name="Old$") "";

对于像这样的简单情况，直接为声明名称指定正则表达式可能更可取，也可以使用 regextarget 完成：
%rename("$ignore", regextarget=1) "Old$";

请注意，检查仅针对声明本身的名称进行，如果您需要匹配 C++ 声明的全名，则必须使用 fullname 属性：
%rename("$ignore", regextarget=1, fullname=1) "NameSpace::ClassName::.*Old$";

至于 notregexmatch，它只匹配不匹配指定正则表达式的字符串。因此，要将所有声明重命名为小写，但仅由大写字母组成的声明除外：
%rename("$(lower)s", notregexmatch$name="^[AZ]+$") "";

最后，%rename 和 %ignore 指令的变体可用于帮助包装 C++ 重载函数和方法，或使用默认参数的 C++ 方法。这在 C++ 章节的歧义解决和重命名部分中进行了描述。
5.4.7.4 忽略所有内容然后包装一些选定的符号
使用上述技术，可以忽略标头中的所有内容，然后有选择地包装一些选定的方法或类。例如，考虑一个头文件 myheader.h，它有很多类，而这个头文件中只需要一个名为 Star 的类，可以采用以下方法：
%ignore ""; // Ignore everything

// Unignore chosen class 'Star'
%rename("%s") Star;

// As the ignore everything will include the constructor, destructor, methods etc
// in the class, these have to be explicitly unignored too:
%rename("%s") Star::Star;
%rename("%s") Star::~Star;
%rename("%s") Star::shine; // named method

%include "myheader.h"

另一种方法可能更合适，因为它不需要命名所选类中的所有方法，即首先忽略这些类。这不会向类的任何成员添加显式忽略，因此当所选类被忽略时，其所有方法都将被包装。
%rename($ignore, %$isclass) ""; // Only ignore all classes
%rename("%s") Star; // Unignore 'Star'
%include "myheader.h"

5.4.8 默认/可选参数
SWIG 支持 C 和 C++ 代码中的默认参数。例如：
int plot(double x, double y, int color=WHITE);

在这种情况下，SWIG 生成包装器代码，其中默认参数在目标语言中是可选的。例如，这个函数可以在 Tcl 中使用，如下所示：
% plot -3.4 7.5 # 使用默认值
% plot -3.4 7.5 10 # 将颜色设置为 10

尽管 ANSI C 标准不允许默认参数，但 SWIG 接口中指定的默认参数适用于 C 和 C++。
注意：关于默认参数的使用和 SWIG 生成的包装器代码存在一个微妙的语义问题。当在 C 代码中使用默认参数时，默认值被发送到包装器中，并使用完整的参数集调用该函数。这与为每个默认参数生成重载包装方法的 C++ 包装不同。有关更多详细信息，请参阅C++ 章节中有关默认参数的部分。
5.4.9 指向函数和回调的指针
有时，C 库可能包含期望接收函数指针的函数——可能用作回调函数。SWIG 提供对函数指针的完全支持，前提是回调函数是用 C 而不是目标语言定义的。例如，考虑这样的函数：
int binary_op(int a, int b, int (*op)(int, int));

当您第一次将这样的东西包装到扩展模块中时，您可能会发现该功能无法使用。例如，在 Python 中：
>>> def add(x, y):
...     return x+y
...
>>> binary_op(3, 4, add)
Traceback (most recent call last):
  File "<stdin>", line 1, in ?
TypeError: Type error. Expected _p_f_int_int__int
>>>

此错误的原因是 SWIG 不知道如何将脚本语言函数映射到 C 回调。但是，现有的 C 函数可以用作参数，前提是您将它们安装为常量。一种方法是使用 %constant 指令，如下所示：
/* 带回调函数 */
int binary_op(int a, int b, int (*op)(int, int));

/* 一些回调函数 */
%constant int add(int, int);
%constant int sub(int, int);
%constant int mul(int, int);

在这种情况下，add、sub 和 mul 成为目标脚本语言中的函数指针常量。这允许您按如下方式使用它们：
>>> binary_op(3, 4, add)
7
>>> binary_op(3, 4, mul)
12
>>>

不幸的是，通过将回调函数声明为常量，它们不再能作为函数访问。例如：
>>> add(3, 4)
Traceback (most recent call last):
  File "<stdin>", line 1, in ?
TypeError: object is not callable: '_ff020efc_p_f_int_int__int'
>>>

如果你想让一个函数同时作为回调函数和函数使用，你可以使用 %callback 和 %nocallback 指令，如下所示：
/* 带回调函数 */
int binary_op(int a, int b, int (*op)(int, int));

/* 一些回调函数 */
%callback("%s_cb");
int add(int, int);
int sub(int, int);
int mul(int, int);
%nocallback;

%callback 的参数是一个 printf 风格的格式字符串，它指定回调常量的命名约定（%s 被函数名替换）。回调模式一直有效，直到使用 %nocallback 明确禁用它。当你这样做时，界面现在工作如下：
>>> binary_op(3, 4, add_cb)
7
>>> binary_op(3, 4, mul_cb)
12
>>> add(3, 4)
7
>>> mul(3, 4)
12

请注意，当该函数用作回调时，会使用诸如 add_cb 之类的特殊名称。要正常调用函数，只需使用原始函数名称，例如 add()。
SWIG 为标准 C printf 格式提供了许多扩展，这些扩展在此上下文中可能很有用。例如，以下变体将回调安装为所有大写常量，例如 ADD、SUB 和 MUL：
/* Some callback functions */
%callback("%(uppercase)s");
int add(int, int);
int sub(int, int);
int mul(int, int);
%nocallback;

"%(lowercase)s" 格式字符串将所有字符转换为小写。一串 "%(title)s" 将第一个字符大写，并将其余字符转换为小写。
现在，关于函数指针支持的最后说明。尽管 SWIG 通常不允许用目标语言编写回调函数，但这可以通过使用类型映射和其他高级 SWIG 功能来实现。有关 类型 映射和单个目标语言章节的更多信息，请参阅类型 映射章节，了解有关回调和“导演”功能的更多信息。
5.5 结构体和联合体
本节介绍 SWIG 在处理 ANSI C 结构体和联合体声明时的行为。处理 C++ 的扩展将在下一节中描述。
如果 SWIG 遇到结构体或联合体的定义，它会创建一组访问器函数。尽管 SWIG 不需要结构体定义来构建接口，但提供定义使得访问结构体成员成为可能。SWIG 生成的访问器函数只是获取一个指向对象的指针并允许访问单个成员。例如，声明：
struct Vector {
  double x, y, z;
}

被转换为以下一组访问器函数：
double Vector_x_get(struct Vector *obj) {
  return obj->x;
}
double Vector_y_get(struct Vector *obj) { 
  return obj->y;
}
double Vector_z_get(struct Vector *obj) { 
  return obj->z;
}
void Vector_x_set(struct Vector *obj, double value) {
  obj->x = value;
}
void Vector_y_set(struct Vector *obj, double value) {
  obj->y = value;
}
void Vector_z_set(struct Vector *obj, double value) {
  obj->z = value;
}

此外，如果接口中没有定义任何构造函数和析构函数，SWIG 会创建默认的构造函数和析构函数。例如：
struct Vector *new_Vector() {
    return (Vector *) calloc(1, sizeof(struct Vector));
}
void delete_Vector(struct Vector *obj) {
    free(obj);
}

使用这些低级访问器函数，可以使用如下代码从目标语言最少操作对象：
v = new_Vector()
Vector_x_set(v, 2)
Vector_y_set(v, 10)
Vector_z_set(v, -5)
...
delete_Vector(v)

但是，SWIG 的大部分语言模块也提供了更方便的高级接口。继续阅读。
5.5.1 typedef 和结构体
SWIG 支持以下在 C 程序中很常见的结构体：
typedef struct {
  double x, y, z;
} Vector;

遇到时，SWIG 假定对象的名称是“Vector”，并像以前一样创建访问器函数。唯一的区别是使用typedef 允许 SWIG 在其生成的代码中删除 struct 关键字。例如：
double Vector_x_get(Vector *obj) {
  return obj->x;
}

如果像这样使用两个不同的名称：
typedef struct vector_struct {
  double x, y, z;
} Vector;

名称 Vector 会被使用，而不是 vector_struct，因为这是更典型的 C 编程风格。如果稍后在接口中定义的声明，使用类型 struct vector_struct，则 SWIG 知道这与 Vector 相同，并生成适当的类型检查代码。
5.5.2 字符串和结构
涉及字符串的结构需要小心。SWIG 假定 char * 类型的所有成员都已使用 malloc() 动态分配，并且它们是以 NULL 结尾的 ASCII 字符串。当这样的成员被修改时，之前的内容将被释放，并分配新的内容。例如 ：
%module mymodule
...
struct Foo {
  char *name;
  ...
}

这导致以下访问器功能：
char *Foo_name_get(Foo *obj) {
  return Foo->name;
}

char *Foo_name_set(Foo *obj, char *c) {
  if (obj->name)
    free(obj->name);
  obj->name = (char *) malloc(strlen(c)+1);
  strcpy(obj->name, c);
  return obj->name;
}

如果此行为与您在应用程序中所需的行为不同，则可以使用 SWIG“memberin”类型映射来更改它。有关更多详细信息，请参阅“类型映射”一章。
注意：如果使用 -c++ 选项，则使用 new 和 delete 进行内存分配。
5.5.3 数组成员
数组可以作为结构体的成员出现，但它们是只读的。SWIG 将编写一个访问器函数，该函数返回指向数组第一个元素的指针，但不会编写一个函数来更改数组本身的内容。当检测到这种情况时，SWIG 可能会生成如下警告消息：
interface.i:116. Warning. Array member will be read-only

为了消除警告消息，可以使用类型映射，但这将在后面的章节中讨论。在许多情况下，警告消息是无害的。
5.5.4 结构数据成员
有时，结构体会包含本身就是结构体的数据成员。例如：
typedef struct Foo {
  int x;
} Foo;

typedef struct Bar {
  int y;
  Foo f;           /* struct member */
} Bar;

当一个结构成员被包装时，它被作为一个指针处理，除非使用 %naturalvar 指令在它更像一个 C++ 引用的地方使用（参见 C++ 成员数据）。作为指针的成员变量的访问器被有效地包装如下：
Foo *Bar_f_get(Bar *b) {
  return &b->f;
}
void Bar_f_set(Bar *b, Foo *value) {
  b->f = *value;
}

其原因有些微妙，但与修改和访问数据成员内部数据的问题有关。例如，假设你想修改一个 Bar 对象的 f.x 的值是这样的：
Bar *b;
b->f.x = 37;

将此赋值转换为函数调用（将在脚本语言界面中使用）产生以下代码：
Bar *b;
Foo_x_set(Bar_f_get(b), 37);

在该代码中，如果 Bar_f_get() 函数是返回一个 Foo 代替 Foo *，然后将所得的修改将被应用到一个复制的 f，而不是数据成员 f 本身。显然这不是你想要的！
应该注意的是，这种对指针的转换仅在 SWIG 知道数据成员是结构或类时才会发生。例如，如果你有这样的结构，
struct Foo {
  WORD   w;
};

并且对 WORD 一无所知，那么 SWIG 将生成更正常的访问器函数，如下所示：
WORD Foo_w_get(Foo *f) {
    return f->w;
}
void Foo_w_set(FOO *f, WORD value) {
    f->w = value;
}

兼容性说明： SWIG-1.3.11 和更早版本将所有非原始成员数据类型转换为指针。从 SWIG-1.3.12 开始，只有在已知数据类型是结构、类或联合时才会发生这种转换。这不太可能破坏现有代码。但是，如果您需要告诉 SWIG 未声明的数据类型实际上是一个结构体，只需使用前向结构体声明，例如“struct Foo;”。
5.5.5 C 构造函数和析构函数
在包装结构时，拥有用于创建和销毁对象的机制通常很有用。如果您什么都不做，SWIG 将使用malloc() 和 free() 自动生成用于创建和销毁对象的函数。注意： malloc() 的使用仅适用于在 C 代码上使用 SWIG 时（即，当命令行上未提供 -c++选项时）。C++ 的处理方式不同。
如果您不希望 SWIG 为您的接口生成默认构造函数，您可以使用%nodefaultctor指令或 -nodefaultctor 命令行选项。例如：
swig -nodefaultctor example.i 

要么
%module foo
...
%nodefaultctor;        // Don't create default constructors
... declarations ...
%clearnodefaultctor;   // Re-enable default constructors

如果您需要更精确的控制，%nodefaultcto r可以有选择地针对单个结构定义。例如：
%nodefaultctor Foo;      // No default constructor for Foo
...
struct Foo {             // No default constructor generated.
};

struct Bar {             // Default constructor generated.
};

由于在大多数情况下忽略隐式或默认析构函数会产生内存泄漏，因此 SWIG 将始终尝试生成它们。但是，如果需要，您可以使用 %nodefaultdtor 有选择地禁用默认/隐式析构函数的生成
%nodefaultdtor Foo; // No default/implicit destructor for Foo
...
struct Foo {              // No default destructor is generated.
};

struct Bar {              // Default destructor generated.
};

兼容性说明：在 SWIG-1.3.7 之前，SWIG 不会生成默认构造函数或析构函数，除非您使用-make_default 显式打开它们。但是，似乎大多数用户都希望拥有构造函数和析构函数，因此现在已将其作为默认行为启用。
注意：还有 -nodefault 选项和 %nodefault 指令，它们禁用默认或隐式析构函数生成。这可能会导致跨目标语言的内存泄漏，强烈建议您不要使用它们。
5.5.6 向 C 结构体中添加成员函数
大多数语言都提供了创建类和支持面向对象编程的机制。从 C 的角度来看，面向对象编程实际上只是归结为将函数附加到结构的过程。这些函数通常对结构（或对象）的实例进行操作。尽管 C++ 自然映射到这种方案，但没有直接的机制可以将它与 C 代码一起使用。但是，SWIG 提供了一个特殊的 %extend 指令，可以将方法附加到 C 结构以构建面向对象的接口。假设您有一个带有以下声明的 C 头文件：
/* file : vector.h */
...
typedef struct Vector {
  double x, y, z;
} Vector;

您可以通过编写这样的 SWIG 接口使 Vector 看起来很像一个类：
// file : vector.i
%module mymodule
%{
#include "vector.h"
%}

%include "vector.h"          // Just grab original C header file
%extend Vector {             // Attach these functions to struct Vector
  Vector(double x, double y, double z) {
    Vector *v;
    v = (Vector *) malloc(sizeof(Vector));
    v->x = x;
    v->y = y;
    v->z = z;
    return v;
  }
  ~Vector() {
    free($self);
  }
  double magnitude() {
    return sqrt($self->x*$self->x+$self->y*$self->y+$self->z*$self->z);
  }
  void print() {
    printf("Vector [%g, %g, %g]\n", $self->x, $self->y, $self->z);
  }
};

注意 $self 特殊变量的用法。它的用法与 C++ 'this' 指针相同，并且应该在需要访问结构实例时使用。另请注意，C++ 构造函数和析构函数语法已用于模拟构造函数和析构函数，即使对于 C 代码也是如此。不过，与普通的 C++ 构造函数实现有一个细微的区别，那就是尽管构造函数声明与普通的 C++ 构造函数一样，但必须返回新构造的对象，就好像构造函数声明具有返回值一样，在这种情况下为Vector * .
现在，当与 Python 中的代理类一起使用时，您可以执行以下操作：
>>> v = Vector(3, 4, 0)                 # Create a new vector
>>> print v.magnitude()                # Print magnitude
5.0
>>> v.print()                  # Print it out
[ 3, 4, 0 ]
>>> del v                      # Destroy it

上述 %extend 指令还可以在 Vector 结构体的定义内使用。例如：
// file : vector.i
%module mymodule
%{
#include "vector.h"
%}

typedef struct Vector {
  double x, y, z;
  %extend {
    Vector(double x, double y, double z) { ... }
    ~Vector() { ... }
    ...
  }
} Vector;

请注意，%extend 可用于访问外部编写的函数，前提是它们遵循本示例中使用的命名约定：
/* File : vector.c */
/* Vector methods */
#include "vector.h"
Vector *new_Vector(double x, double y, double z) {
  Vector *v;
  v = (Vector *) malloc(sizeof(Vector));
  v->x = x;
  v->y = y;
  v->z = z;
  return v;
}
void delete_Vector(Vector *v) {
  free(v);
}

double Vector_magnitude(Vector *v) {
  return sqrt(v->x*v->x+v->y*v->y+v->z*v->z);
}


// File : vector.i
// Interface file
%module mymodule
%{
#include "vector.h"
%}

typedef struct Vector {
  double x, y, z;
  %extend {
    Vector(int, int, int); // This calls new_Vector()
    ~Vector();           // This calls delete_Vector()
    double magnitude();  // This will call Vector_magnitude()
    ...
  }
} Vector;

用于 %extend 的名称应该是结构的名称，而不是结构的任何 typedef 的名称。例如：
typedef struct Integer {
  int value;
} Int;
%extend Integer { ...  } /* Correct name */
%extend Int { ...  } /* Incorrect name */

struct Float {
  float value;
};
typedef struct Float FloatValue;
%extend Float { ...  } /* Correct name */
%extend FloatValue { ...  } /* Incorrect name */

这个规则有一个例外，那就是当结构被匿名命名时，例如：
typedef struct {
  double value;
} Double;
%extend Double { ...  } /* Okay */

%extend 指令的一个鲜为人知的特性是，它还可用于添加合成属性或修改现有数据属性的行为。例如，假设您希望将量级设置为 Vector 的只读属性 而不是方法。为此，您可以编写如下代码：
// 给 Vector 添加一个新属性
%extend Vector {
    const double magnitude;
}
// Now supply the implementation of the Vector_magnitude_get function
%{
const double Vector_magnitude_get(Vector *v) {
  return (const double) sqrt(v->x*v->x+v->y*v->y+v->z*v->z);
}
%}

现在，出于所有实际目的，量级将像对象的属性一样出现。
类似的技术也可用于处理要处理的数据成员。例如，考虑这个接口：
typedef struct Person {
  char name[50];
  ...
} Person;

假设您想确保名称始终为大写，您可以按如下方式重写接口以确保在读取或写入名称时发生这种情况：
typedef struct Person {
  %extend {
    char name[50];
  }
  ...
} Person;

%{
#include <string.h>
#include <ctype.h>

void make_upper(char *name) {
  char *c;
  for (c = name; *c; ++c)
    *c = (char)toupper((int)*c);
}

/* Specific implementation of set/get functions forcing capitalization */

char *Person_name_get(Person *p) {
  make_upper(p->name);
  return p->name;
}

void Person_name_set(Person *p, char *val) {
  strncpy(p->name, val, 50);
  make_upper(p->name);
}
%}

最后，应该强调的是，即使 %extend 可用于添加新的数据成员，但这些新成员不能要求在对象中分配额外的存储空间（例如，它们的值必须完全从结构的现有属性或在别处获得）。
兼容性注意：在%extend 指令是 ％addmethods 指令的一个新名字。由于 %addmethods 可用于扩展结构，而不仅仅是方法，因此选择了更合适的指令名称。
5.5.7 嵌套结构体
有时，C 程序会涉及这样的结构体：
typedef struct Object {
  int objtype;
  union {
    int ivalue;
    double dvalue;
    char *strvalue;
    void *ptrvalue;
  } intRep;
} Object;

当 SWIG 遇到这种情况时，它会执行结构拆分操作，将声明转换为以下内容：
typedef union {
  int ivalue;
  double dvalue;
  char *strvalue;
  void *ptrvalue;
} Object_intRep;

typedef struct Object {
  int objType;
  Object_intRep intRep;
} Object;


然后 SWIG 将创建一个Object_intRep 结构体以便在接口文件中使用。将为这两个结构体创建访问器函数。在这种情况下，将创建这样的函数：
Object_intRep *Object_intRep_get(Object *o) {
  return (Object_intRep *) &o->intRep;
}
int Object_intRep_ivalue_get(Object_intRep *o) {
  return o->ivalue;
}
int Object_intRep_ivalue_set(Object_intRep *o, int value) {
  return (o->ivalue = value);
}
double Object_intRep_dvalue_get(Object_intRep *o) {
  return o->dvalue;
}
... etc ...

尽管这个过程有点麻烦，但它的工作方式与您在目标脚本语言中所期望的一样——尤其是在使用代理类时。例如，在 Perl 中：
# 访问嵌套成员的 Perl5 脚本
$o = CreateObject(); # 以某种方式创建一个对象
$o->{intRep}->{ivalue} = 7 # 改变 o.intRep.ivalue 的值

如果您有很多嵌套的结构声明，建议在运行 SWIG 后仔细检查它们。尽管它们很有可能会起作用，但在某些情况下您可能需要修改接口文件。
最后，请注意嵌套在 C++ 模式下的处理方式不同，请参阅嵌套类。
5.5.8 关于结构体包装的其他注意事项
SWIG 不关心 .i 文件 中的结构体声明是否与底层 C 代码中使用的声明完全匹配（嵌套结构的情况除外）。因此，省略有问题的成员或完全省略结构定义没有问题。如果您乐于传递指针，则无需为 SWIG 提供结构定义即可完成此操作。
从 SWIG1.3 开始，对 SWIG 的代码生成器进行了许多改进。具体来说，即使结构访问已经根据诸如此类的高级访问器功能进行了描述，
double Vector_x_get(Vector *v) {
  return v->x;
}

大多数生成的代码实际上直接内联到包装函数中。因此，在生成的包装文件中实际上不存在函数Vector_x_get()。例如，在创建 Tcl 模块时，会生成以下函数：
static int
_wrap_Vector_x_get(ClientData clientData, Tcl_Interp *interp, 
                   int objc, Tcl_Obj *CONST objv[]) {
  struct Vector *arg1 ;
  double result ;

  if (SWIG_GetArgs(interp, objc, objv, "p:Vector_x_get self ", &arg0,
                   SWIGTYPE_p_Vector) == TCL_ERROR)
    return TCL_ERROR;
  result = (double ) (arg1->x);
  Tcl_SetObjResult(interp, Tcl_NewDoubleObj((double) result));
  return TCL_OK;
}

此规则的唯一例外是使用 %extend 定义的方法 。在这种情况下，添加的代码包含在单独的函数中。
最后，需要注意的是，大多数语言模块可能会选择构建更高级的界面。尽管您可能永远不会使用这里描述的低级接口，但 SWIG 的大多数语言模块都以某种方式使用它。
5.6 代码插入
有时需要在 SWIG 生成的结果包装文件中插入特殊代码。例如，您可能希望包含额外的 C 代码来执行初始化或其他操作。有四种常见的代码插入方式，但首先了解 SWIG 的输出结构是很有用的。
5.6.1 SWIG 的输出
当 SWIG 创建其输出 C/C++ 文件时，它被分为五个部分，分别对应于运行时代码、头文件、包装函数和模块初始化代码（按此顺序）。
- 开始部分。Begin section
供用户将代码放在 C/C++ 包装器文件开头的占位符。这最常用于定义后面部分中使用的预处理器宏。
- 运行时代码。Runtime code
此代码是 SWIG 的内部代码，用于包含模块其余部分使用的类型检查和其他支持功能。
- 标题部分。Header section
这是用户定义的支持代码，已包含在 %{ ... %} 指令中。通常这包括头文件和其他帮助函数。
- 包装代码。Wrapper code
这些是 SWIG 自动生成的包装器。
- 模块初始化。Module initialization
SWIG 生成的函数，用于在加载时初始化模块。
5.6.2 代码插入块
所述%insert 指令允许插入的代码块到所生成的代码的给定部分。它可以通过以下两种方式之一使用：
%insert("section") "filename"
%insert("section") %{ ... %}

第一个将给定 filename 中的文件内容转储到命名 section 中。第二个将大括号之间的代码插入到命名section 中。例如，以下将代码添加到运行时部分：
%insert("runtime") %{
  ... code in runtime section ...
%}

有 5 个部分，但是，一些目标语言添加了额外的部分，其中一些导致代码生成到目标语言文件而不是 C/C++ 包装器文件中。这些在目标语言章节中可用时进行记录。以代码段命名的宏可作为附加指令使用，这些宏指令通常用于代替 %insert 。例如，使用 %runtime 代替 %insert("runtime")。生成的 C/C++ 包装器文件中的有效部分和部分的顺序如下所示：
%begin %{
  ... code in begin section ...
%}

%runtime %{
  ... code in runtime section ...
%}

%header %{
  ... code in header section ...
%}

%wrapper %{
  ... code in wrapper section ...
%}

%init %{
  ... code in init section ...
%}

裸 %{ ... %} 指令是与 %header %{ ... %} 相同的快捷方式。
在 %begin section 被有效地空的，因为它只是默认情况下包含 SWIG banner。提供此 section 是为了让用户在生成任何其他代码之前，在包装器文件的顶部插入代码。代码插入块中的所有内容都被逐字复制到输出文件中，并且不会被 SWIG 解析。大多数 SWIG 输入文件至少有一个这样的块来包含头文件并支持 C 代码。可以根据需要将其他代码块放置在 SWIG 文件中的任何位置。
%module mymodule
%{
#include "my_header.h"
%}
... Declare functions here
%{

void some_extra_function() {
  ...
}
%}

代码块的一个常见用途是编写“辅助”函数。这些函数专门用于构建接口，但通常对普通 C 程序不可见。例如 ：
%{
/* Create a new vector */
static Vector *new_Vector() {
  return (Vector *) malloc(sizeof(Vector));
}

%}
// Now wrap it 
Vector *new_Vector();

5.6.3 内联代码块
由于编写辅助函数的过程相当普遍，因此有一种特殊的内联形式的代码块，其用法如下：
%inline %{
/* Create a new vector */
Vector *new_Vector() {
  return (Vector *) malloc(sizeof(Vector));
}
%}

所述 %inline 指令逐字插入的所有到接口文件的报头部分的代码。然后代码由 SWIG 预处理器和解析器解析。因此，上面的示例仅使用一个声明就创建了一个新命令 new_Vector。由于%inline %{ ... %}块中的代码同时提供给 C 编译器和 SWIG，因此在%{ ... %}块中包含任何 SWIG 指令都是非法的。
5.6.4 初始化块
当代码包含在 %init 部分时，它会直接复制到模块初始化函数中。例如，如果你需要在模块加载时执行一些额外的初始化，你可以这样写：
%init %{
  init_variables();
%}

5.7 接口构建策略
本节介绍使用 SWIG 构建接口的一般方法。在后面的章节中可以找到与特定脚本语言相关的细节。
5.7.1 为 SWIG 准备 C 程序
SWIG 不需要修改您的 C 代码，但如果您将原始 C 头文件或源代码的集合提供给它，结果可能不是您所期望的——事实上，它们可能很糟糕。以下是为 C 程序创建接口的一系列步骤：
- 确定要包装的函数。可能没有必要访问 C 程序的每一个函数——因此，稍加考虑就可以极大地简化生成的脚本语言界面。C 头文件是寻找要包装的东西的特别好的来源。
- 创建一个新的接口文件来描述程序的脚本语言接口。
- 将适当的声明复制到接口文件中或使用 SWIG 的 %include 指令来处理整个 C 源/头文件。
- 确保接口文件中的所有内容都使用 ANSI C/C++ 语法。
- 确保所有必要的 ` typedef ' 声明和类型信息在接口文件中可用。特别是，确保按照 C/C++ 编译器要求的正确顺序指定类型信息。最重要的是，在使用之前定义一个类型！如果需要，C 编译器会告诉您完整类型信息是否不可用，而 SWIG 通常不会发出警告或错误，因为它旨在在没有完整类型信息的情况下工作。但是，如果未正确指定类型信息，则包装器可能不是最佳的，甚至会导致无法编译 C/C++ 代码。
- 如果您的程序有 main() 函数，您可能需要重命名它（继续阅读）。
- 运行 SWIG 并编译。
虽然这听起来可能很复杂，但一旦掌握了窍门，这个过程就会变得相当容易。
在构建接口的过程中，SWIG 可能会遇到语法错误或其他问题。解决这个问题的最好方法是简单地将有问题的代码复制到一个单独的接口文件中并进行编辑。但是，SWIG 开发人员非常努力地改进 SWIG 解析器——您应该向 swig-devel 邮件列表或 SWIG 错误跟踪器报告解析错误。
5.7.2 SWIG 接口文件
使用 SWIG 的首选方法是生成单独的接口文件。假设您有以下 C 头文件：
/* File : header.h */

#include <stdio.h>
#include <math.h>

extern int foo(double);
extern double bar(int, int);
extern void dump(FILE *f);

此头文件的典型 SWIG 接口文件如下所示：
/* File : interface.i */
%module mymodule
%{
#include "header.h"
%}
extern int foo(double);
extern double bar(int, int);
extern void dump(FILE *f);

当然，在这种情况下，我们的头文件非常简单，因此我们可以使用更简单的方法并使用如下接口文件：
/* File : interface.i */
%module mymodule
%{
#include "header.h"
%}
%include "header.h"

这种方法的主要优点是在将来头文件更改时对接口文件的维护最少。在更复杂的项目中，由于较低的维护开销，包含大量 %include 和 #include 语句的接口文件是最常见的接口文件设计方法之一。
5.7.3 为什么要使用单独的接口文件？
尽管 SWIG 可以解析许多头文件，但更常见的是编写一个特殊的 .i 文件来定义包的接口。您可能想要这样做的原因有几个：
- 很少需要访问大包中的每一个函数。许多 C 函数在脚本环境中可能很少或根本没有用。因此，为什么要包装它们？
- 单独的接口文件提供了提供关于如何构建接口的更精确规则的机会。
- 接口文件可以提供更多的结构和组织。
- SWIG 无法解析出现在头文件中的某些定义。使用单独的文件可以消除或解决这些问题。
- 接口文件提供了更精确的接口定义。想要扩展系统的用户可以转到接口文件并立即查看可用的内容，而无需从头文件中挖掘它。
5.7.4 获取正确的头文件
有时，为了使 SWIG 生成的代码正确编译，需要使用某些头文件。确保使用%{ %}块包含某些头文件，如下所示：
%module graphics
%{
#include <GL/gl.h>
#include <GL/glu.h>
%}

// Put the rest of the declarations here
...

5.7.5 用 main() 做什么
如果您的程序定义了 main() 函数，您可能需要删除它或重命名它以使用脚本语言。大多数脚本语言定义了自己的 main() 过程，该过程被调用。main() 在处理动态加载时也没有意义。有几种方法可以解决main() 冲突：
- 完全摆脱 main()。
- 将 main() 重命名为其他名称。您可以通过使用 -Dmain=oldmain 之类的选项编译 C 程序来实现此目的。
- 不使用脚本语言时，使用条件编译仅包含 main()。
摆脱 main() 可能会导致程序的潜在初始化问题。为了解决这个问题，您可以考虑编写一个名为program_init() 的特殊函数，它在启动时初始化您的程序。然后可以在第一次操作时从脚本语言调用此函数，或者在加载 SWIG 生成的模块时调用此函数。
作为一般说明，许多 C 程序仅使用 main() 函数来解析命令行选项和设置参数。但是，通过使用脚本语言，您可能正在尝试创建更具交互性的程序。在许多情况下，旧的 main() 程序可以完全替换为 Perl、Python 或 Tcl 脚本。
注意：在某些情况下，您可能倾向于为 main() 创建脚本语言包装器。如果你这样做，编译可能会工作，你的模块甚至可能正确加载。唯一的麻烦是，当您调用 main() 包装器时，您会发现它实际上调用了脚本语言解释器本身的 main() ！此行为是动态链接器中使用的符号绑定机制的副作用。底线：不要这样做。

6 SWIG 和 C++
本章介绍 SWIG 对包装 C++ 的支持。作为先决条件，您应该首先阅读SWIG 基础一章，了解 SWIG 如何包装 ANSI C。对 C++ 的支持建立在 ANSI C 包装的基础上，该材料将有助于理解本章。
6.1 C++ 包装评论
由于其复杂性以及 C++ 难以与自身集成的事实，更不用说其他语言了，SWIG 仅提供对 C++ 功能子集的支持。幸运的是，现在这是一个相当大的子集。
在某种程度上，C++ 包装的问题在于没有语义上明显（或自动）的方式将其许多高级功能映射到其他语言。作为一个简单的例子，如将 C++ 多重继承包装到没有这种支持的目标语言。类似地，当目标语言中不存在此类功能时，使用重载运算符和重载函数也可能会出现问题。
C++ 的一个更微妙的问题与一些 C++ 程序员对编程库的看法有关。在 SWIG 的世界中，您实际上是在尝试创建用于其他语言的二进制级软件组件。为了使其工作，“组件”必须包含真正的可执行指令，并且必须有某种二进制链接机制来访问其功能。相比之下，C++ 的大部分功能越来越依赖于泛型编程和模板。尽管模板是一个强大的功能，但它们在很大程度上与二进制组件和库的整个概念正交。例如，一个 STL vector 没有定义 SWIG 可以为其创建包装器的任何类型的二进制对象。更复杂的是，这些库经常使用大量的幕后魔法，其中看似基本的操作（例如，指针解引用、过程调用等）的语义可以以戏剧性且有时不明显的方式进行更改。尽管这种“魔法”在仅使用 C++ 的世界中可能不会出现什么问题，但它使跨越语言边界的问题大大复杂化，并提供了许多机会让自己陷入困境。你只能小心。
6.2 方法
为了包装 C++，SWIG 使用分层方法来生成代码。在最低级别，SWIG 生成 ANSI-C 样式包装器程序的集合。这些包装器负责 C++ 绑定的基本类型转换、类型检查、错误处理和其他低级细节。这些包装器也足以将 C++ 绑定到任何支持内置过程的目标语言中。从某种意义上说，您可能会将这一层包装视为是 C++ 提供了一个 C 库接口。在低级程序（扁平化）接口之上，SWIG 生成代理类，为底层代码提供自然的面向对象 (OO) 接口。代理类通常是用目标语言本身编写的。例如，在 Python 中，一个真正的 Python 类用于为底层 C++ 对象提供一个包装器。
需要强调的是，SWIG 对 C++ 包装采取了一种刻意保守和非侵入性的方法。SWIG 不会将 C++ 类封装在特殊的 C++ 适配器中，它不依赖于模板，也不会在生成包装器时添加额外的 C++ 继承。大多数 C++ 程序需要的最后一件事是更多的编译器魔法。因此，SWIG 尝试在 C++ 应用程序的实现和生成的包装器代码之间保持非常严格和清晰的分离。您可能会说 SWIG 的编写遵循了“最少惊喜”的原则——它不会在 C++ 类型系统上耍花招，不会弄乱您的类层次结构，也不会引入新的语义。虽然这种方法可能无法提供与 C++ 最无缝的集成，但它是安全的，
本章的某些内容侧重于 C++ 的低级过程接口，该接口用作所有语言模块的基础。请记住，目标语言还通过代理类提供高级 OO 接口。可以在每种目标语言的文档中找到更详细的覆盖范围。
6.3 支持的 C++ 特性
SWIG 目前支持大多数 C++ 功能，包括：
- 类
- 构造函数和析构函数
- 虚函数
- 公共继承（包含多继承）
- 静态函数
- 函数和方法重载
- 许多标准运算符的运算符重载
- 引用
- 模板（包括特化和成员模板）
- 指向成员的指针
- 命名空间
- 默认参数
- 智能指针
当前不支持以下 C++ 功能：
- 某些运算符的重载版本（new、delete 等）
根据经验，不应在原始 C++ 源文件上使用 SWIG，而应仅使用头文件。
SWIG 的 C++ 支持是一个正在进行的项目，因此在未来的版本中可能会取消其中一些限制。但是，我们不作任何承诺。此外，提交错误报告是修复问题的好方法（眨眼）。
6.4 命令行选项和编译
包装 C++ 代码时，使用 ` -c++ ' 选项调用 SWIG 至关重要。这改变了许多关键功能（如内存管理）的处理方式。它还可以识别 C++ 关键字。如果没有 -c++ 标志，SWIG 将在遇到接口文件中的 C++ 代码时发出警告或大量语法错误。
在编译和链接生成的包装文件时，使用 C++ 编译器是正常的。例如：
$ swig -c++ -tcl example.i 
$ c++ -fPIC -c example_wrap.cxx 
$ c++ example_wrap.o $(OBJS) -o example.so

不幸的是，每个平台上的过程略有不同。请务必参阅有关每种目标语言的文档以获取更多详细信息。SWIG Wiki 还提供了更多详细信息。
兼容性说明：默认情况下，SWIG 的早期版本仅生成一个扁平化的低级 C 风格 API 到 C++ 类。有一个 -noproxy 命令行选项会被许多目标语言识别，从而产生眼前这个接口的早期版本。
6.5 代理类
为了提供从 C++ 类到目标语言类的自然映射，SWIG 的目标语言大多使用特殊的代理类包装 C++ 类。这些代理类通常在目标语言本身中实现。例如，如果您正在构建一个 Python 模块，则每个 C++ 类都由一个 Python 代理类包装。或者，如果您正在构建 Java 模块，则每个 C++ 类都由一个 Java 代理类包装。
6.5.1 代理类的构建
代理类总是被构造为使用低级访问器函数的额外包装层。为了说明这一点，假设您有一个这样的 C++ 类：
class Foo {
  public:
    Foo();
    ~Foo();
    int  bar(int x);
    int  x;
};

使用 C++ 作为伪代码，代理类看起来像这样：
class FooProxy {
  private:
    Foo    *self;
  public:
    FooProxy() {
      self = new_Foo();
    }
    ~FooProxy() {
      delete_Foo(self);
    }
    int bar(int x) {
      return Foo_bar(self, x);
    }
    int x_get() {
      return Foo_x_get(self);
    }
    void x_set(int x) {
      Foo_x_set(self, x);
    }
};

当然，请始终记住，真正的代理类是用目标语言编写的。例如，在 Python 中，代理可能大致如下所示：
class Foo: 
    def __init__(self): 
        self.this = new_Foo() 
    def __del__(self): 
        delete_Foo(self.this) 
    def bar(self, x): 
        return Foo_bar(self.this, x) 
    def __getattr__(self, name): 
        if name == 'x': 
            return Foo_x_get(self.this) 
        ... 
    def __setattr__(self, name, value): 
        if name == 'x': 
            Foo_x_set(self.this, value) 
        ...

再次强调，低级访问器函数始终由代理类使用，这一点很重要。只要有可能，代理就会尝试利用类似于 C++ 的语言特性。这可能包括运算符重载、异常处理和其他功能。
6.5.2 代理中的资源管理
代理的一个主要问题涉及包装对象的内存管理。考虑以下 C++ 代码：
class Foo {
public:
  Foo();
  ~Foo();
  int bar(int x);
  int x;
};

class Spam {
public:
      Foo *value;
      ...
};

考虑一些使用这些类的脚本代码：
f = Foo()               # Creates a new Foo
s = Spam()              # Creates a new Spam
s.value = f             # Stores a reference to f inside s
g = s.value             # Returns stored reference
g = 4                   # Reassign g to some other value
del f                   # Destroy f 

现在，考虑由此产生的内存管理问题。在脚本中创建对象时，对象由新创建的代理类包装。也就是说，既有新的代理类实例，又有底层 C++ 类的新实例。在这个例子中，f 和 s 都是以这种方式创建的。然而，语句 s.value 相当奇怪——在执行时，指向 f 的指针存储在另一个对象中。这意味着脚本代理类和另一个 C++ 类共享对同一对象的引用。更有趣的是，请考虑语句 g = s.value。执行时，这会创建一个新的代理类 g，它为存储在s.value 中的 C++ 对象提供了一个包装器 。一般来说，没有办法知道这个对象来自哪里——它可能是由脚本创建的，但也可能是内部生成的。在这个特定的例子中， g 的赋值导致 f 的第二个代理类。换句话说，对f的引用现在由两个代理类和 一个 C++ 类共享。
最后，考虑当对象被销毁时会发生什么。在语句 g=4 中，变量g被重新赋值。在许多语言中，这使得 g的旧值可用于垃圾收集。因此，这会导致代理类之一被破坏。稍后，语句 del f 破坏了另一个代理类。当然，仍然存在对存储在另一个 C++ 对象中的原始对象的引用。它会发生什么？对象是否仍然有效？
为了处理内存管理问题，代理类提供了一个用于控制所有权的 API。在 C++ 伪代码中，所有权控制可能大致如下所示：
class FooProxy {
  public:
    Foo    *self;
    int     thisown;

    FooProxy() {
      self = new_Foo();
      thisown = 1;       // Newly created object
    }
    ~FooProxy() {
      if (thisown) delete_Foo(self);
    }
    ...
    // Ownership control API
    void disown() {
      thisown = 0;
    }
    void acquire() {
      thisown = 1;
    }
};

class FooPtrProxy: public FooProxy {
public:
  FooPtrProxy(Foo *s) {
    self = s;
    thisown = 0;
  }
};

class SpamProxy {
  ...
  FooProxy *value_get() {
    return FooPtrProxy(Spam_value_get(self));
  }
  void value_set(FooProxy *v) {
    Spam_value_set(self, v->self);
    v->disown();
  }
  ...
};

查看此代码，有几个核心功能：
- 每个代理类都有一个额外的标志来指示所有权。C++ 对象只有在设置了所有权标志时才会被销毁。
- 在目标语言中创建新对象时，会设置所有权标志。
- 当返回对内部 C++ 对象的引用时，它由代理类包装，但代理类没有所有权。
- 在某些情况下，所有权会有所调整。例如，当一个值被分配给一个类的成员时，所有权就丢失了。
- 手动所有权控制由特殊的 disown() 和 acquire() 方法提供。
鉴于 C++ 内存管理的棘手性质，代理类不可能自动处理所有可能的内存管理问题。但是，代理确实提供了一种手动控制机制，可用于（如有必要）解决一些更棘手的内存管理问题。
6.5.3 语言特定细节
代理类的语言特定细节包含在描述每种目标语言的章节中。本章仅以非常笼统的方式介绍了该主题。
6.6 简单的 C++ 包装
以下代码显示了一个简单 C++ 类的 SWIG 接口文件。
%module list
%{
#include "list.h"
%}

// Very simple C++ example for linked list

class List {
public:
  List();
  ~List();
  int  search(char *value);
  void insert(char *);
  void remove(char *);
  char *get(int n);
  int  length;
static void print(List *l);
};

为了为此类生成包装器，SWIG 首先将类缩减为一组低级 C 样式访问器函数，然后由代理类使用。
6.6.1 构造函数和析构函数
C++ 构造函数和析构函数被转换为访问器函数，例如：
List * new_List(void) {
  return new List;
}
void delete_List(List *l) {
  delete l;
}

6.6.2 默认构造函数、复制构造函数和隐式析构函数
遵循隐式构造函数和析构函数的 C++ 规则，即使它们没有在类接口中显式声明，SWIG 也会自动假定存在一个。
一般来说：
- 如果 C++ 类未声明任何显式构造函数，SWIG 将自动为其生成一个包装器。
- 如果 C++ 类未声明显式复制构造函数，则在使用 %copyctor 时，SWIG 将自动为其生成一个包装器 。
- 如果 C++ 类未声明显式析构函数，SWIG 将自动为其生成一个包装器。
和在 C++ 中一样，有一些规则可以改变以前的行为：
- 如果类已经定义了带参数的构造函数，则不会创建默认构造函数。
- 不会为具有纯虚方法的类或从抽象类继承的类生成默认构造函数，不会为所有纯方法提供定义。
- 除非所有基类都支持默认构造函数，否则不会创建默认构造函数。
- 如果类在私有或受保护部分中定义了默认构造函数和隐式析构函数，则不会创建它们。
- 如果任何基类定义了非公共默认构造函数或析构函数，则不会创建默认构造函数和隐式析构函数。
SWIG 永远不应为非法的类生成默认构造函数、复制构造函数或默认析构函数包装器。但是，在某些情况下，通过手动禁用隐式构造函数/析构函数生成是必要的（如果整个类定义都对 SWIG 不可见，或者想要缩小最终接口的大小）。
要手动禁用这些，可以使用 %nodefaultctor 和 %nodefaultdtor 功能标志 指令。请注意，这些指令仅影响隐式生成，如果在类接口中显式声明了默认/复制构造函数或析构函数，则它们不起作用。
例如：
%nodefaultctor Foo; // 禁用类 Foo 的默认构造函数。
class Foo { // 不生成默认构造函数，除非声明了一个
... 
}; 
class Bar { // 如果可能，生成默认构造函数
... 
};

指令 %nodefaultctor 也可以“全局”应用，如下所示：
%nodefaultctor; // 禁止创建默认构造函数
class Foo { // 不生成默认构造函数，除非声明了一个
... 
}; 
class Bar {   
public：
  Bar()；// 生成默认构造函数，因为声明了一个
}; 
%clearnodefaultctor; // 再次启用默认构造函数的创建

如果需要，相应的 %nodefaultdtor 指令可用于禁用默认或隐式析构函数的生成。但是请注意，这可能会导致目标语言中的内存泄漏。因此，建议仅在众所周知的情况下使用此指令。例如：
%nodefaultdtor Foo; // 禁用类 Foo 的隐式/默认析构函数。
class Foo { // 不生成析构函数，除非声明了一个
... 
};

兼容性说明：默认构造函数/隐式析构函数的生成在 SWIG 1.3.7 中成为默认行为。这可能会破坏某些较旧的模块，但可以使用 %nodefault 或 -nodefault 命令行选项轻松恢复旧行为。此外，为了让 SWIG 正确生成（或不生成）默认构造函数，它必须能够从 private 和 protected 部分中收集信息（具体来说，它需要知道是否定义了或受保护的构造函数/析构函数）。在旧版本的 SWIG 中，由于解析器的限制，简单地删除或注释掉类的私有和受保护部分是相当普遍的。但是，此删除现在可能会导致 SWIG 错误地为在这些部分中定义构造函数的类生成构造函数。考虑恢复界面中的那些部分或使用 %nodefault 来解决问题。
注意：上述 %nodefault 指令/ -nodefault 选项禁用默认构造函数和隐式析构函数，可能导致内存泄漏，因此强烈建议不要使用它们。
6.6.3 未创建构造函数包装器时
如果一个类定义了一个构造函数，SWIG 通常会尝试为它生成一个包装器。但是，如果 SWIG 认为会导致非法包装器代码，则不会生成构造器包装器。确实有两种情况可能会出现这种情况。
首先，SWIG 不会为受保护或私有构造函数生成包装器。例如：
class Foo {
protected：
  Foo(); // 没有包装。
public：
    ... 
};

接下来，如果一个类看起来是抽象的——也就是说，它有未定义的纯虚拟方法，那么 SWIG 不会为它生成包装器。这里有些例子：
class Bar{
public：
  Bar（）；// 没有包装。Bar 是抽象的。
  virtual void spam(void) = 0;
}; 

class Grok : public Bar { 
public: 
      Grok(); // 没有包装。没有抽象 spam() 的实现。
};

一些用户惊讶（或困惑）在他们的接口中发现缺少构造函数包装器。在几乎所有情况下，这都是在类被确定为抽象时引起的。要查看是否是这种情况，请在打开所有警告的情况下运行 SWIG：
% swig -Wall -python module.i

在这种模式下，SWIG 将对所有抽象类发出警告。可以使用以下方法强制类为非抽象类：
%feature("notabstract") Foo; 

class Foo : public Bar { 
public: 
  Foo(); // 无论如何生成---不是抽象的。
  ... 
};

有关 %feature 的更多信息可以在 自定义功能 章节中找到。
6.6.4 复制构造函数
如果一个类定义了多个构造函数，它的行为取决于目标语言的能力。如果支持重载，则可以使用普通构造函数访问复制构造函数。例如，如果你有这个：
class List {
public:
    List();    
    List(const List &);      // Copy constructor
    ...
};

然后可以按如下方式使用复制构造函数：
x = List()               # Create a list
y = List(x)              # Copy list x

如果目标语言不支持重载，则复制构造函数可通过如下特殊函数使用：
List *copy_List(List *f) { 
    return new List(*f); 
}

注意：对于类 X，SWIG 仅将构造函数视为复制构造函数，前提是它可以应用于类型 X 或 X * 的对象。如果定义了多个复制构造函数，则只有出现的第一个定义用作复制构造函数——其他定义将导致名称冲突。形如 X(const X&），X(X&) ，和 X(X *) 的这些构造器，都会被 SWIG 以复制构造器处理。
注：SWIG 并没有产生一个拷贝构造函数包装除非在类显式声明。这与默认构造函数和析构函数的处理不同。然而，拷贝构造函数包装可以，如果使用会产生copyctor 功能标志。例如：
%copyctor List;

class List {
public:
    List();    
};

将为List生成一个复制构造函数包装器。
兼容性说明：直到 SWIG-1.3.12 才添加对复制构造函数的特殊支持。在以前的版本中，可以包装复制构造函数，但它们必须重命名。例如：
class Foo {
public:
    Foo();
  %name(CopyFoo) Foo(const Foo &);
    ...
};

为了向后兼容，如果构造函数已被手动重命名，SWIG 不会执行任何特殊的复制构造函数处理。例如，在上面的示例中，构造函数的名称设置为 new_CopyFoo()。这与旧版本相同。
6.6.5 成员函数
所有成员函数都大致翻译成这样的访问器函数：
int List_search(List *obj, char *value) { 
  return obj->search(value); 
}

即使成员函数已声明为 virtual ，此转换也是相同的。
应该注意的是，SWIG 实际上并没有在它生成的代码中创建 C 访问器函数。相反，诸如 obj->search(value) 之类的成员访问直接内联到生成的包装器函数中。但是，低级过程包装器的名称和调用约定与上述访问器函数原型相匹配。
6.6.6 静态成员
直接调用静态成员函数，无需进行任何特殊转换。例如，静态成员函数 print (List *l) 在生成的包装器代码中直接调用 List::print(List *l)。
6.6.7 成员数据
成员属性的处理方式与 C 结构完全相同。有效地创建了一对访问器函数。例如 ：
int List_length_get(List *obj) { 
  return obj->length; 
} 
int List_length_set(List *obj, int value) {
  obj->length = value;
  return value；
}

可以使用 %immutable 和 %mutable 功能标志 指令创建只读成员。例如，我们可能不希望用户更改列表的长度，因此我们可以执行以下操作以使该值可用但只读。
class List { 
public: 
... 
%immutable; 
  int length；
%mutable; 
... 
};

或者，您可以像这样提前指定一个不可变成员：
%immutable List::length;
... 
class List { 
  ... 
  int length; // 上述指令不可变
  ... 
};

类似地，所有声明为 const 的数据属性都被包装为只读成员。
默认情况下，SWIG 对基本类型的成员使用 const 引用类型映射。在包装非原始类型的数据成员（例如类）时，存在一些微妙的问题。例如，如果你有另一个这样的类：
class Foo {
public:
    List items;
    ...

那么 items 成员的低级访问器实际上使用了指针。例如：
List *Foo_items_get(Foo *self) { 
    return &self->items; 
} 
void Foo_items_set(Foo *self, List *value) { 
    self->items = *value; 
}

有关更多信息，请参见 SWIG 基础章节 结构数据成员 部分。
为类生成访问器的包装代码来自指针类型映射。对于某些类型，这可能有点不自然。例如，用户希望在目标语言中将 STL std::string 类成员变量包装为字符串，而不是指向此类的指针。const 引用类型映射提供这种类型的编组，因此有一个功能告诉 SWIG 使用常量引用类型映射而不是指针类型映射。有一个 naturalvar 特性，可用于有效地将访问器的生成方式更改为以下内容：
const List &Foo_items_get(Foo *self) { 
    return self->items; 
} 
void Foo_items_set(Foo *self, const List &value) { 
    self->items = value; 
}

所述 ％naturalvar 指令是一个宏，并因此等价于，％feature("naturalvar") 。它可以按如下方式使用：
// 所有 List 变量都将使用 const List& typemaps 
%naturalvar List; 

// 只有 Foo::myList 会使用 const List& typemaps 
%naturalvar Foo::myList; 
struct Foo {
  List myList; 
}; 

// 所有非原始类型都将使用 const 引用 typemaps
%naturalvar;

细心的读者会注意到 %naturalvar 像任何其他 功能标志 指令一样工作，但具有一些额外的灵活性。上面的第一个示例用法显示了 %naturalvar 附加到 myList 的变量类型，即 List 类。第二个用法显示 %naturalvar 附加到变量名称。因此，naturalvar 特性可用于变量的名称或类型。请注意，在变量名称上使用 naturalvar 功能会覆盖附加到变量类型的任何自然变量功能。
全局使用此功能通常是一个好主意，因为与指针类型映射相比，引用类型映射具有额外的 NULL 检查。指针可以是 NULL，而引用不能，因此额外的检查确保目标语言用户不会传入转换为 NULL 指针的值，从而防止任何潜在的 NULL 指针取消引用。在 ％naturalvar 功能将适用于全局变量，除了在一些语言模块成员变量，如 C# 和 Java。
也可以通过 -naturalvar 命令行选项或模块模式选项 %module(naturalvar=1) 将 natural val 行为作为全局设置打开。但是，任何 %feature("naturalvar") 的使用都将覆盖全局设置。
兼容性注：该 ％naturalvar 功能是 SWIG-1.3.28 引入的，这之前，有必要引入到手动应用 const 引用typemaps，如 %apply const std::string & { std::string * }，但这个例子还将类型映射应用于采用 std::string 指针的方法。
兼容性说明：只读访问过去由一对指令 %readonly 和 %readwrite 控制。尽管这些指令仍然有效，但它们会生成警告消息。只需将指令更改为 %immutable; 和 %mutable; 可解决警告。不要忘记额外的分号！
兼容性说明： 直到 SWIG-1.3.12, 所有未知类型的成员都被包装成使用指针的访问器函数。例如，如果你有一个这样的结构体：
struct Foo { 
  size_t len; 
};

并且对 size_t 一无所知，然后将编写访问器以使用 size_t *。从 SWIG-1.3.12 开始，此行为已被修改。具体来说，只有 当 SWIG 知道数据类型对应于结构或类时，才会使用指针。因此，上面的代码将被包装到涉及 size_t 的访问器中。这种变化很微妙，但它解决了一些与结构包装和 SWIG 的一些自定义功能相关的问题。
6.7 默认参数
SWIG 将包装所有类型的具有默认参数的函数。例如成员函数：
class Foo { 
public: 
    void bar(int x, int y = 3, int z = 4); 
};

SWIG 通过为每个默认参数生成一个额外的重载方法来处理默认参数。SWIG 正在有效地处理带有默认参数的方法，就好像它在包装等效的重载方法一样。因此，对于上面的示例，就好像我们已将以下内容提供给 SWIG：
class Foo { 
public: 
    void bar(int x, int y, int z); 
    void bar(int x, int y); 
    void bar(int x)；
};

生成的包装器与将上述代码输入 SWIG 完全相同。后面的 包装重载函数和方法 部分将介绍这方面的详细信息。这种方法允许 SWIG 包装所有可能的默认参数，但可能很冗长。例如，如果一个方法有十个默认参数，则生成十一个包装方法。
有关将 %feature 与具有默认参数的函数一起使用的更多信息，请参阅 功能和默认参数 部分 。该 歧义解决和重命名 部分也涉及使用 ％rename 和 ％ignore 与默认参数的方法。如果您正在为具有默认参数的方法中使用的类型编写自己的类型映射，您可能还需要编写一个类型检查的 typemap. 参考类型映射和重载 部分获得细节，或者使用下面提到的 compactdefaultargs 特性标志。
兼容性说明： SWIG-1.3.23 之前的 SWIG 版本包装默认参数略有不同。相反，生成了一个包装器方法，并将默认值复制到 C++ 包装器中，以便然后使用指定的所有参数调用被包装的方法。如果包装器的大小是一个问题，那么可以使用 compactdefaultargs 功能标志重新激活这种使用默认参数包装方法的方法。
%feature("compactdefaultargs") Foo::bar; 
class Foo { 
public: 
    void bar(int x, int y = 3, int z = 4); 
};

这对于减少包装器的大小非常有用，但需要注意的是它不适用于静态类型语言，例如 C# 和 Java，它们在语言中没有可选参数，此功能的另一个限制是无法处理非公开的默认参数。以下示例说明了这一点：
class Foo {
private:
  static const int spam;
public:
  void bar(int x, int y = spam); //Won't work with %feature("compactdefaultargs")-
                                 //private default value
};

这会产生不可编译的包装器代码，因为 C++ 中的默认值在与成员函数相同的范围内计算，而 SWIG 在包装器函数的范围内计算它们（意味着这些值必须是公共的）。
当使用默认参数包装C 代码时，compactdefaultargs 功能会自动打开。如果为 C 或 C++ 函数指定了关键字参数功能 (kwargs)，并且目标语言支持 kwargs，则某些目标语言也会自动打开此功能， compactdefaultargs 功能也会自动打开。关键字参数是一些脚本语言的语言特性，例如 Ruby 和 Python。SWIG 在包装重载方法时无法支持 kwargs，因此无法使用默认方法。
6.8 保护
SWIG 按照 C++ 约定包装公共类成员，即通过显式公共声明或使用 using指令。通常，私有或受保护部分中指定的任何内容都将被忽略，尽管内部代码生成器有时会查看私有和受保护部分的内容，以便为默认构造函数和析构函数正确生成代码。董事还可以修改对待非公开虚拟受保护成员的方式。
默认情况下，类定义的成员被假定为私有的，直到您明确给出一个 ` public: ' 声明（这与 C++ 使用的约定相同）。
6.9 枚举和常量
枚举和常量由不同的语言模块以不同的方式处理，并在相应的语言章节中进行了详细描述。但是，许多语言将类定义中的枚举和常量映射为以类名作为前缀的常量。例如 ：
class Swig { 
public: 
  enum {ALE, LAGER, PORTER, STOUT}; 
};

在目标脚本语言中生成以下常量集：
Swig_ALE = Swig::ALE 
Swig_LAGER = Swig::LAGER 
Swig_PORTER = 
Swig ::PORTER Swig_STOUT = Swig::STOUT

声明为 const 的成员被包装为只读成员并且不创建常量。
6.10 友元
SWIG 识别友元声明。例如，如果您有以下代码：
class Foo {
public:
  ...
  friend void blah(Foo *f);
  ...
};

那么朋友声明确实会产生一个包装代码，相当于为以下声明生成的代码
class Foo {
public:
    ...
};

void blah(Foo *f);  

友元声明，如在 C++ 中，被理解为在声明类的同一范围内，因此，您可以有
%ignore bar::blah(Foo *f);

namespace bar {

  class Foo {
  public:
    ...
    friend void blah(Foo *f);
    ...
  };
}

并且不会生成方法 'blah' 的包装器。
6.11 引用和指针
支持 C++ 引用，但 SWIG 将它们转换回指针。例如，这样的声明：
class Foo { 
public: 
  double bar(double &a); 
}

有一个低级访问器
double Foo_bar(Foo *obj, double *a) { 
  obj->bar(*a); 
}

作为一种特殊情况，大多数语言模块通过值而不是指针传递 对原始数据类型（int、short、 float等）的 const 引用。例如，如果您有这样的功能，
void foo(const int &x);

它被一个脚本如下调用
foo(3) # 注意按值传递

返回引用的函数被重新映射为返回一个指针。例如：
class Bar{
public：
  Foo &spam(); 
};

生成这样的访问器：
Foo *Bar_spam(Bar *obj) { 
  Foo &result = obj->spam(); 
  return &result; 
}

但是，返回对原始数据类型（int、short 等）的 const 引用的函数通常将结果作为值而不是指针返回。例如，像这样的函数，
const int &bar();

将返回目标脚本语言中的整数，例如 37 或 42，而不是指向整数的指针。
不要返回对分配为堆栈上的局部变量的对象的引用。SWIG 不会复制对象，因此这可能会导致您的程序崩溃。
注意：对原始数据类型引用的特殊处理对于提供与更高级的 C++ 包装应用程序的更无缝集成是必要的——尤其是与模板和 STL 相关的应用程序。这是在 SWIG-1.3.12 中首次添加的。
6.12 按值传递和返回
有时，C++ 程序会按值传递和返回类对象。例如，可能会出现这样的函数：
Vector cross_product(Vector a, Vector b);

如果没有提供有关Vector 的信息, SWIG 将建立一个如下的包装器函数:
Vector *wrap_cross_product(Vector *a, Vector *b) {
  Vector x = *a;
  Vector y = *b;
  Vector r = cross_product(x, y);
  return new Vector(r);
}

为了编译包装器代码，Vector 必须定义一个复制构造函数和一个默认构造函数。
如果Vector被定义为接口中的一个类，但它不支持默认构造函数，SWIG 通过将参数封装在一个特殊的 C++ 模板包装类中，通过一个称为“富尔顿转换”的过程来更改包装代码。这会生成一个如下所示的包装器：
Vector cross_product(Vector *a, Vector *b) {
  SwigValueWrapper<Vector> x = *a;
  SwigValueWrapper<Vector> y = *b;
  SwigValueWrapper<Vector> r = cross_product(x, y);
  return new Vector(r);
}

这种转换有点偷偷摸摸，但即使类不提供默认构造函数，它也支持按值传递，并且可以正确支持许多 SWIG 的自定义选项。SwigValueWrapper 的定义可以通过阅读 SWIG 包装器代码找到。这个类实际上只不过是一个围绕指针的薄包装。
尽管 SWIG 通常会检测应应用富尔顿变换的类，但在某些情况下，有必要覆盖它。这是通过 %feature("valuewrapper") 来确保它被使用和 %feature("novaluewrapper") 来确保它不被使用：
%feature("novaluewrapper") A;    
class A;

%feature("valuewrapper") B;
struct B { 
    B();
    // ....
}; 

非常值得考虑为具有默认构造函数的类启用此功能。它将在包装器中的变量声明点删除多余的构造函数调用，因此将为大对象或具有昂贵构造的类产生明显更好的性能。或者考虑返回一个引用或一个指针。
注意：此转换对类型映射或 SWIG 的任何其他部分没有影响——它应该是透明的，除非您在阅读 SWIG 输出文件时可能会看到此代码。
注意: 此模板转换是 SWIG-1.3.11 中的新增功能，可能会在未来的 SWIG 版本中进行改进。实际上，只有对没有定义默认构造函数的类才绝对有必要这样做。
注意：此模板的使用仅在对象按值传递或返回时发生。它不用于 C++ 指针或引用。
6.13 继承
SWIG 支持类的 C++ 继承，并在目标语言的限制或允许下允许单继承和多继承。SWIG 类型检查器了解基类和派生类之间的关系，并允许在基类的函数中使用指向派生类的任何对象的指针。类型检查器正确地转换指针值并且可以安全地用于多重继承。
SWIG 将私有或受保护的继承视为尽可能接近 C++ 精神和目标语言功能。在大多数情况下，这意味着 SWIG 将解析非公共继承声明，但这不会对生成的代码产生任何影响，除了为构造函数和析构函数派生的隐式策略。
以下示例展示了 SWIG 如何处理继承。为清楚起见，省略了完整的 C++ 代码。
// shapes.i
%module shapes
%{
#include "shapes.h"
%}

class Shape {
public:
  double x, y;
  virtual double area() = 0;
  virtual double perimeter() = 0;
  void    set_location(double x, double y);
};
class Circle : public Shape {
public:
  Circle(double radius);
  ~Circle();
  double area();
  double perimeter();
};
class Square : public Shape {
public:
  Square(double size);
  ~Square();
  double area();
  double perimeter();
}

当包装到 Python 中时，我们可以执行以下操作（使用低级 Python 访问器显示）：
$ python
>>> import shapes
>>> circle = shapes.new_Circle(7)
>>> square = shapes.new_Square(10)
>>> print shapes.Circle_area(circle)
153.93804004599999757
>>> print shapes.Shape_area(circle)
153.93804004599999757
>>> print shapes.Shape_area(square)
100.00000000000000000
>>> shapes.Shape_set_location(square, 2, -3)
>>> print shapes.Shape_perimeter(square)
40.00000000000000000
>>>

在此示例中，已创建 Circle 和 Square 对象。通过调用 Circle_area、Square_area 等，可以在每个对象上调用成员函数。但是，只需在任一对象上使用 Shape_area 函数即可获得相同的结果。
关于继承的一个重要点是，低级访问器函数只为它们实际声明的类生成。例如，在上面的例子中，方法 set_location( ) 只能作为 Shape_set_location() 而不是作为 Circle_set_location() 或Square_set_location() 访问 。当然，Shape_set_location() 函数将接受从 Shape 派生的任何类型的对象。类似地，属性 x 和 y 的访问器函数生成为 Shape_x_get()、Shape_x_set()、Shape_y_get() 和 Shape_y_set()。Circle_x_get() 等函数不可用——您应该使用 Shape_x_get()。
请注意，低级访问器函数和代理方法之间存在一一对应关系，因此 C++ 类方法和生成的代理类方法之间也存在一一对应关系。
注意：为了获得最佳结果，SWIG 要求在接口中定义所有基类。否则，您可能会收到如下警告消息：
example.i:18: Warning 401: Nothing known about base class 'Foo'. Ignored.

如果未定义任何基类，SWIG 仍会生成正确的类型关系。例如，接受 Foo * 的函数将接受从 Foo 派生的任何对象，无论 SWIG 是否实际包装了 Foo 类。如果你真的不想为基类生成包装器，但你想消除警告，你可以考虑使用 %import 指令来包含定义 Foo 的文件. %import 简单的收集类型信息，但是不会生成包装器。或者，你可以仅仅在 SWIG 接口里，定义 Foo 作为一个空的类，或者使用 警告排除 
注意： typedef -names可以用作基类。例如：
class Foo {
...
};

typedef Foo FooObj;
class Bar : public FooObj {     // Ok.  Base class is Foo
...
};

类似地，typedef 允许将未命名的结构用作基类。例如：
typedef struct { 
  ... 
} Foo; 

class Bar : public Foo { // 好的。
... 
};

兼容性说明：从 1.3.7 版开始，SWIG 仅为每个类中实际定义的声明，生成低级访问器包装器。这与 SWIG1.1 不同，后者用于继承基类中定义的所有声明，并重新生成专门的访问器函数，例如Circle_x_get(), Square_x_get(), Circle_set_location(), 和 Square_set_location()。这种行为导致大型类层次结构的大量重复代码，并使构建跨多个模块的应用程序变得笨拙（因为访问器函数在每个模块中重复）。当使用代理类等高级功能时，也没有必要使用此类包装器。注意：使用 -fvirtual 选项时会启用进一步优化，这可避免为已在基类中定义的虚拟成员重新生成包装函数。
6.14 多继承、指针和类型检查的简单讨论
当目标脚本语言引用 C++ 对象时，它通常使用包含指针值和类型字符串的标记指针对象。例如，在 Tcl 中，C++ 指针可能被编码为这样的字符串：
_808fea88_p_Circle

一个比较常见的问题是类型标签是否可以安全地从指针中移除。例如，为了获得更好的性能，是否可以去掉所有类型标签而只使用简单的整数？
一般来说，这个问题的答案是否定的。在包装器中，所有指针都被转换为目标语言中的公共数据表示。通常，这相当于将指针转换为 void *。这意味着与指针关联的任何 C++ 类型信息都将在转换中丢失。
丢失类型信息的问题在于它需要正确支持许多高级 C++ 特性——尤其是多重继承。例如，假设您有这样的代码：
class A {
public:
  int x;
};

class B {
public:
  int y;
};

class C : public A, public B {
};

int A_function(A *a) {
  return a->x;
}

int B_function(B *b) {
  return b->y;
}

现在，考虑以下使用void * 的代码。
C *c = new C();
void *p = (void *) c;
...
int x = A_function((A *) p);
int y = B_function((B *) p);

在这段代码中，A_function() 和B_function() 都可以合法地接受 C * 类型的对象（通过继承）。但是，当如图所示使用时，其中一个函数将始终返回错误的结果。这样做的原因是，即使 p 指向类型 C 的对象，转换操作也不会像您期望的那样工作。在内部，这与C的数据表示有关。通过多重继承，来自每个基类的数据堆叠在一起。例如：
             ------------    <--- (C *),  (A *)
            |     A      |
            |------------|   <--- (B *)
            |     B      |
             ------------   

由于这种堆叠，C * 类型的指针在转换为 A * 或 B * 时可能会改变值。但是，如果您从 void * 转换，则不会发生这种调整。
类型标签的使用将所有指针标记为底层对象的真实类型。然后 SWIG 生成的包装器使用这些额外信息在继承下正确转换指针值（避免上述问题）。
一些语言模块能够通过存储指针的多个实例来解决这个问题，例如，A *，在 A 代理类中，以及C *在 C 代理类中。然后可以通过选择 要使用的正确 void * 指针来进行正确的转换，并保证在转换为 void 指针并返回到相同类型时不会丢失任何类型信息：
C *c = new C();
void *p = (void *) c;
void *pA = (void *) c;
void *pB = (void *) c;
...
int x = A_function((A *) pA);
int y = B_function((B *) pB);

实际上，指针在目标语言代理类中被保存为整数。
6.15 包装重载的函数和方法
在许多语言模块中，SWIG 为重载的函数、方法和构造函数提供部分支持。例如，如果您为 SWIG 提供这样的重载函数：
void foo(int x) {
  printf("x is %d\n", x);
}
void foo(char *x) {
  printf("x is '%s'\n", x);
}

该功能以完全自然的方式使用。例如：
>>> foo(3)
x is 3
>>> foo("hello")
x is 'hello'
>>>

对于方法和构造函数，重载以类似的方式工作。例如，如果您有此代码，
class Foo {
public:
  Foo();
  Foo(const Foo &);   // Copy constructor
  void bar(int x);
  void bar(char *s, int y);
};

它可能像这样使用
>>> f = Foo() # 创建一个 Foo 
>>> f.bar(3) 
>>> g = Foo(f) # 复制 Foo 
>>> f.bar("hello", 2)

6.15.1 调度函数生成
由于脚本语言的动态特性，重载函数和方法的实现有些复杂。与在编译时绑定重载方法的 C++ 不同，SWIG 必须确定正确的函数作为脚本语言目标的运行时检查。由于某些脚本语言的无类型特性，这种检查变得更加复杂。例如，在 Tcl 中，所有类型都只是字符串。因此，如果你有两个这样的重载函数，
void foo(char *x); 
void foo(int x);

检查参数的顺序起着相当关键的作用。
对于静态类型语言，SWIG 使用该语言的方法重载机制。为了实现脚本语言的重载，SWIG 生成了一个调度函数来检查传递的参数数量及其类型。为了创建这个函数，SWIG 首先检查所有重载的方法并根据以下规则对它们进行排名：
1. 必需参数的数量。方法按所需参数数量的增加进行排序。
2. 参数类型优先级。所有 C++ 数据类型都分配了一个数字类型优先级值（由语言模块确定）。
Type              Precedence
----------------  ----------
TYPE *            0     (High)
void *            20
Integers          40
Floating point    60
char              80
Strings           100   (Low)

使用这些优先级值，具有相同数量的必需参数的重载方法按优先级值的增加顺序排序。
这听起来可能很混乱，但举个例子会有所帮助。考虑以下重载方法的集合：
void foo(double);
void foo(int);
void foo(Bar *);
void foo();
void foo(int x, int y, int z, int w);
void foo(int x, int y, int z = 3);
void foo(double x, double y);
void foo(double x, Bar *z);

第一条规则只是根据所需的参数计数对函数进行排序。这将产生以下列表：
rank
-----
[0]   foo()
[1]   foo(double);
[2]   foo(int);
[3]   foo(Bar *);
[4]   foo(int x, int y, int z = 3);
[5]   foo(double x, double y)
[6]   foo(double x, Bar *z)
[7]   foo(int x, int y, int z, int w);

第二条规则，通过查看参数类型优先级值来简单地优化排名。
rank
----- 
[0] foo() 
[1] foo(Bar *); 
[2] foo(int); 
[3] foo(double); 
[4] foo(int x, int y, int z = 3); 
[5] foo(double x, Bar *z) 
[6] foo(double x, double y) 
[7] foo(int x, int y, int z, int w);

最后，为了生成调度函数，传递给重载方法的参数只是按照它们在此排名中出现的相同顺序进行检查。
如果您仍然感到困惑，请不要担心——SWIG 可能正在做正确的事情。
6.15.2 重载中的歧义
遗憾的是，SWIG 无法支持所有可能的有效 C++ 重载使用。考虑以下示例：
void foo(int x); 
void foo(long x);

在 C++ 中，这是完全合法的。但是，在脚本语言中，一般只有一种整数对象。因此，您选择这些功能中的哪一个？显然，仅通过查看整数本身的值（ int和long甚至可能具有相同的精度）无法真正做出区分。因此，当 SWIG 遇到这种情况时，它可能会为脚本语言生成如下警告消息：
example.i:4: Warning 509: Overloaded method foo(long) effectively ignored,
example.i:3: Warning 509: as it is shadowed by foo(int).

或者对于像 Java 这样的静态类型语言：
example.i:4: Warning 516: Overloaded method foo(long) ignored,
example.i:3: Warning 516: using foo(int) instead.
at example.i:3 used.

这意味着第二个重载函数将无法从脚本接口访问，或者该方法根本不会被包装。这样做是因为 SWIG 不知道如何从早期的方法中消除歧义。
已知在以下情况下会出现二义性问题：
- 整数转换。在某些语言中，无法消除 int、long 和 short 等数据类型的歧义。如上所示。
- 浮点数转换。float 和 double 在某些语言中无法消除歧义。
- 指针和引用。例如，Foo * 和 Foo & 。
- 指针和数组。例如，Foo * 和 Foo [4] 。
- 指针和实例。例如，Foo 和 Foo * 。注意：SWIG 将所有实例转换为指针。
- 预选赛。例如，const Foo * 和 Foo *。
- 默认与非默认参数。例如，foo(int a, int b) 和 foo(int a, int b = 3)。
当出现歧义时，方法按照它们出现在接口文件中的相同顺序进行检查。因此，较早的方法会影响稍后出现的方法。
包装重载函数时，您可能会收到如下警告消息：
example.i:3: Warning 467: Overloaded foo(int) not supported (incomplete type checking rule - 
no precedence level in typecheck typemap for 'int').

这个错误意味着目标语言模块支持重载，但由于某种原因，没有可用于生成工作调度函数的类型检查规则。由此产生的行为是未定义的。如果这是由于 SWIG 提供的类型映射之一造成的，您应该将此作为错误报告给 SWIG 错误跟踪数据库。
如果您收到如下错误消息，
foo.i:6. Overloaded declaration ignored.  Spam::foo(double )
foo.i:5. Previous declaration is Spam::foo(int )
foo.i:7. Overloaded declaration ignored.  Spam::foo(Bar *, Spam *, int )
foo.i:5. Previous declaration is Spam::foo(int )

这意味着目标语言模块尚未实现对重载函数和方法的支持。解决问题的唯一方法是阅读下一节。
6.15.3 歧义解决和重命名
如果在重载解析中出现歧义，或者模块不允许重载，则有几种策略可以处理该问题。首先，您可以告诉 SWIG 忽略其中一种方法。这很简单---只需使用 %ignore 指令。例如：
%ignore foo(long);

void foo(int);
void foo(long);       // Ignored.  Oh well.

另一种选择是重命名其中一个方法。这可以使用%rename来完成。例如：
%rename("foo_short") foo(short); 
%rename(foo_long) foo(long); 

void foo(int); 
void foo(short); // 作为 foo_short() 访问
void foo(long); // 作为 foo_long() 访问

请注意，新名称周围的引号是可选的，但是，如果新名称是 C/C++ 关键字，则它们将是必不可少的，以避免解析错误。％ignore 和 ％rename 指令匹配的声明的能力都相当强大。当以简单的形式使用时，它们适用于全局函数和方法。例如：
/* 前向重命名声明 */ 
%rename(foo_i) foo(int); 
%rename(foo_d) foo(double); 
... 
void foo(int); // 变成 'foo_i' 
void foo(char *c); // 保持 'foo'（未重命名）

class Spam { 
public: 
  void foo(int); // 变成 'foo_i' 
  void foo(double); // 变成 'foo_d' 
  ... 
};

如果您只想将重命名应用于某个范围，则可以使用 C++ 范围解析运算符 (::)。例如：
%rename(foo_i)::foo(int); // 只在全局范围内重命名 foo(int)。
                          // (不会重命名类成员) 

%rename(foo_i) Spam::foo(int); // 只在 Spam 类中重命名 foo(int)

当重命名运算符应用于 Spam::foo(int) 中的类时，它会应用于该类和所有派生类。这可用于在整个类层次结构中应用一致的重命名，只需几个声明。例如：
%rename(foo_i) Spam::foo(int);
%rename(foo_d) Spam::foo(double);

class Spam {
public:
  virtual void foo(int);      // Renamed to foo_i
  virtual void foo(double);   // Renamed to foo_d
  ...
};

class Bar : public Spam {
public:
  virtual void foo(int);      // Renamed to foo_i
  virtual void foo(double);   // Renamed to foo_d
  ...
};

class Grok : public Bar {
public:
  virtual void foo(int);      // Renamed to foo_i
  virtual void foo(double);   // Renamed to foo_d
  ...
};

类定义本身中也可以包含 %rename 规范。例如：
class Spam {
  %rename(foo_i) foo(int);
  %rename(foo_d) foo(double);
public:
  virtual void foo(int);      // Renamed to foo_i
  virtual void foo(double);   // Renamed to foo_d
  ...
};

class Bar : public Spam {
public:
  virtual void foo(int);      // Renamed to foo_i
  virtual void foo(double);   // Renamed to foo_d
...
};

在这种情况下，%rename 指令仍然应用于整个继承层次结构，但不再需要显式指定类前缀 Spam::。
一种特殊形式的 %rename 可用于仅对类成员（所有类的）应用重命名：
%rename(foo_i) *::foo(int); // 仅当 foo(int) 出现在类中时才重命名。

注意：*::语法是非标准的 C++，但 '*' 是一个匹配任何类名的通配符（我们想不出更好的选择，所以如果你有更好的主意，请发送电子邮件到 swig-devel mailing list。
虽然这个讨论主要集中在 %rename，但是所有的规则也可以同样用于 %ignore。例如：
%ignore foo(double);          // Ignore all foo(double)
%ignore Spam::foo;            // Ignore foo in class Spam
%ignore Spam::foo(double);    // Ignore foo(double) in class Spam
%ignore *::foo(double);       // Ignore foo(double) in all classes

当应用于基类时，%ignore强制派生类中的所有定义消失。例如，%ignore Spam::foo(double) 将从 Spam 和从Spam派生的所有类中消除 foo(double) 。
关于 %rename 和 %ignore 的注意事项：
- 由于%rename声明用于提前声明重命名，因此可以将其放置在接口文件的开头。这使得可以应用一致的名称解析而无需修改头文件。例如：
%module foo 

/* 重命名这些重载函数 */ 
%rename(foo_i) foo(int); 
%rename(foo_d) foo(double); 

%include "header.h"

- 作用域限定符 (::) 也可用于简单名称。例如：
%rename(bar)::foo; // 仅在全局范围内将 foo 重命名为 bar 
%rename(bar) Spam::foo; // 仅在 Spam 类中将 foo 重命名为 bar 
%rename(bar) *::foo; // 仅在类中重命名 foo

- 名称匹配尝试找到定义的最具体的匹配。诸如 Spam::foo 之类的限定名称始终比非限定名称 foo 具有更高的优先级。Spam::foo 的 优先级高于 *::foo 并且 *::foo 的优先级高于 foo。在相同范围级别内，参数化名称比未参数化名称具有更高的优先级。然而，具有范围限定符的非参数化名称比全局范围内的参数化名称具有更高的优先级（例如，Spam::foo 的重命名优先于 foo(int) 的重命名 ）。
- %rename 指令的定义顺序并不重要，只要它们出现在要重命名的声明之前。因此，以下说法没有区别：
%rename(bar) foo; 
%rename(foo_i) Spam::foo(int); 
%rename(Foo) Spam::foo;

还有这个
%rename(Foo) Spam::foo; 
%rename(bar) foo; 
%rename(foo_i) Spam::foo(int);

（声明不存储在链表中，顺序无关紧要）。当然，如果提供了完全相同的名称、范围和参数，重复的 %rename 指令将更改先前 %rename 指令的设置。
- 对于为多个基类定义重命名规则的多重继承，使用在类层次结构的深度优先遍历中找到的第一个重命名规则。
- 姓名匹配规则严格遵循会员资格规则。例如，如果你有一个这样的类：
class Spam { 
public: 
  ... 
  void bar() const; 
  ... 
};

声明
%rename(name) Spam::bar();

将不适用，因为没有不合格的成员 bar()。当限定符正确匹配时，以下将适用：
%rename(name) Spam::bar() const;

一个经常被忽视的 C++ 特性是类可以定义两个不同的重载成员，它们仅在限定符上有所不同，如下所示：
class Spam {
public:
  ...
  void bar();         // Unqualified member
  void bar() const;   // Qualified member
  ...
};

然后可以使用 %rename 分别针对每个重载方法。例如，我们可以在目标语言中给它们单独的名称：
%rename(name1) Spam::bar();
%rename(name2) Spam::bar() const;

同样，如果您只想忽略其中一个声明，请使用 %ignore 和完整限定。例如，以下指令会告诉 SWIG 忽略上面 bar() 的 const 版本：
%ignore Spam::bar() const;   // Ignore bar() const, but leave other bar() alone

- 当前不执行解析以匹配函数参数。这意味着函数参数类型必须完全匹配。例如，命名空间限定符和 typedef 将不起作用。typedef 的以下用法说明了这一点：
typedef int Integer;

%rename(foo_i) foo(int);

class Spam {
public:
  void foo(Integer);  // Stays 'foo' (not renamed)
};
class Ham {
public:
  void foo(int);      // Renamed to foo_i
};

- 在包装具有默认参数的方法时，名称匹配规则还使用默认参数进行更精细的控制。回想一下，带有默认参数的方法被包装，就好像已经解析了等效的重载方法一样（默认参数部分）。让我们考虑以下示例类：
class Spam { 
public: 
  ... 
  void bar(int i=-1, double d=0.0); 
  ... 
};

以下 %rename 将完全匹配并应用于所有目标语言重载方法，因为具有默认参数的声明与包装方法完全匹配：
%rename(newbar) Spam::bar(int i=-1, double d=0.0);

无论指定了多少参数，都可以从目标语言使用新名称调用 C++ 方法，例如： newbar(2, 2.0)、newbar(2) 或 newbar()。但是，如果 %rename 不包含默认参数，它将仅适用于单个等效的目标语言重载方法。所以如果我们有：
%rename(newbar) Spam::bar(int i, double d);

当提供了两个参数或原始名称为bar(2)（一个参数）或bar() （无参数）时，必须使用新名称newbar(2, 2.0) 从目标语言调用 C++ 方法。事实上，可以在等效的重载方法上使用 %rename 来重命名所有等效的重载方法：
%rename(bar_2args) Spam::bar(int i, double d); 
%rename(bar_1arg) Spam::bar(int i); 
%rename(bar_default) Spam::bar();

类似地，可以使用 %ignore 选择性地忽略额外的重载方法。
兼容性注意：在SWIG-1.3.23，％rename 指令同时为改变包装方式与默认参数介绍默认参数匹配规则 进行了介绍。
6.15.4 重载注释
SWIG-1.3.14 中首次添加了对重载方法的支持。与类似工具相比，该实现有些不寻常。例如，在 SWIG 中，声明出现的顺序在很大程度上是无关紧要的。此外，SWIG 不依赖于试验执行或异常处理来确定要调用的方法。
在内部，重载机制完全由目标语言模块配置。因此，重载支持的程度可能因语言而异。作为一般规则，Java 等静态类型语言能够提供比 Perl、Python、Ruby 和 Tcl 等动态类型语言更多的支持。
6.16 包装重载运算符
C++ 重载运算符声明可以被包装。例如，考虑这样一个类：
class Complex {
private:
  double rpart, ipart;
public:
  Complex(double r = 0, double i = 0) : rpart(r), ipart(i) { }
  Complex(const Complex &c) : rpart(c.rpart), ipart(c.ipart) { }
  Complex &operator=(const Complex &c) {
    rpart = c.rpart;
    ipart = c.ipart;
    return *this;
  }
  Complex operator+(const Complex &c) const {
    return Complex(rpart+c.rpart, ipart+c.ipart);
  }
  Complex operator-(const Complex &c) const {
    return Complex(rpart-c.rpart, ipart-c.ipart);
  }
  Complex operator*(const Complex &c) const {
    return Complex(rpart*c.rpart - ipart*c.ipart,
                   rpart*c.ipart + c.rpart*ipart);
  }
  Complex operator-() const {
    return Complex(-rpart, -ipart);
  }
  double re() const { return rpart; }
  double im() const { return ipart; }
};

当运算符声明出现时，它们的处理 方式与常规方法完全相同。但是，这些方法的名称设置为字符串，例如“ operator + ”或“ operator - ”。这些名称的问题在于它们在大多数脚本语言中都是非法标识符。例如，你不能只在 Python 中创建一个名为“ operator + ”的方法——没有任何方法可以调用它。
一些语言模块已经知道如何自动处理某些运算符（将它们映射到目标语言中的运算符）。但是，它的底层实现实际上是使用 %rename 指令以非常通用的方式进行管理的。例如，在 Python 中，使用了与此类似的声明：
%rename(__add__) Complex::operator+;

这将 + 运算符绑定到一个名为 __add__ 的方法（它与用于实现 Python + 运算符的名称很方便）。在内部，为包装运算符生成的包装器代码类似于以下伪代码：
_wrap_Complex___add__(args) { 
  ... get args ... 
  obj->operator+(args); 
  ... 
}

在目标语言中使用时，现在可以正常使用重载运算符。例如：
>>> a = Complex(3, 4) 
>>> b = Complex(5, 2) 
>>> c = a + b # 调用 __add__ 方法

重要的是要意识到这里没有任何神奇的事情发生。％rename 指令真的只选择一个有效的方法名。如果你这样写：
%rename(add) operator+;

生成的脚本界面可能像这样工作：
a = Complex(3, 4) 
b = Complex(5, 2) 
c = a.add(b) # 调用 a.operator+(b)

所描述的处理重载函数的所有技术也适用于运算符。例如：
%ignore Complex::operator=;             // Ignore = in class Complex
%ignore *::operator=;                   // Ignore = in all classes
%ignore operator=;                      // Ignore = everywhere.

%rename(__sub__) Complex::operator-; 
%rename(__neg__) Complex::operator-();  // Unary - 

这个例子的最后一部分说明了如何处理 operator- 方法的多个定义。
以这种方式处理运算符通常很简单。但是，有一些微妙的问题需要牢记：
- 在 C++ 中，定义不同版本的运算符来解释不同的类型是相当常见的。例如，一个类也可能包含一个像这样的友元函数：
class Complex {
public:
  friend Complex operator+(Complex &, double);
};
Complex operator+(Complex &, double)

SWIG 只是忽略所有友元声明。此外，它不知道如何将关联的 operator+ 与类相关联（因为它不是类的成员）。
仍然可以为该运算符制作包装器，但您必须像处理普通函数一样处理它。例如：
%rename(add_complex_double) operator+(Complex &, double);

- 默认情况下会忽略某些运算符。例如，忽略 new 和 delete 运算符以及转换和索引运算符。显示如下警告：
example.i:12: Warning 503: Can't wrap 'operator []' unless renamed to a valid identifier.

- 由于 C++ 实现的差异，索引运算符 operator[] 特别难以重载。具体来说，其他语言中的 get 和 set 操作符通常被分成两种方法，这样可以将额外的逻辑打包到操作中；C# 使用 this[type key] { get { ... } set { ... }}，Python 使用__getitem__和__setitem__等。在 C++ 中，如果 operator[] 的返回类型是引用并且方法是 const，则它通常表示 setter 和 getter 通常是一个 const 函数，按值返回一个对象。在没有任何硬性规则和可能有多个索引运算符的情况下，用户可以通过使用 %rename 来选择要使用的 getter 和 setter，如前面所示。
- 某些 C++ 运算符的语义可能与目标语言中的语义不匹配。
6.17 类扩展
可以使用 %extend 指令将新方法添加到类中。该指令主要与代理类结合使用，以向现有类添加附加功能。例如 ：
%module vector
%{
#include "vector.h"
%}

class Vector {
public:
  double x, y, z;
  Vector();
  ~Vector();
  ... bunch of C++ methods ...
  %extend {
    char *__str__() {
      static char temp[256];
      sprintf(temp, "[ %g, %g, %g ]", $self->x, $self->y, $self->z);
      return &temp[0];
    }
  }
};

这段代码向我们的类添加了一个__str__方法，用于生成对象的字符串表示。在 Python 中，这样的方法允许我们使用print 命令打印对象的值。
>>>
>>> v = Vector();
>>> v.x = 3
>>> v.y = 4
>>> v.z = 0
>>> print(v)
[ 3.0, 4.0, 0.0 ]
>>>

C++ 'this' 指针经常需要访问成员变量、方法等。$self 特殊变量应该用在任何可以使用 'this' 的地方。上面的示例演示了如何访问成员变量。请注意，由 $self 取消引用的成员必须是公共成员，因为代码最终会生成到全局函数中，因此无法访问非公共成员。C++ 方法中存在的隐式 “this” 指针在 %extend方法中不存在。为了访问扩展类或其基类中的任何内容，需要显式的“this”。以下示例显示了如何访问基类成员：
struct Base {
  virtual void method(int v) {
    ...
  }
  int value;
};
struct Derived : Base {
};
%extend Derived {
  virtual void method(int v) {
    $self->Base::method(v); // akin to this->Base::method(v);
    $self->value = v;       // akin to this->value = v;
    ...
  }
}

如果在 %extend 块中使用，以下特殊变量会被扩展：$name、$symname、$overname、$decl、$fulldecl、$parentclassname 和 $parentclasssymname。在 特殊的变量部分提供了更多的这些特殊变量的信息。
所述 ％extend 指令如下所有的相同的约定与 C 结构的使用。有关更多详细信息，请参阅向 C 结构添加成员函数部分。
兼容性注意：在SWIG1.1，％extend 指令是 ％addmethods 的一个新名字。由于 %addmethods 可用于扩展结构，而不仅仅是方法，因此选择了这个更合适的指令名称。
6.18 模板
模板类型名称可以出现在接口文件中需要类型的任何位置。例如：
void foo(vector<int> *a, int n); 
void bar(list<int, 100> *x);

非类型参数的使用有一些限制。支持简单的文字，一些常量表达式也是如此。但是，SWIG 目前不支持在常量表达式中使用 '<' 和 '>'（尽管是 '<=' 和 '>='）。例如：
void bar(list<int, 100> *x); // OK 
void bar(list<int, 2*50> *x); // OK 
void bar(list<int, (2>1 ? 100 : 50)> *x) // 不支持

类型系统足够聪明，可以找出您可能会尝试使用 typedef 玩的聪明游戏。例如，考虑以下代码：
typedef int Integer;
void foo(vector<int> *x, vector<Integer> *y);

在这种情况下，vector<Integer> 与 vector<int> 的类型完全相同。foo() 的包装器将接受任一变体。
从 SWIG-1.3.7 开始，还可以包装简单的 C++ 模板声明。SWIG-1.3.12 大大扩展了早期的实现。在进一步讨论这个之前，你需要了解一些关于模板包装的事情。首先，裸 C++ 模板没有定义任何类型的可运行对象代码，SWIG 通常可以为其创建包装器。因此，为了包装模板，您需要提供有关特定模板实例的 SWIG 信息（例如，vector<int>、array<double>等）。其次，像vector<int>这样的实例化名称在大多数目标语言中通常不是有效的标识符名称。因此，您需要为模板实例化一个更合适的名称，例如intvector 创建包装器时。
为了说明，请看以下模板定义：
template<class T> class List {
private:
    T *data;
    int nitems;
    int maxitems;
public:
    List(int max) {
      data = new T [max];
      nitems = 0;
      maxitems = max;
    }
    ~List() {
      delete [] data;
    };
    void append(T obj) {
      if (nitems < maxitems) {
        data[nitems++] = obj;
      }
    }
    int length() {
      return nitems;
    }
    T get(int n) {
      return data[n];
    }
};

就其本身而言，此模板声明是无用的——SWIG 只是忽略它，因为除非提供T的定义，否则它不知道如何生成任何代码。
为特定模板实例化创建包装器的一种方法是直接提供类的扩展版本，如下所示：
%rename(intList) List<int>;       // Rename to a suitable identifier
class List<int> {
private:
    int *data;
    int nitems;
    int maxitems;
public:
    List(int max);
    ~List();
    void append(int obj);
    int length();
    int get(int n);
};

％rename 指令需要给模板类在目标语言中的合适的标识符名称（大多数语言不承认 C++ 模板语法作为一个有效的类名）。其余代码与出现在普通类定义中的代码相同。
由于模板的手动扩展很快就会过时，因此 %template 指令可用于创建模板类的实例化。从语义上讲，%template 只是一个快捷方式——它以与上
面显示的完全相同的方式扩展模板代码。这里有些例子：
/* 实例化几个不同版本的模板 */ 
%template(intList) List<int>; 
%template(doubleList) List<double>;

%template() 的参数是目标语言中实例化的名称。您选择的名称不应与接口文件中的任何其他声明冲突，只有一个例外——模板名称可以与 typedef 声明的名称相匹配。例如：
%template(intList) List<int>;
...
typedef List<int> intList;    // OK

SWIG 还可以使用类似的技术为函数模板生成包装器。例如：
// Function template
template<class T> T max(T a, T b) { return a > b ? a : b; }

// Make some different versions of this function
%template(maxint) max<int>;
%template(maxdouble) max<double>;

在这种情况下，maxint和maxdouble成为函数特定实例的唯一名称。
提供给 %template 的参数数量应该与原始模板定义中的数量相匹配。支持模板默认参数。例如：
template vector<typename T, int max=100> class vector {
...
};

%template(intvec) vector<int>;           // OK
%template(vec1000) vector<int, 1000>;     // OK

％template 指令不应该被用于包装不止一次在同一范围内实例化的相同的模板。这将产生错误。例如：
%template(intList) List<int>; 
%template(Listint) List<int>; // 错误。模板已包装。

导致此错误的原因是模板扩展导致具有相同名称的两个相同类。这会产生符号表冲突。此外，为了减少代码膨胀的可能性，只包装一次特定的实例化可能更有效。
由于类型系统知道如何处理 typdef，通常不需要为那些被计算的类型名字，实例化不同版本的模板。作为例子请看一下代码：
%template(intList) vector<int>;
typedef int Integer;
...
void foo(vector<Integer> *x);

在这种情况下，vector<Integer>与 vector<int> 的类型完全相同。Vector<Integer> 的任何使用都映射回之前创建的 vector<int> 的实例化。因此，没有必要为Integer类型实例化一个新类 （这样做是多余的，只会导致代码膨胀）。
当使用 %template 实例化模板时，有关该类的信息由 SWIG 保存并在程序中的其他地方使用。例如，如果你写了这样的代码，
... 
%template(intList) List<int>; 
... 
class UltraList : public List<int> { 
  ... 
};

然后 SWIG 知道 List<int> 已经被包装为一个名为 intList 的类并安排正确处理继承。另一方面，如果对 List<int> 一无所知 ，您将收到类似于以下内容的警告消息：
example.h:42: Warning 401. Nothing known about class 'List<int >'. Ignored. 
example.h:42: Warning 401. Maybe you forgot to instantiate 'List<int >' using %template.

如果模板类继承自另一个模板类，则需要确保在派生类之前实例化基类。例如：
template<class T> class Foo {
...
};

template<class T> class Bar : public Foo<T> {
...
};

// Instantiate base classes first 
%template(intFoo) Foo<int>;
%template(doubleFoo) Foo<double>;

// Now instantiate derived classes
%template(intBar) Bar<int>;
%template(doubleBar) Bar<double>;

顺序很重要，因为 SWIG 使用实例化名称在生成的包装器代码中正确设置继承层次结构（并且需要在派生类之前包装基类）。别担心——如果您弄错了顺序，SWIG 应该会生成一条警告消息。
有时，您可能需要告诉 SWIG 有关由模板定义但不应该被包装的基类。由于 SWIG 无法为此目的自动实例化模板，因此您必须手动进行。为此，只需使用空模板实例化，即没有名称的 %template。例如：
// Instantiate traits<double, double>, but don't wrap it.
%template() traits<double, double>;

如果您必须为许多不同类型实例化许多不同的类，您可以考虑编写一个 SWIG 宏。例如：
%define TEMPLATE_WRAP(prefix, T...) 
%template(prefix ## Foo) Foo<T >; 
%template(prefix ## Bar) Bar<T >; 
... 
%enddef 

TEMPLATE_WRAP(int, int) 
TEMPLATE_WRAP(double, double) 
TEMPLATE_WRAP(String, char *) 
TEMPLATE_WRAP(PairStringInt, std::pair<string, int>) 
...

请注意对类型 T 使用 vararg 宏。如果不使用它，则上一个示例中模板化类型中的逗号将无法使用。
SWIG 模板机制确实支持特化。例如，如果你定义一个这样的类，
template<> class List<int> {
private:
    int *data;
    int nitems;
    int maxitems;
public:
    List(int max);
    ~List();
    void append(int obj);
    int length();
    int get(int n);
};

那么每当用户展开 List<int> 时，SWIG 都会使用此代码 。在实践中，这对底层包装器代码的影响可能很小，因为专门化通常用于提供稍微修改的方法体（SWIG 会忽略它们）。但是，特殊的 SWIG 指令（例如 %typemap、%extend 等）可以附加到专门化以提供特定类型的自定义。
SWIG 部分支持部分模板特化。例如，此代码定义了模板参数为指针时应用的模板。
template<class T> class List<T*> {
private:
    T *data;
    int nitems;
    int maxitems;
public:
    List(int max);
    ~List();
    void append(int obj);
    int length();
    T get(int n);
};

SWIG 支持模板显式特化和部分特化。考虑：
template<class T1, class T2> class Foo { };  // (1) primary template
template<> class Foo<double *, int *> { };    // (2) explicit specialization
template<class T1, class T2> class Foo<T1, T2 *> { };//(3) partial specialization

SWIG 能够正确匹配显式实例化：
Foo<double *, int *>      // 显式特化匹配 (2)

SWIG 实现了模板参数推导，以便以下部分特化示例可以像使用 C++ 编译器一样工作：
Foo<int *, int *>         // 偏特化匹配(3)
Foo<int *, const int *>   // 偏特化匹配(3)
Foo<int *, int **>        // 偏特化匹配(3)

支持成员函数模板。基本原理与普通模板相同——除非您提供有关类型的更多信息，否则 SWIG 无法创建包装器。例如，具有成员模板的类可能如下所示：
class Foo { 
public: 
  template<class T> void bar(T x, T y) { ... }; 
  ... 
};

要扩展模板，只需在类中使用 %template 即可。
class Foo { 
public: 
  template<class T> void bar(T x, T y) { ... }; 
  ... 
  %template(barint) bar<int>; 
  %template(bardouble) bar<double>; 
};

或者，如果您想单独保留原始类定义，只需执行以下操作：
class Foo {
public:
  template<class T> void bar(T x, T y) { ... };
  ...
};
...
%extend Foo {
  %template(barint)    bar<int>;
  %template(bardouble) bar<double>;
};

或者干脆
class Foo {
public:
  template<class T> void bar(T x, T y) { ... };
  ...
};
...

%template(bari) Foo::bar<int>;
%template(bard) Foo::bar<double>;

在这种情况下，不需要 %extend 指令，并且 %template 完成完全相同的工作，即，它向 Foo 类添加了两个新方法。
注意：由于模板的处理方式， %template 指令必须始终出现在要扩展的模板定义之后。
现在，如果您的目标语言支持重载，您甚至可以尝试
%template(bar) Foo::bar<int>; 
%template(bar) Foo::bar<double>;

并且由于两个新包装的方法具有相同的名称“bar”，它们将被重载，并且在调用时，将根据参数类型调度正确的方法。
当与成员一起使用时，%template指令可以放在另一个模板类中。这是一个有点反常的例子：
// 一个模板
template<class T> class Foo { 
public: 
  // 一个成员模板
  template<class S> T bar(S x, S y) { ... }; 
  ... 
}; 

// 展开几个成员模板
%extend Foo { 
  %template(bari) bar<int>; 
  %template(bard) bar<double>; 
} 

// 为模板创建一些包装器
%template(Fooi) Foo<int>; 
%template(Food) Foo<double>;

神奇的是，你会发现Foo的每次展开都有成员函数 bari() 和bard() 被添加进去.
成员模板的一个常见用途是为复制和转换定义构造函数。例如：
template<class T1, class T2> struct pair { 
  T1 first; 
  T2 second；
  pair() : first(T1()), second(T2()) { } 
  pair(const T1 &x, const T2 &y) : first(x), second(y) { } 
  template<class U1, class U2> pair (const pair<U1, U2> &x) 
                                       : first(x.first), second(x.second) { } 
};

这个声明对于 SWIG 来说是完全可以接受的，但是构造函数模板将被忽略，除非你明确地扩展它。为此，您可以在模板类本身中扩展构造函数的几个版本。例如：
%extend pair { 
  %template(pair) pair<T1, T2>; // 生成默认的拷贝构造函数
};

以这种方式使用 %extend 时，请注意您仍然可以使用原始模板定义中的模板参数。
或者，您可以在选定的实例中扩展构造函数模板。例如：
// 实例化几个版本
%template(pairii) pair<int, int>; 
%template(pairdd) pair<double, double>; 

// 只创建一个默认构造函数
%extend pair<int, int> { 
  %template(paird) pair<int, int>; // 默认构造函数
}; 

// 创建默认和转换构造函数
%extend pair<double, double> { 
  %template(paird) pair<double, dobule>; // 默认构造函数
  %template(pairc) pair<int, int>; // 转换构造函数
};

如果您的目标语言支持重载，那么您可以尝试：
// 创建默认和转换构造函数
%extend pair<double, double> { 
  %template(pair) pair<double, dobule>; // 默认构造函数
  %template(pair) pair<int, int>; // 转换构造函数
};

在这种情况下，默认构造函数和转换构造函数具有相同的名称。因此，SWIG 将重载它们并定义一个唯一的可见构造函数，它将根据参数类型调度正确的调用。
如果所有这些还不够，并且您真的想让某人的头爆炸，那么 SWIG 指令（例如%rename、 %extend和%typemap ）可以直接包含在模板定义中。例如：
// 文件 : list.h 
template<class T> class List { 
  ... 
public: 
  %rename(__getitem__) get(int); 
  List(int max);
  ~List()；
  ... 
  T get(int index); 
  %extend { 
    char *__str__() { 
      /* 进行字符串表示 */ 
      ... 
    } 
  } 
};

在此示例中，额外的 SWIG 指令传播到 每个模板实例化。
也可以将这些声明与模板类分开。例如：
rename(__getitem__) List::get;
%extend List {
  char *__str__() {
    /* Make a string representation */
    ...
  }
  /* Make a copy */
  T *__copy__() {
    return new List<T>(*$self);
  }
};

...
template<class T> class List {
    ...
    public:
    List() { }
    T get(int index);
    ...
};

当 %extend 与类定义分离时，使用与类定义中提供的模板参数相同的模板参数是合法的。这些在模板展开时被替换。此外，%extend 指令可用于向特定实例添加其他方法。例如：
%template(intList) List<int>;

%extend List<int> {
    void blah() {
          printf("Hey, I'm an List<int>!\n");
    }
};

SWIG 甚至支持重载的模板化函数。像往常一样， %template指令用于包装模板化函数。例如：
template<class T> void foo(T x) { };
template<class T> void foo(T x, T y) { };

%template(foo) foo<int>;

这将生成两个重载包装器方法，第一个会取一个简单的整数作为一个参数，而第二个则用两个整数参数。
甚至可以使用模板方法通过 %extend 扩展类，例如：
%include <std_string.i> 

%inline %{ 
class ExtendMe { 
public: 
  template <typename T> 
  T do_stuff_impl(int a, T b, double d) { 
    return b; 
  } 
}; 
%} 

%extend ExtendMe { 
  template<typename T> 
  T do_overloaded_stuff(T b) { 
    return $self->do_stuff_impl(0, b, 4.0); 
  } 
} 
%template(do_overloaded_stuff) ExtendMe::do_overloaded_stuff<std::string>; 
%template(do_overloaded_stuff) ExtendMe::do_overloaded_stuff<double>;

包装好的 ExtendMe 类将有两个（重载）方法，称为do_overloaded_stuff。
兼容性说明：在 3.0.12 版本中添加了使用模板方法扩展类
毋庸置疑，SWIG 的模板支持提供了大量打破宇宙的机会。也就是说，重要的最后一点是SWIG 不会对模板执行广泛的错误检查！具体来说，SWIG 不执行类型检查，也不检查模板声明的实际内容是否有意义。由于 C++ 编译器在编译生成的包装器文件时会检查这一点，因此 SWIG 没有实际理由复制此功能。
由于 SWIG 的模板支持不执行类型检查，因此 %template 可以早在模板声明之后使用。在声明模板参数之前，您可以并且很少必须使用 %template。例如：
%include <std_string.i>

%inline %{
class ExtendMe {
public:
  template <typename T>
  T do_stuff_impl(int a, T b, double d) {
    return b;
  }
};
%}

%extend ExtendMe {
  template<typename T>
  T do_overloaded_stuff(T b) {
    return $self->do_stuff_impl(0, b, 4.0);
  }
}
%template(do_overloaded_stuff) ExtendMe::do_overloaded_stuff<std::string>;
%template(do_overloaded_stuff) ExtendMe::do_overloaded_stuff<double>;

兼容性说明：模板支持的第一个实现在很大程度上依赖于预处理器中的宏扩展。模板已更紧密地集成到 SWIG-1.3.12 中的解析器和类型系统中，不再使用预处理器。在模板扩展中依赖预处理功能的代码将不再有效。但是，SWIG 仍然允许使用 # 运算符从模板参数生成字符串。
兼容性说明：在 SWIG 的早期版本中， %template 指令引入了一个新的类名。然后可以将此名称与其他指令一起使用。例如：
%template(vectori) vector<int>; 
%extend vectori { 
    void somemethod() { } 
};

不再支持此行为。相反，您应该使用原始模板名称作为类名。例如：
%template(vectori) vector<int>; 
%extend vector<int> { 
    void somemethod() { } 
};

类似的更改适用于类型图和其他自定义功能。
6.19 命名空间
对 C++ 命名空间的支持是全面的，但默认情况下很简单，但是，一些目标语言可以通过 nspace 功能打开更高级的命名空间支持，稍后描述。未命名命名空间内的代码将被忽略，因为没有对未命名命名空间内声明的符号的外部访问。在详细介绍命名命名空间的默认实现之前，值得注意的是 C++ 命名空间的语义非常重要——尤其是在 C++ 类型系统和类机制方面。在最基本的层面上，命名空间有时用于封装通用功能。例如：
namespace math {
  double sin(double);
  double cos(double);

  class Complex {
    double im, re;
  public:
    ...
  };
  ...
};

命名空间的成员在 C++ 中通过在名称前加上命名空间前缀来访问。例如：
double x = math::sin(1.0);
double magnitude(math::Complex *c);
math::Complex c;
...

在这个级别，命名空间相对容易管理。但是，当您以其他方式使用命名空间时，事情开始变得非常难看。例如，可以使用 using从命名空间导出选择性符号。
using math::Complex;
double magnitude(Complex *c);       // Namespace prefix stripped

类似地，整个命名空间的内容可以像这样可用：
using namespace math;
double x = sin(1.0);
double magnitude(Complex *c);

或者，命名空间可以是别名：
namespace M = math;
double x = M::sin(1.0);
double magnitude(M::Complex *c);

使用这些特性的组合，可以像这样编写令人头疼的代码：
namespace A {
  class Foo {
  };
}

namespace B {
  namespace C {
    using namespace A;
  }
  typedef C::Foo FooClass;
}

namespace BIGB = B;

namespace D {
  using BIGB::FooClass;
  class Bar : public FooClass {
  }
};

class Spam : public D::Bar {
};

void evil(A::Foo *a, B::FooClass *b, B::C::Foo *c, BIGB::FooClass *d,
          BIGB::C::Foo *e, D::FooClass *f);

考虑到这种变态的可能性，很难想象每个 C++ 程序员可能希望将这样的代码包装到目标语言中。很明显，这段代码定义了三个不同的类。但是，其中一个类至少可以使用六个不同的名称访问！
SWIG 在其内部类型系统和类处理代码中完全支持 C++ 命名空间。如果您向 SWIG 提供上述代码，它将被正确解析，它将生成可编译的包装器代码，并生成一个工作脚本语言模块。但是，默认的包装行为是在目标语言中扁平化命名空间。这意味着所有命名空间的内容在生成的脚本语言模块中合并在一起。例如，如果您有这样的代码，
%module foo
namespace foo {
  void bar(int);
  void spam();
}

namespace bar {
  void blah();
}

然后 SWIG 只需在目标语言中创建三个包装函数 bar()、 spam() 和 blah()。SWIG 不会在名称前加上命名空间前缀，也不会将函数打包在任何类型的嵌套范围内。
采取这种方法有一些理由。由于 C++ 命名空间通常用于在 C++ 中定义模块，因此 SWIG 模块的可能内容与命名空间的内容之间存在自然关联。例如，假设程序员可能为每个 C++ 命名空间创建一个单独的扩展模块并不是没有道理的。在这种情况下，当模块本身已经作为目标语言中的命名空间时，为所有内容添加额外的命名空间前缀将是多余的。或者换句话说，如果您希望 SWIG 将命名空间分开，只需用自己的 SWIG 接口包装每个命名空间。
由于命名空间是扁平化的，因此在不同命名空间中定义的符号可能会在目标语言中产生名称冲突。例如：
namespace A {
  void foo(int);
}
namespace B {
  void foo(double);
}

发生此冲突时，您将收到类似于以下内容的错误消息：
example.i:26. Error. 'foo' is multiply defined in the generated target language module.
example.i:23. Previous declaration of 'foo'

要解决此错误，只需使用 %rename 消除声明的歧义。例如：
%rename(B_foo) B::foo;
...
namespace A {
  void foo(int);
}
namespace B {
  void foo(double);     // Gets renamed to B_foo
}

同样，%ignore 可用于忽略声明。
using 声明对生成的包装器代码没有任何影响。SWIG 语言模块会忽略它们，并且不会生成任何代码。然而，这些声明被使用的内部类型的系统来追踪类型名称。因此，如果您有这样的代码：
namespace A {
  typedef int Integer;
}
using namespace A;
void foo(Integer x);

SWIG 知道 Integer 与 A::Integer 相同，后者与 int 相同。
命名空间可以与模板结合使用。如有必要，可以使用 %template 指令扩展在不同命名空间中定义的模板。例如：
namespace foo {
    template<typename T> T max(T a, T b) { return a > b ? a : b; }
}

using foo::max;

%template(maxint)   max<int>;           // Okay.
%template(maxfloat) foo::max<float>;    // Okay (qualified name).

namespace bar {
    using namespace foo;
    %template(maxdouble)  max<double>;    // Okay.
}

命名空间和其他 SWIG 指令的组合可能会引入与范围相关的微妙问题。要记住的关键是所有 SWIG 生成的包装器都是在全局 命名空间中生成的。来自其他命名空间的符号总是使用完全限定名称访问——名称永远不会导入全局空间，除非接口碰巧使用 using 声明这样做。在几乎所有情况下，SWIG 都会将类型名和符号调整为完全限定。但是，这不是在函数体、类型映射、异常处理程序等代码片段中完成的。例如，请考虑以下情况：
namespace foo {
  typedef int Integer;
  class bar {
    public:
      ...
  };
}

%extend foo::bar {
  Integer add(Integer x, Integer y) {
    Integer r = x + y;        // Error. Integer not defined in this scope
    return r;
  }
};

在这种情况下，SWIG 正确解析添加的方法参数并将类型返回到foo::Integer。然而，由于函数体没有被解析，并且这样的代码是在全局命名空间中发出的，所以这段代码会产生一个关于Integer的编译器错误。要解决此问题，请确保使用完全限定名称。例如：
%extend foo::bar {
  Integer add(Integer x, Integer y) {
    foo::Integer r = x + y;        // Ok.
    return r;
  }
};

注意：SWIG 不会传播 using 声明到生成的封装代码中。如果这些声明在一个接口中出现，他们也应该出现在所有被包含在 %{ ... %} 中的头文件。换句话说，不要插入额外的 using 声明到一个 SWIG 接口，除非他们已经出现在底层的 C++ 代码中。 
注意：诸如 %{ ... % }或 %inline %{ ... %} 之类的代码包含指令，不应放置在命名空间声明中。这些指令发出的代码不会包含在命名空间中，您可能会得到非常奇怪的结果。如果您需要将命名空间与这些指令一起使用，请考虑以下事项：
// Good version
%inline %{
namespace foo {
  void bar(int) { ... }
  ...
}
%}

// Bad version.  Emitted code not placed in namespace.
namespace foo {
%inline %{
  void bar(int) { ... }   /* I'm bad */
  ...
  %}
}

注意：当 %extend 指令在命名空间内使用时，命名空间名称包含在生成的函数中。例如，如果您有这样的代码，
namespace foo { 
  class bar { 
    public: 
      %extend { 
        int blah(int x); 
      }; 
  }; 
}

添加的方法 blah() 会映射到函数int foo_bar_blah(foo::bar *self, int x)。该函数驻留在全局命名空间中。
注意：尽管命名空间在目标语言中是扁平化的，但 SWIG 生成的包装器代码遵守与输入文件中使用的命名空间相同的约定。因此，如果输入中没有符号冲突，则生成的代码中也不会有冲突。
注意：与未不定参数进行解析的方式相同，转换运算符名称必须与其定义方式完全匹配。不要改变 operator 的形式。例如，假设你有一个这样的接口：
namespace foo {
  class bar;
  class spam { 
    public: 
    ... 
    operator bar(); // 转换 spam -> bar 
    ... 
  }; 
}

以下是为成功匹配而编写该功能的方式：
%rename(tofoo) foo::spam::operator bar();

以下不起作用，因为在转换运算符名称的匹配中，不会执行命名空间解析：
%rename(tofoo) foo::spam::operator foo::bar();

但是请注意，如果操作符是在其名称中使用限定符定义的，那么该功能也必须使用它......
%rename(tofoo) foo::spam::operator bar(); // 不会匹配
%rename(tofoo) foo::spam::operator foo::bar(); // 将匹配
namespace foo { 
  class bar; 
  class spam { 
    public: 
    ... 
    operator foo::bar(); 
    ... 
  }; 
}

兼容性说明： 1.3.32 之前的 SWIG 版本在此方法中不一致。通常需要一个完全限定的名称，但在某些情况下不起作用。
注意：名称空间的扁平化仅用作基本的名称空间实现。目前没有任何目标语言模块具有任何命名空间意识。将来，语言模块可能会，也可能不会提供更高级的命名空间支持。
6.19.1 命名空间的 nspace 特性
某些目标语言提供对nspace 功能 支持。该功能可以应用于命名空间内声明的任何类、结构、联合或枚举。该功能将类型包装在命名空间的目标语言特定概念中，例如 Java 包或 C# 命名空间。请查看特定语言部分，了解您感兴趣的目标语言是否支持 nspace 功能。
下面使用以下示例为 C# 演示了该功能：
%feature("nspace") MyWorld::Material::Color;
%nspace MyWorld::Wrapping::Color; // %nspace is a macro for %feature("nspace")

namespace MyWorld {
  namespace Material {
    class Color {
    ...
    };
  }
  namespace Wrapping {
    class Color {
    ...
    };
  }
}

如果没有上面的 nspace 功能指令或 %rename ，您将收到以下警告，导致只有一个 Color类可用于目标语言：
example.i:9: Error: 'Color' is multiply defined in the generated target language module.
example.i:5: Error: Previous declaration of 'Color'

使用 nspac e功能，两个 Colo r类被包装到等效的 C# 命名空间中。然后在 C# 中对这两种类型的完全限定构造函数调用是：
MyWorld.Material.Color materialColor = new MyWorld.Material.Color(); 
MyWorld.Wrapping.Color wrappingColor = new MyWorld.Wrapping.Color();

请注意，nspace 功能不适用于仅在命名空间中声明的变量和函数。例如，以下符号不能在不重命名的情况下在目标语言中共存。这可能会在未来的版本中改变。
namespace MyWorld {
  namespace Material {
    int quantity;
    void dispatch();
  }
  namespace Wrapping {
    int quantity;
    void dispatch();
  }
}

兼容性说明： nspace 功能最初是在 SWIG-2.0.0 中引入的。
6.20 重命名命名空间中的模板类型
如前所述，当 %rename 包含参数时，参数类型必须完全匹配（不执行 typedef 或命名空间解析）。SWIG 对模板化类型的处理略有不同，并且具有额外的匹配规则，因此与非模板化类型不同，并不总是需要完全匹配。如果指定了完全限定的模板类型，它将比通用模板类型具有更高的优先级。在下面的示例中，通用模板类型重命名为 bbb，完全限定类型用于重命名为 ccc。
%rename(bbb) Space::ABC::aaa(T t);//will match but with lower precedence than ccc
%rename(ccc) Space::ABC<Space::XYZ>::aaa(Space::XYZ t);// will match but with higher 
                                                       // precedence than bbb
namespace Space {
  class XYZ {};
  template<typename T> struct ABC {
    void aaa(T t) {}
  };
}
%template(ABCXYZ) Space::ABC<Space::XYZ>;

现在应该很明显，有很多方法可以使用 %rename 实现重命名。下面的两个例子证明了这一点，它们实际上与上面的例子相同。下面显示了如何将 %rename 放置在命名空间中。
namespace { 
  %rename(bbb) ABC::aaa(T t); // 将匹配但优先级低于 ccc 
  %rename(ccc) ABC<Space::XYZ>::aaa(Space::XYZ t);// 将匹配但优先级高于 bbb 
  %rename(ddd) ABC<Space::XYZ>::aaa(XYZ t); // 将不匹配
} 

namespace Space { 
  class XYZ {}; 
  template<typename T> struct ABC { 
    void aaa(T t) {} 
  }; 
} 
%template(ABCXYZ) Space::ABC<Space::XYZ>;

请注意，ddd 不匹配，因为参数类型没有命名空间解析，并且必须为模板类型扩展指定完全限定类型。以下示例显示了如何将 %rename 放置在 %extend 中。
命名空间空间 { 
  %extend ABC { 
    %rename(bbb) aaa(T t);         // 将匹配但优先级低于 ccc 
  } 
  %extend ABC<Space::XYZ> { 
    %rename(ccc) aaa(Space::XYZ t);// 将匹配但优先级高于 bbb 
    %rename(ddd) aaa(XYZ t);       // 将不匹配
  } 
}

namespace Space { 
  class XYZ {}; 
  template<typename T> struct ABC { 
    void aaa(T t) {} 
  }; 
} 
%template(ABCXYZ) Space::ABC<Space::XYZ>;

6.21 异常规范
当 C++ 程序利用异常时，有时将异常行为指定为函数或方法声明的一部分。例如：
class Error{}；

class Foo { 
public: 
    ... 
    void blah() throw(Error); 
    ... 
};

如果使用异常规范，SWIG 会自动生成包装器代码以捕获指示的异常，并在可能的情况下将其重新抛出到目标语言中，否则将其转换为目标语言中的错误。例如，在 Python 中，您可以编写如下代码：
f = Foo() 
try: 
    f.blah() 
except Error, e: 
    # e 是“Error”的包装实例

“throws”类型映射 部分概述了如何定制代码以处理捕获的 C++ 异常并将其转换为目标语言的异常/错误处理机制的详细信息。
由于异常规范有时只很少使用，因此仅凭这一点可能不足以正确处理 C++ 异常。为此，使用了一组不同的特殊 SWIG 指令。有关 详细信息，请参阅“使用 %exception 处理异常”部分。下一节详细介绍了一种模拟异常规范或替换现有规范的方法。
6.22 %catchs 的异常处理
具有异常规范的方法会自动处理异常。通过 %catches 功能，可以为没有异常规范的方法实现类似的处理。也可以使用 %catches 功能替换任何声明的异常规范 。事实上，%catches 使用与 SWIG 在处理异常时用于异常规范相同的“抛出”类型映射。在 %catchs 功能必须包含可以抛出的可能类型列表。对于列表中的每个类型，SWIG 将生成一个 catch 处理程序，其方式与异常规范中声明的类型相同。请注意，该列表还可以包含 catch all 规范 “...”。例如，
struct EBase { virtual ~EBase(); };
struct Error1 : EBase { };
struct Error2 : EBase { };
struct Error3 : EBase { };
struct Error4 : EBase { };

%catches(Error1, Error2, ...) Foo::bar();
%catches(EBase) Foo::blah();

class Foo {
public:
    ...
    void bar();
    void blah() throw(Error1, Error2, Error3, Error4);
    ...
};

对于可以抛出任何内容的Foo::bar()方法，SWIG 将为Error1、Error2以及 catch all 处理程序 (...)生成捕获处理程序。每个捕获处理程序将转换捕获的异常并将其转换为目标语言错误/异常。catch all 处理程序会将捕获的异常转换为未知错误/异常。
如果没有将%catches功能附加到 Foo::blah()，SWIG 将为异常规范中的所有类型生成捕获处理程序，即 Error1、Error2、Error3、Error4。但是，使用上面的 %catches 功能，只需为基类创建一个单独的 catch 处理程序，就会生成 EBase 以将 C++ 异常转换为目标语言错误/异常。
6.23 成员指针
从 SWIG-1.3.7 开始，对指向 C++ 类成员的指针的解析支持有限。例如：
double do_op(Object *o, double (Object::*callback)(double, double)); 
extern double (Object::*fooptr)(double, double); 
%constant double (Object::*FOO)(double, double) = &Object::foo;

尽管这些类型的指针可以由 SWIG 类型系统解析和表示，但由于与标准 C 指针的实现差异，很少有语言模块知道如何处理它们。 强烈建议读者查阅高级文本，例如“The Annotated C++ Manual”以了解具体细节。
当支持指向成员的指针时，指针值可能会显示为一个特殊的字符串，如下所示：
>>> print example.FOO
_ff0d54a800000000_m_Object__f_double_double__double
>>>

在这种情况下，十六进制数字表示指针的整个值，这通常是大多数机器上小型 C++ 结构的内容。
在处理成员指针时，SWIG 的类型检查机制也受到更多限制。通常，SWIG 在检查类型时会尝试跟踪继承。但是，目前没有为成员指针提供此类支持。
6.24 智能指针和运算符->()
在一些 C++ 程序中，对象通常由智能指针或代理类封装。有时这样做是为了实现自动内存管理（引用计数）或持久性。通常，智能指针由模板类定义，其中 -> 运算符已重载。这个类然后被包裹在其他一些类中。例如：
// Smart-pointer class
template<class T> class SmartPtr {
    T *pointee;
public:
    SmartPtr(T *p) : pointee(p) { ... }
    T *operator->() {
        return pointee;
    }
    ...
};

// Ordinary class
class Foo_Impl {
public:
    int x;
    virtual void bar();
    ...
};

// Smart-pointer wrapper
typedef SmartPtr<Foo_Impl> Foo;

// Create smart pointer Foo
Foo make_Foo() {
    return SmartPtr<Foo_Impl>(new Foo_Impl());
}

// Do something with smart pointer Foo
void do_something(Foo f) {
    printf("x = %d\n", f->x);
    f->bar();
}

// Call the wrapped smart pointer proxy class in the target language 'Foo'
%template(Foo) SmartPtr<Foo_Impl>;

这种方法的一个关键特性是通过定义 operator-> 智能指针包装的对象的方法和属性是透明的。例如，诸如此类的表达式（来自前面的示例），
f->x 
f->bar()

透明地映射到以下
(f.operator->())->x; 
(f.operator->())->bar();

在生成包装器时，SWIG 尝试尽可能地模拟此功能。为此，每当在类中遇到 operator->() 时，SWIG 都会查看其返回的类型并使用它来生成用于访问底层对象属性的包装器。例如，包装上面的代码会产生这样的包装器：
int Foo_x_get(Foo *f) { 
  return (*f)->x; 
} 
void Foo_x_set(Foo *f, int value) { 
  (*f)->x = value; 
} 
void Foo_bar(Foo *f) { 
  (*f)->bar(); 
}

这些包装器将智能指针实例作为参数，但以某种方式取消引用它以访问由 operator->()返回的对象。您应该仔细地将这些包装器与本章第一部分中的包装器进行比较（它们略有不同）。
最终结果是访问看起来与 C++ 非常相似。例如，您可以在 Python 中执行此操作：
>>> f = make_Foo() 
>>> 打印 fx 
0 
>>> f.bar() 
>>>

通过智能指针生成包装器时，SWIG 会尝试为可通过operator->()访问的所有方法和属性生成包装器。这包括可通过继承访问的任何方法。但是，有许多限制：
- 成员变量和方法通过智能指针包装。枚举、构造函数和析构函数没有被包装。
- 如果智能指针类和底层对象都定义了同名的方法或变量，则智能指针版本优先。例如，如果您有此代码
class Foo {
public:
    int x;
};

class Bar {
public:
    int x;       
    Foo *operator->();
};

然后 Bar::x 的包装器访问 Bar 中定义的 x，而不是 Foo 中定义的 x。
如果您的意图是只在接口中公开智能指针类，则没有必要同时包装智能指针类和底层对象的类。但是，如果您希望本节中描述的技术起作用，您仍然必须将这两个类都告诉 SWIG。要仅为智能指针类生成包装器，您可以使用 %ignore 指令。例如：
%ignore Foo;
class Foo {       // Ignored
};

class Bar {
public:
  Foo *operator->();
  ...
};

或者，您可以使用 %import 从单独的文件中导入 Foo 的定义。
注意：当一个类定义operator->() 时，运算符本身被包装为一个方法 __deref__()。例如：
f = Foo()         # 智能指针
p = f.__deref__() # 来自 operator-> 的原始指针

注意：要禁用智能指针行为，请使用 %ignore 忽略 operator->()。例如：
%ignore Bar::operator->;

注意：智能指针支持首先在 SWIG-1.3.14 中添加。
6.25 C++ 引用计数对象 - ref/unref 特性
C++ 中另一个类似的习惯用法是使用引用计数对象。考虑例如：
class RCObj  {
  // implement the ref counting mechanism
  int add_ref();
  int del_ref();
  int ref_count();

public:
  virtual ~RCObj() = 0;

  int ref() const {
    return add_ref();
  }

  int unref() const {
    if (ref_count() == 0 || del_ref() == 0 ) {
      delete this;
      return 0;
    }
    return ref_count();
  }
};


class A : RCObj {
public:
  A();
  int foo();
};


class B {
  A *_a;

public:
  B(A *a) : _a(a) { 
    a->ref(); 
  }

  ~B() { 
    a->unref(); 
  }
};

int main() {
  A *a  = new A();       // (count: 0)
  a->ref();           // 'a' ref here (count: 1)

  B *b1 = new B(a);   // 'a' ref here (count: 2)
  if (1 + 1 == 2) {
    B *b2 = new B(a); // 'a' ref here (count: 3)
    delete b2;        // 'a' unref, but not deleted (count: 2)
  }

  delete b1;          // 'a' unref, but not deleted (count: 1)
  a->unref();         // 'a' unref and deleted (count: 0)
}

在上面的例子中，'A' 类实例 'a' 是一个引用计数对象，它不能被任意删除，因为它在对象 'b1' 和 'b2' 之间共享。'A' 派生自引用计数对象'RCObj'，它实现了 ref/unref 习惯用法。
要告诉 SWIG 'RCObj' 及其所有派生类都是引用计数对象，请使用“ref”和“unref” 功能。它们也可以分别作为%refobject和 %unrefobject 使用。例如：
%module example
... 

%feature("ref") RCObj "$this->ref();" 
%feature("unref") RCObj "$this->unref();" 

%include "rcobj.h" 
%include "A.h" 
...

每当将新对象传递给 python 或 python 尝试释放代理对象实例时，将根据需要执行传递给“ref”和“unref”功能的代码。
在 python 方面，引用计数对象的使用与任何其他常规实例没有什么不同：
def create_A(): 
    a = A()         # SWIG ref 'a' - 新对象被传递给 python (count: 1) 
    b1 = B(a)       # C++ ref 'a (count: 2) 
    if 1 + 1 == 2: 
        b2 = B(a)   # C++ ref 'a' (count: 3) 
    return a        # 'b1'和'b2'被释放和删除，C++ unref 'a'两次(count: 1) 

a = create_A()      # (count: 1) 
exit                # 'a' 被释放，在析构函数包装器中调用 SWIG unref 'a' (count: 0)

请注意，用户不需要明确调用 'a->ref()' 或 'a->unref()'（也不需要 'delete a'）。相反，SWIG 负责根据需要执行“ref”和“unref”调用。如果用户没有为类型指定“ref/unref”功能，SWIG 将生成与定义这些功能等效的代码：
%feature("ref") "" 
%feature("unref") "delete $this;"

换句话说，当一个新对象传递给 python 时，SWIG 不会做任何特殊的事情，当 python 释放代理实例时，它总是会“删除”底层对象。
在％newobject 功能的设计中，以指示目标语言，它应该返回对象的所有权。当与具有关联的“ref”功能的类型结合使用时，它还会将“ref”功能中的代码发送到 C++ 包装器中。除了上述之外，还可以考虑包装以下工厂函数：
%newobject AFactory; 
A *AFactory() { 
  return new A(); 
}

该AFactory功能，现在的作用很像的一个电话 一个相对于内存处理构造函数：
a = AFactory() # SWIG ref 'a' 由于 %newobject (count: 1) 
exit # 'a' 被释放，在析构函数包装器中调用 SWIG unref 'a' (count: 0)

6.26 Using 的声明和继承
using 声明有时用于调整对基类成员的访问。例如：
class Foo {
public:
      int  blah(int x);
};

class Bar {
public:
      double blah(double x);
};

class FooBar : public Foo, public Bar {
public:
      using Foo::blah;  
      using Bar::blah;
      char *blah(const char *x);
};

在这个例子中，using 声明使得不同版本的重载 blah() 方法可以从派生类访问。例如：
FooBar *f;
f->blah(3);         // Ok. Invokes Foo::blah(int)
f->blah(3.5);       // Ok. Invokes Bar::blah(double)
f->blah("hello");   // Ok. Invokes FooBar::blah(const char *);

SWIG 在创建包装器时模拟相同的功能。例如，如果您用 Python 包装此代码，则该模块的工作方式与您预期的一样：
>> import example
>>> f = example.FooBar()
>>> f.blah(3)
>>> f.blah(3.5)
>>> f.blah("hello")

using 声明还可用于在适用时更改访问权限。例如：
class Foo {
protected:
    int x;
    int blah(int x);
};

class Bar : public Foo {
public:
    using Foo::x;       // Make x public
    using Foo::blah;    // Make blah public
};

这也适用于 SWIG ---公开的声明将被正常包装。
当如这些示例中所示使用 using声明时，来自基类的声明被复制到派生类中并正常包装。复制时，声明会保留可能已使用 %rename 、%ignore 或 %feature 附加的任何属性。因此，如果一个方法在基类中被忽略，它也会被 using 声明忽略。
由于 using 声明不提供对导入的声明的细粒度控制，因此可能难以在大量使用 SWIG 自定义功能的应用程序中管理此类声明。如果您无法正常使用，您可以随时将界面更改为以下内容：
class FooBar : public Foo, public Bar {
public:
#ifndef SWIG
      using Foo::blah;  
      using Bar::blah;
#else
      int blah(int x);         // explicitly tell SWIG about other declarations
      double blah(double x);
#endif

      char *blah(const char *x);
};

笔记：
- 如果派生类重新定义基类中定义的方法，则 using 声明不会导致冲突。例如：
class Foo {
public:
  int blah(int );
  double blah(double);
};

class Bar : public Foo {
public:
  using Foo::blah;    // Only imports blah(double);
  int blah(int);
};

- 解决重载中的歧义可能会阻止使用. 例如：
%rename(blah_long) Foo::blah(long);
class Foo {
public:
  int blah(int);
  long blah(long);  // Renamed to blah_long
};

class Bar : public Foo {
public:
  using Foo::blah;     // Only imports blah(int)
  double blah(double x);
};

6.27 嵌套类
如果目标语言支持嵌套类概念（如 Java），则嵌套的 C++ 类将包装为嵌套的目标语言代理类。（在 Java 的情况下 - “静态”嵌套类。）仅包装公共嵌套类。否则嵌套类和普通类之间几乎没有区别。
如果目标语言不直接支持嵌套类，或者语言模块中没有实现支持（比如当前的 python），那么可见嵌套类将被移动到与包含类相同的命名空间（嵌套层次结构为“压扁”）。%feature ("flatnested"); 可以为 C# 和 Java 开启相同的行为；如果外部命名空间中有一个同名的类，则内部类（或全局类）可能会被重命名或忽略：
%rename (Bar_Foo) Bar::Foo;
class Foo {};
class Bar {
  public:
  class Foo {};
};

如果外部类中的嵌套类必须用作外部类中的模板参数，则必须在外部类开始之前使用 %template 实例化模板。可以在 模板部分找到一个示例。
兼容性说明：在 SWIG-3.0.0 之前，嵌套类支持有限。嵌套类被视为不透明指针。但是，在这些旧版本中，有一个嵌套类支持的解决方法，要求用户在全局范围内复制嵌套类，在全局范围内为嵌套类添加 typedef 并使用嵌套类上的“nestedworkaround”功能. 这导致与“扁平化”功能大致相同的行为。SWIG-3.0.0 现在提供了适当的嵌套类支持，此功能已被弃用，不再需要更改代码。如果您看到以下警告：
example.i:8: Warning 126: The nestedworkaround feature is deprecated

考虑使用上面讨论的“扁平嵌套”功能，它会生成一个非嵌套代理类，就像“嵌套变通”功能所做的那样。或者，使用默认的嵌套类代码生成，这可能会生成与目标语言中的嵌套代理类等效的代码，具体取决于目标语言支持。
SWIG-1.3.40 和更早版本没有 nestedworkaround 功能，并且通过解析嵌套类生成的代码并不总是能够编译。使用 %warnfilter 也无法抑制嵌套类警告。
6.28 关于 const-correctness 的简短咆哮
使用 C++ 程序时的一个常见问题是处理 const 限定符（或缺少const限定符）会破坏您的程序的所有可能方式，所有与您的程序链接的程序，以及所有与这些程序链接的程序。
尽管 SWIG 知道如何在其内部类型系统中正确处理 const，并且知道如何生成没有与 const 相关警告的包装器，但 SWIG 并未尝试在目标语言中保持 const 的正确性。因此，可以将 cons t限定对象传递给非常量方法和函数。例如，考虑以下 C++ 代码：
const Object * foo();
void bar(Object *);

...
// C++ code
void blah() {
  bar(foo());         // Error: bar discards const
};

现在，考虑包装到 Python 模块时的行为：
>>> bar(foo())         # Okay
>>> 

尽管这显然违反了 C++ 类型系统，但解决该问题似乎不值得增加在 SWIG 运行时类型系统中支持它所需的实现复杂性。没有计划在未来的版本中改变这一点（尽管我们永远不会完全排除任何事情）。
最重要的是，对于大多数 SWIG 项目来说，这个特定问题似乎不是问题。当然，如果保持常量性是您项目中最重要的部分，您可能需要考虑使用其他工具。
6.29 从何处获取更多信息
如果您正在封装严肃的 C++ 代码，您可能需要获取 Ellis 和 Stroustrup 所著的“带注释的 C++ 参考手册”的副本。这是我们用来指导很多 SWIG 的 C++ 支持的参考文档。

7 SWIG 和 C++11
7.1 简介
本章简要概述了 C++11 标准的 SWIG 实现。SWIG 的这一部分仍在进行中。
SWIG 支持新的 C++ 语法更改，但在某些领域（例如 decltype 表达式和可变参数模板）有一些小限制。新 STL 类型（unordered_container、result_of、tuples）的包装器不完整。新容器的包装器的工作方式与 C++03 容器非常相似，欢迎用户通过调整现有容器接口文件并将它们作为补丁提交以包含在 SWIG 的未来版本中来提供帮助。
7.2 核心语言变化
7.2.1 右值引用和移动语义
SWIG 正确解析了右值引用语法“&&”，例如它在下面的移动构造函数和移动赋值运算符中的典型用法：
class MyClass { 
... 
  std::vector<int> numbers; 
public：
  MyClass(MyClass &&other) : numbers(std::move(other.numbers)) {} 
  MyClass & operator=(MyClass &&other) { 
    numbers = std::move(other.numbers); 
   return *this; 
  } 
};

右值引用是为 C++ 临时对象设计的，因此在非 C++ 目标语言中使用时不是很有用。通常，您会在解析类之前通过 %ignore 忽略它们。例如，忽略移动构造函数：
%ignore MyClass::MyClass(MyClass &&);

计划是在 SWIG 的未来版本中默认忽略移动构造函数。请注意，在大多数目标语言中，默认情况下会忽略普通赋值运算符和移动赋值运算符，并显示以下警告：
example.i:18：警告 503：除非重命名为有效标识符，否则无法包装“operator =”。
7.2.2 广义常量表达式
SWIG 解析并识别关键字constexpr，但无法充分利用它。这些 C++ 编译时常量可用作目标语言的运行时常量。下面显示了从编译时常量函数分配 C++ 编译时常量的示例用法：
constexpr int XXX() { return 10; } 
constexpr int YYY = XXX() + 100;

当从目标语言使用其中任何一个时，将进行运行时调用以获取基础常量。
7.2.3 外部模板
SWIG 能正确的解析关键字 extern template。然而，这个模板实例化阻碍了没有相关 C++ 编译器以外的翻译单元，并且因此不会被 SWIG 使用。SWIG 只使用 %template 来实例化和包装模板。
tempate class std::vector<int>; // C++ 03 显式实例化在 C++ 
extern template class std::vector<int>; // C++11 显式实例化抑制在 C++ 
%template(VectorInt) std::vector<int>; // SWIG 实例化

7.2.4 初始化列表
初始化器列表在很大程度上是一种 C++ 编译器构造，不能从包装器中轻松访问，因为它们旨在用于使用特殊std::initializer_list 类型的类的编译时初始化。SWIG 检测初始化列表的使用，并在每次使用时发出一个特殊的信息警告：
example.i:33: Warning 476: Initialization using std::initializer_list.

初始化列表通常出现在构造函数中，但可以出现在任何函数或方法中。它们经常出现在构造函数中，这些构造函数重载了用于初始化类的替代方法，例如用于向容器添加元素的 std 容器的 push_back 方法。推荐的方法是简单地忽略初始化列表构造函数，例如：
%ignore Container::Container(std::initializer_list<int>);
class Container {
public:
  Container(std::initializer_list<int>); // initializer-list constructor
  Container();
  void push_back(const int &);
  ...
};

或者，您可以修改该类并通过其他方式添加另一个用于初始化的构造函数，例如通过 std::vector：
%include <std_vector.i>
class Container {
public:
  Container(const std::vector<int> &);
  Container(std::initializer_list<int>); // initializer-list constructor
  Container();
  void push_back(const int &);
  ...
};

然后从您的目标语言调用此构造函数，例如，在 Python 中，以下将调用 带有 std::vector的构造函数：
>>> c = Container( [1, 2, 3, 4] )

如果您无法修改被包装的类，请考虑忽略初始化列表构造函数并使用 %extend 添加替代构造函数：
%include <std_vector.i> 
%extend Container { 
  Container(const std::vector<int> &elements) { 
    Container *c = new Container(); 
    for (int element : elements) 
      c->push_back(element); 
    return c; 
  } 
} 

%ignore Container::Container(std::initializer_list<int>); 

class Container {
public：
  Container（std::initializer_list<int>）；// 初始化列表构造函数
  Container(); 
  void push_back(const int &); 
  ... 
};

上面使包装器看起来好像类已经声明如下：
%include <std_vector.i>
class Container {
public:
  Container(const std::vector<int> &);
//  Container(std::initializer_list<int>); // initializer-list constructor (ignored)
  Container();
  void push_back(const int &);
  ...
};;

std::initializer_list只是一个只能在编译时初始化的容器。由于它只是一个 C++ 类型，因此可以为目标语言容器编写类型映射以映射到 std::initializer_list。但是，这只能对固定数量的元素执行，因为初始化列表不是设计为在运行时使用可变数量的参数构造的。下面的示例是一种非常简单的方法，它忽略传入的任何参数，仅使用在编译时选择的固定整数值的固定列表进行初始化：
%typemap(in) std::initializer_list<int> { 
  $1 = {10, 20, 30, 40, 50}; 
} 
class Container { 
public: 
  Container(std::initializer_list<int>); // 初始化列表构造函数
  Container(); 
  void push_back(const int &); 
  ... 
};

任何从目标语言传入值的尝试都将被忽略并替换为{10, 20, 30, 40, 50}. 不用说，这个方法非常有限，但是可以改善，但是仅仅是一点。一个 typemap 可以用来映射一个固定数量元素到 std::initializer_list 上，而在运行时进行赋值。此类型映射会由目标语言指定。
请注意，std::initializer_list的默认类型 映射只会发出警告，因此任何用户提供的类型映射都将覆盖它并取消警告。
7.2.5 统一初始化
SWIG 完全支持用于成员初始化的大括号 {}：
struct BasicStruct { 
 int x; 
 double y; 
}; 
 
struct AltStruct { 
  AltStruct(int x, double y) : x_{x}, y_{y} {} 
 
  int x_; 
  double y_；
}; 

BasicStruct var1{5, 3.2}; // 只填充结构体组件
AltStruct var2{2, 4.3}; // 调用构造函数

统一初始化不会影响目标语言的使用，例如在 Python 中：
>>>一个= AltStruct（10，142.15）
>>> a.x_ 
10 
>>> a.y_ 
142.15

7.2.6 类型推断
SWIG 支持decltype()有一些限制。允许使用单个变量，但尚不支持表达式。例如，以下代码将起作用：
int i;
decltype(i) j;

但是，在 decltype 中使用表达式会导致语法错误：
int i; int j;
decltype(i+j) k;  // syntax error

7.2.7 基于范围的 for 循环
此功能仅是实现块的一部分。SWIG 忽略了它。
7.2.8 Lambda 函数和表达式
SWIG 正确解析大部分 Lambda 函数语法。例如：
auto val = [] { return something；}; 
auto sum = [](int x, int y) { return x+y; }; 
auto sum = [](int x, int y) -> int { return x+y; };

由于目标语言中缺少对闭包（lambda 函数的范围）的支持，因此暂时从包装器中删除了 lambda 函数。
用于创建变量的 Lambda 函数也可以被解析，但由于从表达式推导出类型时对auto 的支持有限，变量被简单地忽略。
auto six = [](int x, int y) { return x+y; }(4, 2);

在以后的版本中应该会提供更好的支持。
7.2.9 替代函数语法
SWIG 完全支持函数的新定义。例如：
struct SomeStruct { 
  int FuncName(int x, int y); 
};

现在可以像在 C++11 中那样编写：
struct SomeStruct { 
  auto FuncName(int x, int y) -> int; 
};
 
auto SomeStruct::FuncName(int x, int y) -> int { 
  return x + y; 
}

目标语言中的用法保持不变，例如在 Python 中：
>>> a = SomeStruct() 
>>> a.FuncName(10, 5) 
15

根据前面描述的限制，SWIG 还将处理返回类型的类型推断。例如：
auto square(float a, float b) -> decltype(a);

7.2.10 对象构建改进
对象构造改进分为三个部分。第一个改进是构造函数委托，如下所示：
class A {
public:
  int a;
  int b;
  int c;

  A() : A(10) {}
  A(int aa) : A(aa, 20) {}
  A(int aa, int bb) : A(aa, bb, 30) {}
  A(int aa, int bb, int cc) { a=aa; b=bb; c=cc; }
};

可以调用对等构造函数的地方。SWIG 可以毫无问题地处理这个问题。
第二个改进是通过using 声明继承构造函数。这被正确解析，但额外的构造函数当前未添加到目标语言中的派生代理类。一个例子如下所示：
class BaseClass { 
public: 
  BaseClass(int iValue); 
}; 

class DerivedClass: public BaseClass { 
  public: 
  using BaseClass::BaseClass; // 添加 DerivedClass(int) 构造函数
};

最后一部分是声明站点的成员初始化。这种初始化由 SWIG 处理。
class SomeClass { 
public: 
  SomeClass() {} 
  explicit SomeClass(int new_value) : value(new_value) {} 

  int value = 5; 
};

7.2.11 显式覆盖和最终
特殊标识符final和override可用于方法和析构函数，例如在以下示例中：
struct BaseStruct {
  virtual void ab() const = 0;
  virtual void cd();
  virtual void ef();
  virtual ~BaseStruct();
};
struct DerivedStruct : BaseStruct {
  virtual void ab() const override;
  virtual void cd() final;
  virtual void ef() final override;
  virtual ~DerivedStruct() override;
};

7.2.12 空指针常量
该 nullptr 常数是在包装大多是不重要的。在它有影响的少数地方，它被视为 NULL。
7.2.13 强类型枚举
SWIG 支持强类型枚举并解析新的枚举类语法和枚举的前向声明符，例如：
enum class MyEnum : unsigned int;

强类型枚举通常用于避免名称冲突，例如：
struct Color { 
  enum class RainbowColors : unsigned int { 
    Red, Orange, Yellow, Green, Blue, Indigo, Violet 
  };
  
  enum class WarmColors {
    Yellow, Orange, Red
  };

  // Note normal enum
  enum PrimeColors {
    Red=100, Green, Blue
  };
};

目标语言有多种处理枚举的方式，因此在本节中无法准确说明它们是如何处理的。然而，一般来说，大多数脚本语言会修改强类型枚举的类名，但不会对普通枚举使用任何额外的修改。例如，在 Python 中，以下代码
print Color.RainbowColors_Red、Color.WarmColors_Red、Color.Red

结果是
0 2 100
强类型语言通常将普通枚举包装到一个枚举类中，因此对待普通枚举和强类型枚举是一样的。Java 中的等价物是：
System.out.println(Color.RainbowColors.Red.swigValue() + " " + Color.WarmColors.Red.swigValue() + " " + Color.PrimeColors.Red.swigValue());

7.2.14 双尖括号
SWIG 正确解析符号 >> 作为关闭模板块，如果在顶层找到它，或者作为右移运算符 >> 。
std::vector<std::vector<int>> myIntTable;

7.2.15 显式转换运算符
除了构造函数之外，SWIG 现在可以正确解析运算符的显式关键字。例如：
class U {
public:
  int u;
};

class V {
public:
  int v;
};

class TestClass {
public:
  //implicit converting constructor
  TestClass(U const &val) { t=val.u; }

  // explicit constructor
  explicit TestClass(V const &val) { t=val.v; }

  int t;
};

struct Testable {
  // explicit conversion operator
  explicit operator bool() const {
    return false;
  }
};

显式构造函数和运算符的效果与代理类几乎没有关系，因为目标语言没有与 C++ 相同的隐式转换概念。转换运算符无论是否显式都需要重命名为有效的标识符名称，以使它们可用作普通代理方法。
7.2.16 类型别名和别名模板
类型别名是以下形式的语句：
using PFD = void (*)(double); // 新引入的语法

这相当于旧样式的 typedef：
typedef void (*PFD)(double); // 旧样式

以下是别名模板的示例：
template< typename T1, typename T2, int N > 
class SomeType { 
public: 
  T1 a; 
  T2 b; 
}; 

template< typename T2 > 
using TypedefName = SomeType<char*, T2, 5>;

SWIG 支持类型别名和别名模板。但是，为了使用别名模板，必须使用两个%template指令：
%template(SomeTypeBool) SomeType<char*, bool, 5>; 
%template() TypedefName<bool>;

首先，根据被包装的任何模板，使用目标语言使用的名称实例化实际模板。其次，别名模板需要空模板实例化%template()。第二个要求是将适当的实例化模板类型添加到类型系统中的必要条件，因为 SWIG 不会自动实例化模板。有关包装模板的更多一般信息，请参阅 模板部分。
7.2.17 无限制联合
SWIG 完全支持联合内的任何类型，即使它没有定义简单的构造函数。例如，以下代码的包装器正确地提供了对联合中所有成员的访问：
struct point {
  point() {}
  point(int x, int y) : x_(x), y_(y) {}
  int x_, y_;
};

#include <new> // For placement 'new' in the constructor below
union P {
  int z;
  double w;
  point p; // Illegal in C++03; legal in C++11.
  // Due to the point member, a constructor definition is required.
  P() {
    new(&p) point();
  }
} p1;

7.2.18 可变模板
SWIG 支持可变参数模板语法（在 <> 块内部、可变参数类继承和可变参数构造函数和初始化器），但有一些限制。以下代码被正确解析：
template <typename... BaseClasses> class ClassName : public BaseClasses... { 
public: 
  ClassName (BaseClasses &&... baseClasses) : BaseClasses(baseClasses)... {} 
}

然而，现在%template指令只接受一个参数替换变量模板参数。
%template(MyVariant1) ClassName<> // 尚不支持零参数
%template(MyVariant2) ClassName<int> // ok 
%template(MyVariant3) ClassName<int, int> // 尚不支持太多参数

对可变参数sizeof()函数的支持被正确解析：
const int SIZE = sizeof...(ClassName<int, int>);

在上面的例子中SIZE当然被包装成一个常量。
7.2.19 新的字符串文字
SWIG 支持宽字符串和 Unicode 字符串常量以及原始字符串文字。
// 新的字符串文字
wstring aa = L"Wide string"; 
const char *bb = u8"UTF-8 字符串"; 
const char16_t *cc = u"UTF-16 字符串"; 
const char32_t *dd = U"UTF-32 字符串"; 

// 原始字符串字面量
const char *xx = ")I'm an \"ascii\" \\ string."; 
const char *ee = R"XXX()I'm an "ascii" \ string.)XXX"; // 与 xx 相同
wstring          ff =  LR"XXX(I'm a "raw wide" \ string.)XXX";
const char      *gg = u8R"XXX(I'm a "raw UTF-8" \ string.)XXX";
const char16_t  *hh =  uR"XXX(I'm a "raw UTF-16" \ string.)XXX";
const char32_t  *ii =  UR"XXX(I'm a "raw UTF-32" \ string.)XXX";

但是，非 ASCII 字符串支持在各种目标语言之间差异很大。
注意：当前存在一个错误，即 SWIG 的预处理器错误地解析原始字符串文字中奇数个双引号。
7.2.20 用户定义的文字
SWIG 解析用户定义文字的声明，即 操作符 "" _mysuffix()函数语法。
一些例子是原始文字：
OutputType operator "" _myRawLiteral(const char * value);

数字化文字：
OutputType operator "" _mySuffixIntegral(unsigned long long);
OutputType operator "" _mySuffixFloat(long double);

和字符串化：
OutputType operator "" _mySuffix(const char * string_values, size_t num_chars);
OutputType operator "" _mySuffix(const wchar_t * string_values, size_t num_chars);
OutputType operator "" _mySuffix(const char16_t * string_values, size_t num_chars);
OutputType operator "" _mySuffix(const char32_t * string_values, size_t num_chars);

与 SWIG 解析的其他运算符一样，会发出有关重命名运算符以便对其进行包装的警告：
example.i:27: Warning 503: Can't wrap 'operator "" _myRawLiteral' unless renamed to a valid identifier.

如果使用 %rename，则可以像任何其他包装方法一样调用它。当前，您需要指定完整的声明，包括 %rename 的参数：
%rename(MyRawLiteral)  operator"" _myRawLiteral(const char * value);

或者，如果您只想完全忽略它：
%ignore operator "" _myRawLiteral(const char * value);

请注意，使用如下用户定义的文字仍然会出现语法错误：
OutputType var1 = "1234"_suffix;
OutputType var2 = 1234_suffix;
OutputType var3 = 3.1416_suffix;

7.2.21 Thread-local 存储
SWIG 正确解析 thread_local 关键字。例如，当前线程可访问的变量可以定义为：
struct A { 
  static thread_local int val; 
}; 
thread_local int global_val;

使用thread_local存储说明符不影响包装过程；与未指定时相比，它不会修改包装器代码。如果从目标语言的不同线程访问变量，则变量将是线程本地的，就像从 C++ 代码访问时，变量将是线程本地的一样。
7.2.22 显式默认函数和删除函数
SWIG 处理显式默认的函数，即将= default 添加到函数声明中。删除的定义，也称为删除的函数，在函数声明中添加了= delete。例如：
struct NonCopyable { 
  NonCopyable & operator=(const NonCopyable &) = delete; /* 删除 operator= */ 
  NonCopyable(const NonCopyable &) = delete; /* 删除复制构造函数 */ 
  NonCopyable() = default; /* 明确允许空构造函数 */ 
};

已删除函数的包装器在目标语言中不可用。默认函数的包装器当然可以在目标语言中使用。显式默认函数对 SWIG 包装没有直接影响，因为声明的处理方式与 SWIG 解析的任何其他方法声明非常相似。
删除的函数还旨在防止调用函数时进行隐式转换。例如，C++ 编译器不会编译任何尝试使用 int 作为传递给f的参数类型的代码：
struct NoInt { 
  void f(double i); 
  void f(int) = delete；
};

这是 C++ 编译时检查，SWIG 不会尝试检测目标语言是否使用 int 而不是 double，因此在这种情况下，完全有可能将 int 而不是 double 传递给 Java、Python 等中的f。
7.2.23 类型 long long int
SWIG 正确解析并使用了不久前在 C99 中引入的新long long类型。
7.2.24 静态断言
SWIG 正确解析新的static_assert声明。这是一个 C++ 编译时指令，因此 SWIG 对它没有任何用处。
tempalte<typename T> 
struct Check { 
  static_assert(sizeof(int) <= sizeof(T)，“not engoth”）；
};

7.2.25 允许 sizeof 在没有显式对象的情况下处理类的成员
SWIG 可以在类型和对象上解析新的 sizeof()。例如：
struct A { 
  int member；
}; 

const int SIZE = sizeof(A::member); // 不适用于 C++03。用 C++11 没问题

在 Python 中：
>>> SIZE
8

7.2.26 异常规范和 noexcept
C++11 在 noexcept 规范中添加到异常规范，以指示函数可能会或可能不会抛出异常，而无需实际命名任何异常。SWIG 理解这些，尽管没有任何有用的方式可以让目标语言利用这些信息，因此在包装过程中忽略它一样好。以下是函数声明中 noexcept 的一些示例：
static void noex1() noexcept;
int noex2(int) noexcept(true);
int noex3(int, bool) noexcept(false);

7.2.27 控制和查询对象对齐方式
一个alignof操作者主要用于内C ++中的字节数返回对准，但是也可以使用初始化变量如下所示。目标语言可以像任何其他变量的编译时初始化值一样访问该变量的值。
const int align1 = alignof(A::member);

尚不支持用于变量对齐的alignas说明符。用法示例：
struct alignas(16) S { 
  int num; 
}; 
alignas(double) unsigned char c[sizeof(double)];

现在使用预处理器来解决这个问题：
#define alignas(T)

7.2.28 属性
如下所示的属性尚不受支持，并且会出现语法错误。
int [[attr1]] i [[attr2, attr3]]; 

[[noreturn, nothrow]] void f [[noreturn]]();

7.3 标准库变化
7.3.1 线程工具
SWIG 目前不包装或使用任何引入的新线程类（线程、互斥锁、锁、条件变量、任务）。主要原因是 SWIG 目标语言提供了自己的线程工具，因此它们的用途有限。
7.3.2 元组类型
SWIG 尚未为新元组类型提供库文件。可变模板支持需要进一步的工作来提供大量的元组包装器。
7.3.3 哈希表
STL 中的新哈希表是 unordered_set、 unordered_multiset、unordered_map、unordered_multimap 。这些在 SWIG 中不可用，但原则上应该通过调整当前的 STL 容器来轻松实现。
7.3.4 Regular expressions
虽然 SWIG 可以为新的 C++11 正则表达式类提供包装器，但几乎没有必要，因为目标语言有自己的正则表达式工具。
7.3.5 通用智能指针
SWIG 以与支持boost::shared_ptr相同的方式为std::shared_ptr提供特殊的智能指针处理。请参阅shared_ptr 智能指针 库部分。目前还没有可用于std::weak_ptr和std::unique_ptr 的特殊智能指针处理。
7.3.6 可扩展的随机数工具
此功能仅扩展和标准化标准库，不会影响 C++ 语言或 SWIG。
7.3.7 包装参考
包装器引用类似于普通的 C++ 引用，但它是可复制构造和可复制赋值的。可以想象，它们可以用于公共 API。尽管 SWIG 中没有对 std::reference_wrapper 的特殊支持。如果使用包装器引用，用户将需要编写自己的类型映射，这些类似于普通的 C++ 引用类型映射。
7.3.8 函数对象的多态包装器
SWIG 以一种非常自然的方式支持几种语言的函数类。然而，尚未为新的提供任何内容 std::function 模板. SWIG 会像其他模板解析模板用法
%rename(__call__) Test::operator(); // 默认重命名用于 Python 

struct Test { 
  bool operator()(int x, int y); // 函数对象
}; 

#include <function> 
std::function<void (int, int)> pF = Test; // 函数模板包装器

Python 中支持的普通函子的使用示例如下所示。它不涉及std::function。
t = Test() 
b = t(1, 2) # 调用 C++ 函数对象

7.3.9 元编程的类型特征
支持 C++ 元编程的 type_traits 函数在编译时很有用，并且专门针对 C++ 开发：
#include <type_traits> 

// 第一种操作方式。
template< bool B > struct algorithm { 
  template< class T1, class T2 > static int do_it(T1 &, T2 &) { /*...*/ return 1; } 
}; 

// 第二种操作方式。
template<> struct algorithm<true> { 
  template< class T1, class T2 > static int do_it(T1, T2) { /*...*/ return 2; } 
}; 

// 实例化 'elaborate' 会根据使用的类型自动实例化正确的操作方式。
template< class T1, class T2 > int Explain(T1 A, T2 B) { 
  // 仅当 'T1' 是整数且如果 'T2' 是浮点数时才使用第二种方式，
  // 否则使用第一种方式。
  return algorithm< std::is_integral<T1>::value && std::is_floating_point<T2>::value >::do_it(A, B); 
}

SWIG 正确解析模板特化、模板类型等。但是，元编程和 type_traits 标头中的额外支持实际上是为了编译时，在运行时对于目标语言并没有多大用处。例如，由于 SWIG 需要通过%template显式实例化模板，因此std::is_integral<int>本身不会提供太多内容。但是，使用这种元编程技术的模板函数可能对包装很有用。例如，可以进行以下实例化：
%template(Elaborate) elaborate<int, int>; 
%template(Elaborate) elaborate<int, double>;

然后，可以从目标语言（例如 Python）为上述%template实例化给出的类型子集调用适当的算法：
>>> Elaborate(0, 0) 
1 
>>> Elaborate(0, 0.0) 
2

7.3.10 计算函数对象返回类型的统一方法
<function> 头文件中引入的新std::result_of类提供了一种通过std::result_of::type获取函数类型的返回类型的通用方法。没有任何库接口文件来支持这种类型。通过一些工作，SWIG 将 使用下面显示的方法推导出在std::result_of 中使用时函数的返回类型。该技术基本上向前声明了std::result_of模板类，然后部分地将其专门用于感兴趣的函数类型。SWIG 将使用部分特化，因此正确使用部分特化中提供的 std::result_of::type。
%inline %{ 
#include <function> 
typedef double(*fn_ptr)(double); 
%} 

namespace std { 
  // 前向声明 result_of 
  template<typename Func> struct result_of; 
  // 添加 result_of 的部分特化
  template<> struct result_of< fn_ptr(double) > { 
    typedef double type; 
  }; 
} 

%template() std::result_of< fn_ptr(double) >; 

%inline %{ 

double square(double x) { 
  return (x * x); 
} 

template<class Fun, class Arg> 
typename std::result_of<Fun(Arg)>::type test_result_impl(Fun fun, Arg arg) { 
  return fun(arg); } 
} 
%}

%template(test_result) test_result_impl< fn_ptr, double >; 
%constant double (*SQUARE)(double) = square;

请注意SWIG 需要实例化模板的第一次使用 %template。空模板实例化就足够了，因为std::result_of<Fun(Arg)>::type 不需要代理类， 因为这种类型实际上只是一个 double。第二个 %template 实例化被包装用作回调的模板函数。然后可以将 %constant 用于任何回调函数，如指向函数和回调的指针中所述。
Python 的示例用法应该给出不太令人惊讶的结果：
>>> test_result(SQUARE, 5.0) 
25.0

呼，要使回调正常工作需要付出很多努力。您可以选择更具吸引力的选项，即 在函数声明中使用double作为返回类型而不是result_of ！

8 预处理
SWIG 包括其自己的 C 预处理器的增强版本。预处理器支持标准的预处理器指令和宏扩展规则。但是，已经进行了许多修改和增强。本章描述了其中的一些修改。
8.1 文件包含
要将另一个文件包含到 SWIG 接口中，请使用%include 指令，如下所示：
%include "pointer.i"

不同于＃include ，％include 只包含每个文件一次（并在后续将不重新加载该文件 ％include  声明）。因此，没有必要在 SWIG 接口中使用包含保护。
默认情况下，#include 将被忽略，除非您使用 -includeall 选项运行 SWIG 。忽略传统包含的原因是您通常不希望 SWIG 尝试包装标准头系统头文件和辅助文件中包含的所有内容。
8.2 文件导入
SWIG 提供了另一个带有 %import 指令的文件包含指令。例如：
%import "foo.i"

%import 的目的是从另一个 SWIG 接口文件或头文件中收集某些信息，而无需实际生成任何包装器代码。此类信息通常包括类型声明（例如typedef）以及可能用作接口中类声明的基类的 C++ 类。当 SWIG 用于生成扩展作为相关模块的集合时，%import 的使用也很重要。这是一个高级主题，稍后将在“使用模块”一章中进行介绍。
该 -importall 指令告诉 SWIG 遵循所有的 #include 语句导入。如果您想从系统头文件中提取类型定义而不生成任何包装器，这可能很有用。
8.3 条件编译
SWIG 完全支持使用#if、#ifdef、 #ifndef、#else、#endif来有条件地包含接口的一部分。SWIG 在解析接口时预定义了以下符号：
SWIG                            Always defined when SWIG is processing a file
SWIGIMPORTED                    Defined when SWIG is importing a file with %import
SWIG_VERSION                    Hexadecimal (binary-coded decimal) number containing SWIG version,
                                such as 0x010311 (corresponding to SWIG-1.3.11).

SWIGALLEGROCL                   Defined when using Allegro CL
SWIGCFFI                        Defined when using CFFI
SWIGCHICKEN                     Defined when using CHICKEN
SWIGCLISP                       Defined when using CLISP
SWIGCSHARP                      Defined when using C#
SWIGGUILE                       Defined when using Guile
SWIGJAVA                        Defined when using Java
SWIGJAVASCRIPT                  Defined when using Javascript
SWIG_JAVASCRIPT_JSC             Defined when using Javascript for JavascriptCore
SWIG_JAVASCRIPT_V8              Defined when using Javascript for v8 or node.js 
SWIGLUA                         Defined when using Lua
SWIGMODULA3                     Defined when using Modula-3
SWIGMZSCHEME                    Defined when using Mzscheme
SWIGOCAML                       Defined when using Ocaml
SWIGOCTAVE                      Defined when using Octave
SWIGPERL                        Defined when using Perl
SWIGPHP                         Defined when using PHP5 or PHP7
SWIGPHP5                        Defined when using PHP5
SWIGPHP7                        Defined when using PHP7
SWIGPIKE                        Defined when using Pike
SWIGPYTHON                      Defined when using Python
SWIGR                           Defined when using R
SWIGRUBY                        Defined when using Ruby
SWIGSCILAB                      Defined when using Scilab
SWIGSEXP                        Defined when using S-expressions
SWIGTCL                         Defined when using Tcl
SWIGXML                         Defined when using XML

此外，SWIG 定义了以下标准 C/C++ 宏集：
__LINE__    当前行号
__FILE__    当前文件名
__STDC__    定义为指示 ANSI C 
__cplusplus 使用 -c++ 选项时定义

接口文件可以根据需要查看这些符号，以更改生成接口的方式或将 SWIG 指令与 C 代码混合。这些符号也在 SWIG 生成的 C 代码中定义（除了仅在 SWIG 编译器中定义的符号“ SWIG ”）。
8.4 宏扩展
传统的预处理器宏可用于 SWIG 接口。请注意，# define 语句也用于尝试和检测常量。因此，如果您的文件中有这样的内容，
#ifndef _FOO_H 1 
#define _FOO_H 1 
... 
#endif

您可能会在脚本界面中看到一些额外的常量，例如 _FOO_H。
可以以标准方式定义更复杂的宏。例如：
#define EXTERN extern 
#ifdef __STDC__ 
#define _ANSI(args) (args) 
#else 
#define _ANSI(args) () 
#endif

以下运算符可以出现在宏定义中：
- #x
将宏参数x转换为由双引号 ("x") 包围的字符串。
- x ## y 
x 和 y 连接在一起形成xy。
- `x`
如果x是一个被双引号包围的字符串，则什么都不做。否则，变成像#x这样的字符串。这是一个非标准的 SWIG 扩展。
8.5 SWIG 宏
SWIG 提供增强的宏功能 %define and %enddef 指令。例如：
%define ARRAYHELPER(type, name) 
%inline %{ 
type *new_ ## name (int nitems) { 
  return (type *) malloc(sizeof(type)*nitems); 
} 
void delete_ ## name(type *t) { 
  free(t); 
}
type name ## _get(type *t, int index) { 
  return t[index]; 
} 
void name ## _set(type *t, int index, type val) { 
  t[index] = val; 
} 
%} 
%enddef 

ARRAYHELPER(int, IntArray) 
ARRAYHELPER(double, DoubleArray)

%define 的主要目的是定义大型代码宏。与普通的 C 预处理器宏不同，没有必要用连续字符 (\) 终止每一行——宏定义扩展到 %enddef 的第一次出现。此外，当这些宏被扩展时，它们会通过 C 预处理器重新解析。因此，除嵌套的 %define 语句外，SWIG 宏可以包含所有其他预处理器指令。
SWIG 宏功能是生成大量代码的一种非常快速和简单的方法。事实上，SWIG 的许多高级功能和库都是使用这种机制构建的（例如 C++ 模板支持）。
8.6 C99 和 GNU 扩展
SWIG-1.3.12 和更新版本支持可变参数预处理器宏。例如：
#define DEBUGF(fmt, ...) fprintf(stderr, fmt, __VA_ARGS__)

在使用时，任何额外的参数给...放入特殊变量__VA_ARGS__。这也适用于使用%define 定义的特殊 SWIG 宏。
SWIG 允许可变数量的参数为空。但是，这通常会导致结果扩展中出现额外的逗号 (, ) 和语法错误。例如：
DEBUGF("hello");   --> fprintf(stderr, "hello", );

要去掉多余的逗号，请像这样使用##：
#define DEBUGF(fmt, ...) fprintf(stderr, fmt, ##__VA_ARGS__)

SWIG 还支持 GNU 风格的可变参数宏。例如：
#define DEBUGF(fmt, args...) fprintf(stdout, fmt, args)

评论：不完全清楚可变参数宏如何对接口构建有用。但是，它们在内部用于实现许多 SWIG 指令，并提供它们以使 SWIG 与 C99 代码更兼容。
8.7 预处理和定界符
预处理器以不同的方式处理 { }、" " 和 %{ %} 分隔符。
8.7.1 预处理和 %{ ... %} & " ... " 分隔符
SWIG 预处理器不处理包含在代码块 %{ ... %} 中的任何文本。因此，如果您编写这样的代码，
%{ 
#ifdef NEED_BLAH 
int blah() { 
  ... 
} 
#endif 
%}

%{ ... %}块的内容不加修改地复制到输出（包括所有预处理器指令）。
8.7.2 预处理和 { ... } 分隔符
SWIG 始终对出现在{ ... } 中的文本运行预处理器。但是，有时需要将预处理器指令传递到输出文件。例如：
%extend Foo { 
  void bar() { 
    #ifdef DEBUG 
      printf("I'm in bar\n"); 
    #endif 
  } 
}

默认情况下，SWIG 将解释#ifdef DEBUG语句。但是，如果您真的希望该代码真正进入包装文件，请在预处理器指令前使用%像这样：
%extend Foo {
  void bar() {
    %#ifdef DEBUG
      printf("I'm in bar\n");
    %#endif
  }
}

SWIG 会去掉额外的 % 然后留下预处理指令在代码里。
8.8 预处理器和类型图
类型映射支持一个称为 noblock 的特殊属性，其中可以使用 { ... } 分隔符，但分隔符实际上并未生成到代码中。效果然后类似于使用“”或％{％}定界符但是代码是通过预处理器运行。例如：
#define SWIG_macro(CAST) (CAST)$input 
%typemap(in) Int {$1= SWIG_macro(int);}

可能会产生
  { 
    arg1=(int)jarg1; 
  }

然而
#define SWIG_macro(CAST) (CAST)$input 
%typemap(in, noblock=1) Int {$1= SWIG_macro(int);}

可能会产生
arg1=(int)jarg1;

和
#define SWIG_macro(CAST) (CAST)$input 
%typemap(in) Int %{$1=SWIG_macro(int);%}

会产生
arg1=SWIG_macro(int);

8.9 查看预处理器输出
与许多编译器一样，SWIG 支持 -E 命令行选项来显示预处理器的输出。使用 -E 开关时，SWIG 不会生成任何包装器。而是显示预处理器运行后的结果。这可能有助于调试和查看宏扩展的结果。
8.10 #error 和 #warning 指令
SWIG 支持常用的 #warning 和 #error 预处理器指令。该指令 #warning 指令将导致 SWIG 发出警告，然后继续处理。该 #ERROR 指令将导致 SWIG 退出致命错误。用法示例：
#error "这是一条致命错误消息" 
#warning "这是一条警告消息"

该 #error 行为可以做出像 #warning 一样的效果，如果使用命令行选项 -cpperraswarn 。或者，#pragma 指令可用于达到相同的效果，例如：
  /* 修改行为：#error 不会导致 SWIG 以错误退出 */ 
  #pragma SWIG cpperraswarn=1 
  /* 正常行为：#error 确实导致 SWIG 以错误退出 */ 
  #pragma SWIG cpperraswarn=0


9 SWIG 库
为了帮助构建扩展模块，SWIG 打包了一个支持文件库，您可以将其包含在自己的接口中。这些文件通常定义新的 SWIG 指令或提供可用于访问部分标准 C 和 C++ 库的实用程序函数。本章提供了对当前支持的库文件集的参考。
兼容性说明：旧版本的 SWIG 包含许多用于操作指针、数组和其他结构的库文件。大多数这些文件现在已被弃用并已从发行版中删除。替代库提供了类似的功能。如果您使用旧库，请仔细阅读本章。
9.1 %include 指令和库搜索路径
使用 %include 指令包含库文件。搜索文件时，按以下顺序搜索目录：
1. 当前目录
2. 使用 - I命令行选项指定的目录
3. . /swig_lib
4. swig -swiglib 报告的 SWIG 库安装位置，例如 /usr/local/share/swig/1.3.30
5. 在 Windows 上，还会搜索与 swig.exe位置相关的目录Lib。
在第 3-5 点提到的目录中，SWIG 首先查找与目标语言（例如，python、 tcl等）对应的子目录。如果找到，SWIG 将首先搜索特定于语言的目录。这允许库文件的特定语言实现。
您可以通过设置 SWIG_LIB 环境变量来忽略已安装的 SWIG 库。设置环境变量以保存替代库目录。
使用 -verbose 命令行选项时会显示搜索到的目录。
9.2 C 数组和指针
本节描述用于操作低级 C 数组和指针的库模块。这些模块的主要用途是支持操作诸如 int *、 double *或 void * 之类的裸指针的 C 声明。这些模块可用于分配内存、制造指针、取消引用内存以及将指针包装为类对象。由于这些函数提供对内存的直接访问，因此它们的使用可能不安全，您应该谨慎行事。
9.2.1 cpointer.i
所述 cpointer.i 模块定义宏可用于用来产生围绕简单 C 指针包装。该模块的主要用途是生成指向原始数据类型（如 int 和 double ）的指针。
%pointer_functions（type，name）
生成用于操作指针 类型 *的四个函数的集合：
type *new_name()
创建一个类型为type的新对象并返回一个指向它的指针。在 C 中，对象是使用calloc()创建的。在 C++ 中，使用 new。
type *copy_name（type value）
创建一个类型为type的新对象并返回一个指向它的指针。通过从value复制它来设置初始值。在 C 中，对象是使用calloc()创建的。在 C++ 中，使用new。
type *delete_name（type *obj）
删除一个对象类型type。
void name_assign(type *obj, type value)
分配 *obj = value。
type name_value（type *obj）
返回*obj的值。
使用此宏时，类型可以是任何类型，名称 必须是目标语言中的合法标识符。name不应与接口文件中使用的任何其他名称相对应。
这是使用 %pointer_functions() 的简单示例：
%module example
%include "cpointer.i"

/* Create some functions for working with "int *" */
%pointer_functions(int, intp);

/* A function that uses an "int *" */
void add(int x, int y, int *result);

现在，在 Python 中：
>>> import example 
>>> c = example.new_intp() # 创建一个“int”来存储结果
>>> example.add(3, 4, c) # 调用函数
>>> example.intp_value(c) #取消引用
7 
>>> example.delete_intp(c) # 删除

%pointer_class（type，name）
将* 类型的指针包装在基于类的接口中。这个接口如下：
struct name{
  name()；// 创建指针对象
  ~name(); // 删除指针对象
  void assign(type value); // 赋值
  type value(); // 获取值
  type *cast(); // 将指针转换为原始类型
  static name *frompointer(type *); // 从现有的
                                     // 指针创建类包装器
};

使用此宏时，类型被限制为简单的类型名称，如 int、float 或 Foo。不允许使用指针和其他复杂类型。name 必须是尚未使用的有效标识符。当一个指针被包装为一个类时，“类”可以透明地传递给任何需要该指针的函数。
如果目标语言不支持代理类，则使用此宏将生成与 %pointer_functions() 宏相同的示例函数。
应该注意的是，类接口确实引入了一个新对象或将一个指针包裹在一个特殊的结构中。相反，直接使用原始指针。
这是使用类代替的相同示例：
%module example 
%include "cpointer.i" 

/* 将类接口包裹在 "int *" */ 
%pointer_class(int, intp); 

/* 一个使用“int *”的函数 */ 
void add(int x, int y, int *result);

现在，在 Python 中（使用代理类）
>>> import example 
>>> c = example.intp() # 创建一个“int”来存储结果
>>> example.add(3, 4, c) # 调用函数
>>> c.value() # 解引用
7

在这两个宏中，%pointer_class 在使用简单指针时可能是最方便的。这是因为指针像对象一样访问，并且它们可以很容易地被垃圾收集（指针对象的销毁会破坏底层对象）。
%pointer_cast(type1, type2, name)
创建一个将 type1 转换为 type2 的转换函数 。函数的名称是name。例如：
%pointer_cast(int *, unsigned int *, int_to_uint);

在此示例中，函数int_to_uint()将用于在目标语言中强制转换类型。
注意：这些宏都不能用于安全地处理字符串（char *或char **）。
注意：当使用简单的指针时，通常可以使用类型映射来提供更无缝的操作。
9.2.2 carrays.i
该模块定义了帮助将普通 C 指针包装为数组的宏。该模块不提供任何安全性或额外的包装层——它仅提供用于创建、销毁和修改原始 C 数组数据内容的功能。
%array_functions(type, name)
创建四个函数。
type *new_name(int nelements)
创建类型为 type 的对象的新数组。在 C 中，数组是使用 calloc() 分配的。在 C++ 中，使用new []。
type  *delete_name（type *ary）
删除一个数组。在 C 中，使用free()。在 C++ 中，使用delete []。
type name_getitem(type *ary, int index)
返回 ary[index] 的值
void name_setitem(type *ary, int index, type value)
使 ARY[index] = value
使用此宏时，类型可以是任何类型，名称 必须是目标语言中的合法标识符。name不应与接口文件中使用的任何其他名称相对应。
这是 %array_functions() 的示例。假设你有一个这样的函数：
void print_array(double x[10]) { 
  int i; 
  for (i = 0; i < 10; i++) { 
    printf("[%d] = %g\n", i, x[i]); 
  } 
}

要包装它，你可以这样写：
%module example

%include "carrays.i" 
%array_functions(double, doubleArray); 

void print_array(double x[10]);

现在，在脚本语言里，你可以这样写：
a = new_doubleArray(10)             # Create an array
for i in range(0, 10):
    doubleArray_setitem(a, i, 2*i)  # Set a value
print_array(a)                      # Pass to C
delete_doubleArray(a)               # Destroy array

%array_class(type, name)
将* 类型的指针包装在基于类的接口中。这个接口如下：
struct name {
  name(int nelements);                  // Create an array
  ~name();                              // Delete array
  type getitem(int index);              // Return item
  void setitem(int index, type value);  // Set item
  type *cast();                         // Cast to original type
  static name *frompointer(type *);     // Create class wrapper from
                                        // existing pointer
};

使用此宏时，类型被限制为简单的类型名称，如 int 或 float。不允许使用指针和其他复杂类型。name 必须是尚未使用的有效标识符。当一个指针被包装为一个类时，它可以透明地传递给任何需要该指针的函数。
当与代理类结合使用时，%array_class() 宏会特别有用。例如：
%module example
%include "carrays.i" 
%array_class(double, doubleArray); 

void print_array(double x[10]);

允许您执行以下操作：
import example 
c = example.doubleArray(10) # Create double[10] 
for i in range(0, 10): 
    c[i] = 2*i # 赋值
example.print_array(c) # 传递给 C

注意：这些宏没有将 C 数组封装在特殊的数据结构或代理中。没有任何类型的边界检查或安全性。如果你想要这个，你应该考虑使用一个特殊的数组对象而不是一个裸指针。
注意： %array_functions() 和 %array_class() 不应与 char 或者 char * 类型一起使用。
9.2.3 cmalloc.i
该模块定义了用于包装低级 C 内存分配函数malloc()、calloc()、 realloc()和free() 的宏。
%malloc(type [, name=type])
使用以下原型围绕malloc()创建一个包装器：
type *malloc_ name (int nbytes = sizeof( type ));

如果type为void，则需要大小参数 nbytes。的名称参数只需要一个包装型不是一个有效的标识符时要指定（例如，“ int * ”，“double ** ”，等等）。
%calloc(type [, name=type])
使用以下原型围绕 calloc() 创建一个包装器：
type *calloc_ name (int nobj =1, int sz = sizeof( type ));

如果 type 为 void，则必须输入大小参数 sz。
%realloc(type [, name=type])
使用以下原型围绕 realloc() 创建一个包装器：
type *realloc_name（type *ptr, int nitems);

注意：与 C realloc() 不同，此宏生成的包装器隐式包含相应类型的大小。例如，realloc_int(p, 100) 重新分配 p 使其包含 100 个整数。
%free(type [, name=type])
使用以下原型围绕free()创建一个包装器：
void free_ name ( type *ptr);

%sizeof(type [, name=type])
创建常量：
%constant int sizeof_ name = sizeof( type );

%allocators(type [, name=type])
为上述所有五个操作生成包装器。
下面是一个简单的例子，说明了这些宏的使用：
// SWIG 接口
%module 示例
%include "cmalloc.i" 

%malloc(int); 
%free(int); 

%malloc(int *, intp); 
%free(int *, intp); 

%allocators(double);

现在，在脚本中：
>>> from example import * 
>>> a = malloc_int() 
>>> a 
'_000efa70_p_int' 
>>> free_int(a) 
>>> b = malloc_intp() 
>>> b 
'_000efb20_p_p_int' 
>>> free_intp(b ) 
>>> c = calloc_double(50) 
>>> c 
'_000fab98_p_double' 
>>> c = realloc_double(100000) 
>>> free_double(c) 
>>> 打印 sizeof_double 
8 
>>>

9.2.4 cdata.i
所述 cdata.i 模块定义了从或者到原始 C 数据转换成目标语言串的函数。该模块的主要应用是二进制数据结构的打包/解包——例如，如果您需要从缓冲区中提取数据。目标语言必须支持带有嵌入二进制数据的字符串才能使其工作。
const char *cdata(void *ptr, size_t nbytes)
将 ptr 处的 nbytes 数据转换为字符串。 ptr 可以是任何指针。
void memmove(void *ptr, const char *s)
将 s 中的所有字符串数据复制到 ptr 指向的内存中。该字符串可能包含嵌入的 NULL 字节。这实际上是标准 C 库memmove函数的包装器，它被声明为 void memmove(void *ptr, const void *src, size_t n)。的 src 和长度 n 参数从底层包装代码提取语言特定字符串 s 。
这些函数的一种用途是从内存中打包和解包数据。这是一个简短的例子：
// SWIG 接口
%module example
%include "carrays.i" 
%include "cdata.i" 

%array_class(int, intArray);

Python 示例：
>>> a = intArray(10) 
>>> for i in range(0, 10): 
... a[i] = i 
>>> b = cdata(a, 40) 
>>> b 
'\x00\ x00\x00\x00\x00\x00\x00\x01\x00\x00\x00\x02\x00\x00\x00\x03\x00\x00\x00\x04 
\x00\x00\x00\x05\x00\x05\x00\x00 x00\x06\x00\x00\x00\x07\x00\x00\x00\x08\x00\x00\x00\t' 
>>> c = intArray(10) 
>>> memmove(c, b) 
>>> print c[4] 
4 
>>>

由于数据的大小并不总是已知的，因此还定义了以下宏：
%cdata(type [, name=type])
生成以下函数以提取给定类型的 C 数据。
char *cdata_name(type* ptr, int nitems)

nitems 是要提取的给定类型的项目数。
注意：这些函数提供对内存的直接访问，并可用于覆盖数据。显然，它们是不安全的。
9.3 C 字符串处理
使用 C 程序时的一个常见问题是处理使用 char * 操作原始字符数据的函数。在某种程度上，问题的出现是因为对 char * 有不同的解释——它可能是以 NULL 结尾的字符串，也可能指向二进制数据。此外，操作原始字符串的函数可能会改变数据、执行隐式内存分配或使用固定大小的缓冲区。
使用char *的问题（和危险）是众所周知的。但是，SWIG 的职责不是强制执行道德。本节中的模块提供操作原始 C 字符串的基本功能。
9.3.1 默认字符串处理
假设你有一个带有这个原型的 C 函数：
char *foo(char *s);

此函数的默认包装行为是将 s 设置 为原始 char *，该字符引用目标语言中的内部字符串数据。换句话说，如果你使用像 Tcl 这样的语言，并且你写了这个，
% foo Hello

那么 s 将指向 Tcl 解释器中“Hello”的表示。返回 char * 时，SWIG 假定它是一个以 NULL 结尾的字符串并制作它的副本。这为目标语言提供了自己的结果副本。
默认行为存在明显的问题。首先，由于一个 char* 参数指向目标语言内部的数据，它是修改这些数据的不安全函数（这样做可能会损坏解释，并导致崩溃）。此外，默认行为不适用于二进制数据。相反，字符串被假定为以 NULL 结尾。
9.3.2 传递二进制数据
如果您有一个需要二进制数据的函数，
size_t parity(char *str, size_t len, size_t initial);

您可以使用类型映射将参数(char *str, size_t len)包装为单个参数。只需这样做：
%apply (char *STRING, size_t LENGTH) { (char *str, size_t len) }; 
... 
size_t parity(char *str, size_t len, size_t initial);

现在，在目标语言中，您可以像这样使用二进制字符串数据：
>>> s = "H\x00\x15eg\x09\x20"
>>> parity(s, 0)

在包装函数中，传递的字符串将被扩展为指针和长度参数。该 (char STRING, int LENGTH) 的多参数类型映射也是除了可用 (char *STRING, size_t LENGTH) 以外的补充。
9.3.3 使用 %newobject 释放内存
如果你有一个像这样分配内存的函数，
char *foo() { 
  char *result = (char *) malloc(...); 
  ...
  return result;
}

那么 SWIG 生成的包装器将发生内存泄漏——返回的数据将被复制到一个字符串对象中，而旧的内容将被忽略。
要修复内存泄漏，请使用%newobject指令。
%newobject foo; 
... 
char *foo();

如果适当的目标语言支持可用，这将发布结果。SWIG 为 char * 提供了适当的“newfree”类型映射，以便释放内存，但是，您可能需要为其他类型提供自己的“newfree”类型映射。有关更多详细信息，请参阅 对象所有权和 %newobject。
9.3.4 cstring.i
该 cstring.i 库文件提供宏的集合与函数处理，要么发生变异字符串参数或通过他们的论点试图输出字符串数据。这种函数的一个例子可能是这个相当有问题的实现：
void get_path(char *s) { 
  // 潜在的缓冲区溢出---呃，哦。
  sprintf(s, "%s/%s", base_directory, sub_directory); 
} 
... 
// C 程序中的其他地方
{ 
  char path[1024]; 
  ... 
  get_path(path); 
  ... 
}

（题外话：如果你的程序真的有这样的功能，你最好用涉及边界检查的更安全的替代方案替换它们）。
本模块中定义的宏都扩展为各种类型映射的组合。因此，相同的模式匹配规则和想法适用。
%cstring_bounded_output(parm, maxsize)
将参数 parm 转换为输出值。假设输出字符串以 NULL 结尾且小于 maxsize 个字符。下面是一个例子：
%cstring_bounded_output(char *path, 1024); 
... 
void get_path(char *path);

在目标语言中：
>>> get_path() 
/home/beazley/packages/Foo/Bar 
>>>

在内部，包装函数分配一个请求大小的小缓冲区（在堆栈上）并将其作为指针值传递。然后将存储在缓冲区中的数据作为函数返回值返回。如果函数已经返回一个值，则返回值和输出字符串一起返回（多个返回值）。如果写入的字节数超过maxsize字节，您的程序将因缓冲区溢出而崩溃！
%cstring_chunk_output(parm, chunksize)
将参数parm转换为输出值。输出字符串始终 CHUNKSIZE 并可能包含二进制数据。下面是一个例子：
%cstring_chunk_output(char *packet, PACKETSIZE); 
... 
void get_packet(char *packet);

在目标语言中：
>>> get_packet() 
'\xa9Y:\xf6\xd7\xe1\x87\xdbH;y\x97\x7f\xd3\x99\x14V\xec\x06\xea\xa2\x88' 
>>>

这个宏本质上与 %cstring_bounded_output 相同 。唯一的区别是，结果总是 CHUNKSIZE 字符。此外，结果可以包含二进制数据。如果写入的字节数超过 maxsize 字节，您的程序将因缓冲区溢出而崩溃！
%cstring_bounded_mutable(parm, maxsize)
将参数parm转换为可变字符串参数。假定输入字符串以 NULL 结尾且小于maxsize 个字符。还假定输出字符串以 NULL 结尾且少于maxsize 个 字符。
%cstring_bounded_mutable(char *ustr, 1024); 
... 
void make_upper(char *ustr);

在目标语言中：
>>> make_upper("hello world") 
'hello world' 
>>>

在内部，这个宏几乎与 %cstring_bounded_output 完全相同。唯一的区别是该参数接受用于初始化内部缓冲区的输入值。需要强调的是，这个函数不会改变传递的字符串值——而是复制输入值，改变它，然后将其作为结果返回。如果写入的字节数超过maxsize 字节，您的程序将因缓冲区溢出而崩溃！
%cstring_mutable(parm [, expansion])
将参数parm转换为可变字符串参数。假定输入字符串以 NULL 结尾。可选参数扩展指定了字符串在修改时可能增长的额外字符数。假设输出字符串以 NULL 结尾并且小于输入字符串加上任何扩展字符的大小。
%cstring_mutable(char *ustr); 
... 
void make_upper(char *ustr); 

%cstring_mutable(char *hstr, HEADER_SIZE); 
... 
void attach_header(char *hstr);

在目标语言中：
>>> make_upper("hello world") 
'HELLO WORLD' 
>>> attach_header("Hello world") 
'header: Hello world' 
>>>

这个宏与 %cstring_bounded_mutable() 的不同之处在于缓冲区是动态分配的（在堆上使用 malloc/new ）。该缓冲区总是足够大，可以存储输入值的副本以及可能已请求的任何扩展字节。需要强调的是，这个函数不会直接改变传递的字符串值——而是复制输入值，改变它，然后将其作为结果返回。如果函数扩展结果超过扩展额外字节，那么程序将因缓冲区溢出而崩溃！
%cstring_output_maxsize(parm, maxparm)
此宏用于处理有界字符输出函数，其中提供了char *和最大长度参数。作为输入，用户只需提供最大长度。返回值假定为以 NULL 结尾的字符串。
%cstring_output_maxsize(char *path, int maxpath); 
... 
void get_path(char *path, int maxpath);

在目标语言中：
>>> get_path(1024) 
'/home/beazley/Packages/Foo/Bar' 
>>>

该宏为需要将字符串数据写入缓冲区的函数提供了一种更安全的替代方法。用户提供的缓冲区大小用于在堆上动态分配内存。结果被放入该缓冲区并作为字符串对象返回。
%cstring_output_withsize(parm, maxparm)
该宏用于处理有界字符输出函数，其中传递了char *和指针int *。最初，int *参数指向一个包含最大大小的值。返回时，假定该值包含实际字节数。作为输入，用户只需提供最大长度。输出值是一个可能包含二进制数据的字符串。
%cstring_output_withsize(char *data, int *maxdata); 
... 
void get_data(char *data, int *maxdata);

在目标语言中：
>>> get_data(1024) 
'x627388912' 
>>> get_data(1024) 
'xyzzy' 
>>>

这个宏是 %cstring_output_chunk() 的一个更强大的版本。内存是动态分配的，可以是任意大的。此外，函数可以通过更改 maxparm 参数的值来控制实际返回的数据量。
%cstring_output_allocate(parm, release)
这个宏用以返回在程序内部被分配内存的字符串，然后在一个char ** 类型的参数里返回其内容。例如：
void foo(char **s) { 
  *s = (char *) malloc(64); 
  sprintf(*s, "Hello world\n"); 
}

假定返回的字符串以 NULL 结尾。 release 指定如何释放分配的内存（如果适用）。下面是一个例子：
%cstring_output_allocate(char **s, free(*$1)); 
... 
void foo(char **s);

在目标语言中：
>>> foo() 
'Hello world\n' 
>>>

%cstring_output_allocate_size(parm, szparm, release)
此宏用于返回在程序中分配的字符串，并在char **和 int *类型的两个参数中返回。例如：
void foo(char **s, int *sz) { 
  *s = (char *) malloc(64); 
  *sz = 64; 
  // 写入一些二进制数据
  ... 
}

返回的字符串可能包含二进制数据。release 指定如何释放分配的内存（如果适用）。下面是一个例子：
%cstring_output_allocate_size(char **s, int *slen, free(*$1)); 
... 
void foo(char **s, int *slen);

在目标语言中：
>>> foo() 
'\xa9Y:\xf6\xd7\xe1\x87\xdbH;y\x97\x7f\xd3\x99\x14V\xec\x06\xea\xa2\x88' 
>>>

这是在 SWIG 中返回二进制字符串数据的最安全、最可靠的方法。如果您有符合另一个原型的函数，您可以考虑使用辅助函数包装它们。例如，如果你有这个：
char *get_data(int *len);

你可以用这样的函数包装它：
void my_get_data(char **result, int *len) { 
  *result = get_data(len); 
}

评论：
- 对cstring.i模块的支持取决于目标语言。目前并非所有 SWIG 模块都支持此库。
- 可靠地处理原始 C 字符串是一个微妙的话题。在 SWIG 中有很多方法可以实现这一点。该库为一些常用技术提供支持。
- 如果在 C++ 中使用，该库使用new和delete [] 进行内存分配。如果使用 ANSI C，则库使用malloc() 和free()。
- 与其直接操作char *，不如考虑使用特殊的字符串结构或类。
9.4 STL/C++ 库
本节中的库模块提供对包括 STL 在内的标准 C++ 库部分的访问。SWIG 对 STL 的支持是一项持续的努力。对某些语言模块的支持非常全面，但一些较少使用的模块没有编写那么多的库代码。
下表显示了支持哪些 C++ 类以及 C++ 库的等效 SWIG 接口库文件。
无法复制加载中的内容
这份清单绝不是完整的。一些语言模块支持上述的一个子集，一些支持额外的 STL 类。请在相应的语言库目录中查找库文件。
9.4.1 std::string
std_string.i 库提供了用于转换 C++ 的 std :: string 对象的 typemaps，和从目标的脚本语言的字符串到 std::string 对象的 typemaps。例如：
%module example
%include "std_string.i" 

std::string foo(); 
void bar(const std::string &x);

在目标语言中：
x = foo(); # 返回一个字符串对象
bar("Hello World"); # 将字符串作为 std::string 传递

人们遇到的一个常见问题是包含 std::string 的类/结构。这可以通过定义类型映射来克服。例如：
%module example
%include "std_string.i" 

%apply const std::string& {std::string* foo}; 

struct my_struct 
{ 
  std::string foo; 
};

在目标语言中:
x = my_struct(); 
x.foo="Hello World"; # 用字符串赋值
print x.foo; # 打印为字符串

该模块仅支持 std::string 和 const std::string & 类型。指针和非常量引用保持不变并作为 SWIG 指针返回。
该库文件完全了解 C++ 命名空间。如果导出 std::string 或使用 typedef 重命名它，请确保在接口中包含这些声明。例如：
%module example
%include "std_string.i" 

using namespace std; 
typedef std::string String；
... 
void foo(string s, const String &t); // std_string 类型映射仍然适用

9.4.2 std::vector
std_vector.i 库提供了用于 C ++ 支持的 STL std ::vector 类。使用此库涉及使用 %template 指令。您需要做的就是为要使用的类型实例化不同版本的 vector。例如：
%module example
%include "std_vector.i"

namespace std { 
  %template(vectori) vector<int>; 
  %template(vectord) vector<double>; 
};

当模板 vector<X> 被实例化时，会发生很多事情：
- 在目标语言中创建了一个公开 C++ API 的类。这可以用于创建对象、调用方法等。这个类目前是真正的 STL 向量类的子集。
- 输入类型映射是为 vector<X>、const vector<X> &和const vector<X> * 定义的。对于这些中的每一个，可以传递一个指针 vector<X> * 或目标语言中的本机列表对象。
- 为 vector<X> 定义了一个输出类型映射。在这种情况下，向量中的值被扩展为目标语言的列表对象。
- 对于该类型的所有其他变体，包装器希望以通常的方式接收 vector<X> * 对象。
- 定义了 std::out_of_range 的异常处理程序。
- 可选地，可以定义用于索引、项目检索、切片和元素分配的特殊方法。这取决于目标语言。
为了说明这个库的使用，请考虑以下函数：
/* 文件：example.h */ 

#include <vector> 
#include <algorithm> 
#include <functional> 
#include <numeric> 

double average(std::vector<int> v) { 
  return std::accumulate(v.begin(), v.end(), 0.0)/v.size(); 
} 

std::vector<double> half(const std::vector<double>& v) { 
  std::vector<double> w(v); 
  for (unsigned int i=0; i<w.size(); i++) 
    w[i] /= 2.0; 
  return w; 
} 

void halve_in_place(std::vector<double>& v) { 
  std::transform(v.begin(), v.end(), v.begin(), 
                 std::bind2nd(std::divides<double> (), 2.0)); 
}

要使用 SWIG 包装，您可以编写以下内容：
%module example 
%{ 
#include "example.h" 
%} 

%include "std_vector.i" 
// 实例化 example 用到的模板
namespace std { 
  %template(IntVector) vector<int>; 
  %template(DoubleVector) vector<double>; 
} 

// 包含具有上述原型的头文件
%include "example.h"

现在，为了说明脚本解释器中的行为，请考虑以下 Python 示例：
>>> from example import *
>>> iv = IntVector(4)         # Create an vector<int>
>>> for i in range(0, 4):
...      iv[i] = i
>>> average(iv)               # Call method
1.5
>>> average([0, 1, 2, 3])        # Call with list
1.5
>>> half([1, 2, 3])             # Half a list
(0.5, 1.0, 1.5)
>>> halve_in_place([1, 2, 3])   # Oops
Traceback (most recent call last):
  File "<stdin>", line 1, in ?
TypeError: Type error. Expected _p_std__vectorTdouble_t
>>> dv = DoubleVector(4)
>>> for i in range(0, 4):
...       dv[i] = i
>>> halve_in_place(dv)       # Ok
>>> for i in dv:
...       print i
...
0.0
0.5
1.0
1.5
>>> dv[20] = 4.5
Traceback (most recent call last):
  File "<stdin>", line 1, in ?
  File "example.py", line 81, in __setitem__
    def __setitem__(*args): return apply(examplec.DoubleVector___setitem__, args)
IndexError: vector index out of range
>>>

这个库模块完全了解 C++ 命名空间。如果您使用具有其他名称的向量，请确保包含适当的 using 或 typedef 指令。例如：
include "std_vector.i"

namespace std {
  %template(IntVector) vector<int>;
}

using namespace std;
typedef std::vector Vector;

void foo(vector<int> *x, const Vector &x);

注意：该模块利用了几个高级 SWIG 功能，包括模板化类型映射和模板部分特化。如果您尝试使用模板包装其他 C++ 代码，您可以查看 std_vector.i 中包含的代码。或者，如果你想让他们的头爆炸，你可以向他们展示代码。
注意：此模块是为所有 SWIG 目标语言定义的。然而，参数转换细节和暴露给解释器的公共 API 各不相同。
9.4.3 STL 异常
许多 STL 包装函数添加了参数检查，如果值无效，将抛出依赖于语言的错误/异常。经典的例子是数组边界检查。编写库包装器以在出现错误时抛出 C++ 异常。C++ 异常反过来被转换为目标语言的适当错误/异常。总的来说，这种处理不需要定制，但是，可以通过提供适当的“抛出”类型映射轻松实现定制。例如：
%module exapmle
%include "std_vector.i" 
%typemap(throws) std::out_of_range { 
  // 自定义异常处理程序
} 
%template(VectInt) std::vector<int>;

例如，自定义异常处理程序可能会记录异常，然后将其转换为目标语言的特定错误/异常。
使用 STL 时，建议添加异常处理程序以捕获所有 STL 异常。所述％异常指令可以用于通过将下面的代码来被包装的任何其他方法或库之前：
%include "exception.i" 

%exception { 
  try { 
    $action 
  } catch (const std::exception& e) { 
    SWIG_exception(SWIG_RuntimeError, e.what()); 
  } 
}

任何抛出的 STL 异常都将得到妥善处理，而不是导致崩溃。
9.4.4 shared_ptr 智能指针
一些目标语言支持处理 shared_ptr 引用计数智能指针。这个智能指针在标准 C++11 库中作为std::shared_ptr 可用。在完全标准化之前，它也在 TR1 中作为 std::tr1::shared_ptr 出现。还可以支持广泛使用的 boost::shared_ptr。
为了使用 std::shared_ptr， 应该包括 std_shared_ptr.i库文件：
%include <std_shared_ptr.i>

预标准 std::tr1::shared_ptr 可以通过在包含 std_shared_ptr.i 库文件之前包含以下宏来使用：
#define SWIG_SHARED_PTR_SUBNAMESPACE tr1 
%include <std_shared_ptr.i>

为了使用boost::shared_ptr，应该包含 boost_shared_ptr.i 库文件：
%include <boost_shared_ptr.i>

您一次只能在接口文件中使用 shared_ptr 的这些变体之一。并且所有三个变体都必须与 %shared_ptr(T) 宏结合使用，其中T是基础指针类型，等同于使用 shared_ptr<T>。类型 T 必须是非原始的。一个简单的例子演示了用法：
%module example
%include <boost_shared_ptr.i> 
%shared_ptr(IntValue) 

%inline %{ 
#include <boost/shared_ptr.hpp> 

struct IntValue { 
  int value; 
  IntValue(int v) : value(v) {} 
}; 

static int extractValue(const IntValue &t) { 
  return t.value; 
} 

static int extractValueSmart(boost::shared_ptr<IntValue> t) { 
  return t->value; 
} 
%}

请注意，%shared_ptr(IntValue) 声明发生在包含提供宏的 boost_shared_ptr.i 库之后，非常重要的是，发生在 IntValue 类型的任何使用或声明之前。在 ％shared_ptr 的宏提供，有几件事情都处理这个智能指针，但大多是一些 typemaps 的。这些类型映射覆盖默认类型映射，以便存储底层代理类并将其作为指向 shared_ptr 的指针传递而不是指向基础类型的普通指针。这种方法意味着该类型的任何实例化都可以通过值、引用、指针或作为智能指针传递给采用该类型的方法。感兴趣的读者可能想查看生成的代码，但是，用法很简单，不需要与目标语言不同的处理方式。例如，上述 Java 代码的一个简单用例是：
IntValue iv = new IntValue(1234); 
int val1 = example.extractValue(iv); 
int val2 = example.extractValueSmart(iv); 
System.out.println(val1 + " " + val2);

这个 shared_ptr 库的工作方式与 SWIG 的正常但有些有限的智能指针处理完全不同。除了代理类之外，shared_ptr 库不会生成额外的包装器，仅用于智能指针处理。包含继承关系的普通代理类照常生成。%shared_ptr 宏引入的唯一真正变化是代理类存储了一个指向 shared_ptr 实例的指针，而不是一个指向该实例的原始指针。从一个被 shared_ptr 包装的基派生的代理类也可以并且 必须 被包装为一个 shared_ptr。换句话说，继承层次结构中的所有类都必须与 %shared_ptr 一起使用 宏。例如，以下代码可以与前面显示的基类一起使用：
%shared_ptr(DerivedIntValue) 
%inline %{ 
struct DerivedIntValue : IntValue { 
  DerivedIntValue(int value) : IntValue(value) {} 
  ... 
}; 
%}

现在可以将派生类的 shared_ptr 传递给目标语言中需要基类的方法，就像在 C++ 中一样：
DerivedIntValue div = new DerivedIntValue(5678); 
int val3 = example.extractValue(div); 
int val4 = example.extractValueSmart(div);

如果继承层次结构中的任何类省略了 %shared_ptr 宏，SWIG 将对此发出警告，并且生成的代码可能会也可能不会导致 C++ 编译错误。例如，以下输入：
%include "boost_shared_ptr.i" 
%shared_ptr(Parent); 

%inline %{ 
  #include <boost/shared_ptr.hpp> 
  struct GrandParent { 
    virtual ~GrandParent() {} 
  }; 

  struct Parent : GrandParent { 
    virtual ~Parent() {} 
  }; 

  struct Child : Parent { 
    virtual ~Child() {} 
  }; 
%}

警告丢失的智能指针信息：
example.i:12: Warning 520: Base class 'GrandParent' of 'Parent' is not similarly marked as a smart pointer.
example.i:16: Warning 520: Derived class 'Child' of 'Parent' is not similarly marked as a smart pointer.

添加缺少的 %shared_ptr 宏将解决此问题：
%include "boost_shared_ptr.i" 
%shared_ptr(GrandParent); 
%shared_ptr(Parent); 
%shared_ptr(Child); 

... as before ...

注意：对 %shared_ptr 和 director feature 的支持有些有限，完成的程度因不同的目标语言而异。请通过提供具有改进功能的补丁来帮助改进此支持。
9.4.5 auto_ptr 智能指针
尽管std::auto_ptr 在 C++11 中已弃用，但一些现有代码可能仍在使用它，因此 SWIG 为此类提供有限的支持：std_auto_ptr.i 定义了适用于返回此类型对象的函数的类型映射。不直接支持  std_auto_ptr.i 的任何其他用途。
一个典型的使用示例是
%include <std_auto_ptr.i> 

%auto_ptr(Klass) 
%inline %{ 
class Klass { 
public: 
  // 创建这个类的对象的工厂函数：
  static std::auto_ptr<Klass> Create(int value) { 
    return std::auto_ptr <Klass>(新的Klass(value)); 
  } 

  int getValue() const { return m_value; } 

private: 
  DerivedIntValue(int value) : m_value(value) {} 
  int m_value; 
}; 
%}

返回的对象可以自然地从目标语言使用，例如从 C#：
Klass k = Klass.Create(17); 
int value = k.getValue();

9.5 实用程序库
9.5.1 exception.i
该 exception.i 库提供了一个独立于语言的功能在目标语言养运行时异常。该库主要由 SWIG 库编写者使用。如果可能，请使用目标语言可用的错误处理方案，因为在可以抛出哪些错误/异常方面具有更大的灵活性。
SWIG_exception(int code，const char *message)
在目标语言中引发异常。code是以下符号常量之一：
SWIG_MemoryError 
SWIG_IOError 
SWIG_RuntimeError 
SWIG_IndexError 
SWIG_TypeError 
SWIG_DivisionByZero 
SWIG_OverflowError 
SWIG_SyntaxError 
SWIG_ValueError 
SWIG_SystemError

message 是一个字符串，表示有关问题的更多信息。
该模块的主要用途是编写与语言无关的异常处理程序。例如：
%include "exception.i" 
%exception std::vector::getitem { 
  try { 
    $action 
  } catch (std::out_of_range & e) { 
    SWIG_exception(SWIG_IndexError, const_cast<char*>(e.what())); 
  } 
}

10 参数处理
在第 3 章中，描述了 SWIG 对基本数据类型和指针的处理。特别是对 int 和 double 等原始类型映射到目标语言中的相应类型。对于其他一切，指针用于引用结构、类、数组和其他用户定义的数据类型。但是，在某些应用程序中，需要更改 SWIG 对特定数据类型的处理。例如，您可能希望通过函数的参数返回多个值。本章介绍了执行此操作的一些技术。
10.1 typemaps.i 库
本节介绍 typemaps.i 库文件——通常用于更改参数转换的某些属性。
10.1.1 简介
假设你有一个这样的 C 函数：
void add(double a, double b, double *result) { 
  *result = a + b; 
}

从阅读源代码可以看出，该函数在 double *result 参数中存储了一个值。但是，由于 SWIG 不检查函数体，因此无法知道这是底层行为。
处理这个问题的一种方法是使用 typemaps.i 库文件并编写如下接口代码：
// 使用类型映射的简单示例
%module example 
%include "typemaps.i" 

%apply double *OUTPUT { double *result }; 
%inline %{ 
extern void add(double a, double b, double *result); 
%}

在 ％apply 指令告诉 SWIG，你将要用一个特殊类型的处理规则适用于一个类型。“ double *OUTPUT ” 规范定义了一个名称，此名称定义了描述如何从 double * 类型的参数返回输出值的规则。此规则适用于花括号中列出的所有数据类型——在本例中为“ double *result ”。
创建生成的模块后，您现在可以使用这样的函数（针对 Python 显示）：
>>> a = add(3, 4) 
>>> print a
7 
>>>

在这种情况下，您可以看到通常在第三个参数中返回的输出值是如何神奇地转换为函数返回值的。显然，这使得函数更易于使用，因为不再需要制造特殊的 double * 对象并以某种方式将其传递给函数。
一旦一个类型映射被应用到一个类型上，它对所有未来出现的类型和名称都有效。例如，您可以编写以下内容：
%module example
%include "typemaps.i" 

%apply double *OUTPUT { double *result }; 

%inline %{ 
extern void add(double a, double b, double *result); 
extern void sub(double a, double b, double *result); 
extern void mul(double a, double b, double *result); 
extern void div(double a, double b, double *result); 
%} 
...

在这种情况下，double *OUTPUT 规则适用于后面的所有函数。
类型映射转换甚至可以扩展到多个返回值。例如，考虑以下代码：
%include "typemaps.i" 
%apply int *OUTPUT { int *width, int *height }; 

// 返回一对 (width, height) 
void getwinsize(int winid, int *width, int *height);

在这种情况下，该函数返回多个值，允许像这样使用它：
>>> w, h = genwinsize(wid) 
>>> print w 
400 
>>> print h 
300 
>>>

还应该注意的是，虽然 %apply 指令用于将类型映射规则与数据类型相关联，但您也可以直接在参数中使用规则名称。例如，你可以这样写：
// 使用类型映射的简单示例
%module example 
%include "typemaps.i" 

%{ 
extern void add(double a, double b, double *OUTPUT); 
%} 
extern void add(double a, double b, double *OUTPUT);

类型映射一直有效，直到它们被明确删除或重新定义为其他内容。要清除类型映射， 应使用 %clear 指令。例如：
%clear double *result; // 删除 double *result 的所有类型映射

10.1.2 输入参数
以下类型映射指示 SWIG 指针实际上仅包含单个输入值：
int *INPUT 
short *INPUT 
long *INPUT 
unsigned int *INPUT 
unsigned short *INPUT 
unsigned long *INPUT 
double *INPUT 
float *INPUT

使用时，它允许传递值而不是指针。例如，考虑这个函数：
double add(double *a, double *b) { 
  return *a+*b; 
}

现在，考虑这个 SWIG 接口：
%module example
%include "typemaps.i" 
... 
%{ 
extern double add(double *, double *); 
%} 
extern double add(double *INPUT, double *INPUT);

当该函数在脚本语言解释器中使用时，它会像这样工作：
result = add(3, 4)

10.1.3 输出参数
以下类型映射规则告诉 SWIG 指针是函数的输出值。使用时，调用函数时不需要提供参数。而是返回一个或多个输出值。
int *OUTPUT 
short *OUTPUT 
long *OUTPUT 
unsigned int *OUTPUT 
unsigned short *OUTPUT 
unsigned long *OUTPUT 
double *OUTPUT 
float *OUTPUT

可以如前面的示例中所示使用这些方法。例如，如果你有这个 C 函数：
void add(double a, double b, double *c) { 
  *c = a+b; 
}

SWIG 接口文件可能如下所示：
%module example
%include "typemaps.i" 
... 
%inline %{ 
extern void add(double a, double b, double *OUTPUT); 
%}

在这种情况下，只返回一个输出值，但这不是限制。通过将输出规则应用于多个参数（如前所示），可以返回任意数量的输出值。
如果函数还返回一个值，则它与参数一起返回。例如，如果你有这个：
extern int foo(double a, double b, double *OUTPUT);

该函数将返回两个值，如下所示：
iresult, dresult = foo(3.5, 2)

10.1.4 输入/输出参数
当指针同时用作输入和输出值时，您可以使用以下类型映射：
int *INOUT 
short *INOUT 
long *INOUT 
unsigned int *INOUT 
unsigned short *INOUT 
unsigned long *INOUT 
double *INOUT 
float *INOUT

一个使用它的 C 函数可能是这样的：
void negate(double *x) { 
  *x = -(*x); 
}

要使 x 既作为输入值又作为输出值，请在接口文件中像这样声明函数：
%module example
%include "typemaps.i" 
... 
%{ 
extern void negate(double *); 
%} 
extern void negate(double *INOUT);

现在在脚本中，您可以简单地正常调用函数：
a = negate(3); # a = -3 调用后

INOUT 规则的一个微妙之处是许多脚本语言对原始对象强制执行可变性约束（这意味着不应该更改像整数和字符串这样的简单对象）。因此，您不能像本示例中的底层 C 函数那样就地修改对象的值。因此， INOUT 规则将修改后的值作为新对象返回，而不是直接覆盖原始输入对象的值。
兼容性注意：该 INOUT 使用规则被称为 BOTH 在早期版本痛饮。保留向后兼容性，但已弃用。
10.1.5 使用不同的名称
如前所述，%apply 指令可用于将 INPUT、OUTPUT 和 INOUT 类型映射应用于不同的参数名称。例如：
// 使 double *result 成为输出值
%apply double *OUTPUT { double *result }; 

// 使 Int32 *in 成为输入值
%apply int *INPUT { Int32 *in }; 

// 使 long *x inout 
%apply long *INOUT {long *x};

要清除规则，请使用%clear指令：
%clear double *result; 
%clear Int32 *in, long *x;

类型映射声明是词法范围的，因此类型映射从定义点到文件末尾或匹配的 %clear 声明生效。
10.2 对输入值应用约束
除了改变对各种输入值的处理之外，还可以使用类型映射来应用约束。例如，您可能想确保一个值是正数，或者一个指针是非 NULL。这可以通过 constraints.i 库文件来完成。
10.2.1 简单约束示例
以下接口文件最好地说明了约束库：
// 带有约束的接口文件
%module example 
%include "constraints.i" 

double exp(double x); 
double log(double POSITIVE);     // 只允许正值
double sqrt(double NONNEGATIVE); // 非负值只有
double inv(double NONZERO);      // 非零值
void free(void *NONNULL);        // 仅非空指针

此文件的行为与您预期的完全一样。如果任何参数违反约束条件，将引发脚本语言异常。因此，可以捕获错误的值，防止神秘的程序崩溃等。
10.2.2 约束方法
以下约束目前可用
POSITIVE                     Any number > 0 (not zero)
NEGATIVE                     Any number < 0 (not zero)
NONNEGATIVE                  Any number >= 0
NONPOSITIVE                  Any number <= 0
NONZERO                      Nonzero number
NONNULL                      Non-NULL pointer (pointers only).

10.2.3 对新数据类型应用约束
约束库仅支持原始 C 数据类型，但使用%apply可以轻松将其应用于新数据类型。例如 ：
// 对 Real 变量应用约束
%apply Number POSITIVE { Real in }; 

// 对指针类型应用约束
%apply Pointer NONNULL { Vector * };

“数字”和“指针”的特殊类型可以分别应用于任何数字和指针变量类型。要稍后删除约束，可以使用%clear指令：
%clear Real in；
%clear Vector *;


11 Typemaps
11.1 简介
很有可能，您正在阅读本章是出于以下两个原因之一；您要么想自定义 SWIG 的行为，要么无意中听到有人嘟囔着一些关于“typemaps”的难以理解的胡言乱语，然后问自己“typemaps，那些是什么？” 也就是说，让我们先做一个简短的免责声明，即“Typemaps”是一种高级自定义功能，可以直接访问 SWIG 的低级代码生成器。不仅如此，它们还是 SWIG C++ 类型系统（它自己的一个重要主题）的组成部分。typemaps 通常不是使用 SWIG 的必需部分。因此，如果您已经找到了进入本章的方法，并且对 SWIG 默认情况下已经做了什么只有一个模糊的概念，那么您可能需要重新阅读前面的章节。
11.1.1 类型转换
包装器代码生成中最重要的问题之一是编程语言之间数据类型的转换或编组。具体来说，对于每个 C/C++ 声明，SWIG 必须以某种方式生成包装器代码，允许值在语言之间来回传递。由于每种编程语言都不同地表示数据，这不是简单地将代码与 C 链接器链接在一起的问题。相反，SWIG 必须了解数据在每种语言中的表示方式以及如何操作。
为了说明这一点，假设您有一个简单的 C 函数，如下所示：
int factorial(int n);

要从 Python 访问此函数，需要使用一对 Python API 函数来转换整数值。例如：
long PyInt_AsLong(PyObject *obj); /* Python --> C */ 
PyObject *PyInt_FromLong(long x); /* C --> Python */

第一个函数用于将输入参数从 Python 整数对象转换为 C long。第二个函数用于将值从 C 转换回 Python 整数对象。
在包装函数内部，您可能会看到这些函数的使用方式如下：
PyObject *wrap_factorial(PyObject *self, PyObject *args) {
  int       arg1;
  int       result;
  PyObject *obj1;
  PyObject *resultobj;

  if (!PyArg_ParseTuple("O:factorial", &obj1)) return NULL;
  arg1 = PyInt_AsLong(obj1);
  result = factorial(arg1);
  resultobj = PyInt_FromLong(result);
  return resultobj;
}

SWIG 支持的每种目标语言都具有以类似方式工作的函数。例如，在 Perl 中，使用了以下函数：
IV SvIV(SV *sv); /* Perl --> C */ 
void sv_setiv(SV *sv, IV val); /* C --> Perl */

在 Tcl 中：
int Tcl_GetLongFromObj(Tcl_Interp *interp, Tcl_Obj *obj, long *value); 
Tcl_Obj *Tcl_NewIntObj(long value);

精确的细节并不是那么重要。重要的是，所有的底层类型转换都是由实用函数的集合和像这样的一小段 C 代码来处理的——你只需要阅读你喜欢的语言的扩展文档就知道它是如何工作的（剩下的练习给读者）。
11.1.2 Typemaps
由于类型处理对于包装器代码生成非常重要，因此 SWIG 允许用户完全定义（或重新定义）它。为此，使用了一个特殊的 %typemap 指令。例如：
/* 从 Python 转换 --> C */ 
%typemap(in) int { 
  $1 = PyInt_AsLong($input); 
} 

/* 从 C 转换 --> Python */ 
%typemap(out) int { 
  $result = PyInt_FromLong($1); 
}

乍一看，这段代码看起来有点混乱。然而，它真的没有多少。第一个类型映射（“in”类型映射）用于将值从目标语言转换为 C。第二个类型映射（“out”类型映射）用于向另一个方向转换。每个类型映射的内容都是一小段代码，直接插入到 SWIG 生成的包装器函数中。代码通常是 C 或 C++ 代码，它们将生成到 C/C++ 包装函数中。请注意，情况并非总是如此，因为某些目标语言模块允许在生成到目标语言特定文件中的类型映射中使用目标语言代码。在此代码中，扩展了许多以 $ 为前缀的特殊变量。这些实际上只是在创建包装函数的过程中生成的 C/C++ 变量的占位符。在这种情况下，$input 指的是需要转换为 C/C++ 的输入对象， $result 指的是将由包装函数返回的对象。$1 指的是一个 C/C++ 变量，它的类型与 typemap 声明中指定的类型相同（本例中为 int）。
一个简短的例子可能会使这更清楚一点。如果您要包装这样的函数：
int gcd(int x, int y);

一个包装函数大概是这样的：
PyObject *wrap_gcd(PyObject *self, PyObject *args) {
  int arg1;
  int arg2;
  int result;
  PyObject *obj1;
  PyObject *obj2;
  PyObject *resultobj;

  if (!PyArg_ParseTuple("OO:gcd", &obj1, &obj2)) return NULL;

  /* "in" typemap, argument 1 */
  {
    arg1 = PyInt_AsLong(obj1);
  }
  /* "in" typemap, argument 2 */
  {
    arg2 = PyInt_AsLong(obj2);
  }
  result = gcd(arg1, arg2);

  /* "out" typemap, return value */
  {
    resultobj = PyInt_FromLong(result);
  }
  return resultobj;
}

在这段代码中，您可以看到 typemap 代码是如何插入到函数中的。您还可以看到特殊的 $ 变量如何被扩展以匹配包装函数内的某些变量名称。这确实是 typemaps 背后的全部想法——它们只是让您将任意代码插入到生成的包装函数的不同部分中。因为可以插入任意代码，所以可以完全改变值的转换方式。
11.1.3 模式匹配
顾名思义，类型映射的目的是将 C 数据类型“映射”到目标语言中的类型。一旦为 C 数据类型定义了类型映射，它就会应用于输入文件中该类型的所有未来出现。例如：
/* 从 Perl 转换 --> C */ 
%typemap(in) int { 
  $1 = SvIV($input); 
} 

... 
int factorial( int n); 
int gcd( int x, int y); 
int count(char *s, char *t, int max);

类型映射与 C 数据类型的匹配不仅仅是简单的文本匹配。事实上，类型映射完全内置于底层类型系统中。因此，类型映射不受 typedef、命名空间和其他可能隐藏底层类型的声明的影响。例如，你可以有这样的代码：
/* Convert from Ruby--> C */
%typemap(in) int {
  $1 = NUM2INT($input);
}
...
typedef int Integer;
namespace foo {
  typedef Integer Number;
};

int foo(int x);
int bar(Integer y);
int spam(foo::Number a, foo::Number b);

在这种情况下，即使类型名并不总是与文本 “int” 匹配，类型映射仍然适用于正确的参数。这种跟踪类型的能力是 SWIG 的关键部分——事实上，所有目标语言模块的工作只是为基本类型定义一组类型映射。然而，从来没有必要为 typedef 引入的类型名编写新的类型映射。
除了跟踪类型名称之外，类型映射也可以专门用于匹配特定的参数名称。例如，您可以编写这样的类型映射：
%typemap(in) double nonnegative {
  $1 = PyFloat_AsDouble($input);
  if ($1 < 0) {
    PyErr_SetString(PyExc_ValueError, "argument must be nonnegative.");
    SWIG_fail;
  }
}

...
double sin(double x);
double cos(double x);
double sqrt(double nonnegative);

typedef double Real;
double log(Real nonnegative);
...

对于某些任务，例如输入参数转换，可以为连续参数序列定义类型映射。例如：
%typemap(in) ( char *str, int len ) { 
  $1 = PyString_AsString($input); /* 字符 *str */ 
  $2 = PyString_Size($input); /* int len */ 
} 
... 
int count( char *str, int len, char c);

在这种情况下，单个输入对象被扩展为一对 C 参数。此示例还提供了对涉及$1、$2等的不寻常变量命名方案的提示。
11.1.4 重用类型映射
类型映射通常是为特定类型和参数名称模式定义的。但是，也可以复制和重用类型映射。一种方法是使用这样的赋值：
%typemap(in) Integer = int;   
%typemap(in) (char *buffer, int size) = (char *str, int len);

在 %apply 指令中可以找到更通用的复制形式，如下所示：
%typemap(in) int { 
  /* 转换整数参数 */ 
  ... 
} 
%typemap(out) int { 
  /* 返回一个整数值 */ 
  ... 
} 

/* 将所有整数类型映射应用于 size_t */ 
%apply int { size_t };    

%apply 仅获取为一种类型定义的所有类型映射并将它们应用于其他类型。注意：您可以在 %apply 的 { ... } 部分包含一组逗号分隔的类型。
应该注意的是，没有必要为 typedef 相关的类型复制类型映射。例如，如果你有这个，
typedef int size_t;

那么 SWIG 已经知道int 类型映射适用。你不必做任何事情。
11.1.5 typemaps 可以做什么？
Typemaps 的主要用途是在单个 C/C++ 数据类型级别定义包装器生成行为。目前，typemaps 可以解决六大类问题：
参数处理
int foo( int x, double y, char *s );

- 输入参数转换（“in”类型映射）。
- 重载方法中使用的类型的输入参数类型检查（“typecheck”类型映射）。
- 输出参数处理（“argout”类型映射）。
- 输入参数值检查（“check”类型映射）。
- 输入参数初始化（“arginit”类型映射）。
- 默认参数（“default”类型映射）。
- 输入参数资源管理（“freearg”类型映射）。
返回值处理
int foo(int x, double y, char *s);

- 函数返回值转换（“out”类型映射）。
- 返回值资源管理（“ret”类型映射）。
- 新分配对象的资源管理（“newfree”类型映射）。
异常处理
int foo(int x, double y, char *s) throw( MemoryError, IndexError );

- 处理 C++ 异常规范。（“抛出”类型图）。
全局变量
int foo;

- 全局变量的赋值。（“varin”类型映射）。
- 读取全局变量。（“varout”类型映射）。
成员变量
struct Foo {
   int x[20] ; 
};

- 将数据分配给类/结构成员。（“memberin”类型映射）。
常量创建
#define FOO 3 
%constant int BAR = 42; 
Enum { ALE, LAGER, STOUT };

- 创建常量值。（“consttab”或“constcode”类型映射）。
稍后将介绍这些类型图的详细信息。此外，某些语言模块可能会定义在此列表上扩展的其他类型映射。例如，Java 模块定义了各种类型映射来控制 Java 绑定的其他方面。有关更多详细信息，请参阅特定于语言的文档。
11.1.6 typemaps 不能做什么？
类型映射不能用于定义适用于整个 C/C++ 声明的属性。例如，假设你有一个这样的声明，
Foo *make_Foo(int n);

并且您想告诉 SWIG make_Foo(int n)返回了一个新分配的对象（为了提供更好的内存管理）。显然，这个属性 make_Foo(INT N) 是不 会与属性 Foo*本身 的数据类型关联。因此，为此目的使用了完全不同的 SWIG 自定义机制 ( %feature )。有关更多信息，请参阅 自定义功能一章。
类型映射也不能用于重新排列或转换参数的顺序。例如，如果你有一个这样的函数：
void foo(int, char *);

你不能使用类型映射来交换参数，允许你像这样调用函数：
foo("hello", 3) # 反转参数

如果要更改函数的调用约定，请编写辅助函数。例如：
%rename(foo) wrap_foo; 
%inline %{ 
void wrap_foo(char *s, int x) { 
  foo(x, s); 
} 
%}

11.1.7 与面向切面编程的相似之处
SWIG 与 面向切面的软件开发 (AOP)相似。与 SWIG 类型映射相关的 AOP 术语可以查看如下：
- 横切关注点：横切关注点是类型映射实现的功能的模块化，主要是从/到目标语言和 C/C++ 的类型编组。
- 建议：typemap 主体包含在需要编组时执行的代码。
- 切入点：切入点是类型映射代码生成到的包装器代码中的位置。
- Aspect：Aspects 是切入点和通知的组合，因此每个 typemap 都是一个切面。
SWIG 也可以被视为具有基于 %feature 的第二组切面。诸如 %exception 之类的功能也是横切关注点，因为它们封装了可用于向任何函数添加日志记录或异常处理的代码。
11.1.8 本章的其余部分
本章的其余部分为想要编写新类型映射的人提供了详细信息。此信息对于打算编写新 SWIG 目标语言模块的任何人都特别重要。高级用户还可以使用此信息来编写特定于应用程序的类型转换规则。
由于类型映射与底层 C++ 类型系统紧密相关，因此后续部分假设您相当熟悉值、指针、引用、数组、类型限定符（例如，const）、结构、命名空间、模板和内存管理的基本细节在 C/C++ 中。如果没有，建议您在继续之前查阅 Kernighan 和 Ritchie 的“C 编程语言”或 Stroustrup 的“C++ 编程语言”的副本。
11.2 Typemap 规范
本节描述 %typemap 指令本身的行为。
11.2.1 定义 typemap
使用 %typemap 声明定义新类型映射。该声明的一般形式如下（[ ... ] 中的部分是可选的）：
%typemap( method [, modifiers ]) typelist code；
method 只是一个名称，用于指定正在定义的类型映射类型。它通常是一个名称，如"in"、 "out"或"argout"。这些方法的目的将在后面描述。
modifiers 是一个可选的逗号分隔的 name="value"值列表。这些有时用于将额外信息附加到类型映射，并且通常依赖于目标语言。它们也称为类型映射属性。
typelist 是 typemap 将匹配的 C++ 类型模式的列表。该列表的一般形式如下：
typelist : typepattern [, typepattern, typepattern, ... ] ; 

typepattern :  type [ (parms) ]
            |  type name [ (parms) ]
            |  ( typelist ) [ (parms) ]

每个类型模式要么是一个简单类型，一个简单类型和参数名称，要么是多参数类型映射的类型列表。此外，每个类型模式都可以使用临时变量（参数）列表进行参数化。稍后将解释这些变量的用途。
code 指定类型映射中使用的代码。通常这是 C/C++ 代码，但在静态类型的目标语言中，例如 Java 和 C#，这可以包含某些类型映射的目标语言代码。它可以采用以下任何一种形式：
code       : { ... }
           | " ... "
           | %{ ... %}

请注意，预处理器将在 {} 分隔符内展开代码，但不会在最后两种分隔符样式中展开，请参阅 Preprocessor 和 Typemaps。以下是一些有效类型映射规范的示例：
/* 简单的类型映射声明 */ 
%typemap(in) int { 
  $1 = PyInt_AsLong($input); 
} 
%typemap(in) int "$1 = PyInt_AsLong($input);"; 
%typemap(in) int %{ 
  $1 = PyInt_AsLong($input); 
%} 

/* 带有额外参数名称的类型映射 */ 
%typemap(in) int nonnegative { 
  ... 
} 

/* 一个类型映射中有多种类型 */ 
%typemap(in) int, short, long { 
  $1 = SvIV($input) ; 
} 

/* 带有修饰符的类型映射 */ 
%typemap(in, doc="integer") int "$1 = scm_to_int($input);"; 

/* 应用于多个参数模式的类型映射 */ 
%typemap(in) (char *str, int len), 
             (char *buffer, int size) 
{
  $1 = PyString_AsString($input); 
  $2 = PyString_Size($input); 
} 

/* 带有额外模式参数的类型映射 */ 
%typemap(in, numinputs=0) int *output (int temp), 
                          long *output (long temp) 
{ 
  $1 = &temp; 
}

诚然，乍一看，这并不是最易读的语法。但是，各个部分的目的将变得清晰。
11.2.2 类型图作用域
定义后，类型映射对随后的所有声明保持有效。可以为输入文件的不同部分重新定义类型映射。例如：
// typemap1 
%typemap(in) int { 
... 
} 

int fact(int); // typemap1 
int gcd(int x, int y); // typemap1 

// typemap2 
%typemap(in) int { 
... 
} 

int isprime(int); // 类型映射2

typemap 范围规则的一个例外与 %extend 声明有关。%extend 用于将新声明附加到类或结构定义。因此，%extend 块中的所有声明都受在定义类本身时生效的类型映射规则的约束。例如：
class Foo { 
  ... 
}; 

%typemap(in) int { 
 ... 
} 

%extend Foo { 
  int blah(int x); // typemap 没有效果。声明附加在 Foo 上，
                   // 出现在 %typemap 声明之前。
};

11.2.3 复制 typemap
类型映射是通过使用赋值来复制的。例如：
%typemap(in) Integer = int;

或这个：
%typemap(in) Integer, Number, int32_t = int;

类型通常由不同类型映射的集合管理。例如：
%typemap(in) int { ... } 
%typemap(out) int { ... } 
%typemap(varin) int { ... } 
%typemap(varout) int { ... }

要将所有这些类型映射复制到新类型，请使用 %apply。例如：
%apply int { Integer }; // 将所有 int 类型映射复制到 Integer 
%apply int { Integer, Number }; // 将所有 int 类型映射复制到 Integer 和 Number

%apply 的模式遵循与 %typemap 相同的规则。例如：
%apply int *output { Integer *output }; // 名称为
%apply (char *buf, int len) { (char *buffer, int size) }; // 多个参数

11.2.4 删除 typemap
只需不定义代码就可以删除类型映射。例如：
%typemap(in) int; // 清除 int 的类型映射 
%typemap(in) 的 typemap int, long, short; // 清除 int、long、short 的类型映射 
%typemap(in) int *output;       

在 ％clear 指令清除给定类型的所有typemaps。例如：
%clear int；// 删除所有类型的 int 
%clear int *output, long *output;

注意：由于 SWIG 的默认行为是由类型映射定义的，因此清除像 int 这样的基本类型将使该类型无法使用，除非您还在清除操作后立即定义一组新的类型映射。
11.2.5 typemap 的放置
类型映射声明可以在全局范围、C++ 命名空间和 C++ 类中声明。例如：
%typemap(in) int { 
  ... 
} 

namespace std { 
  class string; 
  %typemap(in) string { 
    ... 
  } 
} 

class Bar { 
public: 
  typedef const int & const_reference; 
  %typemap(out) const_reference { 
    ... 
  } 
};

当 typemap 出现在命名空间或类中时，它会一直有效，直到 SWIG 输入结束（就像以前一样）。但是，类型映射考虑了本地范围。因此，这段代码
namespace std {
  class string；
  %typemap(in) string { 
    ... 
  } 
}

实际上是为 std::string 类型定义了一个类型映射。你可以有这样的代码：
namespace std {
  class string;
  %typemap(in) string { /* std::string */ 
    ... 
  } 
} 

namespace Foo { 
  class string; 
  %typemap(in) string { /* Foo::string */ 
    ... 
  } 
}

在这种情况下，有两个完全不同的类型映射适用于两种完全不同的类型（std::string和 Foo::string）。
应该注意的是，要使作用域生效，SWIG 必须知道 string 是在特定命名空间中定义的类型名。在此示例中，这是使用前向类声明类字符串完成的。
11.3 模式匹配规则
本节描述了 C/C++ 数据类型与 typemap 关联的模式匹配规则。在实践中可以通过使用还描述的调试选项来观察匹配规则。
11.3.1 基本匹配规则
类型映射使用类型和名称（通常是参数的名称）进行匹配。对于给定的 TYPE NAME 对，依次应用以下规则来查找匹配项。使用找到的第一个类型映射。
- 与 TYPE 和 NAME 完全匹配的 typemap。
- 仅与 TYPE 完全匹配的 typemap。
- 如果 TYPE 是类型为 T< TPARMS > 的 C++ 模板，其中 TPARMS 是模板参数，则该类型将被去除模板参数，然后进行以下检查：
  - 与 T 和 NAME 完全匹配的 typemap。
  - 仅与 T 完全匹配的 typemap。
如果 TYPE 包含限定符（const、volatile 等），则每个限定符一次剥离一个以形成新的剥离类型，并在剥离类型上重复上述匹配规则。最左边的限定符首先被剥离，导致最右边（或顶级）的限定符最后被剥离。例如 int const* const 首先被剥离为 int *const 然后是 int *。
如果 TYPE 是一个数组。进行了以下转换：
- 将所有维度替换为 [ANY] 并查找通用数组类型映射。
为了说明这一点，假设您有一个这样的函数：
int foo(const char *s);

要查找参数 const char *s 的类型映射，SWIG 将搜索以下类型映射：
const char *s     精确类型和名称匹配
const char *      精确类型匹配
char *s           类型和名称匹配（去除限定符）
char *            类型匹配（去除限定符）

当可能定义多个类型映射规则时，实际上只使用找到的第一个匹配项。这是一个示例，显示了如何应用一些基本规则：
%typemap(in) int *x { 
  ... typemap 1 
} 

%typemap(in) int * { 
  ... typemap 2 
} 

%typemap(in) const int *z { 
  ... typemap 3 
} 

%typemap(in) int [4] { 
  ... typemap 4 
} 

%typemap(in) int [ANY] { 
  ... typemap 5 
} 

void A(int *x);        // int *x rule       (typemap 1)
void B(int *y);        // int * rule        (typemap 2)
void C(const int *x);  // int *x rule       (typemap 1)
void D(const int *z);  // const int *z rule (typemap 3)
void E(int x[4]);      // int [4] rule      (typemap 4)
void F(int x[1000]);   // int [ANY] rule    (typemap 5)

兼容性说明： SWIG-2.0.0 引入了一次剥离限定符的功能。以前的版本一步剥离了所有限定符。
11.3.2 Typedef 缩减匹配
如果使用上一节中的规则未找到匹配项，SWIG 会对该类型应用 typedef 缩减，并为缩减的类型重复 typemap 搜索。为了说明，假设您有这样的代码：
%typemap(in) int {
  ... typemap 1
}

typedef int Integer;
void blah(Integer x);

要查找 Integer x 的类型映射，SWIG 将首先搜索以下类型映射：
Integer x
Integer

找不到匹配项，然后将归约Integer -> int 应用于类型并重复搜索。
int x 
int --> 匹配：typemap 1

尽管两种类型可能通过 typedef 相同，但 SWIG 允许为每个类型名独立定义类型映射。这允许仅基于类型名本身进行有趣的自定义可能性。例如，您可以编写如下代码：
typedef double  pdouble;     // Positive double

// typemap 1
%typemap(in) double {
  ... get a double ...
}
// typemap 2
%typemap(in) pdouble {
  ... get a positive double ...
}
double sin(double x);           // typemap 1
pdouble sqrt(pdouble x);        // typemap 2

减少类型时，一次只应用一个 typedef 减少。搜索过程继续应用缩减，直到找到匹配或直到无法再进行缩减。
对于复杂的类型，减少过程可以生成一长串模式。考虑以下：
typedef int Integer；
typedef Integer Row4[4]; 
void foo(Row4 rows[10]);

为了找到Row4 rows[10]参数的匹配项，SWIG 将检查以下模式，仅在找到匹配项时停止：
Row4 rows[10] 
Row4 [10] 
Row4 rows[ANY] 
Row4 [ANY] 

# Reduce Row4 --> Integer[4] 
Integer rows[10][4] 
Integer [10][4] 
Integer rows[ANY][ANY] ] 
Integer [ANY][ANY] 

# Reduce Integer --> int 
int rows[10][4] 
int [10][4] 
int rows[ANY][ANY] 
int [ANY][ANY]

对于像模板这样的参数化类型，情况更加复杂。假设你有一些这样的声明：
typedef int Integer;
typedef foo<Integer, Integer> fooii;
void blah(fooii *x);

在这种情况下，将在以下类型映射模式中搜索参数fooii *x：
fooii *x
fooii *

# Reduce fooii --> foo<Integer, Integer>
foo<Integer, Integer> *x
foo<Integer, Integer> *

# Reduce Integer -> int
foo<int, Integer> *x
foo<int, Integer> *

# Reduce Integer -> int
foo<int, int> *x
foo<int, int> *

Typemap 缩减总是应用于出现的最左边的类型。只有当不能对最左边的类型进行缩减时，才会对该类型的其他部分进行缩减。这种行为意味着您可以为 foo<int, Integer> 定义类型映射，但永远不会匹配foo<Integer, int> 的类型映射。诚然，这是相当深奥的——几乎没有实际的理由来编写这样的类型映射。当然，您可以依靠这一点来进一步混淆您的同事。
作为澄清的一点，值得强调的是 typedef 匹配只是一个 typedef 缩减过程，也就是说，SWIG 不会搜索每个可能的 typedef。给定声明中的类型，它只会减少类型，而不会构建它以寻找 typedef。例如，给定类型Struct，下面的类型映射将不会用于aStruct参数，因为 Struct已完全减少：
struct Struct {...};
typedef Struct StructTypedef;

%typemap(in) StructTypedef { 
  ...
}

void go(Struct aStruct);

11.3.3 默认的 typemap 匹配规则
如果基本模式匹配规则导致没有匹配，即使在 typedef 减少之后，默认的 typemap 匹配规则依然用于寻找合适的 typemap 匹配。这些规则匹配基于保留的 SWIGTYPE 基类型的通用类型映射。例如，指针将使用 SWIGTYPE * 而引用将使用 SWIGTYPE &。更准确地说，这些规则基于 C++ 编译器在寻找合适的部分模板特化时使用的 C++ 类模板部分特化匹配规则。这意味着匹配是从可用的最专业的通用类型映射类型集中选择的。例如，当寻找与 int const * 的匹配时，规则将更喜欢匹配 SWIGTYPE const * 如果之前匹配可用 SWIGTYPE *，匹配前 SWIGTYPE。
大多数 SWIG 语言模块使用类型映射来定义 C 基元类型的默认行为。这完全是直截了当的。例如，一组按值或常量引用编组的原语类型映射是这样写的：
%typemap(in) int           "... convert to int ...";
%typemap(in) short         "... convert to short ...";
%typemap(in) float         "... convert to float ...";
...
%typemap(in) const int &   "... convert ...";
%typemap(in) const short & "... convert ...";
%typemap(in) const float & "... convert ...";
...

由于类型映射匹配遵循所有 typedef 声明，因此通过 typedef 映射到原始类型的任何类型的类型或常量引用都将被这些原始类型映射中的一个选择。大多数语言模块还为 char 指针和 char 数组定义了 typemaps 来处理字符串，因此这些非默认类型也将优先使用，因为基本 typemap 匹配规则提供比默认 typemap 匹配规则更好的匹配。
下面是语言模块提供的典型默认类型的列表，显示了 “in” 类型映射的样子：
%typemap(in) SWIGTYPE &            { ... 默认引用处理 ...            }; 
%typemap(in) SWIGTYPE *            { ... 默认指针处理 ...            }; 
%typemap(in) SWIGTYPE *const       { ... 默认指针常量处理 ...        }; 
%typemap(in) SWIGTYPE *const&      { ... 默认指针常量引用处理 ...     }; 
%typemap(in) SWIGTYPE[ANY]         { ... 一维固定大小的数组处理 ...   }; 
%typemap(in) SWIGTYPE []           { ... 未知大小的数组处理 ...      }; 
%typemap(in) enum SWIGTYPE         { ... 枚举值的默认处理 ...        };
%typemap(in) const enum SWIGTYPE & { ... 常量枚举引用值的默认处理 ... }; 
%typemap(in) SWIGTYPE (CLASS::*)   { ... 默认指针成员处理 ...        }; 
%typemap(in) SWIGTYPE              { ... 简单的默认处理 ...          };

如果您想更改 SWIG 对简单指针的默认处理方式，只需重新定义 SWIGTYPE * 的规则即可。请注意，简单的默认类型映射规则用于匹配不匹配任何其他规则的简单类型：
%typemap(in) SWIGTYPE { ... 简单的默认处理 ... } 

此 typemap 很重要，因为它是使用按值调用或返回时触发的规则。例如，如果您有这样的声明：
double dot_product(Vector a, Vector b);

该 Vector 类型通常会一下就对匹配 SWIGTYPE。SWIGTYPE的默认实现是将值转换为指针（如前一节所述）。
通过重新定义SWIGTYPE，可以实现其他行为。例如，如果您清除了 SWIGTYPE 的所有类型映射，则 SWIG 根本不会包装任何未知数据类型（这可能对调试有用）。或者，您可以修改 SWIGTYPE 以将对象编组为字符串，而不是将它们转换为指针。
让我们考虑一个示例，其中定义了以下类型映射，并且 SWIG 正在为下面显示的枚举寻找最佳匹配：
%typemap(in) const Hello &          { ... }
%typemap(in) const enum SWIGTYPE &  { ... }
%typemap(in) enum SWIGTYPE &        { ... }
%typemap(in) SWIGTYPE &             { ... }
%typemap(in) SWIGTYPE               { ... }

enum Hello {};
const Hello &hi;

列表顶部的类型映射将被选择，不是因为它首先被定义，而是因为它与被包装的类型最接近。如果上述列表中的任何类型映射未定义，则列表中的下一个将具有优先权。
探索默认类型映射的最佳方法是查看已为特定语言模块定义的类型映射。类型映射定义通常在 SWIG 库中的一个文件中找到 ，例如 java.swg、csharp.swg 等。 然而，对于许多目标语言，类型映射隐藏在复杂的宏后面，因此查看默认类型映射的最佳方法，或与此相关的任何类型映射都是通过在任何接口文件上运行 swig -E 来查看预处理输出。最后，查看 typemap 匹配规则的最佳方法是通过稍后介绍的 调试 typemap 模式匹配 选项。
兼容性说明：默认类型映射匹配规则在 SWIG-2.0.0 中从一个稍微简单的方案进行了修改，以匹配当前的 C++ 类模板部分特化匹配规则。
11.3.4 多参数 typemap
当指定多参数 typemap 时，它们优先于为单个类型指定的任何类型映射。例如：
%typemap(in) (char *buffer, int len) {
  // typemap 1
}

%typemap(in) char *buffer {
  // typemap 2
}

void foo(char *buffer, int len, int count); // (char *buffer, int len)
void bar(char *buffer, int blah);           // char *buffe

多参数类型映射在匹配方式上也更具限制性。目前，第一个参数遵循上一节中描述的匹配规则，但所有后续参数必须完全匹配。
11.3.5 匹配规则与 C++ 模板的比较
对于那些非常熟悉 C++ 模板的人来说，比较类型映射匹配规则和模板类型推导是很有趣的。考虑的两个方面首先是默认类型映射及其与部分模板专业化的相似性，其次是非默认类型映射及其与完整模板专业化的相似性。
对于默认 (SWIGTYPE) 类型映射，规则受 C++ 类模板部分特化的启发。例如，给定 T const& 的部分特化：
template <typename T> struct X             { void a(); };
template <typename T> struct X< T const& > { void b(); };

完整（非特化）模板与大多数类型匹配，例如：
X< int & >            x1;  x1.a();

并且以下所有匹配T const& 部分特化：
X< int *const& >      x2;  x2.b();
X< int const*const& > x3;  x3.b();
X< int const& >       x4;  x4.b();

现在，仅给出这两个默认类型映射，其中 T 类似于 SWIGTYPE：
%typemap(...) SWIGTYPE { ... } 
%typemap(...) SWIGTYPE const& { ... }

通用默认类型映射 SWIGTYPE与大多数类型一起使用，例如
int &

并且以下都匹配 SWIGTYPE const& 类型映射，就像部分模板匹配一样：
int *const& 
int const*const& 
int const&

请注意，模板和类型映射匹配规则对于所有默认类型映射并不相同，例如，对于数组。
对于非默认类型映射，人们可能希望 SWIG 遵循完全专用的模板规则。这几乎是这种情况，但并非完全如此。考虑一个与早期部分专门化模板非常相似的例子，但这次有一个完全专门化的模板：
template <typename T> struct Y       { void a(); };
template <> struct Y< int const & >  { void b(); };

只有一种类型与专用模板完全匹配：
Y< int & >             y1;  y1.a();
Y< int *const& >       y2;  y2.a();
Y< int const *const& > y3;  y3.a();
Y< int const& >        y4;  y4.b(); // fully specialized match

给定的类型映射与上面声明的模板使用的类型相同，其中 T 再次类似于 SWIGTYPE：
%typemap(...) SWIGTYPE { ... } 
%typemap(...) int const& { ... }

非默认类型映射和完全专用的单参数模板之间的比较结果是相同的，因为只有一种类型将匹配非默认类型映射：
int &
int *const&
int const*const&
int const&        // matches non-default typemap int const&

但是，如果改用非常量类型：
%typemap(...) SWIGTYPE { ... } 
%typemap(...) int & { ... }

那么模板匹配有明显的不同，因为 const 和非常量类型都匹配 typemap：
int &             // matches non-default typemap int &
int *const&
int const*const&
int const&        // matches non-default typemap int &

还有其他细微的差异，例如 typedef 处理，但至少应该清楚，typemap 匹配规则与专门模板处理的规则相似。
11.3.6 调试 typemap 模式匹配
有两个有用的调试命令行选项可用于调试类型映射，-debug-tmsearch 和 -debug-tmused 。
该 -debug-tmsearch 选项是用于调试类型映射搜索一个详细选项。这对于观察正在运行的模式匹配过程和调试使用了哪些类型映射非常有用。该选项显示在成功进行模式匹配之前要查找的所有类型映射和类型。由于显示包括对包装所需的每种类型的搜索，因此显示的信息量可能很大。通常，您会手动搜索您感兴趣的特定类型的显示信息。
例如，考虑已经涵盖的 Typedef 缩减 部分中使用的一些代码：
typedef int Integer;
typedef Integer Row4[4];
void foo(Row4 rows[10]);

下面显示了“in”类型映射的调试输出示例：
swig -perl -debug-tmsearch example.i
...
example.h:3: Searching for a suitable 'in' typemap for: Row4 rows[10]
  Looking for: Row4 rows[10]
  Looking for: Row4 [10]
  Looking for: Row4 rows[ANY]
  Looking for: Row4 [ANY]
  Looking for: Integer rows[10][4]
  Looking for: Integer [10][4]
  Looking for: Integer rows[ANY][ANY]
  Looking for: Integer [ANY][ANY]
  Looking for: int rows[10][4]
  Looking for: int [10][4]
  Looking for: int rows[ANY][ANY]
  Looking for: int [ANY][ANY]
  Looking for: SWIGTYPE rows[ANY][ANY]
  Looking for: SWIGTYPE [ANY][ANY]
  Looking for: SWIGTYPE rows[ANY][]
  Looking for: SWIGTYPE [ANY][]
  Looking for: SWIGTYPE *rows[ANY]
  Looking for: SWIGTYPE *[ANY]
  Looking for: SWIGTYPE rows[ANY]
  Looking for: SWIGTYPE [ANY]
  Looking for: SWIGTYPE rows[]
  Looking for: SWIGTYPE []
  Using: %typemap(in) SWIGTYPE []
...

表明 SWIG 提供的最佳默认匹配是 SWIGTYPE [] 类型映射。如示例所示，成功匹配以以下简化格式之一显示使用的类型映射源，包括类型映射方法、类型和可选名称：
- 使用：%typemap(method) type name
- 使用：%typemap(method) type name = type2 name2
- 使用：%apply type2 name2 { type name }
此信息可能满足您的调试需求，但是，您可能需要进一步分析。如果您接下来使用 -E 选项调用 SWIG以显示预处理的输出，并搜索使用的特定类型映射，您将找到完整的类型映射内容（下面显示的 Python 示例）：
%typemap(in, noblock=1) SWIGTYPE [] (void *argp = 0, int res = 0) { 
  res = SWIG_ConvertPtr($input, &argp, $descriptor, $disown | 0 ); 
  if (!SWIG_IsOK(res)) { 
    SWIG_exception_fail(SWIG_ArgError(res), "in method '" "$symname" "', argument " 
                       "$argnum"" of type '" "$type""'"); 
  } 
  $1 = ($ltype)(argp); 
}

为 foo 包装器生成的代码将包含类型映射的片段，并扩展了特殊变量。本章的其余部分需要阅读才能完全理解所有这些，但是，可以在下面看到上述类型映射生成代码的相关部分：
SWIGINTERN PyObject *_wrap_foo(PyObject *SWIGUNUSEDPARM(self), PyObject *args) { 
... 
  void *argp1 = 0 ; 
  int res1 = 0; 
... 
  res1 = SWIG_ConvertPtr(obj0, &argp1, SWIGTYPE_p_a_4__int, 0 | 0); 
  if (!SWIG_IsOK(res1)) { 
    SWIG_exception_fail(SWIG_ArgError(res1), "in method '" "foo" "', argument " 
                       "1"" of type '" "int [10][4]""'") ; 
  } 
  arg1 = (int (*)[4])(argp1); 
... 
}

除非确实存在匹配的多参数类型映射，否则不会提及搜索多参数类型映射。例如， 前面多参数部分中代码的输出如下：
...
example.h:39: Searching for a suitable 'in' typemap for: char *buffer
  Looking for: char *buffer
  Multi-argument typemap found...
  Using: %typemap(in) (char *buffer, int len)
...

调试的第二个选项是 -debug-tmused，它显示使用的类型映射。此选项是 -debug-tmsearch 选项的不太详细的版本，因为它仅在单独的一行中显示每个成功找到的类型映射。输出显示类型和名称（如果存在），括号中的类型映射方法，然后是 -debug-tmsearch 选项输出的相同简化格式中使用的实际类型映射。以下是本节开头的调试示例代码的输出。
$ swig -perl -debug-tmused example.i
example.h:3: Typemap for Row4 rows[10] (in) : %typemap(in) SWIGTYPE []
example.h:3: Typemap for Row4 rows[10] (typecheck) : %typemap(typecheck) SWIGTYPE *
example.h:3: Typemap for Row4 rows[10] (freearg) : %typemap(freearg) SWIGTYPE []
example.h:3: Typemap for void foo (out) : %typemap(out) void

现在，考虑以下接口文件：
%module example

%{ 
void set_value(const char* val) {} 
%} 

%typemap(check) char *NON_NULL { 
  if (!$1) { 
    /* ... 错误处理 ... */ 
  } 
} 

// 使用默认值指针处理而不是字符串
%apply SWIGTYPE * { const char* val, const char* another_value } 

%typemap(check) const char* val = char* NON_NULL; 

%typemap(arginit, noblock=1) const char* val { 
  $1 = ""; 
} 

void set_value(const char* val);

和输出调试：
swig -perl5 -debug-tmused example.i
example.i:21: Typemap for char const *val (arginit) : %typemap(arginit) char const *val
example.i:21: Typemap for char const *val (in) : %apply SWIGTYPE * { char const *val }
example.i:21: Typemap for char const *val (typecheck) : %apply SWIGTYPE * { char const *val }
example.i:21: Typemap for char const *val (check) : %typemap(check) char const *val = char *NON_NULL
example.i:21: Typemap for char const *val (freearg) : %apply SWIGTYPE * { char const *val }
example.i:21: Typemap for void set_value (out) : %typemap(out) void

可以注意到以下关于显示内容的观察结果（同样适用于-debug-tmsearch）：
- 显示了相关的类型映射，但对于类型映射复制，会显示适当的 %typemap 或 %apply，例如，“check”和“in”类型映射。
- 未显示类型映射 修饰符，例如“ arginit ”类型映射中的 noblock=1 修饰符。
- 确切的 %apply 语句可能与实际代码中的内容不同。例如，const char* another_value 未显示，因为它在此处不相关。此外，类型的显示方式可能略有不同—— char const * 而不是 const char*。
11.4 代码生成规则
本节描述了将类型映射代码插入到生成的包装器代码中的规则。
11.4.1 范围
当 typemap 像这样定义时：
%typemap(in) int { 
  $1 = PyInt_AsLong($input); 
}

使用新的块作用域将类型映射代码插入到包装函数中。换句话说，包装器代码将如下所示：
wrap_whatever() {
  ...
  // Typemap code
  {
    arg1 = PyInt_AsLong(obj1);
  }
  ...
}

因为 typemap 代码包含在它自己的块中，所以在 typemap 执行期间声明临时变量是合法的。例如：
%typemap(in) short {
  long temp;          /* Temporary value */
  if (Tcl_GetLongFromObj(interp, $input, &temp) != TCL_OK) {
    return TCL_ERROR;
  }
  $1 = (short) temp;
}

当然，您在类型映射中声明的任何变量都会在类型映射代码执行后立即销毁（它们对包装函数的其他部分或可能使用相同变量名称的其他类型映射不可见）。
有时，会使用一些替代形式指定类型映射代码。例如：
%typemap(in) int "$1 = PyInt_AsLong($input);"; 
%typemap(in) int %{ 
$1 = PyInt_AsLong($input); 
%} 
%typemap(in, noblock=1) int { 
$1 = PyInt_AsLong($input); 
}

这三种形式主要用于化妆品——指定的代码在发出时不包含在块范围内。这有时会导致看起来不太复杂的包装函数。请注意，三个类型映射中只有第三个具有通过 SWIG 预处理器传递的类型映射代码。
11.4.2 声明新的局部变量
有时，声明一个存在于整个包装函数范围内的新局部变量很有用。一个很好的例子可能是您想要在其中编组字符串的应用程序。假设你有一个这样的 C++ 函数
int foo(std::string *s);

并且您想将目标语言中的本机字符串作为参数传递。例如，在 Perl 中，您希望函数像这样工作：
$x = foo("Hello World");

为此，您不能仅将原始 Perl 字符串作为 std::string * 参数传递。相反，您必须创建一个临时的 std::string 对象，将 Perl 字符串数据复制到其中，然后传递一个指向该对象的指针。为此，只需使用一个额外的参数指定类型映射，如下所示：
%typemap(in) std::string * (std::string temp) {
  unsigned int len;
  char        *s;
  s = SvPV($input, len);         /* Extract string data */
  temp.assign(s, len);           /* Assign to temp */
  $1 = &temp;                   /* Set argument to point to temp */
}

在这种情况下，temp 成为整个包装函数范围内的局部变量。例如：
wrap_foo() {
  std::string temp;    <--- Declaration of temp goes here
  ...

  /* Typemap code */
  {
    ...
    temp.assign(s, len);
    ...
  }
  ...
}

当您将 temp 设置为一个值时，它会在包装函数的持续时间内持续存在，并在退出时自动清除。
在同一个声明中使用多个涉及局部变量的类型映射是完全安全的。例如，您可以将函数声明为：
void foo(std::string *x, std::string *y, std::string *z);

这是安全的处理，因为 SWIG 实际上通过附加参数编号后缀来重命名所有局部变量引用。因此，生成的代码实际上如下所示：
wrap_foo() {
  int *arg1;    /* Actual arguments */
  int *arg2;
  int *arg3;
  std::string temp1;    /* Locals declared in the typemap */
  std::string temp2;
  std::string temp3;
  ...
  {
    char *s;
    unsigned int len;
    ...
    temp1.assign(s, len);
    arg1 = *temp1;
  }
  {
    char *s;
    unsigned int len;
    ...
    temp2.assign(s, len);
    arg2 = &temp2;
  }
  {
    char *s;
    unsigned int len;
    ...
    temp3.assign(s, len);
    arg3 = &temp3;
  }
  ...
}

有一个例外：如果变量名称以 _global_ 前缀开头，则不会附加参数编号。这样的变量可以在整个生成的包装函数中使用。例如，上面的类型映射可以重写为使用 _global_temp 而不是temp，然后生成的代码将包含一个_global_temp 变量而不是 temp1、 temp2 和 temp3：
%typemap(in) std::string * (std::string _global_temp) { 
 ... 同上 ... 
}

某些类型映射不识别局部变量（或者它们可能根本不适用）。目前，只有适用于参数转换的类型映射支持此（输入类型映射，例如 “in” 类型映射）。
笔记：
为多种类型声明类型映射时，每种类型都必须有自己的局部变量声明。
%typemap(in) const std::string *, std::string * (std::string temp) // 不！
// 只有 std::string * 有一个局部变量
// const std::string * 没有 (oops) 
.... 

%typemap(in) const std::string * (std::string temp), std: :string * (std::string temp) // 正确
....

11.4.3 特殊变量
在所有类型映射中，扩展了以下特殊变量。这绝不是一个完整的列表，因为某些目标语言具有额外的特殊变量，这些变量记录在语言特定的章节中。
无法复制加载中的内容
在表中，$ n指的是类型映射规范中的特定类型。例如，如果你写这个
%typemap(in) int *INPUT { 

}

那么 $1 指的是 int *INPUT。如果你有这样的类型图，
%typemap(in) (int argc, char *argv[]) { 
  ... 
}

那么 $1 指的是 int argc， $2 指的是 char *argv[]。
与类型和名称相关的替换总是填充来自匹配的实际代码的值。当类型映射可能匹配多个 C 数据类型时，这很有用。例如：
%typemap(in) int, short, long { 
  $1 = ($1_ltype) PyInt_AsLong($input); 
}

在这种情况下，$1_ltype 被替换为实际匹配的数据类型。
发出类型映射代码时，特殊变量$1和$2的 C/C++ 数据类型始终是“ltype”。“ltype”只是一种可以合法出现在 C 赋值操作左侧的类型。下面是几个类型和 ltypes 的例子：
type              ltype
------            ----------------
int               int
const int         int
const int *       int *
int [4]           int *
int [4][5]        int (*)[5]

在大多数情况下，ltype 只是去掉了限定符的 C 数据类型。此外，数组被转换为指针。
$&1_type 和 $*1_type 等变量用于通过删除或添加指针来安全地修改类型。尽管在大多数类型映射中不需要，但有时需要这些替换来正确处理在指针和值之间转换值的类型映射。
如有必要，在声明局部变量时也可以使用类型相关的替换。例如：
%typemap(in) int * ($*1_type temp) { 
  temp = PyInt_AsLong($input); 
  $1 = &temp; 
}

以这种方式声明局部变量有一个注意事项。如果您使用诸如$1_ltype temp 之类的类型替换声明局部变量，则它不会像您对数组和某些类型的指针所期望的那样工作。例如，如果你写了这个，
%typemap(in) int [10][20] { 
  $1_ltype temp; 
}

那么 temp 的声明将被扩展为
int (*)[20] temp;

这是非法的 C 语法，无法编译。由于扩展和处理类型映射代码的方式，目前在 SWIG 中没有直接的方法来解决此问题。但是，一种可能的解决方法是简单地选择一种替代类型，例如 void * 并在需要时使用强制转换来获取正确的类型。例如：
%typemap(in) int [10][20] { 
  void *temp; 
  ... 
  (($1_ltype) temp)[i][j] = x; /* 设置一个值 */ 
  ... 
}

另一种仅适用于数组的方法是使用 $1_basetype 替换。例如：
%typemap(in) int [10][20] { 
  $1_basetype temp[10][20]; 
  ... 
  temp[i][j] = x; /* 设置一个值 */ 
  ... 
}

11.4.4 特殊变量宏
特殊变量宏类似于宏函数，因为它们采用一个或多个用于宏扩展的输入参数。它们看起来像宏/函数调用，但在宏名称前使用了特殊的变量 $ 前缀。请注意，与普通宏不同，扩展不是由预处理器完成的，而是在 SWIG 解析/编译阶段完成的。以下特殊变量宏可用于所有语言模块。
11.4.4.1 $descriptor（type）
该宏扩展到 type.c 中指定的任何 C/C++ 类型的类型描述符结构。它的行为类似于上面描述的 $1_descriptor 特殊变量，不同之处在于要扩展的类型是从宏参数中获取的，而不是从类型映射类型中推断出来的。例如，$descriptor(std::vector<int> *) 将扩展为 SWIGTYPE_p_std__vectorT_int_t。此宏主要用于脚本目标语言，稍后将在运行时类型检查器使用部分进行演示。
11.4.4.2 $typemap(method, typepattern)
这个宏使用前面描述的模式匹配规则来查找，然后用匹配的类型映射中的代码替换特殊变量宏。要搜索的类型映射由参数指定，其中方法 是类型映射方法名称，类型模式是根据 定义类型映射部分中的 %typemap 规范的类型模式。
匹配类型映射中的特殊变量被扩展为匹配类型映射类型的变量，而不是调用宏的类型映射。实际上，这个宏在脚本目标语言中几乎没有用处。它主要用于静态类型化的目标语言，作为获取给定 C/C++ 类型的目标语言类型的一种方式，更常见的是仅当 C++ 类型是模板参数时。
下面的示例仅适用于 C#，并使用了 C# 章节中记录的一些类型映射方法名称，但它显示了一些可能的语法变体。
%typemap(cstype) unsigned long "uint" 
%typemap(cstype) unsigned long bb "bool" 
%typemap(cscode) BarClass %{ 
  void foo($typemap(cstype, unsigned long aa) var1, 
           $typemap(cstype, unsigned long bb) var2, 
           $typemap(cstype, (unsigned long bb)) var3, 
           $typemap(cstype, unsigned long) var4) 
  { 
    // 做某事
  } 
%}

结果如下展开
%typemap(cstype) unsigned long "uint" 
%typemap(cstype) unsigned long bb "bool" 
%typemap(cscode) BarClass %{ 
  void foo(uint var1, 
           bool var2, 
           bool var3, 
           uint var4) 
  { 
    // 做点什么
  } 
%}

11.4.5 特殊变量和类型映射属性
自 SWIG-3.0.7 起，typemap 属性还将扩展特殊变量和特殊变量宏。
示例用法显示了 'out' 属性（特定于 C#）以及主要类型映射主体中的扩展：
%typemap(ctype, out="$*1_ltype") unsigned int& "$*1_ltype"

当$*1_ltype扩展为 unsigned int 时，等效于以下内容：
%typemap(ctype, out="unsigned int") unsigned int& "unsigned int"

11.4.6 特殊变量结合特殊变量宏
特殊变量也可以在特殊变量宏中使用。特殊变量在特殊变量宏中使用之前会被扩展。
考虑以下 C# 类型映射：
%typemap(cstype) unsigned int "uint" 
%typemap(cstype, out="$typemap(cstype, $*1_ltype)") unsigned int& "$typemap(cstype, $*1_ltype)"

特殊变量首先被扩展，因此上面的等价于：
%typemap(cstype) unsigned int "uint" 
%typemap(cstype, out="$typemap(cstype, unsigned int)") unsigned int& "$typemap(cstype, unsigned int)"

然后扩展为：
%typemap(cstype) unsigned int "uint" 
%typemap(cstype, out="uint") unsigned int& "uint"

11.5 常用的 typemap 方法
语言模块识别的类型映射集可能会有所不同。但是，以下类型映射方法几乎是通用的：
11.5.1 "in" 类型映射
“in”类型映射用于将函数参数从目标语言转换为 C。例如：
%typemap(in) int { 
  $1 = PyInt_AsLong($input); 
}

以下特殊变量可用：
$input - 包含要转换的值的输入对象。
$symname - 被包装的函数/方法的名称

这可能是最常见的重新定义类型映射，因为它可用于实现自定义转换。
此外，“in”类型映射允许指定转换参数的数量。该 numinputs 属性有助于此。例如：
// 忽略参数。
%typemap(in, numinputs=0) int *out (int temp) { 
  $1 = &temp; 
}

此时，只能转换零个或一个参数。当 numinputs 设置为 0 时，该参数实际上被忽略并且无法从目标语言提供。在进行 C/C++ 调用时仍然需要该参数，并且上面的类型映射显示所使用的值是从名为 temp的本地声明变量中获得的。通常不指定 numinputs，因此默认值为 1，即从目标语言到 C/C++ 调用时使用的参数数量是一对一的映射。多参数类型映射提供了一个类似的概念，其中从目标语言映射到 C/C++ 的参数数量可以更改为多个相邻的 C/C++ 参数。
兼容性说明：指定 numinputs=0 与旧的“ignore”类型映射相同。
11.5.2 "typecheck" 类型映射
“类型检查”类型映射用于支持重载的函数和方法。它只是检查参数以查看它是否与特定类型匹配。例如：
%typemap(typecheck, precedence=SWIG_TYPECHECK_INTEGER) int { 
  $1 = PyInt_Check($input) ? 1：0；
}

对于类型检查，$1 变量始终是一个简单的整数，根据输入参数是否为正确的类型设置为 1 或 0。如果输入参数是正确的类型，则设置为 1，否则设置为 0。
如果你定义了新的“in”类型映射并且你的程序使用了重载方法，你还应该定义一个“类型检查”类型映射的集合。更多详细信息请 参见类型映射和重载部分。
11.5.3 "out" 类型映射
“out”类型映射用于将函数/方法的返回值从 C 转换为目标语言。例如：
%typemap(out) int { 
  $result = PyInt_FromLong($1); 
}

以下特殊变量可用。
$result - 结果对象返回到目标语言。
$symname - 被包装的函数/方法的名称

“out”类型映射支持称为“optimal”的可选属性标志。这是用于代码优化的，在 按值返回时的优化代码生成部分中有详细说明。
11.5.4 "arginit" 类型映射
“arginit”类型映射用于在任何转换发生之前设置函数参数的初始值。这通常不是必需的，但在高度专业化的应用程序中可能很有用。例如：
// 在任何转换发生之前将参数设置为 NULL 
%typemap(arginit) int *data { 
  $1 = NULL; 
}

11.5.5 “default”类型映射
“默认”类型映射用于将参数转换为默认参数。例如：
%typemap(default) int flags { 
  $1 = DEFAULT_FLAGS; 
} 
... 
int foo(int x, int y, int flags);

此类型映射的主要用途是更改默认参数的包装或在不支持它们的语言（如 C）中指定默认参数。不支持可选参数的目标语言（例如 Java 和 C#）实际上会忽略此类型映射指定的值，因为必须提供所有参数。
一旦将默认类型映射应用于参数，后面的所有参数都必须具有默认值。有关默认参数包装的更多信息，请参阅 默认/可选参数部分。
11.5.6 “check”类型映射
“检查”类型映射用于在参数转换期间提供值检查代码。在转换参数后应用类型映射。例如：
%typemap(check) int positive { 
  if ($1 <= 0) { 
    SWIG_exception(SWIG_ValueError, "Expected positive value."); 
  } 
}

11.5.7 "argout" 类型映射
“argout”类型映射用于从参数返回值。这最常用于为需要返回多个值的 C/C++ 函数编写包装器。“argout”类型映射几乎总是与“in”类型映射结合使用——可能会忽略输入值。例如：
/* 设置输入参数指向一个临时变量 */ 
%typemap(in, numinputs=0) int *out (int temp) { 
  $1 = &temp; 
} 

%typemap(argout) int *out { 
  // 将输出值 $1 附加到 $result 
  ... 
}

以下特殊变量可用。
$result - 结果对象返回到目标语言。
$input - 传递的原始输入对象。
$symname - 被包装的函数/方法的名称

提供给“argout”类型映射的代码总是放在“out”类型映射之后。如果使用多个返回值，则额外的返回值通常会附加到函数的返回值中。
有关示例，请参阅 typemaps.i 库文件。
11.5.8 "freearg" 类型映射
“freearg”类型映射用于清理参数数据。它仅在参数可能已分配需要在包装器函数退出时清除的资源时使用。“freearg”类型映射通常会清理“in”类型映射分配的参数资源。例如：
// 获取整数列表
%typemap(in) int *items { 
  int nitems = Length($input); 
  $1 = (int *) malloc(sizeof(int)*nitems); 
} 
// 释放列表
%typemap(freearg) int *items { 
  free($1); 
}

“freearg”类型映射插入在包装函数的末尾，就在控制返回到目标语言之前。这段代码也被放置在一个特殊的变量 $cleanup 中，只要包装函数需要提前中止，它就可以在其他类型映射中使用。
11.5.9 “newfree” 类型图
“newfree”类型映射与 %newobject 指令结合使用，用于释放函数返回结果使用的内存。例如：
%typemap(newfree) string * {
  delete $1; 
} 
%typemap(out) string * { 
  $result = PyString_FromString($1->c_str()); 
} 
... 

%newobject foo; 
...
string *foo();

有关更多详细信息，请参阅对象所有权和 %newobject。
11.5.10 “ret” 类型映射
“ret”类型映射不经常使用，但对于与返回类型相关的任何事情都很有用，例如资源管理，返回值错误检查等。通常这都可以在“out”类型映射中完成，但有时使用原封不动的“out”类型映射代码并使用“ret”类型映射中的代码添加到生成的代码中是很方便的。一种这样的情况是内存清理。例如，定义 stringheap_t 类型表示必须删除返回的内存，定义 string_t 类型表示不能删除返回的内存。
%typemap(ret) stringheap_t %{ 
  free($1); 
%} 

typedef char * string_t; 
typedef char * stringheap_t; 

string_t MakeString1(); 
stringheap_t MakeString2();

上面的“ret”类型映射仅用于MakeString2，但两个函数都将使用 SWIG 提供的 char * 的默认“out”类型映射。上面的代码将确保在所有目标语言中释放适当的内存，因为不需要提供自定义的“out”类型映射（涉及目标语言特定代码）。
这种方法是使用“newfree”类型映射和 %newobject 的替代方法，因为不需要列出所有需要清理内存的函数，它纯粹是在类型上完成的。
11.5.11 “memberin” 类型映射
“memberin”类型映射用于将数据从已转换的输入值复制到结构成员中。它通常用于处理数组成员和其他特殊情况。例如：
%typemap(memberin) int [4] { 
  memmove($1, $input, 4*sizeof(int)); 
}

很少需要编写“memberin”类型映射——SWIG 已经为数组、字符串和其他对象提供了默认实现。
11.5.12 "varin" 类型映射
“varin”类型映射用于将目标语言中的对象转换为 C，以便分配给 C/C++ 全局变量。这是特定于实现的。
11.5.13 “varout”类型映射
“varout”类型映射用于在读取 C/C++ 全局变量时将 C/C++ 对象转换为目标语言中的对象。这是特定于实现的。
11.5.14 “throws”类型映射
“throws”类型映射仅在 SWIG 使用异常规范解析 C++ 方法或将 %catches 功能附加到方法时使用。它提供了一种默认机制来处理声明了它们将抛出的异常的 C++ 方法。此类型映射的目的是将 C++ 异常转换为目标语言中的错误或异常。它与其他类型映射略有不同，因为它基于异常类型而不是参数或变量的类型。例如：
%typemap(throws) const char * %{ 
  PyErr_SetString(PyExc_RuntimeError, $1); 
  SWIG_fail; 
%} 
void bar() throw (const char *);

从下面生成的代码中可以看出，SWIG 生成了一个异常处理程序，其中 catch 块包含“throws”类型映射内容。
...
try { 
  bar(); 
} 
catch(char const *_e) { 
  PyErr_SetString(PyExc_RuntimeError, _e); 
  SWIG_fail; 
} 
...

请注意，如果您的方法没有异常规范但它们确实抛出了异常，则 SWIG 无法知道如何处理它们。有关处理这些问题的巧妙方法，请参阅 %exception部分的异常处理。
11.6 一些 typemap 类型映射示例
本节包含几个示例。有关更多示例，请参阅语言模块文档。
11.6.1 数组的类型映射
类型映射的一个常见用途是为 C 数组提供支持，这些数组既作为函数的参数也作为结构成员出现。
例如，假设你有一个这样的函数：
void set_vector(int type, float value[4]);

如果你想把float value[4]作为一个浮点数列表处理，你可以写一个类似于这样的类型映射：

%typemap(in) float value[4] (float temp[4]) { 
  int i; 
  if (!PySequence_Check($input)) { 
    PyErr_SetString(PyExc_ValueError, "Expected a sequence"); 
    SWIG_fail; 
  } 
  if (PySequence_Length($input) != 4) { 
    PyErr_SetString(PyExc_ValueError, "Size mismatch. Expected 4 elements"); 
    SWIG_fail; 
  } 
  for (i = 0; i < 4; i++) { 
    PyObject *o = PySequence_GetItem($input, i); 
    if (PyNumber_Check(o)) { 
      temp[i] = (float) PyFloat_AsDouble(o); 
    } else { 
      PyErr_SetString(PyExc_ValueError, "Sequence elements must be numbers");   
      SWIG_fail; 
    } 
  } 
  $1 = temp；
}

在此示例中，变量 temp 在 C 堆栈上分配一个小数组。然后 typemap 填充这个数组并将它传递给底层的 C 函数。
在 Python 中使用时，typemap 允许以下类型的函数调用：
>>> set_vector(type, [ 1, 2.5, 5, 20 ])

如果你想将 typemap 推广到所有维度的数组，你可以这样写：
%typemap(in) float value[ANY] (float temp[$1_dim0]) { 
  int i; 
  if (!PySequence_Check($input)) { 
    PyErr_SetString(PyExc_ValueError, "Expected a sequence"); 
    SWIG_fail; 
  } 
  if (PySequence_Length($input) != $1_dim0) { 
    PyErr_SetString(PyExc_ValueError, "Size mismatch. Expected $1_dim0 elements"); 
    SWIG_fail; 
  } 
  for (i = 0; i < $1_dim0; i++) { 
    PyObject *o = PySequence_GetItem($input, i); 
    if (PyNumber_Check(o)) { 
      temp[i] = (float) PyFloat_AsDouble(o); 
    } else { 
      PyErr_SetString(PyExc_ValueError, "Sequence elements must be numbers"); 
      SWIG_fail; 
    } 
  }
  $1 = temp；
}

在这个例子中，特殊变量 $1_dim0 被扩展为实际的数组维度。多维数组可以以类似的方式进行匹配。例如：
%typemap(in) float matrix[ANY][ANY] (float temp[$1_dim0][$1_dim1]) { 
  ... 转换一个二维数组 ... 
}

对于大型数组，使用如图所示的临时变量在堆栈上分配存储可能是不切实际的。要使用堆分配的数据，可以使用以下技术。
%typemap(in) float value[ANY] {
  int i;
  if (!PySequence_Check($input)) {
    PyErr_SetString(PyExc_ValueError, "Expected a sequence");
    SWIG_fail;
  }
  if (PySequence_Length($input) != $1_dim0) {
    PyErr_SetString(PyExc_ValueError, "Size mismatch. Expected $1_dim0 elements");
    SWIG_fail;
  }
  $1 = (float *) malloc($1_dim0*sizeof(float));
  for (i = 0; i < $1_dim0; i++) {
    PyObject *o = PySequence_GetItem($input, i);
    if (PyNumber_Check(o)) {
      $1[i] = (float) PyFloat_AsDouble(o);
    } else {
      free($1);
      PyErr_SetString(PyExc_ValueError, "Sequence elements must be numbers");
      SWIG_fail;
    }
  }
}
%typemap(freearg) float value[ANY] {
  if ($1) free($1);
}

在这种情况下，使用 malloc 分配一个数组。该 freearg 然后类型表是用来释放参数的函数被调用后。
数组类型映射的另一个常见用途是为数组结构成员提供支持。由于 C 中指针和数组之间的细微差别，您不能只是“分配”给数组结构成员。相反，您必须显式地将元素复制到数组中。例如，假设您有这样的结构：
struct SomeObject {
  float  value[4];
  ...
};

当 SWIG 运行时，它不会生成任何代码来设置vec 成员。您甚至可能会收到如下警告消息：
$ swig -python  example.i
example.i:10: Warning 462: Unable to set variable of type float [4].

这些警告消息表明 SWIG 不知道您想如何设置 vec 字段。
要解决此问题，您可以提供一个特殊的“memberin”类型映射，如下所示：
%typemap(memberin) float [ANY] { 
  int i; 
  for (i = 0; i < $1_dim0; i++) { 
      $1[i] = $input[i]; 
  } 
}

memberin 类型映射用于从已经从目标语言转换为 C 的数据中设置结构成员。在这种情况下，$input是存储转换后输入数据的局部变量。这个类型映射然后将此数据复制到结构中。
当与早期的数组类型映射结合使用时，“in”和“memberin”类型映射的组合允许以下用法：
>>> s = SomeObject() 
>>> sx = [1, 2.5, 5, 10]

与结构成员输入相关，可能需要将结构成员作为一种新的对象返回。例如，在这个例子中，你会得到非常奇怪的程序行为，其中结构成员可以很好地设置，但读取成员只是返回一个指针：
>>> s = SomeObject() 
>>> sx = [1, 2.5, 5, 10] 
>>> print s.x
_1008fea8_p_float 
>>>

要解决此问题，您可以编写一个“out”类型映射。例如：
%typemap(out) float [ANY] { 
  int i; 
  $result = PyList_New($1_dim0); 
  for (i = 0; i < $1_dim0; i++) { 
    PyObject *o = PyFloat_FromDouble((double) $1[i]); 
    PyList_SetItem($result, i, o); 
  } 
}

现在，您会发现会员访问非常好：
>>> s = SomeObject()
>>> s.x = [1, 2.5, 5, 10]
>>> print s.x
[ 1, 2.5, 5, 10]

兼容性说明： SWIG1.1 用于提供特殊的“memberout”类型映射。然而，它大多没用，后来被淘汰了。要返回结构成员，只需使用“out”类型映射。
11.6.2 用类型映射实现约束
类型映射的一个特别有趣的应用是参数约束的实现。这可以通过“check”类型映射来完成。使用时，这允许您提供用于检查函数参数值的代码。例如：
%module math

%typemap(check) double posdouble {
  if ($1 < 0) {
    croak("Expecting a positive number");
  }
}

...
double sqrt(double posdouble);

这为您的包装函数提供了健全性检查。如果将负数传递给此函数，将引发 Perl 异常并且程序终止并显示错误消息。
这种检查在使用指针时特别有用。例如：
%typemap(check) Vector * { 
  if ($1 == 0) { 
    PyErr_SetString(PyExc_TypeError, "NULL Pointer not allowed"); 
    SWIG_fail; 
  } 
}

将阻止任何涉及 Vector * 的函数接受 NULL 指针。因此，SWIG 通常可以通过引发异常而不是盲目地将值传递给底层 C/C++ 程序来防止潜在的分段错误或其他运行时问题。
11.7 多种目标语言的类型映射
类型映射中的代码通常依赖于语言，但是，许多目标语言支持相同的类型映射。为了区分不同语言的类型映射，应该使用预处理器。例如，Perl 和 Ruby 的“in”类型映射可以写成：
#if defined(SWIGPERL)
  %typemap(in) int "$1 = ($1_ltype) SvIV($input);"
#elif defined(SWIGRUBY)
  %typemap(in) int "$1 = NUM2INT($input);"
#else
  #warning no "in" typemap defined
#endif

完整的语言特定宏集在 条件编译 部分中定义。上面的示例还显示了对尚未支持的语言发出警告的常见方法。
兼容性说明：在 SWIG-1.1 中，可以通过将语言名称放在%typemap 指令中来区分不同的语言，例如，
%typemap(ruby, in) int "$1 = NUM2INT($input);" .

11.8 按值返回时的最佳代码生成
“out”类型映射是返回类型的主要类型映射。这个类型映射支持一个名为“optimal”的可选属性标志，用于减少临时变量和生成的代码量，从而使编译器有机会使用返回值优化 来生成更快执行的代码。它只有在按值返回对象时才真正产生影响，并且在使用上有一些限制，稍后会解释。
当函数按值返回对象时，SWIG 会生成代码来实例化堆栈上的默认类型，然后将函数调用返回的值分配给它。然后在堆上制作此对象的副本，这就是最终从目标语言存储和使用的内容。考虑一个例子会更清楚。考虑通过 SWIG 运行以下代码：
%typemap(out) SWIGTYPE %{
  $result = new $1_ltype((const $1_ltype &)$1);
%}

%inline %{
#include <iostream>
using namespace std;

struct XX {
  XX() { cout << "XX()" << endl; }
  XX(int i) { cout << "XX(" << i << ")" << endl; }
  XX(const XX &other) { cout << "XX(const XX &)" << endl; }
  XX & operator =(const XX &other) { cout << "operator=(const XX &)" << endl; return *this; }
  ~XX() { cout << "~XX()" << endl; }
  static XX create() { 
    return XX(0);
  }
};
%}

当按值返回对象时，显示的“out”类型映射是 C# 的默认类型映射。从 C#调用XX::create() 时，输出如下：
XX() 
XX(0) 
operator=(const XX &) 
~XX() 
XX(const XX &) 
~XX() 
~XX()

请注意，正在创建三个对象以及一个分配。如果 XX::create() 方法是构造函数被调用的唯一时间，那不是很好吗？由于该方法按值返回，这要求很多，并且 SWIG 默认生成的代码使编译器无法使用返回值优化 (RVO) 。但是，这是“out”类型映射中的“optimal”属性可以提供帮助的地方。如果 typemap 代码保持不变，并且只是像这样指定“optimal”属性：
%typemap(out, optimization="1") SWIGTYPE %{ 
  $result = new $1_ltype((const $1_ltype &)$1); 
%}

然后当代码再次运行时，输出很简单：
XX(0) 
~XX()

使用生成的代码最好地解释了“最佳”属性的工作原理。没有“最优”，生成的代码是：
SWIGEXPORT void * SWIGSTDCALL CSharp_XX_create() {
  void * jresult ;
  XX result;
  result = XX::create();
  jresult = new XX((const XX &)result);
  return jresult;
}

使用“optimal”属性，代码为：
SWIGEXPORT void * SWIGSTDCALL CSharp_XX_create() {
  void * jresult ;
  jresult = new XX((const XX &)XX::create());
  return jresult;
}

主要区别在于不再生成保存 XX::create() 返回值的结果临时变量，而是直接从 XX::create() 返回值进行复制构造函数调用。使用现代编译器实现 RVO，复制实际上并没有完成，事实上，对象根本不会在XX::create() 的堆栈上创建，它只是直接在堆上创建。在第一个实例中，类型 映射中的 $1 特殊变量被扩展为 result。在第二种情况下，$1 被扩展为 XX::create() ，这基本上是“optimal”属性告诉 SWIG 要做的事情。
默认情况下不启用“optimal”属性优化，因为它有许多限制。首先，有些代码不能压缩成一个简单的调用来传递给复制构造函数。一种常见的情况是使用 %exception 时。考虑将以下 %exception 添加到示例中：
%exception XX::create() %{ 
try { 
  $action 
} catch(const std::exception &e) { 
  cout << e.what() << endl; 
} 
%}

SWIG 可以检测何时无法使用“最佳”属性并将忽略它，在这种情况下将发出以下警告：
example.i:28: Warning 474: Method XX::create() usage of the optimal attribute ignored
example.i:14: Warning 474: in the out typemap as the following cannot be used to generate
optimal code: 
try {
  result = XX::create();
} catch(const std::exception &e) {
  cout << e.what() << endl;
}

应该清楚，上面的代码不能作为拷贝构造函数调用的参数，也就是 $1 替换。
其次，如果类型映射多次使用 $1，则将多次调用包装函数。显然这不是很理想。事实上，SWIG 试图检测到这一点，并会发出如下警告：
example.i:21: Warning 475: Multiple calls to XX::create() might be generated due to
example.i:7: Warning 475: optimal attribute usage in the out typemap.

然而，它并不总是正确的，例如当 $1 在一些注释掉的代码中时。
11.9 多参数类型映射
到目前为止，所提供的类型图都集中在处理单个值的问题上。例如，在函数调用中将单个输入对象转换为单个参数。然而，以这种方式难以处理某些转换问题。例如，请考虑本章开头的示例：
int foo(int argc, char *argv[]);

假设您想包装这个函数，以便它接受像这样的单个字符串列表：
>>> foo(["ale", "lager", "stout"])

为此，您不仅需要将字符串列表映射到 char *argv[]，而且 int argc 的值由列表的长度隐式确定。只使用简单的类型映射，这种类型的转换是可能的，但非常痛苦。多参数类型映射在这种情况下有帮助。
多参数类型映射是一种转换规则，它指定如何将目标语言中的单个对象转换为 C/C++ 中的一组连续函数参数。例如，以下多参数映射执行上述示例所描述的转换：
%typemap(in) (int argc, char *argv[]) {
  int i;
  if (!PyList_Check($input)) {
    PyErr_SetString(PyExc_ValueError, "Expecting a list");
    SWIG_fail;
  }
  $1 = PyList_Size($input);
  $2 = (char **) malloc(($1+1)*sizeof(char *));
  for (i = 0; i < $1; i++) {
    PyObject *s = PyList_GetItem($input, i);
    if (!PyString_Check(s)) {
      free($2);
      PyErr_SetString(PyExc_ValueError, "List items must be strings");
      SWIG_fail;
    }
    $2[i] = PyString_AsString(s);
  }
  $2[i] = 0;
}

%typemap(freearg) (int argc, char *argv[]) {
  if ($2) free($2);
}

/* Required for C++ method overloading */
%typecheck(SWIG_TYPECHECK_STRING_ARRAY) (int argc, char *argv[]) {
  $1 = PyList_Check($input) ? 1 : 0;
}

多参数映射总是通过用括号包围参数来指定，如图所示。例如：
%typemap(in) (int argc, char *argv[]) { ... }

在类型映射代码中，变量 $1、$2 等指的是映射中的每个类型。所有常用的替换都适用——只需在变量名上使用适当的 $1 或 $2 前缀（例如，$2_type、$1_ltype 等）
多参数类型映射始终优先于简单类型映射，并且 SWIG 始终执行最长匹配搜索。因此，您将获得以下行为：
%typemap(in) int argc                              { ... typemap 1 ... }
%typemap(in) (int argc, char *argv[])              { ... typemap 2 ... }
%typemap(in) (int argc, char *argv[], char *env[]) { ... typemap 3 ... }

int foo(int argc, char *argv[]);                   // Uses typemap 2
int bar(int argc, int x);                          // Uses typemap 1
int spam(int argc, char *argv[], char *env[]);     // Uses typemap 3

应该强调的是，多参数类型映射可以出现在函数声明中的任何地方，并且可以出现多次。例如，你可以这样写：
%typemap(in) (int scount, char *swords[]) { ... } 
%typemap(in) (int wcount, char *words[]) { ... } 

void search_words(int scount, char *swords[ ], int wcount, char *words[], int maxcount);

其他指令如 %apply 和 %clear 也适用于多参数映射。例如：
%apply (int argc, char *argv[]) { 
    (int scount, char *swords[]), 
    (int wcount, char *words[]) 
}; 
... 
%clear (int scount, char *swords[]), (int wcount, char *words[]); 
...

不要忘记还 为重载函数 提供合适的类型映射，例如上面为 foo 显示的 %typecheck。仅当函数在 C++ 中重载时才需要。
尽管多参数类型映射可能看起来像一个奇特的、很少使用的功能，但在几种情况下它们是有意义的。首先，假设您想包装类似于低级 read() 和 write() 系统调用。例如:
typedef unsigned int size_t; 

int read(int fd, void *rbuffer, size_t len); 
int write(int fd, void *wbuffer, size_t len);

照原样，使用这些函数的唯一方法是分配内存并将某种指针作为第二个参数传递——这个过程可能需要使用辅助函数。但是，使用多参数映射，可以将函数转换为更自然的东西。例如，您可以像这样编写类型映射：
// typemap for an outgoing buffer
%typemap(in) (void *wbuffer, size_t len) {
  if (!PyString_Check($input)) {
    PyErr_SetString(PyExc_ValueError, "Expecting a string");
    SWIG_fail;
  }
  $1 = (void *) PyString_AsString($input);
  $2 = PyString_Size($input);
}

// typemap for an incoming buffer
%typemap(in) (void *rbuffer, size_t len) {
  if (!PyInt_Check($input)) {
    PyErr_SetString(PyExc_ValueError, "Expecting an integer");
    SWIG_fail;
  }
  $2 = PyInt_AsLong($input);
  if ($2 < 0) {
    PyErr_SetString(PyExc_ValueError, "Positive integer expected");
    SWIG_fail;
  }
  $1 = (void *) malloc($2);
}

// Return the buffer.  Discarding any previous return result
%typemap(argout) (void *rbuffer, size_t len) {
  Py_XDECREF($result);   /* Blow away any previous result */
  if (result < 0) {      /* Check for I/O error */
    free($1);
    PyErr_SetFromErrno(PyExc_IOError);
    return NULL;
  }
  $result = PyString_FromStringAndSize($1, result);
  free($1);
}

（注：在上面的例子中，$result 和 result 是两个不同的变量。result 是一个由该函数返回的真正的 C数据类型。$result 是返回到解释器的脚本语言的对象。）
现在，在脚本中，您可以编写简单地将缓冲区作为字符串传递的代码，如下所示：
>>> f = example.open("Makefile") 
>>> example.read(f, 40) 
'TOP = ../..\nSWIG = $(TOP)/.' 
>>> example.read(f, 40) 
'./swig\nSRCS = example.c\nTARGET ' 
>>> example.close(f) 
0 
>>> g = example.open("foo", example.O_WRONLY | example.O_CREAT, 0644) 
>>> example.write(g, "Hello world\n") 
12 
>>> example.write(g, "This is a test\n") 
15 
>>> example.close( g) 
0 
>>>

许多多参数类型映射问题也出现在执行矩阵计算的库中——尤其是当它们映射到低级 Fortran 或 C 代码时。例如，您可能有这样的函数：
int is_symmetric(double *mat, int rows, int columns);

在这种情况下，您可能希望将某种更高级别的对象作为矩阵传递。为此，您可以编写一个多参数类型映射，如下所示：
%typemap(in) (double *mat, int rows, int columns) { 
  MatrixObject *a; 
  a = GetMatrixFromObject($input); /* 以某种方式获取矩阵 */ 

  /* 获取矩阵属性 */ 
  $1 = GetPointer(a); 
  $2 = GetRows(a); 
  $3 = GetColumns(a); 
}

这种技术可用于挂钩脚本语言矩阵包，例如 Numeric Python。但是，还应该强调一些注意事项。例如，在跨语言时，您可能需要担心诸如行优先与列优先排序（并在需要时执行转换）等问题。请注意，多参数类型映射无法处理非连续的 C/C++ 参数；需要编写一个解决方法，例如帮助函数重新排序参数以使其连续。
11.10 Typemap 警告
可以向类型映射添加警告，以便在使用类型映射时 SWIG 生成警告消息。请参阅 发出警告 部分中的信息。
11.11 Typemap 片段
片段的主要目的是减少重复使用类型映射代码可能导致的代码膨胀。片段是可以被认为是类型映射的代码依赖项的代码片段。如果一个片段被多个类型映射使用，则片段内的代码片段仅生成一次。通常通过将类型映射代码移动到支持函数中，然后将支持函数放入片段中来减少代码膨胀。
例如，如果你有一个很长的 typemap
%typemap(in) MyClass * { 
  MyClass *value = 0; 

  ... 多行编组代码 ... 

  $result = value; 
}

相同的编组代码经常在多个类型映射中重复，例如“in”、“varin”、“directorout”等。 SWIG 为每个需要类型映射代码的参数复制代码，很容易导致生成的代码中的代码膨胀。为了消除这种情况，定义一个包含公共编组代码的片段：
%fragment("AsMyClass", "header") {
  MyClass *AsMyClass(PyObject *obj) {
    MyClass *value = 0;

    ... many lines of marshalling code  ...

    return value;
  }
}

%typemap(in, fragment="AsMyClass") MyClass * {
  $result = AsMyClass($input);
}

%typemap(varin, fragment="AsMyClass") MyClass * {
  $result = AsMyClass($input);
}

当需要 MyClass 的“in”或“varin”类型映射时，将名为“AsMyClass”的片段的内容添加到生成代码中的“header”部分，然后发出类型映射代码。因此，方法AsMyClass将在调用它的任何类型映射代码之前生成到包装器代码中。
要定义片段，您需要片段名称、用于生成片段代码的部分名称以及代码本身。有关部分名称的完整列表，请参阅 代码插入块。通常使用的部分名称是“header”。可以使用不同的分隔符：
%fragment("my_name", "header") %{ ... %} 
%fragment("my_name", "header") { ... } 
%fragment("my_name", "header") " ... "

这些遵循 预处理分隔符 部分中提到的常用预处理规则。以下是使用片段的一些规则和指南：
1. 一个片段只添加到包装代码一次。当使用上面的 MyClass * 类型映射并包装方法时：
void foo(MyClass *a, MyClass *b);

生成的代码将类似于：
MyClass *AsMyClass(PyObject *obj) { 
  ... 
} 

void _wrap_foo(...) { 
  .... 
  arg1 = AsMyClass(obj1); 
  arg2 = AsMyClass(obj2); 
  ... 
  foo(arg1, arg2); 
}

即使有重复的类型映射代码来处理a 和b，AsMyClass 方法也将只定义一次。
2. 一个片段应该只定义一次。如果有多个定义，则使用第一个定义。所有其他定义都被默默忽略。例如，如果你有
%fragment("AsMyClass", "header") { ...定义 1 ... } 
.... 
%fragment("AsMyClass", "header") { ...定义 2 ... }

仅使用第一个定义。通过这种方式，您可以通过在库 %include 之前定义您的片段来覆盖 SWIG 库中的默认片段。请注意，此行为与类型映射相反，其中最后定义/应用的类型映射占优势。Fragment 遵循先进先出的约定，因为它们是全局的，而 typemap 是本地专用的。
3. 片段名称不能包含逗号。
4. 一个片段可以使用一个或多个附加片段，例如：
%fragment("<limits.h>", "header") { 
  %#include <limits.h> 
} 

%fragment("AsMyClass", "header", fragment="<limits.h>") { 
  MyClass *AsMyClass (PyObject *obj) { 
    MyClass *value = 0; 

    ... 一些编组代码 ... 

    if (ival < CHAR_MIN /*defined in <limits.h>*/) { 
      ... 
    } else { 
      ... 
    } 
    ... 
    return value; 
  } 
}

在这种情况下，当发出“AsMyClass”片段时，它也会触发包含“<limits.h>”片段。
5. 一个片段可以依赖于许多其他片段，例如：
%fragment("bigfragment", "header", fragment="frag1", fragment="frag2", fragment="frag3") "";

使用“bigfragment”时，“frag1”、“frag2”、“frag3”三个依赖分片也被拉入。注意由于“bigframent”为空（空字符串-“”），所以不加任何代码本身，但仅触发其他片段的包含。
6. 一个 typemap 也可以使用多个片段，但由于语法不同，您需要在逗号分隔列表中指定依赖片段。考虑：
%typemap(in, fragment="frag1, frag2, frag3") {...}

这相当于：
%typemap(in, fragment="bigfragment") {...}

当与上面定义的“bigfragment”一起使用时。
7. 最后，您可以在生成的代码中的任何位置强制包含片段，如下所示：
%fragment("bigfragment");

例如，这在模板类中非常有用。
大多数读者可能希望跳过关于高级片段使用的接下来的两个小节，除非希望真正掌握一些在 SWIG 类型映射库部分中使用的强大但棘手的宏和片段用法。
11.11.1 片段类型特化
片段可以是专门的类型。语法如下：
%fragment("name", "header") { ...一个与类型无关的片段... } 
%fragment("name"{type}, "header") { ...一个依赖于类型的片段... }

其中type是 C/C++ 类型。和 typemap 一样，片段也可以在模板中使用，例如：
template <class T>
struct A {
  %fragment("incode"{A<T>}, "header") {
    ... 'incode' specialized fragment ...
  }

  %typemap(in, fragment="incode"{A<T>}) {
    ... here we use the 'type specialized' fragment "incode"{A<T>} ...
  }
};

11.11.2 片段和自动 typemap 特化
由于片段可以是类型专门化的，它们可以优雅地用于专门化类型映射。例如，如果你有类似的东西：
%fragment("incode"{float}, "header") { 
  float in_method_float(PyObject *obj) { 
    ... 
  } 
} 

%fragment("incode"{long}, "header") { 
  float in_method_long(PyObject *obj) { 
    ... 
  } 
} 

// %my_typemaps 宏定义
%define %my_typemaps(Type) 
%typemap(in, fragment="incode"{Type}) Type { 
  value = in_method_##Type(obj); 
} 
%enddef 

%my_typemaps(float); 
%my_typemaps(long);

然后将使用正确的“incode”{float}或“incode”{long} 片段，并且每当使用 float 或 long 类型作为输入参数时，将调用 in_method_float 和 in_method_long 方法。
此功能在某些脚本语言的 SWIG 库中提供的类型映射中得到了大量使用。感兴趣（或非常勇敢）的读者可以查看 SWIG 随附的 fragment.swg 文件，以了解其实际效果。
11.12 运行时类型检查器
大多数脚本语言在运行时需要类型信息。这个类型信息可以包括如何构造类型、如何垃圾收集类型以及类型之间的继承关系。如果语言接口没有提供自己的类型信息存储，生成的 SWIG 代码需要提供。
对类型系统的要求：
- 存储继承和类型等价信息，并能够正确地重新创建类型指针。
- 在模块之间共享类型信息。
- 模块可以以任何顺序加载，而不管实际的类型依赖。
- 一般避免使用动态分配的内存和库/系统调用。
- 提供相当快的实现，最大限度地减少所有语言模块的查找时间。
- 自定义的、特定于语言的信息可以附加到类型。
- 模块可以从类型系统中卸载。
11.12.1 实施
许多（但不是全部）SWIG 支持的目标语言都使用运行时类型检查器。运行时类型检查器功能不是必需的，因此不用于静态类型语言，例如 Java 和 C#。基于脚本和方案的语言依赖于它，它构成了 SWIG 对这些语言的操作的关键部分。
当指针、数组和对象被 SWIG 包装时，它们通常被转换为类型化指针对象。例如，Foo * 的一个实例可能是一个这样编码的字符串：
_108e688_p_Foo

在基本层面上，类型检查器只是为扩展模块恢复一些类型安全。但是，类型检查器还负责确保正确处理包装的 C++ 类——尤其是在使用继承时。当扩展模块使用多重继承时，这一点尤其重要。例如：
class Foo {
public:
  int x;
};

class Bar {
public:
  int y;
};

class FooBar : public Foo, public Bar {
public:
  int z;
};

当类FooBar在内存中组织时，它包含类Foo和Bar的内容以及它自己的数据成员。例如：
FooBar --> | -----------|  <-- Foo
           |   int x    |
           |------------|  <-- Bar
           |   int y    |
           |------------|
           |   int z    |
           |------------|

由于基类数据堆叠在一起的方式，将 Foobar * 转换为任一基类可能会更改指针的实际值。这意味着使用简单的整数或空的 void * 来表示指针通常是不安全的 ——需要类型标签来实现对指针值的正确处理（并在需要时进行调整）。
在为每种语言生成的包装器代码中，通过使用特殊的类型描述符和转换函数来处理指针。例如，如果您查看 Python 的包装器代码，您将看到类似于以下的代码（为简洁起见进行了简化）：
if (!SWIG_IsOK(SWIG_ConvertPtr(obj0, (void **) &arg1, SWIGTYPE_p_Foo, 0))) { 
  SWIG_exception_fail(SWIG_TypeError, "in method 'GrabVal', expecting type Foo"); 
}

在此代码中，SWIGTYPE_p_Foo 是描述 Foo * 的类型描述符。类型描述符实际上是一个指向结构的指针，该结构包含有关在目标语言中使用的类型名称的信息、等效类型名称的列表（通过 typedef 或继承）和指针值处理信息（如果适用）。所述SWIG_ConvertPtr() 函数是一个简单的效用函数，它在目标语言和类型描述符对象的指针的对象，并使用该信息来生成C ++指针。该 SWIG_IsOK 错误和宏检查返回值SWIG_exception_fail 可以调用以在目标语言中引发异常。但是，转换函数的确切名称和调用约定取决于目标语言（有关详细信息，请参阅特定于语言的章节）。
实际的类型代码在 swigrun.swg 中，并插入到生成的 swig 包装文件的顶部附近。短语“可以转换为类型 Y 的类型 X”意味着给定类型 X，它可以转换为类型 Y。换句话说，X 是 Y 的派生类或 X 是 Y 的 typedef。存储类型信息的结构如下所示：
/* 存储一种类型信息的结构 */ 
typedef struct swig_type_info { 
  const char *name;               /* 这种类型的重整名称 */ 
  const char *str;                /* 这种类型的人类可读名称 */ 
  swig_dycast_func dcast;         /* 沿层次结构动态转换函数 */ 
  struct swig_cast_info *cast;    /* 可以转换为这种类型的类型的链接列表 */ 
  void *clientdata;               /* 语言特定类型数据 */ 
} swig_type_info; 

/* 用于存储类型和转换函数的结构*/ 
typedef struct swig_cast_info { 
  swig_type_info *type;           /* 指向与该类型等价的类型的指针 */
  swig_converter_func converter； /* 转换空指针的函数 */ 
  struct swig_cast_info *next;    /* 指向链表中下一个转换的指针 */ 
  struct swig_cast_info *prev;    /* 指向前一个演员表的指针 */ 
} swig_cast_info;

每个 swig_type_info 存储一个它等价的类型的链表。这个双向链表中的每个条目都存储一个指向另一个 swig_type_info 结构的指针，以及一个指向转换函数的指针。这个转换函数用于解决 FooBar 类的上述问题，正确返回一个指向我们想要的类型的指针。
我们需要解决的基本问题是验证和构建传递给函数的参数。所以回到上面的 SWIG_ConvertPtr() 函数示例，我们期待一个 Foo * 并且需要检查obj0是否实际上是一个 Foo * 。以前，SWIGTYPE_p_Foo只是一个指向描述 Foo * 的 swig_type_info 结构的指针。因此，我们遍历附加到 SWIGTYPE_p_Foo的 swig_cast_info 结构的链表。如果我们看到 obj0 的类型在链表中，我们通过关联的转换函数传递对象，然后返回一个正数。如果我们在没有匹配的情况下到达链表的末尾，则 obj0 无法转换为 Foo *并产生错误。
另一个需要解决的问题是在多个模块之间共享类型信息。更明确地说，我们需要为每种类型设置一个  swig_type_info。如果两个模块都使用该类型，则加载的第二个模块必须从已加载的模块中查找并使用 swig_type_info 结构。由于没有使用动态内存，并且投射信息的循环依赖，加载类型信息有些棘手，这里不做解释。完整的描述在 Lib/swiginit.swg 文件中（靠近任何生成文件的顶部）。
每个模块都有一个 swig_module_info 结构，如下所示：
/* 用于存储模块信息的结构
 * 每个模块生成一个这样的结构，运行时收集
 * 所有这些结构并将它们存储在一个循环链表中。*/ 
typedef struct swig_module_info { 
  swig_type_info **types;        /* 指向模块中 swig_type_info 结构的指针数组 */ 
  int size;                      /* 此模块中的类型数 */ 
  struct swig_module_info *next; /* 指向循环链表中下一个元素的指针 */ 
  swig_type_info **type_initial; /* 最初生成的类型结构数组 */ 
  swig_cast_info **cast_initial; /* 最初生成的铸造结构数组 */
  void *clientdata;              /* 语言特定的模块数据 */ 
} swig_module_info;

每个模块存储一个指向 swig_type_info 结构和该模块中类型数量的指针数组。因此，当加载第二个模块时，它会找到第一个模块的 swig_module_info 结构并搜索类型数组。如果它自己的任何类型在第一个模块中并且已经被加载，它使用那些 swig_type_inf o结构而不是创建新的。这些 swig_module_info 结构在循环链表中链接在一起。
11.12.2 用法
本节介绍如何使用类型映射中的这些函数。要了解如何从外部文件（不是生成的 _wrap.c 文件）调用这些函数，请参阅 对运行时系统 的 外部访问 部分。
在 typemap 中转换指针时，typemap 代码通常类似于以下内容：
%typemap(in) Foo * { 
  if (!SWIG_IsOK(SWIG_ConvertPtr($input, (void **) &$1, $1_descriptor, 0))) { 
    SWIG_exception_fail(SWIG_TypeError, "in method '$symname', expecting type Foo" ); 
  } 
}

最关键的部分是 typemap 是使用了 $1_descriptor 特殊变量。当放置在类型映射中时，它会扩展为上面的 SWIGTYPE_* 类型描述符对象。作为一般规则，您应该始终使用 $1_descripto r而不是尝试直接对类型描述符名称进行硬编码。
您应该始终使用 $1_descriptor 变量还有另一个原因。当这个特殊变量被扩展时，SWIG 将相应的类型标记为“正在使用”。当类型表和类型信息在包装器文件中发出时，仅为接口中实际使用的那些数据类型生成描述符信息。这大大减少了类型表的大小并提高了效率。
有时，您可能需要编写需要转换其他类型指针的类型映射。为了解决这个问题，前面介绍的特殊变量宏 $descriptor(type) 可用于为任何 C 数据类型生成 SWIG 类型描述符名称。例如：
%typemap(in) Foo * {
  if (!SWIG_IsOK(SWIG_ConvertPtr($input, (void **) &$1, $1_descriptor, 0))) {
    Bar *temp;
    if (!SWIG_IsOK(SWIG_ConvertPtr($input, (void **) &temp, $descriptor(Bar *), 0))) {
      SWIG_exception_fail(SWIG_TypeError, "in method '$symname', expecting type Foo or Bar");
    }
    $1 = (Foo *)temp;
  }
}

$descriptor(type) 的主要用途是为容器对象和其他复杂数据结构编写类型映射。参数有一些限制——即它必须是完全定义的 C 数据类型。它不能是任何特殊的类型映射变量。
在某些情况下，SWIG 可能不会像您期望的那样生成类型描述符。例如，如果您以某种非标准方式转换指针或使用接口文件和模块的不寻常组合，您可能会发现 SWIG 忽略了特定类型描述符的信息。要解决此问题，您可能需要使用 %types 指令。例如：
%types(int *, short *, long *, float *, double *);

使用 %types 时，即使这些数据类型从未出现在接口文件的其他地方，SWIG 也会生成类型描述符信息。
有关运行时类型检查的更多详细信息，请参见各个语言模块的文档。阅读源代码也可能有所帮助。SWIG 库中的 Lib/swigrun.swg 文件包含生成的所有类型检查代码的源代码。此代码也包含在每个生成的包装文件中，因此您可能只需查看 SWIG 的输出即可更好地了解如何管理类型。
11.13 Typemaps 和重载
本节不适用于 Java 和 C# 等静态类型语言，在这些语言中，通过在目标语言中生成重载方法，处理类型的重载与 C++ 非常相似。在许多其他目标语言中，SWIG 仍然完全支持 C++ 重载方法和函数。例如，如果您有一个这样的函数集合：
int foo(int x); 
int foo(double x); 
int foo(char *s, int y);

您可以从脚本解释器以正常方式访问这些函数：
# Python 
foo(3) # foo(int) 
foo(3.5) # foo(double) 
foo("hello", 5) # foo(char *, int) 

# Tcl 
foo 3 # foo(int) 
foo 3.5 # foo( double) 
foo hello 5 # foo(char *, int)

为了实现重载，SWIG 为每个重载的方法生成一个单独的包装函数。例如，上述函数将产生大致如下所示的内容：
// wrapper pseudocode
_wrap_foo_0(argc, args[]) {       // foo(int)
  int arg1;
  int result;
  ...
  arg1 = FromInteger(args[0]);
  result = foo(arg1);
  return ToInteger(result);
}

_wrap_foo_1(argc, args[]) {       // foo(double)
  double arg1;
  int result;
  ...
  arg1 = FromDouble(args[0]);
  result = foo(arg1);
  return ToInteger(result);
}

_wrap_foo_2(argc, args[]) {       // foo(char *, int)
  char *arg1;
  int   arg2;
  int result;
  ...
  arg1 = FromString(args[0]);
  arg2 = FromInteger(args[1]);
  result = foo(arg1, arg2);
  return ToInteger(result);
}

接下来，生成一个动态调度函数：
_wrap_foo(argc, args[]) { 
  if (argc == 1) { 
    if (IsInteger(args[0])) { 
      return _wrap_foo_0(argc, args); 
    } 
    if (IsDouble(args[0])) { 
      return _wrap_foo_1(argc, args); 
    } 
  } 
  if (argc == 2) { 
    if (IsString(args[0]) && IsInteger(args[1])) { 
      return _wrap_foo_2(argc, args); 
    } 
  } 
  error("No matching function!\n"); 
}

动态调度函数的目的是根据参数类型选择合适的 C++ 函数——这是大多数 SWIG 目标语言在运行时必须执行的任务。
动态调度函数的生成是一件相对棘手的事情。不仅必须考虑输入类型映射（这些类型映射可以从根本上改变接受的参数类型），而且还必须以非常特定的顺序对重载的方法进行排序和检查，以解决潜在的歧义。在“ SWIG 和 C++ ”一章中可以找到此排名过程的高级概述。在那一章中没有提到的是它的实现机制——作为类型映射的集合。
为了支持动态调度，SWIG 首先定义了一个通用类型层次结构，如下所示：
Symbolic Name                   Precedence Value
------------------------------  ------------------
SWIG_TYPECHECK_POINTER           0  
SWIG_TYPECHECK_VOIDPTR           10 
SWIG_TYPECHECK_BOOL              15 
SWIG_TYPECHECK_UINT8             20 
SWIG_TYPECHECK_INT8              25 
SWIG_TYPECHECK_UINT16            30 
SWIG_TYPECHECK_INT16             35 
SWIG_TYPECHECK_UINT32            40 
SWIG_TYPECHECK_INT32             45 
SWIG_TYPECHECK_UINT64            50 
SWIG_TYPECHECK_INT64             55 
SWIG_TYPECHECK_UINT128           60 
SWIG_TYPECHECK_INT128            65 
SWIG_TYPECHECK_INTEGER           70 
SWIG_TYPECHECK_FLOAT             80 
SWIG_TYPECHECK_DOUBLE            90 
SWIG_TYPECHECK_COMPLEX           100 
SWIG_TYPECHECK_UNICHAR           110 
SWIG_TYPECHECK_UNISTRING         120 
SWIG_TYPECHECK_CHAR              130 
SWIG_TYPECHECK_STRING            140 
SWIG_TYPECHECK_BOOL_ARRAY        1015 
SWIG_TYPECHECK_INT8_ARRAY        1025 
SWIG_TYPECHECK_INT16_ARRAY       1035 
SWIG_TYPECHECK_INT32_ARRAY       1045 
SWIG_TYPECHECK_INT64_ARRAY       1055 
SWIG_TYPECHECK_INT128_ARRAY      1065 
SWIG_TYPECHECK_FLOAT_ARRAY       1080 
SWIG_TYPECHECK_DOUBLE_ARRAY      1090 
SWIG_TYPECHECK_CHAR_ARRAY        1130 
SWIG_TYPECHECK_STRING_ARRAY      1140 

（这些优先级在 swig.swg 中定义，这是一个包含在所有目标语言模块中的库文件。）
在这个表中，优先级决定了检查类型的顺序。低值总是在高值之前检查。例如，在浮点数之前检查整数，在数组之前检查单个值，等等。
使用上表作为指导，每种目标语言都定义了一组“类型检查”类型映射。Python 模块的以下摘录说明了这一点：
/* Python 类型检查规则 */ 
/* 注意：%typecheck(X) 是 %typemap(typecheck, precedence=X) 的宏 */ 

%typecheck(SWIG_TYPECHECK_INTEGER) 
  int, short, long, 
  unsigned int, unsigned short, unsigned long,
  signed char, unsigned char,
  long long, unsigned long long,
  const int &，const short &，const long &，
  const unsigned int &，const unsigned short &，const unsigned long &，
  const long long &，const unsigned long long &, 
  enum SWIGTYPE, 
  bool, const bool & 
{ 
  $1 = (PyInt_Check($input) || PyLong_Check($input)) ? 1：0；
} 

%typecheck(SWIG_TYPECHECK_DOUBLE) 
  float, double, 
  const float &,
{ 
  $1 = (PyFloat_Check($input) || PyInt_Check($input) || PyLong_Check($input)) ? 1：0；
} 

%typecheck(SWIG_TYPECHECK_CHAR) char { 
  $1 = (PyString_Check($input) && (PyString_Size($input) == 1)) ? 1：0；
} 

%typecheck(SWIG_TYPECHECK_STRING) char * { 
  $1 = PyString_Check($input) ? 1：0；
} 

%typemap(typecheck, precedence=SWIG_TYPECHECK_POINTER, noblock=1) SWIGTYPE * { 
  void *vptr = 0; 
  int res = SWIG_ConvertPtr($input, &vptr, $1_descriptor, 0); 
  $1 = SWIG_IsOK(res) ? 1：0；
} 

%typecheck(SWIG_TYPECHECK_POINTER) PyObject * { 
  $1 = ($input != 0); 
}

可能需要考虑一下，但这段代码仅组织了所有基本的 C++ 类型，提供了一些简单的类型检查代码，并为每个类型分配了一个优先级值。
最后，为了生成动态调度函数，SWIG 使用以下算法：
- 重载方法首先按所需参数的数量排序。
- 具有相同数量参数的方法然后按参数类型的优先级值排序。
- 然后发出类型检查类型映射以生成一个调度函数，该函数以正确的顺序检查参数。
如果您还没有自己编写任何类型映射，则无需担心类型检查规则。但是，如果您编写了新的输入类型映射，则可能还必须提供类型检查规则。一种简单的方法是简单地复制现有的类型检查规则之一。这是一个例子，
// C++ 字符串的类型映射
%typemap(in) std::string { 
  if (PyString_Check($input)) { 
    $1 = std::string(PyString_AsString($input)); 
  } else { 
    SWIG_exception(SWIG_TypeError, "string expected"); 
  } 
} 
// 复制“char *”的类型检查代码。  
%typemap(typecheck) std::string = char *;

底线：如果您正在编写新的类型映射并使用重载方法，您可能必须编写新的类型检查代码或复制和修改现有的类型检查代码。
如果你写了一个 typecheck typemap 并省略了优先级，例如将其注释掉，如下所示：
%typemap(typecheck /*, precedence=SWIG_TYPECHECK_INTEGER*/) int { 
  $1 = PyInt_Check($input) ? 1：0；
}

然后该类型的优先级高于任何其他已知的优先级，并发出警告：
example.i:18: Warning 467: Overloaded method foo(int) not supported (incomplete type checking rule - no precedence level in typecheck typemap for 'int').

笔记：
- 类型检查类型映射不用于非重载方法。因此，始终需要检查任何“in”类型映射中的类型。
- 动态调度过程只是一种启发式方法。在许多极端情况下，SWIG 根本无法像 C++ 那样消除类型的歧义。解决这种歧义的唯一方法是使用 %rename 指令重命名重载方法之一（有效地消除重载）。
- 类型检查可能是部分的。例如，如果使用数组，类型检查代码可能只检查第一个数组元素的类型并使用它来分派到正确的函数。随后的“in”类型映射将执行更广泛的类型检查。
- 请务必阅读“ SWIG 和 C++ ”一章中有关重载的部分。
11.14 更多关于 %apply 和 %clear
为了实现某些类型的程序行为，有时需要编写类型映射集。例如，为了支持输出参数，人们经常编写一组这样的类型映射：
%typemap(in, numinputs=0) int *OUTPUT (int temp) { 
  $1 = &temp; 
} 
%typemap(argout) int *OUTPUT { 
  // 以某种方式返回值
}

为了更容易地将类型映射应用于不同的参数类型和名称，%apply指令将所有类型映射从一种类型复制到另一种类型。例如，如果你指定这个，
%apply int *OUTPUT { int *retvalue, int32 *output };

然后将所有 int *OUTPUT 类型映射复制到 int *retvalue 和 int32 *output。
然而，%apply 有一个需要更多描述的微妙方面。也就是说，如果已经为目标数据类型定义了类型映射规则，则 %apply 不会覆盖它。这种行为允许你做两件事：
- 您可以通过首先定义一些类型映射然后使用 %apply 合并其余部分来专门化复杂类型映射规则的部分。
- 可以使用重复的 %apply 指令将一组不同的类型映射应用于相同的数据类型。
例如：
%typemap(in) int *INPUT (int temp) { 
  temp = ... get value from $input ...;
  $1 = &temp; 
} 

%typemap(check) int *POSITIVE { 
  if (*$1 <= 0) { 
    SWIG_exception(SWIG_ValueError, "Expected a positive number!\n");
    return NULL;
  }
}

...
%apply int *INPUT     { int *invalue };
%apply int *POSITIVE  { int *invalue };

由于 %apply 不会覆盖或替换任何现有规则，因此重置行为的唯一方法是使用 %clear 指令。%clear 删除为特定数据类型定义的所有类型映射规则。例如：
%clear int *invalue;

11.15 在 typemaps 之间传递数据
同样重要的是要注意，局部变量的主要用途是创建堆栈分配的对象，以便在包装函数内临时使用（这比在堆上分配数据更快且不易出错）。通常，变量不打算在不同类型的类型映射之间传递信息。但是，如果您意识到本地名称附加了参数编号，则可以这样做。例如，你可以这样做：
%typemap(in) int *(int temp) { 
  temp = (int) PyInt_AsLong($input); 
  $1 = &temp; 
} 

%typemap(argout) int * { 
  PyObject *o = PyInt_FromLong(temp$argnum); 
  ... 
}

在这种情况下，$argnum变量被扩展为参数编号。因此，代码将引用适当的本地，例如 temp1 和 temp2。应该注意的是，这里有很多机会打破宇宙，应该避免以这种方式访问本地人。至少，您应该确保共享信息的类型映射具有完全相同的类型和名称。
11.16 C++“this”指针
所有 typemaps 讨论的规则适用于 C ++ 以及 C。然而除了 C ++ 通过一个额外的参数到每个非静态类方法-在此指针。有时，将类型映射应用于此指针可能很有用（例如，在延迟之前检查并确保它为非空）。实际上，C 也有一个等效的 this 指针，它在访问 C 结构中的变量时使用。
为了自定义 this 指针处理，在你的类型映射中定位一个名为 self 的变量。self 是 SWIG 用来指代包装函数中的额外参数的名称。
例如，如果为 Java 生成包装：
%typemap(check) SWIGTYPE *self %{
if (!$1) {
  SWIG_JavaThrowException(jenv, SWIG_JavaNullPointerException,
    "invalid native object; delete() likely already called");
  return $null;
}
%}

在上述情况下，$1 变量被扩展为 SWIG 用作 this 指针的参数名称。然后，SWIG 将在调用实际 C++ 类方法之前插入检查代码，并引发异常而不是使 Java 虚拟机崩溃。生成的代码将类似于：
 if (!arg1) {
    SWIG_JavaThrowException(jenv, SWIG_JavaNullPointerException,
      "invalid native object; delete() likely already called");
    return ;
  }
  (arg1)->wrappedFunction(...);

请注意，如果您有一个名为 self 的参数，那么它也将匹配类型映射。一种解决方法是创建一个包装方法的接口文件，但为参数指定一个不同于 self 的名称。
11.17 去哪里了解更多信息？
查找有关编写类型映射的更多信息的最佳位置是查看 SWIG 库。大多数语言模块使用类型映射定义它们的所有默认行为。这些可以在 python.swg、perl5.swg、tcl8.swg 等文件中找到 。库中的 typemaps.i 文件还包含许多示例。您应该查看这些文件以了解如何定义您自己的类型映射。一些语言模块支持额外的类型映射，并且在每种目标语言的各个章节中提供了更多信息。在那里您还可以找到更多动手实践的例子。

12 自定义功能
在许多情况下，需要更改接口中特定声明的默认包装。例如，您可能希望提供用于捕获 C++ 异常、添加断言或向底层代码生成器提供提示的钩子。本章介绍了其中一些自定义技术。首先，讨论异常处理。然后，描述了称为“features”的更通用的定制机制。
12.1 %exception 的异常处理
在％exception 指令允许你定义一个通用的异常处理程序。例如，您可以指定以下内容：
%exception { 
  try { 
    $action 
  } 
  catch (RangeError) { 
    ... 处理错误 ... 
  } 
}

如何处理异常取决于目标语言，例如 Python：
%exception { 
  try { 
    $action 
  } 
  catch (RangeError) { 
    PyErr_SetString(PyExc_IndexError, "index out-of-bounds"); 
    SWIG_fail; 
  } 
}

定义后，括在大括号中的代码直接插入低级包装函数中。特殊变量 $action 是少数受支持的 %exception 特殊变量之一，并被要执行的实际操作（函数调用、方法调用、属性访问等）替换。异常处理程序在被显式删除之前一直有效。这是通过使用没有代码的 %exception 或 %noexception 来完成的。例如：
％exception; // 删除任何先前定义的处理程序

兼容性说明：以前版本的 SWIG 使用特殊指令 %except 进行异常处理。该指令已被弃用—— %exception 提供了相同的功能，但更加灵活。
12.1.1 C 代码中的异常处理
C 没有正式的异常处理机制，因此可以使用多种方法。一种比较常见的技术是简单地设置一个特殊的错误代码。例如：
/* File : except.c */

static char error_message[256];
static int error_status = 0;

void throw_exception(char *msg) {
  strncpy(error_message, msg, 256);
  error_status = 1;
}

void clear_exception() {
  error_status = 0;
}
char *check_exception() {
  if (error_status)
    return error_message;
  else
    return NULL;
}

要使用这些函数，函数只需调用 throw_exception() 来指示发生了错误。例如 ：
double inv(double x) {
  if (x != 0)
    return 1.0/x;
  else {
    throw_exception("Division by zero");
    return 0;
  }
}

要捕获异常，您可以编写一个简单的异常处理程序，如下所示（针对 Perl5 显示）：
%exception {
  char *err;
  clear_exception();
  $action
  if ((err = check_exception())) {
    croak(err);
  }
}

在这种情况下，当发生错误时，它会被转换为 Perl 错误。每个目标语言都有自己的方法来在 Perl 中创建运行时错误/异常，它是上面显示的 croak 方法。
12.1.2 使用 longjmp() 处理异常
也可以使用 <setjmp.h>库将异常处理添加到 C 代码中。这是一个依赖于 C 预处理器的简约实现：
/* File : except.c
   Just the declaration of a few global variables we're going to use */

#include <setjmp.h>
jmp_buf exception_buffer;
int exception_status;

/* File : except.h */
#include <setjmp.h>
extern jmp_buf exception_buffer;
extern int exception_status;

#define try if ((exception_status = setjmp(exception_buffer)) == 0)
#define catch(val) else if (exception_status == val)
#define throw(val) longjmp(exception_buffer, val)
#define finally else

/* Exception codes */

#define RangeError     1
#define DivisionByZero 2
#define OutOfMemory    3

现在，在 C 程序中，您可以执行以下操作：
double inv(double x) {
  if (x)
    return 1.0/x;
  else
    throw(DivisionByZero);
}

最后，要创建 SWIG 异常处理程序，请编写以下内容：
%{
#include "except.h"
%}

%exception {
  try {
    $action
  } catch(RangeError) {
    croak("Range Error");
  } catch(DivisionByZero) {
    croak("Division by zero");
  } catch(OutOfMemory) {
    croak("Out of memory");
  } finally {
    croak("Unknown exception");
  }
}

注意：此实现仅用于说明一般思想。为了让它更好地工作，您需要修改它以处理嵌套的 try声明。
12.1.3 处理 C++ 异常
处理 C++ 异常也很简单。例如：
%exception {
  try {
    $action
  } catch(RangeError) {
    croak("Range Error");
  } catch(DivisionByZero) {
    croak("Division by zero");
  } catch(OutOfMemory) {
    croak("Out of memory");
  } catch(...) {
    croak("Unknown exception");
  }
}

异常类型需要在别处声明为类，可能在头文件中：
class RangeError {};
class DivisionByZero {};
class OutOfMemory {};

12.1.4 变量的异常处理程序
默认情况下，所有变量都将忽略 %exception，因此对于所有变量包装器，它被有效地关闭。这适用于全局变量、成员变量和静态成员变量。在 C 中包装变量时，这种方法当然是一种合乎逻辑的方法。但是，在 C++ 中，很可能在分配变量时抛出异常。为确保在包装变量时使用 %exception，需要使用 %allowexception 功能“打开”它。请注意，%allowexception 只是 %feature("allowexcept") 的宏，也就是说，它是一个名为 “allowexcept” 的功能。任何附加了此功能的变量都将使用 %exception 功能，但当然，前提是首先有一个附加到变量的 %exception。在 ％allowexception 功能就像任何其他的功能，因此可以在全局或选择性的变量中。
%allowexception;                // turn on globally
%allowexception Klass::MyVar;   // turn on for a specific variable

%noallowexception Klass::MyVar; // turn off for a specific variable
%noallowexception;              // turn off globally

12.1.5 定义不同的异常处理程序
默认情况下，%exception 指令创建一个异常处理程序，用于所有跟随它的包装函数。除非有一个定义良好（且简单）的错误处理机制，否则定义一个通用异常处理程序可能很笨拙并导致过度的代码膨胀，因为处理程序被内联到每个包装器函数中。
要解决此问题，您可以更有选择性地使用 %exception指令。一种方法是仅将其放置在关键代码段周围。例如：
%exception { 
  ... 你的异常处理程序 ... 
} 
/* 定义可以在这里抛出异常的关键操作 */ 

%exception; 

/* 定义不抛出异常的非关键操作 */

通过将异常处理程序附加到特定的声明名称，可以获得对异常处理的更精确控制。例如：
%exception allocate {
  try {
    $action
  }
  catch (MemoryError) {
    croak("Out of memory");
  }
}

在这种情况下，异常处理程序仅附加到名为“allocate”的声明。这将包括全局和成员函数。提供给%exception的名称遵循与 歧义解析和重命名 部分中描述的 %rename 相同的规则。例如，如果你想为一个特定的类定义一个异常处理程序，你可以这样写：
%exception Object::allocate { 
  try { 
    $action 
  } 
  catch (MemoryError) { 
    croak("Out of memory"); 
  } 
}

当提供类前缀时，异常处理程序将应用于指定类中的相应声明以及派生类中出现的同名函数。
%exceptio n甚至可用于在使用重载时查明精确声明。例如：
%exception Object::allocate(int) { 
  try { 
    $action 
  } 
  catch (MemoryError) { 
    croak("Out of memory"); 
  } 
}

将异常附加到特定声明是减少代码膨胀的好方法。将异常附加到头文件的特定部分也是一种有用的方法。例如：
%module example 
%{ 
#include "someheader.h" 
%} 

// 为特定声明定义一些异常处理程序
%exception Object::allocate(int) { 
  try { 
    $action 
  } 
  catch (MemoryError) { 
    croak("Out of memory "); 
  } 
} 

%exception Object::getitem { 
  try { 
    $action 
  } 
  catch (RangeError) { 
    croak("Index out of range");
  } 
} 
... 
// 读取原始头文件
%include "someheader.h"

兼容性注意：在 ％exception 指令替换由“除”类型映射弃用的所提供的功能。typemap 将允许基于函数的返回类型在目标语言中抛出异常，并且旨在成为一种用于精确定位特定声明的机制。然而，它从来没有真正有效地工作过，新的 %exception 指令要好得多。
12.1.6 %exception 的特殊变量
%exception 指令支持一些特殊变量，它们是代码替换的占位符。下表显示了可用的特殊变量并详细说明了特殊变量被替换的内容。
无法复制加载中的内容
特殊变量通常用于记录方法调用的情况。究竟哪种形式的方法调用需要记录取决于个人要求，但下面的示例代码显示了所有可能的扩展，以及如何定制异常消息以显示 C++ 方法声明：
%exception Special::something {
  log("symname: $symname");
  log("overname: $overname");
  log("wrapname: $wrapname");
  log("decl: $decl");
  log("fulldecl: $fulldecl");
  try {
    $action
  } 
  catch (MemoryError) {
      croak("Out of memory in $decl");
  }
}
void log(const char *message);
struct Special {
  void something(const char *c);
  void something(int i);
};

下面显示了Perl的第一个重载 something 包装器方法的扩展：
  log("symname: Special_something");
  log("overname: __SWIG_0");
  log("wrapname: _wrap_Special_something__SWIG_0");
  log("decl: Special::something(char const *)");
  log("fulldecl: void Special::something(char const *)");
  try {
    (arg1)->something((char const *)arg2);
  } 
  catch (MemoryError) {
    croak("Out of memory in Special::something(char const *)");
  }

12.1.7 使用 SWIG 异常库
该 exception.i 库文件提供了你的接口建立与语言无关的异常的支持。要使用它，只需在您的接口文件中放置一个“ %include exception.i ”。这提供了一个函数 SWIG_exception()，可用于以可移植的方式引发常见的脚本语言异常。例如 ：
// 语言无关的异常处理程序
%include exception.i        

%exception { 
  try { 
    $action 
  } catch(RangeError) { 
    SWIG_exception(SWIG_ValueError, "Range Error"); 
  } catch(DivisionByZero) { 
    SWIG_exception(SWIG_DivisionByZero, "除以零"); 
  } catch(OutOfMemory) { 
    SWIG_exception(SWIG_MemoryError, "内存不足"); 
  } catch(...) { 
    SWIG_exception(SWIG_RuntimeError, "未知异常"); 
  } 
}

作为参数，SWIG_exception() 采用错误类型代码（整数）和错误消息字符串。当前支持的错误类型有：
SWIG_UnknownError 
SWIG_IOError 
SWIG_RuntimeError 
SWIG_IndexError 
SWIG_TypeError 
SWIG_DivisionByZero 
SWIG_OverflowError 
SWIG_SyntaxError 
SWIG_ValueError 
SWIG_SystemError 
SWIG_AttributeError 
SWIG_MemoryError 
SWIG_NullReferenceError

所述 SWIG_exception（）函数也可以在 typemaps 使用。
12.2 对象所有权和 %newobject
某些应用程序中的一个常见问题是管理对象的正确所有权。例如，考虑这样的函数：
Foo *blah() {
  Foo *f = new Foo();
  return f;
}

如果包装函数 blah()，SWIG 不知道返回值是新分配的对象。因此，生成的扩展模块可能会产生内存泄漏（SWIG 是保守的并且永远不会删除对象，除非它确定返回的对象是新创建的）。
要解决此问题，您可以使用 %newobject 指令向代码生成器提供额外提示。例如：
%newobject blah;
Foo *blah();

%newobject 的工作原理与 %rename 和 %exception完全一样。换句话说，您可以像以前一样将其附加到类成员和参数化声明。例如：
%newobject::blah(); // 仅适用于全局 blah 
%newobject Object::blah(int, double); // 只有 blah(int, double) in Object 
%newobject *::copy; // 在所有类中复制方法
...

当提供 %newobject 时，许多语言模块将安排获取返回值的所有权。这允许该值在不再使用时自动进行垃圾收集。然而，这完全取决于目标语言（语言模块也可以选择忽略 %newobject 指令）。
与 %newobject 密切相关的是一个特殊的类型映射。“newfree”类型映射可用于释放新分配的返回值。它仅适用于 已应用%newobject 的方法，并且通常用于清理字符串结果。例如：
%typemap(newfree) char * "free($1);"; 
... 
%newobject strdup; 
... 
char *strdup(const char *s);

在这种情况下，函数的结果是目标语言中的字符串。由于此字符串是原始结果的副本，因此不再需要 strdup() 返回的数据。示例中的“newfree”类型映射只是释放此内存。
作为%newobject的补充，从 SWIG 1.3.28 开始，您可以使用%delobject指令。例如，如果您有两种方法，一种用于创建对象，另一种用于销毁它们，则可以使用：
%newobject create_foo; 
%delobject destroy_foo; 
... 
Foo *create_foo(); 
void destroy_foo(Foo *foo);

或在成员方法中：
%delobject Foo::destroy; 

class Foo { 
public: 
  void destroy() { delete this;} 

private: 
  ~Foo(); 
};

%delobject 指示 SWIG 传递给该方法的第一个参数将被销毁，因此，目标语言不应尝试将其释放两次。这类似于在第一个方法参数中使用 DISOWN 类型映射，实际上，它也取决于目标语言是否正确实现了“disown”机制。
%newobject 的使用也与引用计数集成在一起，并在C++ 引用计数对象部分进行了介绍。
兼容性说明：以前版本的 SWIG 有一个特殊的 %new 指令。但是，与%newobject 不同，它只适用于下一个声明。例如：
%new char *strdup(const char *s);

目前，这仍然受支持，但已弃用。
如何自找麻烦：将 ％newobject 指令不声明修饰符像老 ％new 指令。不要写这样的代码：
%newobject 
char *strdup(const char *s);

结果可能不是您所期望的。
12.3 特性和 %feature 指令
既％except 和％newobject 是被称为更通用的定制机制的示例的“特征”。功能只是附加到特定声明的用户可定义属性。使用 %feature 指令附加功能。例如：
%feature("except") Object::allocate { 
  try { 
    $action 
  } 
  catch (MemoryError) { 
    croak("Out of memory"); 
  } 
} 

%feature("new", "1") *::copy;

事实上，%exception 和 %newobject 指令实际上只不过是涉及 %feature 的宏：
#define %exception %feature("except") 
#define %newobject %feature("new", "1")

歧义解析和重命名部分中概述的名称匹配规则适用于所有 %feature 指令。事实上 %rename 指令只是%feature 的一种特殊形式。匹配规则意味着功能非常灵活，如果需要，可以精确地应用于特定声明。此外，如果未给出声明名称，则称定义了全局特性。然后将此功能附加到随后的每个声明中。这就是定义全局异常处理程序的方式。例如：
/* 定义一个全局异常处理程序 */ 
%feature("except") { 
  try { 
    $action 
  } 
  ... 
} 

... 一堆声明 ...

在％功能指令可以用不同的语法来使用。以下都是等价的：
%feature("except") Object::method { $action }; 
%feature("except") Object::method %{ $action %}; 
%feature("except") Object::method " $action "; 
%feature("except", "$action") Object::method;

第一个变体中的语法将生成使用的{ } 分隔符，而其他变体则不会。
12.3.1 Feature flags
%feature 指令还以与 typemap 相同的方式接受 XML 样式属性。可以指定任意数量的属性。以下是功能的通用语法：
%feature("name", "value", attribute1="AttributeValue1") symbol;
%feature("name", attribute1="AttributeValue1") symbol {value};
%feature("name", attribute1="AttributeValue1") symbol %{value%};
%feature("name", attribute1="AttributeValue1") symbol "value";

可以使用逗号分隔列表指定多个属性。Java 模块是一个使用 %feature("except") 中的属性的示例。在抛出属性指定的Java类的名称添加到代理方法的throws子句。在以下示例中，MyExceptionClass 是用于添加到 throws 子句的 Java 类的名称。
%feature("except", throws="MyExceptionClass") Object::method { 
  try {
    $action
  } catch (...) {
    ... code to throw a MyExceptionClass Java exception ...
  }
};

可以从 Java 异常处理 部分获得更多详细信息。
12.3.2 功能标志
功能标志用于启用或禁用特定功能。功能标志是 %feature 的一个常见但简单的用法，功能值应该是1启用或 0 禁用该功能。
%feature("featurename") // 启用特性
%feature("featurename", "1") // 启用特性
%feature("featurename", "x") // 启用特性
%feature("featurename", "0 ") // 禁用功能
%feature("featurename", "") // 清除功能

实际上，除零以外的任何值都将启用该功能。请注意，如果完全省略该值，则默认值变为 1，从而启用该功能。通过不指定任何值来清除特征，请参阅清除特征。 创建只读变量部分中描述的 %immutable 指令只是 %feature("immutable") 的宏，可用于演示功能标志：
                                // 默认情况下禁用功能
int red;                        // 可变

%feature("immutable");         // 全局启用
int orange;                    // 不可变

%feature("immutable", "0");    // 全局禁用
int 黄色；                     // 可变

%feature("immutable", "1");   // 另一种形式的 global enable 
int green;                    // 不可变

%feature("immutable", "");    // 清除全局特征
int blue;                     // 可变

请注意，默认情况下禁用功能，必须在全局或通过指定目标声明显式启用。上面用 C 代码穿插了 SWIG 指令。当然，您可以明确地针对特征，因此上述内容也可以重写为：
%feature("immutable", "1") orange;
%feature("immutable", "1") green;
int red;                        // mutable
int orange;                     // immutable
int yellow;                     // mutable
int green;                      // immutable
int blue;                       // mutable

当从 C 头文件解析 C 声明时，上述方法允许将 C 声明与 SWIG 指令分开。上面的逻辑当然可以颠倒并重写为：
%feature("immutable", "1");
%feature("immutable", "0") red;
%feature("immutable", "0") yellow;
%feature("immutable", "0") blue;
int red;                        // mutable
int orange;                     // immutable
int yellow;                     // mutable
int green;                      // immutable
int blue;                       // mutable

正如上面 %immutable 所暗示的，大多数功能标志也可以通过替代语法指定。替代语法只是 swig.swg库文件中的一个宏。下面显示了虚构的 featurename 功能的替代语法：
%featurename // 等效于 %feature("featurename", "1") 即启用功能
%nofeaturename // 等效于 %feature("featurename", "0") 即禁用功能
%clearfeaturename // 等效于 %feature("功能名称", "") 即清除功能

接下来讨论清除特征的概念。
12.3.3 清除特征
一项功能将一直有效，直到它被明确清除。通过提供一个没有值的 %feature 指令来清除一个特性。例如 %feature("name", "")。清除的特征意味着在名称匹配规则中不再使用与任何先前定义的特征完全匹配的任何特征。因此，如果某个功能被清除，则可能意味着将应用另一个名称匹配规则。为了澄清，让我们再次考虑 except 功能（%exception）：
// Define global exception handler
%feature("except") {
  try {
    $action
  } catch (...) {
    croak("Unknown C++ exception");
  }
}

// Define exception handler for all clone methods to log the method calls
%feature("except") *::clone() {
  try {
    logger.info("$action");
    $action
  } catch (...) {
    croak("Unknown C++ exception");
  }
}

... initial set of class declarations with clone methods ...

// clear the previously defined feature
%feature("except", "") *::clone();

... final set of class declarations with clone methods ...

在上述场景中，最初的一组克隆方法将记录来自目标语言的所有方法调用。为最后一组克隆方法清除了此特定功能。但是，这些克隆方法仍将有一个异常处理程序（没有日志记录），因为与它们匹配的下一个最佳功能是全局异常处理程序。
请注意，清除功能并不总是与禁用功能相同。使用 %feature("except", "") *::clone() 清除上述功能与 指定 %feature("except", "0") *::clone() 不同 。前者将禁用克隆方法的功能 - 该功能仍然比全局功能更好。另一方面，如果根本没有定义全局异常处理程序，那么清除该功能将与禁用它相同，因为没有其他功能匹配。
请注意，该特征必须完全匹配才能被任何先前定义的特征清除。例如，以下尝试清除初始功能将不起作用：
%feature("except") clone() { logger.info("$action"); $action } 
%feature("except", "") *::clone();

但这将：
%feature("except") clone() { logger.info("$action"); $action } 
%feature("except", "") clone();

SWIG 提供了用于禁用和清除功能的宏。其中许多可以在 swig.swg 库文件中找到。典型的模式是定义三个宏；一种用于定义功能本身，一种用于禁用该功能，另一种用于清除该功能。下面的三个宏显示了“except”功能的这一点：
#define %exception %feature("except") 
#define %noexception %feature("except", "0") 
#define %clearexception %feature("except", "")

12.3.4 特性和默认参数
SWIG 将具有默认参数的方法视为单独的重载方法，如默认参数部分所述。如果在功能中指定了默认参数，则任何针对具有默认参数的方法的 %feature 都将应用于 SWIG 生成的所有额外重载方法。如果功能中未指定默认参数，则该功能将仅匹配该确切的包装器方法，而不匹配 SWIG 生成的额外重载方法。例如：
%feature("except") hello(int i=0, double d=0.0) { ... } 
void hello(int i=0, double d=0.0);

将该功能应用于所有三种包装方法，即：
void hello(int i, double d);
void hello(int i);
void hello();

如果功能中未指定默认参数：
%feature("except") hello(int i, double d) { ... } 
void hello(int i=0, double d=0.0);

那么该功能将仅适用于此包装器方法：
void hello(int i, double d);

而不是这些包装方法：
void hello(int i);
void hello();

如果使用了 compactdefaultargs，则在特性中指定或不指定默认参数之间的区别不适用，因为只生成一个包装器。
兼容性说明：当使用默认参数包装方法的方法发生变化时，SWIG-1.3.23 中引入了使用或不使用默认参数指定的功能的不同行为。
12.3.5 特征示例
如前所述，%feature 指令的预期用途是作为一种高度灵活的自定义机制，可用于用附加信息注释声明，以供特定目标语言模块使用。另一个例子是在 Python 模块中。您可以使用 %feature 重写代理/影子类代码，如下所示：
%module example
%rename(bar_id) bar(int, double);

// Rewrite bar() to allow some nice overloading

%feature("shadow") Foo::bar(int) %{
def bar(*args):
    if len(args) == 3:
        return apply(examplec.Foo_bar_id, args)
    return apply(examplec.Foo_bar, args)
%}
    
class Foo {
public:
  int bar(int x);
  int bar(int x, double y);
}

特定语言模块的文档中描述了 %feature 用法的更多详细信息。

13 合约
包装 C 库时出现的一个常见问题是保持可靠性和检查错误。事实是，许多 C 程序因不提供错误检查而臭名昭著。不仅如此，当您将应用程序的内部结构公开为库时，通常可以通过提供错误的输入或以非预期的方式使用它而使其崩溃。
本章介绍 SWIG 对软件合同的支持。在 SWIG 的上下文中，合约可以被视为附加到声明的运行时约束。例如，您可以轻松附加参数检查规则、检查函数的输出值等。当脚本违反其中一项规则时，会生成运行时异常，而不是让程序继续执行。
13.1 %contract 指令
使用 %contract 指令将合同添加到声明中。这是一个简单的例子：
%contract sqrt(double x) {
require:
  x >= 0;
ensure:
  sqrt >= 0;
}

...
double sqrt(double);

在这种情况下，一个合约被添加到sqrt() 函数中。在％contract 指令必须有问题的声明之前始终出现。在合同中有两个部分，这两个部分都是可选的。的要求：必须持有被称为函数之前部分指定的条件。通常，这用于检查参数值。在保证：该功能后，必须持有部分指定条件被调用。这通常用于检查返回值或程序状态。在这两种情况下，必须满足的条件必须指定为布尔表达式。
在上面的例子中，我们只是确保 sqrt() 返回一个非负数（如果没有，那么它会以某种方式被破坏）。
一旦指定了合约，它就会修改结果模块的行为。例如：
>>> example.sqrt(2)
1.4142135623730951
>>> example.sqrt(-2)
Traceback (most recent call last):
  File "<stdin>", line 1, in ?
RuntimeError: Contract violation: require: (arg1>=0)
>>>

13.2 %contract 和类
在％contract 指令也可以适用于类的方法和构造函数。例如：
%contract Foo::bar(int x, int y) {
require:
  x > 0;
ensure:
  bar > 0;
}

%contract Foo::Foo(int a) {
require:
  a > 0;
}

class Foo {
public:
  Foo(int);
  int bar(int, int);
};

在这种方式里 ％contract 被应用是与 ％feature 指令完全一样的。因此，您为基类指定的任何 contract 也将附加到继承的方法。例如：
class Spam : public Foo {
public:
  int bar(int, int);    // Gets contract defined for Foo::bar(int, int)
};

除此之外，单独的 contract 也可以应用于基类和派生类。例如：
%contract Foo::bar(int x, int) {
require:
  x > 0;
}

%contract Spam::bar(int, int y) {
require:
  y > 0;
}

class Foo {
public:
  int bar(int, int);   // Gets Foo::bar contract.
};

class Spam : public Foo {
public:
  int bar(int, int);   // Gets Foo::bar and Spam::bar contract
};

当应用多个合同时，“require:”部分中指定的条件使用逻辑与运算组合在一起。换句话说，为基类指定的条件和为派生类指定的条件都必须成立。在上面的例子中，这意味着 Spam::bar 的两个参数都必须是正数。
13.3 常量聚合和 %aggregate_check
考虑一个包含以下代码的接口文件：
#define  UP     1
#define  DOWN   2
#define  RIGHT  3
#define  LEFT   4

void move(SomeObject *, int direction, int distance););

您可能想要做的一件事是对方向参数施加约束，以确保它是少数可接受的值之一。为此，SWIG 提供了一个易于使用的宏 %aggregate_check() ，其工作方式如下：
%aggregate_check(int, check_direction, UP, DOWN, LEFT, RIGHT);

这仅仅定义了形式的效用函数
int check_direction(int x);

这会检查参数 x 以查看它是否是列出的值之一。这个效用函数可以在合约中使用。例如：
%aggregate_check(int, check_direction, UP, DOWN, RIGHT, LEFT);

%contract move(SomeObject *, int direction, in) {
require:
  check_direction(direction);
}

#define  UP     1
#define  DOWN   2
#define  RIGHT  3
#define  LEFT   4

void move(SomeObject *, int direction, int distance);

或者，它可以用于类型映射和其他指令。例如：
%aggregate_check(int, check_direction, UP, DOWN, RIGHT, LEFT);

%typemap(check) int direction {
  if (!check_direction($1)) SWIG_exception(SWIG_ValueError, "Bad direction");
}

#define  UP     1
#define  DOWN   2
#define  RIGHT  3
#define  LEFT   4

void move(SomeObject *, int direction, int distance);

遗憾的是，没有使用枚举值执行类似检查的自动方法。也许在未来的版本中。
13.4 注释
合约支持由 Songyan (Tiger) Feng 实现，首次出现在 SWIG-1.3.20。

14 可变长度参数
（又名，“恐怖。恐怖。”）
本章描述了包装带有可变数量参数的函数的问题。例如，为 C printf()系列函数生成包装器。
这个主题已经足够高级了，值得单独写一章。事实上，对可变参数的支持是一个经常被要求的特性，它最初是在 SWIG-1.3.12 中添加的。大多数其他包装器生成工具都明智地选择避免这个问题。
14.1 简介
某些 C 和 C++ 程序可能包含接受可变数量参数的函数。例如，大多数程序员都熟悉 C 库中的函数，例如：
int printf(const char *fmt, ...)
int fprintf(FILE *, const char *fmt, ...);
int sprintf(char *s, const char *fmt, ...);

尽管在脚本语言中包装这些特定的 C 库函数可能没有什么实际目的（这有什么意义？），但库可能包含自己的一组基于类似 API 的特殊函数。例如：
int traceprintf(const char *fmt, ...);

在这种情况下，您可能希望从目标语言获得某种访问权限。
在描述 SWIG 实现之前，重要的是讨论您在实际程序中可能会遇到的可变参数的常见用法。显然，有如图所示的 printf() 风格的输出函数。与此密切相关的是 scanf() 样式的输入函数，它接受格式字符串和放置返回值的指针列表。但是，有时也使用可变长度参数来编写接受以 NULL 结尾的指针列表的函数。一个很好的例子是这样的函数：
int execlp(const char *path, const char *arg1, ...);
...

/* 例子 */
execlp("ls", "ls", "-l", NULL);

此外，varargs 有时用于伪造旧 C 库中的默认参数。例如，低级 open() 系统调用通常被声明为可变参数函数，以便它接受两个或三个参数：
int open(const char *path, int oflag, ...);
...

/* 例子 */
f = open("foo", O_RDONLY);
g = open("bar", O_WRONLY | O_CREAT, 0644);

最后，要实现可变参数函数，请记住您必须使用 <stdarg.h> 中定义的 C 库函数。例如：
List make_list(const char *s, ...) {
  va_list ap;
  List    x;
  ...
  va_start(ap, s);
  while (s) {
    x.append(s);
    s = va_arg(ap, const char *);
  }
  va_end(ap);
  return x;
}

14.2 问题
为可变长度参数函数生成包装器提出了许多特殊挑战。尽管 C 为实现接收可变长度参数的函数提供了支持，但没有任何函数可以反其道而行之。具体来说，您不能编写一个函数来动态创建参数列表并代表您调用 varargs 函数。
尽管可以编写接受特殊类型 va_list 的函数，但这是完全不同的。您不能采用 va_list 结构并将其代替可变长度参数传递给另一个可变参数函数。它只是不起作用。
这不起作用的原因与函数调用的编译方式有关。例如，假设您的程序有一个这样的函数调用：
printf("Hello %s. Your number is %d\n", name, num);

当编译器查看此内容时，它知道您正在使用正好三个参数调用 printf()。此外，它知道参数的数量以及它们的类型和大小 在程序执行期间永远不会改变。因此，这变成了设置三参数堆栈帧的机器代码，然后调用 printf()。
相反，假设您尝试使用如下代码对 printf() 进行某种包装：
int wrap_printf(const char *fmt, ...) {
  va_list ap;
  va_start(ap, fmt);
  ...
  printf(fmt, ap);
  ...
  va_end(ap);
};

尽管此代码可能会编译，但它不会按照您的预期运行。这是因为对 printf() 的调用被编译为仅涉及两个参数的过程调用。但是，如果您的意图是将任意数量的参数传递给真正的 printf()，则调用堆栈的两个参数配置显然是完全错误的。不用说，这行不通。
不幸的是，刚刚描述的情况正是包装器生成工具面临的问题。通常，传递参数的数量直到运行时才会知道。更糟糕的是，直到运行时您才会知道参数的类型和大小。不用说，没有明显的方法可以让 C 编译器为涉及未知数量的未知类型参数的函数调用生成代码。
从理论上讲，它可以是编写一个包装，做正确的事。但是，这涉及了解目标平台和语言的底层 ABI，以及编写在进行过程调用之前手动构建调用堆栈的特殊用途代码。不幸的是，这两项任务都需要使用内联汇编代码。显然，这是您宁愿避免的解决方案。
考虑到这一点，SWIG 为可变参数包装问题提供了许多解决方案。这些解决方案中的大多数都是折衷方案，它们提供有限的可变参数支持，而不必求助于汇编语言。但是，如果您愿意动手，SWIG 也可以支持真正的可变参数包装（使用堆栈帧操作）。继续阅读。
14.3 默认可变参数支持
当可变长度参数出现在接口中时，默认行为是完全删除可变参数列表，用单个 NULL 指针替换它们。例如，如果你有这个功能，
void traceprintf(const char *fmt, ...);

它将被包装，就好像它已声明如下：
void traceprintf(const char *fmt);

当在包装器内部调用该函数时，它的调用方式如下：
traceprintf(arg1, NULL);

可以说，这种方法似乎打败了可变长度参数的全部意义。然而，这实际上为许多简单类型的可变参数函数提供了足够的支持，仍然有用，但是它确实有一个警告。例如，您可以像这样（在 Python 中）进行函数调用：
>>> traceprintf("Hello World")
>>> traceprintf("Hello %s. Your number is %d\n" % (name, num))
>>> traceprintf("Your result is 90%%.")

注意字符串格式化是如何在 Python 而不是 C 中完成的。需要注意的是，传递的字符串必须在 C 中安全使用。例如，如果 name 包含一个“%”，它应该被双重转义以避免不可预测的行为：
>>> traceprintf("Your result is 90%.\n")  # unpredictable behaviour
>>> traceprintf("Your result is 90%%.\n") # good

请继续阅读以获取更多解决方案。
14.4 使用 %varargs 替换参数
不是删除可变长度参数，另一种方法是用一组合适的参数替换(...)。SWIG 提供了一个特殊的 %varargs 指令，可用于执行此操作。例如，
%varargs(int mode = 0) open；
...
int open(const char *path, int oflags, ...);

相当于：
int open(const char *path, int oflags, int mode = 0);

在这种情况下，%varargs 只是提供有关可能传递给函数的额外参数的更具体信息。如果 varargs 函数的参数是统一类型， %varargs 也可以接受数字参数计数，如下所示：
%varargs(3, char *str = NULL) execlp;
...
int execlp(const char *path, const char *arg, ...);

并被有效地视为：
int execlp(const char *path, const char *arg, 
           char *str1 = NULL, 
           char *str2 = NULL, 
           char *str3 = NULL);
这会将 execlp() 包装为一个最多接受 3 个可选参数的函数。根据应用的不同，这对于实际用途来说可能绰绰有余。
可以通过 compactdefaultargs 功能更改默认参数的处理。如果使用此功能，例如
%feature("compactdefaultargs") execlp;
%varargs(3, char *str = NULL) execlp;
...
int execlp(const char *path, const char *arg, ...);

来自未提供最大参数数量的目标语言的调用，例如 execlp("a", "b", "c") 将生成包含缺失默认值的 C 代码，即 execlp(" a", "b", "c", NULL, NULL)。如果 未使用 compactdefaultargs，则生成的代码将为execlp("a", "b", "c") 。前者对于帮助提供终止参数列表的哨兵很有用。但是，这并不能保证，例如当用户为所有参数传递一个非 NULL 值时。使用 compactdefaultargs 时，可以保证 NULL 哨兵通过， 最后一个参数。例如，
%feature("compactdefaultargs") execlp;
%varargs(3, char *str = NULL) execlp;
%typemap(in, numinputs=0) char *str3 ""
...
int execlp(const char *path, const char *arg, ...);

请注意，str3 是最后一个参数的名称，因为我们使用了 %varargs和 3。现在 execlp("a", "b", "c", "d", "e") 将导致错误的为参数 1 传递了太多参数，因为现在只能传递 2 个额外的 'str' 参数，而第 3 个参数始终使用指定的默认NULL。
在额外参数的类型是统一的并且参数的最大数量已知的情况下，参数替换是最合适的。在处理接受混合参数类型（如printf() ）的函数时，参数替换不是很有用。为这些函数提供通用包装会带来一些特殊问题（稍后会介绍）。
14.5 可变参数和类型映射
可变长度参数可用于类型映射规范。例如：
%typemap(in) (...) {
    // 获取可变长度参数（以某种方式）
    ...
}

%typemap(in) (const char *fmt, ...) {
    // 多参数类型映射
}

然而，这立即引发了一个问题，即实际使用什么“类型”来表示(...)。由于缺乏更好的选择，(...)的类型设置为 void *。由于无法将参数动态传递给可变参数函数（如前所述），因此 void * 参数值旨在用作存储有关额外参数（如果有）的某种信息的占位符。此外，SWIG 的默认行为是将 void * 值作为参数传递给函数。因此，如果需要，您可以使用指针来保存有效的参数值。
为了说明这一点，这里是 在 Python 中包装 printf() 的一个更安全的版本：
%typemap(in) (const char *fmt, ...) {
    $1 = "%s"; /* 将格式字符串固定为 %s */
    $2 = (void *) PyString_AsString($input); /* 获取字符串参数 */
};
...
int printf(const char *fmt, ...);

在此示例中，格式字符串被隐式设置为"%s" 。这可以防止程序将伪造的格式字符串传递给扩展。然后，传递的输入对象被解码并放置在为 (...) 参数定义的 void * 参数中。当进行实际的函数调用时，底层包装器代码将大致如下所示：
wrap_printf() {
  char *arg1;
  void *arg2;
  int   result;

  arg1 = "%s";
  arg2 = (void *) PyString_AsString(arg2obj);
  ...
  result = printf(arg1, arg2);
  ...
}

请注意这两个参数是如何传递给函数的，它会执行您期望的操作。
下一个示例说明了一种更高级的可变参数类型映射。免责声明：这需要目标语言模块中的特殊支持，目前不能保证与所有 SWIG 模块一起使用。它还开始更普遍地说明支持可变参数的一些更基本的问题。
如果为任何形式的(...)定义了类型映射，许多 SWIG 模块将生成接受可变数量参数作为输入的包装器，并使这些参数以某种形式可用。这的确切细节取决于所使用的语言模块（有关更多详细信息，请参阅相应的章节）。但是，假设您想为 前面显示的 execlp() 函数创建一个 Python 包装器。要使用 typemap 而不是使用 %varargs 来做到这一点，您可能首先编写这样的 typemap：
%typemap(in) (...)(char *vargs[10]) {
  int i;
  int argc;
  for (i = 0; i < 10; i++) vargs[i] = 0;
  argc = PyTuple_Size(varargs);
  if (argc > 10) {
    PyErr_SetString(PyExc_ValueError, "Too many arguments");
    SWIG_fail;
  }
  for (i = 0; i < argc; i++) {
    PyObject *pyobj = PyTuple_GetItem(varargs, i);
    char *str = 0;
%#if PY_VERSION_HEX>=0x03000000
    PyObject *pystr;
    if (!PyUnicode_Check(pyobj)) {
      PyErr_SetString(PyExc_ValueError, "Expected a string");
      SWIG_fail;
    }
    pystr = PyUnicode_AsUTF8String(pyobj);
    str = strdup(PyBytes_AsString(pystr));
    Py_XDECREF(pystr);
%#else  
    if (!PyString_Check(pyobj)) {
      PyErr_SetString(PyExc_ValueError, "Expected a string");
      SWIG_fail;
    }
    str = PyString_AsString(pyobj);
%#endif
    vargs[i] = str;
  }
  $1 = (void *)vargs;
}

%typemap(freearg) (...) {
%#if PY_VERSION_HEX>=0x03000000
  int i;
  for (i = 0; i < 10; i++) {
    free(vargs$argnum[i]);
  }
%#endif
}

在“in”类型映射中，特殊变量 varargs 是一个元组，包含所有传递的额外参数（这是 Python 模块特有的）。然后类型映射将其分开并将值粘贴到字符串 args 数组中。然后，将数组分配给 $1（回想一下，这是 对应于(...)的 void * 变量）。然而，这个分配只是图片的一半——显然仅凭这一点还不足以使函数工作。'freearg' 类型映射清除在 'in' 类型映射中分配的内存；生成此代码以在 execlp %feature 指令之后调用，如下所示：
%feature("action") execlp {
  char **vargs = (char **) arg3;
  result = execlp(arg1, arg2, vargs[0], vargs[1], vargs[2], vargs[3], vargs[4],
                  vargs[5], vargs[6], vargs[7], vargs[8], vargs[9], NULL);
}

int execlp(const char *path, const char *arg, ...);

这将修补所有内容并创建一个或多或少有效的功能。但是，除非您确定他们已经喝了几杯咖啡，否则不要尝试向您的同事解释这一点。如果你真的想提升你的大师地位并增加你的工作保障，请继续下一部分。
14.6 使用 libffi 包装的可变参数
前面的所有示例都依赖于 SWIG 的可移植特性，这些特性不依赖于任何低级机器级细节。在许多方面，他们都通过将可变参数函数重铸为一些具有固定数量的已知类型参数的较弱变体，从而避免了可变长度参数的真正问题。在许多情况下，这工作得很好。但是，如果您想要比这更通用，则需要带出一些更大的枪支。
一种方法是使用特殊用途的库，例如 libffi ( http://www.sourceware.org/libffi/ )。libffi 是一个库，允许您以相对独立于平台的方式动态构建调用堆栈和调用过程。有关库的详细信息可以在 libffi 发行版中找到，此处不再赘述。
为了说明 libffi 的使用，假设您真的 想为 execlp() 创建一个接受任意 数量参数的包装器。为此，您可以对前面的示例进行一些调整。例如：
/* Take an arbitrary number of extra arguments and place into an array
   of strings */

%typemap(in) (...) {
  char **argv;
  int    argc;
  int    i;

  argc = PyTuple_Size(varargs);
  argv = (char **) malloc(sizeof(char *)*(argc+1));
  for (i = 0; i < argc; i++) {
    PyObject *o = PyTuple_GetItem(varargs, i);
    if (!PyString_Check(o)) {
      free(argv);
      PyErr_SetString(PyExc_ValueError, "Expected a string");
      SWIG_fail;
    }
    argv[i] = PyString_AsString(o);
  }
  argv[i] = NULL;
  $1 = (void *) argv;
}

/* Rewrite the function call, using libffi */    

%feature("action") execlp {
  int       i, vc;
  ffi_cif   cif;
  ffi_type  **types;
  void      **values;
  char      **args;

  vc = PyTuple_Size(varargs);
  types  = (ffi_type **) malloc((vc+3)*sizeof(ffi_type *));
  values = (void **) malloc((vc+3)*sizeof(void *));
  args   = (char **) arg3;

  /* Set up path parameter */
  types[0] = &ffi_type_pointer;
  values[0] = &arg1;
  
  /* Set up first argument */
  types[1] = &ffi_type_pointer;
  values[1] = &arg2;

  /* Set up rest of parameters */
  for (i = 0; i <= vc; i++) {
    types[2+i] = &ffi_type_pointer;
    values[2+i] = &args[i];
  }
  if (ffi_prep_cif(&cif, FFI_DEFAULT_ABI, vc+3,
                   &ffi_type_uint, types) == FFI_OK) {
    ffi_call(&cif, (void (*)()) execlp, &result, values);
  } else {
    free(types);
    free(values);
    free(arg3);
    PyErr_SetString(PyExc_RuntimeError, "Whoa!!!!!");
    SWIG_fail;
  }
  free(types);
  free(values);
  free(arg3);
}

/* Declare the function. Whew! */
int execlp(const char *path, const char *arg1, ...);

看着这个例子，您可能会开始怀疑 SWIG 是否让生活变得更轻松了。考虑到所涉及的代码量，您可能还想知道为什么不直接编写一个手工制作的包装器！要么是那个，要么你想知道“我到底为什么要尝试包装这个可变参数函数？！？” 显然，这些是你必须自己回答的问题。
作为 libffi 的一个更极端的例子，这里是一些试图包装printf() 的代码，
/* A wrapper for printf() using libffi */

%{
/* Structure for holding passed arguments after conversion */
  typedef struct {
    int type;
    union {
      int    ivalue;
      double dvalue;
      void   *pvalue;
    } val;
  } vtype;
  enum { VT_INT, VT_DOUBLE, VT_POINTER };
%}

%typemap(in) (const char *fmt, ...) {
  vtype *argv;
  int    argc;
  int    i;

  /* Format string */
  $1 = PyString_AsString($input);

  /* Variable length arguments */
  argc = PyTuple_Size(varargs);
  argv = (vtype *) malloc(argc*sizeof(vtype));
  for (i = 0; i < argc; i++) {
    PyObject *o = PyTuple_GetItem(varargs, i);
    if (PyInt_Check(o)) {
      argv[i].type = VT_INT;
      argv[i].val.ivalue = PyInt_AsLong(o);
    } else if (PyFloat_Check(o)) {
      argv[i].type = VT_DOUBLE;
      argv[i].val.dvalue = PyFloat_AsDouble(o);
    } else if (PyString_Check(o)) {
      argv[i].type = VT_POINTER;
      argv[i].val.pvalue = (void *) PyString_AsString(o);
    } else {
      free(argv);
      PyErr_SetString(PyExc_ValueError, "Unsupported argument type");
      return NULL;
    }
  }
  $2 = (void *) argv;
}

/* Rewrite the function call using libffi */    
%feature("action") printf {
  int       i, vc;
  ffi_cif   cif;
  ffi_type  **types;
  void      **values;
  vtype     *args;

  vc = PyTuple_Size(varargs);
  types  = (ffi_type **) malloc((vc+1)*sizeof(ffi_type *));
  values = (void **) malloc((vc+1)*sizeof(void *));
  args   = (vtype *) arg2;

  /* Set up fmt parameter */
  types[0] = &ffi_type_pointer;
  values[0] = &arg1;

  /* Set up rest of parameters */
  for (i = 0; i < vc; i++) {
    switch(args[i].type) {
    case VT_INT:
      types[1+i] = &ffi_type_uint;
      values[1+i] = &args[i].val.ivalue;
      break;
    case VT_DOUBLE:
      types[1+i] = &ffi_type_double;
      values[1+i] = &args[i].val.dvalue;
      break;
    case VT_POINTER:
      types[1+i] = &ffi_type_pointer;
      values[1+i] = &args[i].val.pvalue;
      break;
    default:
      abort();    /* Whoa! We're seriously hosed */
      break;   
    }
  }
  if (ffi_prep_cif(&cif, FFI_DEFAULT_ABI, vc+1,
                   &ffi_type_uint, types) == FFI_OK) {
    ffi_call(&cif, (void (*)()) printf, &result, values);
  } else {
    free(types);
    free(values);
    free(args);
    PyErr_SetString(PyExc_RuntimeError, "Whoa!!!!!");
    SWIG_fail;
  }
  free(types);
  free(values);
  free(args);
}

/* The function */
int printf(const char *fmt, ...);

令您惊讶的是，如果您尝试一下，它甚至似乎有效：
>>> import example
>>> example.printf("Grade: %s   %d/60 = %0.2f%%\n", "Dave", 47, 47.0*100/60)
Grade: Dave   47/60 = 78.33%
>>>

当然，仍有一些限制需要考虑：
>>> example.printf("la de da de da %s", 42)
Segmentation fault (core dumped)

并且，在此说明中，我们将进一步探索 libffi 作为练习留给读者。尽管以 Python 为例，本节中的大多数技术都可以通过一些工作来外推到其他语言模块。您需要知道的唯一细节是如何在每种目标语言中访问额外的参数。例如，在 Python 模块中，我们使用特殊的varargs 变量来获取这些参数。诸如 Tcl8 和 Perl5 之类的模块只是为第一个额外参数提供一个参数编号。这可用于索引传递参数的数组以获取值。有关详细信息，请参阅每个语言模块的章节。
14.7 va_list 的包装
与可变长度参数包装密切相关，您可能会遇到接受va_list类型参数的函数。例如：
int vprintf(const char *fmt, va_list ap);

据我们所知，没有明显的方法可以用 SWIG 包装这些函数。这是因为没有记录的方法来组装正确的 va_list 结构（没有 C 库函数可以做到这一点，并且 va_list 的内容是不透明的）。不仅如此， va_list结构的内容与底层调用堆栈紧密相关。目前尚不清楚导出 va_list 是否有任何用处或它是否会起作用。
解决方法可以通过编写一个简单的可变参数 C 包装器，然后使用本章前面讨论的可变参数技术来实现。下面是重命名的 vprintf 的一个简单包装器，以便它仍然可以从您的目标语言中称为 vprintf。示例中使用的 %vararg s 将函数限制为采用一个字符串参数。
%{
int vprintf(const char *fmt, va_list ap);
%}

%varargs(const char *) my_vprintf;
%rename(vprintf) my_vprintf;

%inline %{
int my_vprintf(const char *fmt, ...) {
  va_list ap;
  int result;

  va_start(ap, fmt);
  result = vprintf(fmt, ap);
  va_end(ap);
  return result;
}
%}

14.8 C++ 问题
包装接受可变数量参数的 C++ 成员函数提出了许多挑战。到目前为止，处理这个问题的最简单方法是使用 %varargs 指令。这是可移植的，它完全支持类似于 %rename 指令的类。例如：
%varargs (10, char * = NULL) Foo::bar;

class Foo {
public:
  virtual void bar(char *arg, ...);   // gets varargs above
};

class Spam: public Foo {
public:
  virtual void bar(char *arg, ...);   // gets varargs above
};

%varargs 也适用于构造函数、运算符和任何其他接受可变参数的 C++ 编程构造。
做任何比这更高级的事情很可能会带来严重的痛苦。为了使用像 libffi 这样的库，您需要了解 C++ ABI 的底层调用约定和详细信息。例如，详细说明这是如何传递给成员函数以及可能用于传递附加信息的任何隐藏参数。这些细节是特定于实现的，可能因编译器甚至同一编译器的不同版本而异。另外，请注意，如果成员函数是虚方法，则调用成员函数会更加复杂。在这种情况下，调用可能需要查找表来获取正确的函数地址（尽管您可以通过将绑定指针转换为指向函数的指针来获取地址，如 C++ ARM 部分 18.3.4 中所述）。
如果您决定更改底层操作代码，请注意 SWIG 始终将this指针放在arg1 中。其他参数放在arg2、arg3等中。例如：
%feature("action") Foo::bar {
  ...
  result = arg1->bar(arg2, arg3, etc.);
  ...
}

考虑到有可能让自己陷入困境，与创建可变参数 C++ 成员函数的完全通用包装器相比，重新考虑您的设计或使用辅助函数提供替代接口可能更容易。
14.9 讨论
本章提供了许多可用于解决可变长度参数包装问题的技术。如果您关心可移植性和易用性，%varargs指令可能是解决问题的最简单方法。但是，使用类型映射，可以进行一些非常高级的包装。
一个讨论点涉及上一节中 libffi 示例的结构。查看该代码，完全不清楚这是解决问题的最简单方法。然而，解决方案有许多微妙的方面需要考虑——主要是关于问题的分解方式。首先，该示例的结构方式试图在特定于包装器的信息和函数本身的声明之间保持分离。这里的想法是你可以像这样构建你的界面：
%typemap(const char *fmt, ...) {
  ...
}
%feature("action") traceprintf {
  ...
}

/* 在其中包含一些带有 traceprintf 的头文件 */
%include "someheader.h"

其次，仔细审查会发现涉及 (...)的类型映射与 libffi 库没有任何关系。事实上，就函数实际调用的方式而言，它们是通用的。这种解耦意味着考虑其他库替代方案来进行函数调用会容易得多。例如，如果某个平台不支持 libffi，您可能可以使用其他东西来代替。您可以使用条件编译来控制这一点：
#ifdef USE_LIBFFI
%feature("action") printf {
  ...
}
#endif
#ifdef USE_OTHERFFI
%feature("action") printf {
...
}
#endif

最后，即使您可能倾向于只为可变参数函数编写一个手写的包装器，但上一节中使用的技术具有与 SWIG 的所有其他功能（例如异常处理）兼容的优点。
最后，一些 C 程序员似乎假设可变长度参数函数的包装是一个容易解决的问题。然而，本节有希望消除其中的一些神话。在所有条件相同的情况下，如果可以，最好避免可变长度参数。如果您无法避免它们，请先考虑一些简单的解决方案。如果您不能接受一个简单的解决方案，请谨慎行事。至少，请确保您仔细阅读 Kernighan 和 Ritchie 中的“A7.3.2 函数调用”部分，并确保您完全理解用于可变参数的参数传递约定。此外，请注意这将引入的平台依赖性和可靠性问题。祝你好运。

15 警告信息
15.1 简介
在编译期间，SWIG 可能会生成各种警告消息。例如：
example.i:16: Warning 501: Overloaded declaration ignored.  bar(double)
example.i:15: Warning 501: Previous declaration is bar(int)

通常，警告消息指示输入的非致命问题，其中生成的包装器代码可能会编译，但它可能不会像您期望的那样工作。
15.2 警告消息抑制
所有警告消息都有一个数字代码，显示在警告消息本身中。为了禁止打印警告消息，可以使用多种技术。首先，您可以使用 -w命令行选项运行 SWIG 。例如：
% swig -python -w501 example.i
% swig -python -w501,505,401 example.i

或者，可以通过在输入文件中插入一个特殊的预处理器编译指示来抑制警告：
%module example
#pragma SWIG nowarn=501
#pragma SWIG nowarn=501,505,401

最后，可以使用%warnfilter指令在逐个声明的基础上禁用代码生成警告。例如：
%module example
%warnfilter(501) foo;
...
int foo(int);
int foo(double);              // Silently ignored.

在％warnfilter指令有相同的语义其他的声明修饰语喜欢％的重命名，％无视和 ％的功能，请参阅％的功能指令节。例如，如果您想取消对类层次结构中方法的警告，您可以这样做：
%warnfilter(501) Object::foo;
class Object {
public:
  int foo(int);
  int foo(double);      // Silently ignored
  ...
};

class Derived : public Object {
public:
  int foo(int);
  int foo(double);      // Silently ignored
  ...
};

可以通过提供类名来抑制整个类的警告。例如：
%warnfilter(501) Object;

class Object {
public:
  ...                      // All 501 warnings ignored in class
};

没有选项可以取消所有 SWIG 警告消息。警告消息的存在是有原因的——告诉您界面中的某些内容可能已损坏。忽略警告消息后果自负。
15.3 启用额外警告
某些警告消息在默认情况下处于禁用状态，生成仅用于提供额外的诊断。可以使用 -Wextra 选项打开这些警告。例如：
% swig -Wextra -python example.i

要有选择地打开额外的警告消息，您可以使用上一节中的指令和选项——只需在所有警告编号上添加一个“+”即可。例如：
% swig -w+309,+452 example.i

或在您的界面文件中使用
#pragma SWIG nowarn=+309,+452

要么
%warnfilter(+309,+452) foo;

注意：使用 %warnfilter 选择性启用警告 会覆盖您可能使用 -w 或 #pragma 进行的任何全局设置。
您当然也可以启用所有警告并禁止选择一些警告，例如：
% swig -Wextra -w309,452 example.i

右边的警告优先于左边的警告，所以在上面的例子中 -Wextra 添加了许多警告，包括 452，但是 -w309,452 覆盖了这个，所以 452 被抑制。
如果您希望显示所有警告，而不管使用的警告过滤器如何，请使用 -Wall 选项。该 -Wall 选项还打开了额外的警告，-Wextra 增加，但是，它是 subtely 不同。使用 -Wall 时，它还会禁用所有其他警告过滤器，即在 %warnfilter、#pragma SWIG nowarn 或 -w选项中抑制或添加的任何警告。
15.4 发出警告信息
可以使用许多指令从接口文件发出警告消息。在％警告指令是最简单的：
%warn "900:This is your last warning!"

所有警告消息都可选地以要使用的警告编号为前缀。如果您自己生成警告，请确保不要使用本节末尾的表格中定义的数字。
在 ％ignorewarn 指令相同 ％ignore ，除了它发出每当匹配的声明中警告消息。例如：
%ignorewarn("362:operator= ignored") operator=;

警告消息可以使用类型映射声明的 警告属性与类型映射相关联。例如：
%typemap(in, warning="901:You are really going to regret this usage of $1_type $1_name") blah * {
  ...
}

在这种情况下，每当实际使用类型映射时都会打印警告消息，并且将适当扩展 特殊变量，例如：
example.i:23: Warning 901: You are really going to regret this usage of blah * self
example.i:24: Warning 901: You are really going to regret this usage of blah * stuff

15.5 符号
与 SWIG 一起安装的 swigwarn.swg 文件包含也可用于 %warnfilter 和 #pragma SWIG nowarn 的符号常量。例如，此文件包含以下行：
%define SWIGWARN_TYPE_UNDEFINED_CLASS 401 %enddef

所以可以使用 SWIGWARN_TYPE_UNDEFINED_CLASS 代替 401，例如：
#pragma SWIG nowarn=SWIGWARN_TYPE_UNDEFINED_CLASS

要么
%warnfilter(SWIGWARN_TYPE_UNDEFINED_CLASS) Foo;

15.6 评论
抑制警告消息的能力实际上只提供给高级用户，不建议在正常使用中使用。建议您修改您的界面以尽可能修复警告突出显示的问题，而不是抑制警告。
某些类型的 SWIG 问题是错误。这些通常是由于解析错误（错误的语法）或没有明显恢复的语义问题引起的。没有抑制错误消息的机制。
15.7 作为错误的警告
可以使用 -Werror 命令行选项将警告作为错误处理。如果遇到警告，这将导致 SWIG 以不成功的退出代码退出。
15.8 消息输出格式
可以选择警告和错误的输出格式以与您喜欢的 IDE/编辑器集成。编辑器和 IDE 通常可以解析错误消息，如果采用适当的格式，将很容易将您直接带到错误的源头。默认情况下使用标准格式，但在默认情况下使用 Microsoft 格式的 Windows 上除外。这些可以使用命令行选项覆盖，例如：
$ swig -python -Fstandard example.i
example.i:4: Syntax error in input(1).
$ swig -python -Fmicrosoft example.i
example.i(4) : Syntax error in input(1).

15.9 警告编号参考
15.9.1 过时的特性 (100-199)
- 101. 不推荐使用%extern指令。
- 102. 不推荐使用%val指令。
- 103. 不推荐使用%out指令。
- 104. 不推荐使用%disabledoc指令。
- 105. 不推荐使用%enabledoc指令。
- 106. 不推荐使用%doconly指令。
- 107. 不推荐使用%style指令。
- 108. 弃用%localstyle指令。
- 109. 弃用%title指令。
- 110. 不推荐使用%section指令。
- 111. 不推荐使用%subsection指令。
- 112. 不推荐使用%subsubsection指令。
- 113. 不推荐使用%addmethods指令。
- 114. 弃用%readonly指令。
- 115. 不推荐使用%readwrite指令。
- 116. 不推荐使用%except指令。
- 117. 不推荐使用%new指令。
- 118. 弃用%typemap(except)。
- 119. 弃用%typemap(ignore)。
- 120. 不推荐使用的命令行选项（-runtime、-noruntime）。
- 121. 不推荐使用%name指令。
- 126. 'nestedworkaround' 功能已被弃用。
15.9.2 预处理器 (200-299)
- 201. 无法找到文件名。
- 202. 无法计算表达式expr。
- 203. includeall 和importall 都定义：使用includeall。
- 204. CPP #warning，“警告”。
- 205. CPP #error，“错误”。
- 206. #指令后出现意外标记。
15.9.3 C/C++ 解析器 (300-399)
- 301. 使用了class关键字，但不在 C++ 模式下。
- 302. 标识符“名称”重新定义（忽略）。
- 303.为未声明的类“名称 ”定义了%extend。
- 304. 不支持的常量值（忽略）。
- 305. 错误的常量值（忽略）。
- 306.在此上下文中，“标识符”是私有的。
- 307. 不能设置默认参数值（忽略）
- 308.此处不允许命名空间别名“ name ”。假设 ' name '
- 309.【私| protected] 继承被忽略。
- 310. 模板 ' name ' 已经被包装为 ' name '（被忽略）
- 312. 当前不支持（忽略）未命名的嵌套类。
- 313. Unrecognized extern type " name " (ignored)。
- 314. ' identifier ' 是一个lang关键字。
- 315. 对“标识符”一无所知。
- 316. 重复 %module 指令。
- 317. 非模板' name '的特化。
- 318实例化的模板“名”不明确，实例TEMPL使用实例TEMPL 忽略。
- 319. 没有为基类名称提供访问说明符 （忽略）。
- 320. 显式模板实例化被忽略。
- 321.标识符与内置名称冲突。
- 322. ' name ' 的冗余重新声明。
- 323. ' name ' 的递归范围继承。
- 324. 不支持命名嵌套模板实例化。就好像没有给 %template() 命名一样进行处理。
- 325.当前不支持嵌套种类（ 忽略名称）。
- 326. 不推荐使用 %extend name - 应该使用种类名称“ name ”而不是 typedef 名称“ name ”。
- 350. operator new 被忽略。
- 351. 操作符删除被忽略。
- 352. operator+ 被忽略。
- 353. operator- 被忽略。
- 354. operator* 被忽略。
- 355. 运算符/被忽略。
- 356. 运算符 % 被忽略。
- 357. operator^ 被忽略。
- 358. operator& 被忽略。
- 359.算子| 忽略。
- 360.operator~被忽略。
- 361.算！忽略。
- 362. operator= 被忽略。
- 363.运算符<被忽略。
- 364.运营商>忽略。
- 365. operator+= 被忽略。
- 366. operator-= 被忽略。
- 367.运算符*=被忽略。
- 368. operator/= 被忽略。
- 369. 运算符 %= 被忽略。
- 370. 运算符^= 被忽略。
- 371. operator&= 被忽略。
- 372. 运算符|= 被忽略。
- 373.运算符<<被忽略。
- 374. 运营商>>忽略。
- 375. 运算符<<= 被忽略。
- 376. 运算符>>= 被忽略。
- 377. operator== 被忽略。
- 378. operator!= 被忽略。
- 379. operator<= 被忽略。
- 380. operator>= 被忽略。
- 381.运算符&&被忽略。
- 382.运算符|| 忽略。
- 383. operator++ 被忽略。
- 384. 运算符——被忽略。
- 385.算子，无视。
- 386. 运算符-<* 被忽略。
- 387. 运算符-< 被忽略。
- 388. operator() 被忽略。
- 389. 运算符 [] 被忽略。
- 390. operator+ 被忽略（一元）。
- 391. operator- 忽略（一元）。
- 392. operator* 被忽略（一元）。
- 393. operator& 被忽略（一元）。
- 394. 运算符 new[] 被忽略。
- 395. 操作符 delete[] 被忽略。
15.9.4 类型和类型映射 (400-499)
- 401. 对类“名称”一无所知。忽略。
- 402. 基类“名称”不完整。
- 403. 类 'name' 可能是抽象的。
- 450. 不推荐使用的类型映射功能 ($source/$target)。
- 451. 设置 const char * 变量可能会泄漏内存。
- 452.保留
- 453.不能申请（模式）。没有定义类型映射。
- 460. 无法使用类型类型作为函数参数。
- 461. 无法在函数名中使用返回类型类型 。
- 462. 无法设置类型为type 的变量。
- 463. 无法读取 type类型的变量。
- 464. 不支持的常量值。
- 465. 无法处理类型type。
- 466. 不支持的变量类型type。
- 467.不支持重载声明（不完整的类型检查规则 - ' type ' 的类型检查类型映射中没有优先级）
- 468.没有“扔”类型映射为异常类型定义的类型
- 469. 没有或不正确的为type定义的directorin typemap
- 470. 线程/可重入不安全包装，请考虑按值返回。
- 471. 无法在director 方法中使用返回类型类型
- 474.方法方法在出类型映射为以下忽略的最佳属性的使用不能被用来产生最佳的代码：代码
- 475.由于 out 类型映射中的最佳属性使用，可能会生成对方法的多次调用。
- 476. 使用 std::initializer_list 初始化。
- 477. 没有为类型定义的directorthrows typemap
15.9.5 代码生成 (500-599)
- 501. 忽略重载声明。东方电气。先前的声明是decl。
- 502. 重载的构造函数被忽略。东方电气。先前的声明是decl。
- 503. 不能包装“标识符”，除非重命名为有效标识符。
- 504. 函数名必须有返回类型。忽略。
- 505. 丢弃可变长度参数。
- 506. 不能在启用关键字参数的情况下包装可变参数。
- 507.不支持（忽略）添加本地函数名称。
- 508. ' name ' 的声明隐藏了可通过 operator->() 访问的声明，' declaration ' 的先前声明。
- 509. 重载的方法声明被有效地忽略，因为它被声明遮蔽了。
- 510. 友元函数 ' name ' 被忽略。
- 511. 不能对重载函数使用关键字参数。
- 512. 重载方法声明被忽略，使用非常量方法声明代替。
- 513. 无法为未命名的结构/类生成包装器。
- 514.
- 515.
- 516. 重载方法声明被忽略，使用 声明代替。
- 517.
- 518. 可移植性警告：在不区分大小写的文件系统（如 Windows 的 FAT32 和 NTFS）上，文件file1将被 file2覆盖，除非类/模块名称被重命名。
- 519. %template() 不包含名称。忽略的模板方法： 声明
- 520.基材/派生类“ classname1 ”的“ classname2 ”未类似地标记为智能指针。
- 521. 非法析构函数名称name。忽略。
- 522.不推荐在 %extend 中使用非法的构造函数名称“ name ”，构造函数名称应为“ name ”。
- 523.不赞成在 %extend 中使用非法的析构函数名称“ name ”，析构函数名称应为“ name ”。
15.9.6 特定语言模块 (700-899)
- 801. 名称错误（更正为'名称'）。（红宝石）。
- 810. 没有为类型(Java)定义 jni类型映射。
- 811. 没有为类型(Java)定义 jtype类型映射。
- 812. 没有为类型(Java)定义 jstype类型映射。
- 813.类名警告，基类 被忽略。Java 不支持多重继承。（爪哇）。
- 814.
- 815. 没有为类型(Java)定义的 javafinalize类型映射。
- 816. 没有为类型(Java)定义的 javabody类型映射。
- 817. 没有为类型(Java)定义 javaout类型映射。
- 818. 没有为类型(Java)定义的 javain类型映射。
- 819. 没有为类型(Java)定义的 javadirectorin类型映射。
- 820. 没有为类型(Java)定义的 javadirectorout类型映射。
- 821.
- 822. Java 不支持协变返回类型。代理方法将返回basetype (Java)。
- 823. 没有为类型(Java)定义的 javaconstruct类型映射。
- 824. 在为 类型(Java)定义的导演类型映射中缺少 JNI 描述符。
- 825.类型 “javaconstruct”类型映射中缺少“directorconnect”属性。（爪哇）。
- 826. nspace 功能用于没有-package 的“类型”。生成的代码可能无法编译，因为 Java 不支持命名包中声明的类型访问未命名包中声明的类型。（爪哇）。
- 830. 没有为类型(C#)定义 ctype类型映射。
- 831. 没有为类型(C#)定义 cstype类型映射。
- 832. 没有为类型(C#)定义 cswtype类型映射。
- 833. classname警告，base baseclass 被忽略。C# 不支持多重继承。（C＃）。
- 834.
- 835. 没有为类型(C#)定义的 csfinalize类型映射。
- 836. 没有为类型(C#)定义 csbody类型映射。
- 837. 没有为类型(C#)定义 csout类型映射。
- 838. 没有为类型(C#)定义 csin类型映射。
- 839.
- 840.
- 841.
- 842. C# 不支持协变返回类型。代理方法将返回basetype (C#)。
- 843. 没有为类型(C#)定义 csconstruct类型映射。
- 844. 可能不会抛出 C# 异常 - typemap typemap 中没有 $excode 或 excode 属性。（C＃）。
- 845. 非托管代码包含对 SWIG_CSharpSetPendingException 方法的调用，并且 C# 代码不通过 canthrow 属性处理挂起的异常。（C＃）。
- 870.类名警告：基类 被忽略。PHP 不支持多重继承。(PHP)。
- 871. Unrecognized pragma pragma . (PHP)。
15.9.7 用户定义 (900-999)
您自己的应用程序可以使用这些数字。
15.10 历史
SWIG-1.3.12 首次添加了控制警告消息的功能。

16 使用模块
16.1 模块介绍
每次调用 SWIG 都需要指定一个模块名称。模块名称用于命名生成的目标语言扩展模块。这究竟意味着什么以及名称的用途取决于目标语言，例如，名称可以定义目标语言命名空间，或者仅仅是用于命名文件或帮助程序类的有用名称。本质上，一个模块包含目标语言包装器，用于选定全局变量/函数、结构/类和其他 C/C++ 类型的集合。
可以通过以下两种方式之一提供模块名称。第一个是使用特殊的 %module 指令指定它。该指令必须出现在接口文件的开头。该指令的一般形式是：
%module(option1="value1", option2="value2", ...) modulename

其中模块名是强制性的，选项添加了一个或多个可选的附加功能。通常不指定选项，例如：
%module mymodule

指定模块名称的第二种方法是使用 -module 命令行选项，例如 -module mymodule。如果在命令行上提供了模块名称，它将覆盖 %module 指令指定的名称。
首次使用 SWIG 时，用户通常从创建单个模块开始。也就是说，您可以定义单个 SWIG 接口来包装一些 C/C++ 代码集。然后将所有生成的包装器代码编译在一起并使用它。然而，对于大型应用程序，这种方法是有问题的——生成的包装器代码的大小可能相当大。此外，将目标语言界面分解成更小的部分可能更容易管理。
本章描述了在要创建模块集合的程序中使用 SWIG 的问题。集合中的每个模块都是通过单独调用 SWIG 创建的。
16.2 基础知识
多个模块的基本用例是模块没有交叉引用（即包装多个独立的 C API 时）。在这种情况下，swig 输入文件应该是开箱即用的——您只需创建多个包装 .cxx 文件，将它们链接到您的应用程序中，然后在脚本语言运行时中插入/加载每个文件，就像您在单个模块案例中所做的那样。
更复杂的是模块需要共享信息的情况。例如，当一个模块通过派生来扩展另一个模块的类时：
// File: base.h
class base {
public:
  int foo();
};

// File: base_module.i
%module base_module

%{
#include "base.h"
%}
%include "base.h"

 // File: derived_module.i
%module derived_module

%import "base_module.i"

%inline %{
class derived : public base {
public:
  int bar();
};
%}

为了正确地创建包装器，模块 derived_module 需要知道基类并且它的接口被另一个模块覆盖。%import "base_module.i" 这一行让 SWIG 知道这一点。通常 .h 文件被传递给 %import 而不是.i，不幸的是它不适用于所有语言模块。例如，Python 需要基类所在模块的名称，以便代理类可以完全继承基类的方法。通常，当缺少模块名称时，您会收到警告，例如：
derived_module.i:8: Warning 401: Base class 'base' ignored - unknown module name for base. Either
import
the appropriate module interface file or specify the name of the module in the %import directive.

有时需要导入头文件而不是接口文件并克服上述警告。例如，在导入的接口非常大的情况下，可能需要简化问题，只导入依赖类型的小头文件。这可以通过 在 %import 指令中指定可选的模块属性来完成。 上面显示的 derived_module.i 文件可以替换为以下内容：
// File: derived_module.i
%module derived_module

%import(module="base_module") "base.h"

%inline %{
class derived : public base {
public:
  int bar();
};

请注意，“base_module” 是和模块名称一样的，指定 ％module 在 base_module.i 还有 ％import 在 derived_module.i 里。
要注意的另一个问题是，不应从多个线程并行链接/加载多个依赖包装器，因为 SWIG 不提供锁定 - 有关该问题的更多信息，请继续阅读。
16.3 SWIG 运行时代码
许多 SWIG 的目标语言都会生成一组通常称为“SWIG 运行时”的函数。这些函数主要与运行时类型系统相关，该系统检查指针类型并执行其他任务，例如在 C++ 中正确转换指针值。作为一般规则，静态类型的目标语言（例如 Java）使用该语言的内置静态类型检查，并且不需要 SWIG 运行时。所有动态类型/解释语言都依赖于 SWIG 运行时。
运行时函数对于每个 SWIG 生成的模块都是私有的。也就是说，运行时函数是用“静态”链接声明的，并且仅对在该模块中定义的包装函数可见。这种方法的唯一问题是，当同一个应用程序中使用多个 SWIG 模块时，这些模块通常需要共享类型信息。对于 C++ 程序尤其如此，其中 SWIG 必须收集和共享有关跨模块边界的继承关系的信息。
为了解决跨模块共享信息的问题，指向类型信息的指针存储在目标语言命名空间的全局变量中。在模块初始化期间，类型信息从所有模块加载到类型信息的全局数据结构中。
这种方法有一些权衡。此类型信息在加载的所有 SWIG 模块中是全局的，并且可能导致未设计为协同工作的模块之间的类型冲突。为了解决这种方法，SWIG 运行时代码使用定义 SWIG_TYPE_TABLE 来提供唯一的类型表。在编译生成的 _wrap.cxx 或 _wrap.c 文件时，可以通过将 -DSWIG_TYPE_TABLE=myprojectname 添加到命令行参数来启用此行为。
然后，只有使用设置为 myprojectname 的 SWIG_TYPE_TABLE 编译的模块才会共享类型信息。所以如果你的项目有三个模块，三个模块都应该用 -DSWIG_TYPE_TABLE=myprojectname 编译，然后这三个模块会共享类型信息。但是任何其他项目的类型都不会干扰或与您模块中的类型发生冲突。
另一个与全局类型表相关的问题是线程安全。如果两个模块同时尝试加载，则类型信息可能会损坏。SWIG 目前不提供任何锁定，如果使用线程，则必须确保模块是串行加载的。如果您使用线程和某些脚本语言提供的自动模块加载，请小心。一种解决方案是在产生任何线程之前加载所有模块，或者使用 SWIG_TYPE_TABLE 来分隔类型表，这样它们就不会相互冲突。
最后，SWIG 使用 #define SWIG_RUNTIME_VERSION，位于 Lib/swigrun.swg 并靠近每个生成模块的顶部。当数据结构发生变化时，这个数字会增加，以便不同版本生成的 SWIG 模块可以和平共存。因此，类型结构由 (SWIG_TYPE_TABLE, SWIG_RUNTIME_VERSION) 对分隔，其中默认情况下 SWIG_TYPE_TABLE 为空。只有使用同一对编译的模块才会共享类型信息。
16.4 运行时的外部访问
如运行时类型检查器中所述，有时需要调用函数 SWIG_TypeQuery、 SWIG_NewPointerObj 和其他函数。支持从类型映射调用这些函数，因为类型映射代码嵌入到 _wrap.c文件中，该文件具有可用的这些声明。如果您需要从另一个 C 文件调用 SWIG 运行时函数，则需要包含一个头文件。为了生成需要包含的头文件，SWIG 可以通过 -external-runtime 以不同的模式运行以生成运行时，而不是处理输入接口文件的正常模式。例如：
$ swig -python -external-runtime <filename>

filename 参数是可选的，如果未传递，则默认文件名将类似于 swigpyrun.h，具体取决于语言。这个头文件应该像任何其他 _wrap.c 输出文件一样对待，并且应该在 _wrap 文件时重新生成。包含此标头后，您的代码将能够调用 SWIG_TypeQuery、SWIG_NewPointerObj、SWIG_ConvertPtr 等。这些函数的确切参数参数可能因语言模块而异；请查看语言模块章节以获取更多信息。
在这个头文件中，函数被声明为静态并被内联包含在文件中，因此文件不需要链接到任何 SWIG 库或代码（您可能仍然需要链接到 libpython-2.3 之类的语言库）。数据通过脚本语言中的全局变量在此文件和 _wrap.c 文件之间共享。也可以将这个头文件连同生成的包装文件一起复制到你自己的包中，这样你就可以分发一个可以在没有安装 SWIG 的情况下编译的包（这是可行的，因为头文件是自包含的，不需要链接任何东西）。
此头文件还将使用上述 -DSWIG_TYPE_TABLE，因此在编译包含生成的头文件的任何代码时，应将 SWIG_TYPE_TABLE 定义为与您尝试访问其类型的模块相同。
16.5 关于静态库的警告
使用多个 SWIG 模块时，应注意不要使用静态库。例如，如果您有一个静态库 libfoo.a 并且您将一组 SWIG 模块与该库链接，则每个模块都将获得自己插入的库代码的私有副本。这通常不是您想要的，它可能会导致意外或奇怪的程序行为。使用可动态加载的模块时，您应该尝试专门使用共享库。
16.6 参考文献
由于使用共享库和多个模块的复杂性，咨询外部参考可能是个好主意。强烈推荐 John Levine 的“Linkers and Loaders”。
16.7 减少包装文件大小
将多个模块与 %import 指令一起使用是模块化大型项目的最常用方法。通过这种方式，可以生成多个不同的包装文件，从而避免生成单个大包装文件。通过使用命令行选项和功能来减小包装器文件的大小有几种替代解决方案。
-fcompact
此命令行选项将压缩包装文件的大小，而不会更改生成到包装文件中的代码。它只是删除空行并将代码行连接在一起。这对于具有可以处理的最大文件大小的编译器很有用。
-fvirtual
此命令行选项将删除多余的虚拟方法包装器的生成。考虑以下继承层次结构：
struct Base {
  virtual void method();
  ...
};

struct Derived : Base {
  virtual void method();
  ...
};

通常为这两种方法生成包装器，而此命令行选项将禁止为 Derived::method 生成包装器。普通多态遗存作为 Derived::method 仍然会叫你应该有一个派生的 实例，并要求包装 Base::method。
%feature("compactdefaultargs")
当使用默认参数包装方法时，此功能可以减少包装方法的数量。 默认参数部分讨论了该功能及其限制。

28 SWIG 和 Lua
Lua 是一种扩展编程语言，旨在支持具有数据描述功能的通用过程编程。它还为面向对象编程、函数式编程和数据驱动编程提供了良好的支持。Lua 旨在用作任何需要它的程序的强大、轻量级配置语言。Lua 被实现为一个库，用干净的 C 编写（即在 ANSI C 和 C++ 的公共子集中）。它也是一种非常小的语言，不到 6000 行代码，可编译为 <100 KB 的二进制代码。它可以在 http://www.lua.org上找到
eLua 代表嵌入式 Lua（可以被认为是 Lua 的一种变体），它为嵌入式世界提供了 Lua 编程语言的完整实现，扩展了它的特定功能，以实现高效和可移植的软件嵌入式开发。eLua 在微控制器等较小的设备上运行，并提供常规 Lua 桌面版本的全部功能。有关 eLua 的更多信息，请访问：http ://www.eluaproject.net
28.1 预赛
当前的 SWIG 实现旨在与 Lua 5.0.x、5.1.x 和 5.2.x 一起使用。它应该适用于更高版本的 Lua，但由于大量 API 更改，肯定不适用于 Lua 4.0。可以将 Lua 模块静态链接或动态链接到解释器中（通常 Lua 静态链接其库，因为动态链接并非在所有平台上都可用）。从 eLua 0.8 开始，SWIG 还支持 eLua。由于 SWIG 2.x 和 SWIG 3.0 之间的重大变化以及测试平台的不可用，eLua 状态被降级为“实验性”。
28.2 运行 SWIG
假设您定义了一个 SWIG 模块，如下所示：
%module example
%{
#include "example.h"
%}
int gcd(int x, int y);
extern double Foo;

要构建 Lua 模块，请使用 -lua 选项运行 SWIG 。
$ swig -lua example.i

如果构建 C++ 扩展，请添加 -c++ 选项：
$ swig -c++ -lua example.i

这将创建一个 C/C++ 源文件 example_wrap.c 或 example_wrap.cxx。生成的 C 源文件包含需要编译并与 C/C++ 应用程序的其余部分链接以创建扩展模块的低级包装器。
包装文件的名称源自输入文件的名称。例如，如果输入文件是 example.i，则包装文件的名称是example_wrap.c。要更改此设置，您可以使用 -o 选项。封装的模块将导出一个函数 “int luaopen_example(lua_State* L)”，必须调用该函数向 Lua 解释器注册模块。名称“luaopen_example”取决于模块的名称。
要构建 eLua 模块，请使用 -lua 运行 SWIG并添加 -elua 或 -eluac。
$ swig -lua -elua example.i

要么
$ swig -lua -eluac example.i

-elua 选项将所有 C 函数包装器和变量 get/set 包装器置于 rotables。它还生成一个元表，用于控制从 eLua 对这些变量的访问。它还提供了大量的模块尺寸压缩。另一方面，-eluac 选项将所有包装器放在单一 rotable 中。使用此选项，无论模块有多大，都不会消耗额外的微控制器 SRAM（粗压缩）。但是有一个问题：元表不是用-eluac生成的。要从 eLua 访问任何值，必须直接调用与该值关联的包装函数。
28.2.1 附加命令行选项
下表列出了可用于 Lua 模块的其他命令行选项。也可以使用以下命令查看它们：
swig -lua -help 

无法复制加载中的内容
28.2.2 编译链接和解释器
通常 Lua 被嵌入到另一个程序中，并且会被静态链接。下面给出了一个非常简单的独立解释器（min.c）：
#include <stdio.h>

// Because LUA is a C library, you shuold link it as C extern at g++
#ifdef __cplusplus
extern "C"
{
#endif

#include "lua.h"
#include "lualib.h"
#include "lauxlib.h"

extern int luaopen_example(lua_State* L); // declare the wrapped module

// For C++ linking
#ifdef __cplusplus
}
#endif

int main(int argc, char* argv[])
{
  lua_State *L;
  if (argc<2)
  {
    printf("%s: <filename.lua>\n", argv[0]);
    return 0;
  } 
  L=lua_open(); 
  // L = luaL_newstate(); // After Lua 5.2.1 
  luaopen_base(L);       // load basic libs (eg. print)
  luaopen_example(L);    // load the wrapped module
  if (luaL_loadfile(L, argv[1])==0) // load and run the file
    lua_pcall(L, 0, 0, 0);
  else
    printf("unable to load %s\n", argv[1]);
  lua_close(L);
  return 0;
}

在 Lua 发行版 src/lua/lua.c 中可以找到一组大大改进的代码。包含您的模块，只需添加外部声明并在相关位置添加 #define LUA_EXTRALIBS {"example", luaopen_example}。
用于编译和链接的确切命令因平台而异。这是执行此操作的一组可能的命令：
$ swig -lua example.i -o example_wrap.c 
$ gcc -I/usr/include/lua -c min.c -o min.o 
$ gcc -I/usr/include/lua -c example_wrap.c -o example_wrap .o 
$ gcc -c example.c -o example.o 
$ gcc -I/usr/include/lua -L/usr/lib/lua min.o example_wrap.o example.o -o my_lua

对于 eLua，源代码必须与 SWIG 生成的包装器一起构建。确保 eLua 源文件 platform_conf.h 和  auxmods.h已使用新模块的条目进行更新。请注意：“mod”是模块名称。
/* 示例platform_conf.h */ 
#define LUA_PLATFORM_LIBS_ROM\ 
  _ROM( AUXLIB_PIO, luaopen_pio, pio_map )\ 
  _ROM( AUXLIB_TMR, luaopen_tmr, tmr_map )\ 
  _ROM( AUXLIB_MOD, 
  .... mapopen_)

/* 示例 auxmods.h */ 
#define AUXLIB_PIO "pio" 
LUALIB_API int ( luaopen_pio )(lua_State *L ); 

#define AULIB_MOD "mod" 
LUALIB_API int ( luaopen_mod )(lua_State *L ); 
....

有关构建和配置 eLua 的更多信息，请访问： http ://www.eluaproject.net/doc/v0.8/en_building.html
28.2.3 编译动态模块
大多数但并非所有平台都支持模块的动态加载（Windows 和 Linux 支持）。请参阅 Lua 手册以确定您的平台是否支持它。为了编译动态加载的模块，可以使用相同的包装器。假设您有需要链接到名为 example.c 的文件中的代码，命令将是这样的：
$ swig -lua example.i -o example_wrap.c 
$ gcc -fPIC -I/usr/include/lua -c example_wrap.c -o example_wrap.o 
$ gcc -fPIC -c example.c -o example.o 
$ gcc -shared -I/usr/include/lua -L/usr/lib/lua example_wrap.o example.o -o example.so

SWIG 生成的包装器可以编译并链接到 Lua 5.1.x 及更高版本。加载非常简单。
require("example")

对于使用 Lua 5.0.x 的用户，您还需要一个带有 loadlib 函数的解释器（例如使用 Lua 编译的默认解释器）。为了动态加载模块，您必须使用两个参数调用 loadlib 函数：共享库的文件名和 SWIG 导出的函数。调用 loadlib 应该返回函数，然后调用它来初始化模块
my_init=loadlib("example.so", "luaopen_example") -- 对于 Unix/Linux 
--my_init=loadlib("example.dll", "luaopen_example") -- 对于 Windows 
assert(my_init) -- 确保它不是nil 
my_init() -- 调用 lib 的 init fn

或者可以在一行 Lua 代码中完成
assert(loadlib("example.so", "luaopen_example"))()

如果代码不起作用，请不要惊慌。最好的办法是将模块和解释器复制到一个目录中，然后执行解释器并尝试手动加载模块（注意，所有这些代码都区分大小写）。
a, b, c=package.loadlib("example.so", "luaopen_example") -- for Unix/Linux
--a, b, c=package.loadlib("example.dll", "luaopen_example") -- for Windows
print(a, b, c)

注意：对于 Lua 5.0：
loadlib() 函数在全局命名空间中，而不是在包中。所以它只是 loadlib()。
如果'a'是一个函数，这一切都很好，你需要做的就是调用它
  a()

加载您的库，它将添加一个表“example”，其中添加了所有功能。
如果它不起作用，请查看错误消息，特别是消息 'b'
The specified module could not be found。
意味着找不到模块，请检查您的模块的位置和拼写。
The specified procedure could not be found.
意味着它加载了模块，但找不到命名的函数。再次检查拼写，如果可能，检查以确保正确导出函数。
'loadlib' not installed/supported
很明显（返回并查阅 Lua 文档，了解如何为您的平台启用 loadlib）。
28.2.4 使用你的模块
假设一切顺利，您将能够：
$ ./my_lua 
> print(example.gcd(4, 6)) 
2 
> print(example.Foo) 
3 
> example.Foo=4 
> print(example.Foo) 
4 
>

28.3 基本 C/C++ 包装之旅
默认情况下，SWIG 尝试为您的 C/C++ 代码构建一个非常自然的 Lua 接口。本节简要介绍此包装的基本方面。
28.3.1 模块
SWIG 模块指令指定 Lua 模块的名称。如果你指定`module example'，那么所有的东西都会被包装到一个包含所有函数和变量的 Lua 表  'example'中。选择模块名称时，请确保不要使用与内置 Lua 命令或标准模块名称相同的名称。
28.3.2 函数
全局函数被包装为新的 Lua 内置函数。例如，
%module example
int fact(int n);

创建一个内置函数example.fact(n)，它的工作原理与您认为的完全一样：
> print example.fact(4)
24
>

为避免名称冲突，SWIG 创建了一个 Lua 表，其中包含所有函数、常量、类和全局变量。可以使用以下代码。这很容易覆盖现有功能，因此必须谨慎使用。此选项被视为已弃用，将在不久的将来被删除。
> for k, v in pairs(example) do _G[k]=v end
> print(fact(4))
24
>

也可以通过赋值来重命名模块。
> e=example 
> print(e.fact(4)) 
24 
> print(example.fact(4)) 
24

28.3.3 全局变量
支持全局变量（链接到 C 代码），并且似乎只是 Lua 中的另一个变量。然而，实际的机制更为复杂。给定一个全局变量：
%module example
extern double Foo;

SWIG 将有效地生成两个函数 example.Foo_set() 和 example.Foo_get()。然后将元表添加到表 'example'  以在正确的时间调用这些函数（当您尝试设置或获取 examples.Foo 时）。因此，如果您尝试将全局变量分配给另一个变量，您将在解释器中获得一个本地副本，该副本不再链接到 C 代码。
> print(example.Foo) 
3 
> c=example.Foo -- c 是 example.Foo 的副本，不是一回事
> example.Foo=4 
> print(c) 
3 
> c=5 -- 这不会影响原来的 example.Foo 
> print(example.Foo, c) 
4 5

因此，不可能像函数一样将全局变量“移动”到全局命名空间中。但是，可以使用分配重命名模块，以使其更方便。
> e=example 
> -- e 和 example 是同一个表
> -- 所以 e.Foo 和 example.Foo 是同一个东西
> example.Foo=4 
> print(e.Foo) 
4

如果一个变量被 %immutable 指令标记，那么任何设置这个变量的尝试都会导致 Lua 错误。给定一个全局变量：
%module example
%immutable;
extern double Foo;
%mutable;

SWIG 将允许读取 Foo 但当进行设置尝试时，将调用错误函数。
> print(e.Foo) -- reading works ok
4
> example.Foo=40 -- but writing does not
This variable is immutable
stack traceback:
        [C]: ?
        [C]: ?
        stdin:1: in main chunk
        [C]: ?

对于那些希望 SWIG 默默忽略不可变设置的人（就像以前版本的 Lua 绑定所做的那样），添加 -DSWIGLUA_IGNORE_SET_IMMUTABLE 编译选项将删除它。
与绑定的早期版本不同，现在可以向模块添加新函数或变量，就像它是一个普通表一样。这也允许用户重命名/删除现有的函数和常量（但不是链接变量，可变或不可变）。因此，建议用户在这样做时要小心。
> -- example.PI 不存在
> print(example.PI) 
nil 
> example.PI=3.142 -- 添加新值
> print(example.PI) 
3.142

如果您为 eLua 模块使用了 -eluac 选项，则在操作全局变量时必须遵循不同的方法。（这不适用于使用 -elua 生成的包装器）
> -- 仅适用于 -eluac。(num 已定义) 
> print(example.num_get()) 
20 
> example.num_set(50) -- 添加新值
> print(example.num_get()) 
50

通常，“variable_get()” 和 “variable_set()” 形式的函数由 SWIG 自动生成以与 -eluac 一起使用。
28.3.4 常量和枚举
因为 Lua 并没有真正的常量的概念，所以 C/C++ 常量在 Lua 中并不是真正的常量。它们实际上只是将值复制到 Lua 解释器中。因此，它们可以像任何其他值一样更改。例如给出一些常量：
%module example
%constant int ICONST=42;
#define    SCONST      "Hello World"
enum Days{SUNDAY, MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY};

这被“有效”转换为以下 Lua 代码：
example.ICONST=42 
example.SCONST="Hello World" 
example.SUNDAY=0 
....

常量不能保证在 Lua 中保持不变。常量的名称可能会被意外地重新分配以引用某个其他对象。不幸的是，SWIG 没有简单的方法来生成防止这种情况的代码。你只需要小心。
如果您正在使用 eLua 并且已经使用 -elua 或 -eluac 来生成您的包装器，那么宏常量和枚举应该通过一个名为"const"的可循环访问。在 eLua 中，宏常量和枚举保证保持常量，因为它们都包含在一个可循环中。从 eLua 访问常规 C 常量就像它是常规全局变量一样，只是如果尝试修改 C 常量，就会证明值不变性的属性。
> print(example.ICONST)
10
> print(example.const.SUNDAY)
0
> print(example.const.SCONST)
Hello World

28.3.4.1 常量/枚举和类/结构
枚举被导出到类表中。例如，给定一些枚举：
%module example 
enum Days { SUNDAY = 0, MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY }; 
struct Test { 
  enum { TEST1 = 10, TEST2 = 20 }; 
#ifdef __cplusplus // C 中没有静态成员
  static const int ICONST = 12; 
#endif 
};

由于 C 和 C++ 的作用域规则不同，包装 C 和 C++ 代码的行为略有不同。封装后的C++代码从Lua代码中使用如下：
> print(example.SUNDAY)
0
> print(example.Test.TEST1)
10
> print(example.Test.ICONST)
12

C 结构中的枚举位于全局命名空间中，并在 Lua 中按如下方式使用
> print(example.SUNDAY)
0
> -- See the difference here
> print(example.TEST1)
10

兼容性说明： SWIG-3.0.0 之前的 SWIG 版本未生成上述类表成员。C 包装器没有变化，但以下代码是包装 C++ 成员常量时访问这些常量/枚举的唯一方法：
> print(example.Test_TEST1)
10
> print(example.Test_ICONST)
12

除了新绑定之外，仍会生成旧式绑定。如果使用 -no-old-metatable-bindings 选项，则不会生成这些旧式绑定。
值得一提的是，example.Test.TEST1和 example.Test_TEST1 是不同的实体，改变一个不会改变另一个。鉴于这些是常量并且它们不应该被更改，因此避免此类问题取决于您。
28.3.5 指针
SWIG 完全支持 C/C++ 指针。此外，SWIG 处理不完整的类型信息没有问题。给定 <file.h> 接口的包装：
%module example

FILE *fopen(const char *filename, const char *mode); 
int fputs(const char *, FILE *); 
int fclose(FILE *);

包装后，您将能够以自然的方式使用 Lua 中的函数。例如：
> f=example.fopen("junk", "w") 
> example.fputs("Hello World", f) 
> example.fclose(f)

与许多脚本语言不同，Lua 长期以来一直支持内置的 C/C++ 对象指针。它们被称为“用户数据”。与使用某种编码字符串的许多其他 SWIG 版本不同，所有对象都将表示为用户数据。SWIG-Lua 绑定提供了一个特殊的函数 swig_type()，如果给定一个 userdata 对象，它将以字符串形式返回指向的对象类型（假设它是一个 SWIG 包装对象）。
> print(f)
userdata: 003FDA80
> print(swig_type(f))
FILE * -- it's a FILE*

Lua 强制其用户数据的完整性，因此几乎不可能破坏数据。但是作为指针的用户，您有责任释放它，或关闭与之相关的任何资源（就像在 C 程序中一样）。这不适用于类和结构（见下文）。最后一个注意事项：如果一个函数返回一个 NULL 指针，它不会被编码为用户数据，而是作为 Lua nil。
> f=example.fopen("not there", "r") -- 这将在 C 中返回一个 NULL 
> print(f) 
nil

28.3.6 结构
如果你包装了一个 C 结构，它也会被映射到一个 Lua 用户数据。通过向用户数据添加元表，这提供了一个非常自然的界面。例如，
struct Point{
  int x, y;
};

用法如下：
> p=example.new_Point()
> p.x=3
> p.y=5
> print(p.x, p.y)
3       5
>

为联合和 C++ 类的数据成员提供了类似的访问。
C 结构可以使用函数 new_Point() 创建，C 结构和 C++ 类都可以使用名称 Point() 创建。
如果你在上面的例子中打印出 p 的值，你会看到这样的：
> print(p)
userdata: 003FA320

与上一节中的指针一样，它被保存为用户数据。但是，添加了其他功能以使其更有用。SWIG 有效地创建了一些访问器/修改器函数来获取和设置数据。这些函数将被添加到用户数据的元表中。这提供了对上面显示的成员变量的自然访问（有关完整详细信息，请参见文档末尾）。
结构的 const 成员是只读的。也可以使用 immutable 指令将数据成员强制为只读。与其他不可变项一样，设置尝试将导致错误。例如：
struct Foo {
  ...
  %immutable;
  int x;        // Read-only members
  char *name;
  %mutable;
  ...
};

管理 char* 成员以及数组成员的机制与其他语言类似。这有点麻烦，应该通过定义类型映射（稍后描述）来更好地处理。
当一个结构的成员本身就是一个结构时，它被当作一个指针来处理。例如，假设您有两个这样的结构：
struct Foo {
  int a;
};

struct Bar {
  Foo f;
};

现在，假设您像这样访问 Bar 的 f 属性：
> b = Bar() 
> x = b.f

在这种情况下，x 是指向 b 中的 Foo 的指针。这与此 C 代码生成的值相同：
Bar b;
Foo *x = &b->f;       // Points inside b

因为指针指向结构内部，所以您可以修改内容，一切都如您所愿。例如：
> b = Bar() 
> b.f.a = 3 -- 修改结构成员的属性
> x = b.f 
> x.a = 3 -- 修改同一个结构

对于带有 -eluac 选项的 eLua，必须使用 SWIG 生成的特定结构函数来执行结构操作。假设您有以下结构定义：
struct data {
  int x, y;
  double z;
};

> --From eLua
> a = example.new_data()
> example.data_x_set(a, 10)
> example.data_y_set(a, 20)
> print(example.data_x_get(a), example.data_y_get(a))
10 20

通常，SWIG 会为 C 中定义的每个结构自动生成形式为 "new_struct()"、 "struct_member_get()"、"struct_member_set()"和 "free_struct()" 的函数。（请注意：这不会申请使用 -elua 选项生成的模块）
28.3.7 C++ 类
C++ 类也由 Lua UserData 包装。例如，如果你有这个类，
class List {
public:
  List();
  ~List();
  int  search(char *item);
  void insert(char *item);
  void remove(char *item);
  char *get(int n);
  int  length;
};

你可以像这样在 Lua 中使用它：
> l = example.List() 
> l:insert("Ale") 
> l:insert("Stout") 
> l:insert("Lager") 
> print(l:get(1)) 
Stout 
> print(l :长度) 
3 
>

（注意：对于调用类的方法，您使用class:method(args)，而不是class.method(args)，这是一个容易犯的错误。但是对于数据属性，它是class.attribute）
类数据成员的访问方式与 C 结构相同。静态类成员给 Lua 带来了一个特殊问题，因为 Lua 不支持此类功能。因此，SWIG 会生成尝试解决其中一些问题的包装器。为了说明，假设你有一个这样的类：
class Spam {
public:
  static void foo();
  static int bar;
};

在 Lua 中，C++ 静态成员的访问方式如下：
> example.Spam.foo() -- 调用 Spam::foo() 
> a=example.Spam.bar -- 读取 Spam::bar 
> example.Spam.bar=b -- 写入 Spam::bar

（当前）无法访问实例的静态成员：
> s=example.Spam() -- s 是一个 Spam 实例
> s.foo() -- Spam::foo() 通过一个实例
          -- 不起作用

兼容性说明：在 SWIG-3.0.0 之前的版本中，只有以下名称有效：
> example.Spam_foo() -- 调用 Spam::foo() 
> a=example.Spam_bar -- 读取 Spam::bar 
> example.Spam_bar=b -- 写入 Spam::bar

现在默认生成两个样式名称。但是，如果使用 -no-old-metatable-bindings 选项，那么除了普通名称之外，不会生成向后兼容的名称。
28.3.8 C++继承
SWIG 完全了解与 C++ 继承相关的问题。因此，如果你有这样的课程
class Foo {
...
};

class Bar : public Foo {
...
};

如果你有这样的函数
void spam(Foo *f);

然后函数 spam() 接受一个 Foo 指针或一个指向从 Foo 派生的任何类的指针。
在 SWIG 中使用多重继承是安全的。
28.3.9 指针、引用、值和数组
在 C++ 中，函数可以通过多种不同的方式接收和操作对象。例如：
void spam1(Foo *x);  // 通过指针传递
void spam2(Foo &x);  // 通过引用传递
void spam3(Foo x);   // 按值传递
void spam4(Foo x[]); // 对象数组

在 SWIG 中，没有像这样的详细区分——具体来说，只有“对象”。没有指针、引用、数组等。因此，SWIG 在包装器代码中将所有这些类型统一在一起。例如，如果您确实拥有上述功能，那么这样做是完全合法的：
> f = Foo() -- 创建一个 Foo 
> spam1(f)  -- 好的。指针
> spam2(f)  -- 好的。引用
> spam3(f)  -- 好的。值。
> spam4(f)  -- 好的。数组（1 个元素）

返回值也会发生类似的行为。例如，如果你有这样的功能，
Foo *spam5(); 
Foo &spam6(); 
Foo spam7();

那么所有三个函数都将返回一个指向某个 Foo 对象的指针。由于第三个函数（spam7）返回一个值，所以使用新分配的内存来保存结果并返回一个指针（当返回值被垃圾回收时，Lua会释放这块内存）。另外两个是假定由 C 代码管理的指针，因此不会被垃圾收集。
28.3.10 C++ 重载函数
SWIG 主要支持 C++ 重载函数、方法和构造函数。例如，如果您有两个这样的函数：
void foo(int); 
void foo(char *c);

你可以直接在 Lua 中使用它们：
> foo(3) -- foo(int) 
> foo("Hello") -- foo(char *c)

然而由于 Lua 的强制机制有时会做一些奇怪的事情。
> foo("3") -- "3" 可以被强制转换为 int，所以它调用了 foo(int)！

由于这种强制机制是 Lua 的一个组成部分，除了重命名函数（见下文）之外，没有其他简单的方法可以解决这个问题。
同样，如果你有这样一个类，
class Foo {
public:
  Foo();
  Foo(const Foo &);
  ...
};

你可以这样写 Lua 代码：
> f = Foo()  -- 创建一个 Foo 
> g = Foo(f) -- 复制 f

重载支持不像 C++ 那样灵活。有时，有些方法 SWIG 无法消除歧义。例如：
void spam(int);
void spam(short);

或者
void foo(Bar *b); 
void foo(Bar &b);

如果出现此类声明，您将收到如下警告消息：
example.i:12: Warning 509: Overloaded method spam(short) effectively ignored,
example.i:11: Warning 509: as it is shadowed by spam(int).

要解决此问题，您需要忽略或重命名其中一种方法。例如：
%rename(spam_short) spam(short);
...
void spam(int);
void spam(short); // 作为 spam_short 访问

要么
%ignore spam(short);
...
void spam(int);
void spam(short);   // Ignored

SWIG 使用消歧方案解决重载的函数和方法，该方案根据一组类型优先规则对声明进行排序和排序。声明在输入中出现的顺序无关紧要，除非出现歧义——在这种情况下，第一个声明优先。
有关重载的更多信息，请参阅“SWIG 和 C++”一章。
处理Lua强制机制，优先级大致是（整数、浮点数、字符串、用户数据）。但是最好重命名函数而不是依赖顺序。
28.3.11 C++ 运算符
SWIG 可以自动处理某些 C++ 重载运算符。例如，考虑这样一个类：
class Complex {
private:
  double rpart, ipart;
public:
  Complex(double r = 0, double i = 0) : rpart(r), ipart(i) { }
  Complex(const Complex &c) : rpart(c.rpart), ipart(c.ipart) { }
  Complex &operator=(const Complex &c);
  Complex operator+(const Complex &c) const;
  Complex operator-(const Complex &c) const;
  Complex operator*(const Complex &c) const;
  Complex operator-() const;

  double re() const { return rpart; }
  double im() const { return ipart; }
};

包装后，它的工作原理与您期望的一样：
> c = Complex(3, 4) 
> d = Complex(7, 8) 
> e = c + d 
> e:re() 
10.0 
> e:im() 
12.0

运算符重载支持的一个限制是 SWIG 无法完全处理未定义为类的一部分的运算符。例如，如果你有这样的代码
class Complex { 
... 
friend Complex operator+(double, const Complex &c); 
... 
};

那么 SWIG 不知道如何处理友元函数——事实上，它只是忽略它并发出警告。您仍然可以包装运算符，但您可能必须将其封装在一个特殊的函数中。例如：
%rename(Complex_add_dc) operator+(double, const Complex &);
...
Complex operator+(double, const Complex &c);

有一些方法可以使用 %extend 指令使这个运算符作为类的一部分出现。继续阅读。
另外，请注意某些操作符不能完全映射到 Lua，一些 Lua 操作符不能完全映射到 C++ 操作符。例如，重载赋值运算符不映射到 Lua 语义，将被忽略，C++ 不支持 Lua 的连接运算符 ( .. )。
为了在 SWIG 中的不同语言中保持最大的兼容性，Lua 绑定使用与 python 相同的一组运算符名称。尽管在内部它将函数重命名为其他名称（为了使用 Lua）。
当前可以重载的运算符列表（和替代函数名称）是：
- __add__ 运算符+
- __sub__ 运算符-
- __mul__运算符 *
- __div__ 运算符/
- __unm__ 一元减
- __call__ operator ()（常用于函子类）
- __pow__ 指数 fn（没有 C++ 等价物，Lua 使用 ^）
- __concat__ 连接运算符（Lua 的..）
- __eq__ 运算符==
- __lt__ 运算符<
- __le__ 运算符<=
注意：在 Lua 中，只定义了等于、小于和小于等于运算符。其他运算符 (!=, >, >=) 是通过使用未应用于其他运算符结果的逻辑实现的。
以下操作符不能重载（主要是Lua不支持）
- ++ 和 --
- +=、-=、*= 等
- % 运算符（您必须使用 math.mod）
- 赋值运算符
- 所有按位/逻辑运算
SWIG 还接受将对象转换为字符串的 __str__() 成员函数。这个函数应该返回一个 const char*，最好是静态内存。这将用于Lua 中的 print() 和 tostring() 函数。假设 Complex 类有一个函数
const char* __str__()
{
        static char buffer[255];
        sprintf(buffer, "Complex(%g, %g)", this->re(), this->im());
        return buffer;
}

那么这将支持Lua中的以下代码
> c = Complex(3, 4)
> d = Complex(7, 8)
> e = c + d
> print(e)
Complex(10, 12)
> s=tostring(e) -- s is the number in string form
> print(s)
Complex(10, 12)

也可以重载运算符[]，但目前这不能自动执行。要重载运算符[]，您需要提供两个函数， __getitem__()和__setitem__()
class Complex 
{ 
        //.... 
        double __getitem__(int i)const; // i 是索引，返回数据
        void __setitem__(int i, double d); // i 是索引，d 是数据
};

C++ 操作符被映射到 Lua 预定义的元函数。类从其基础继承以下元函数列表（因此继承了以下运算符和伪运算符）：
- __add__
- __sub__
- __mul__
- __div__
- __unm__
- __mod__
- __call__
- __pow__
- __concat__
- __eq__
- __lt__
- __le__
- __len__
- __getitem__
- __setitem__
- __tostring 由 Lua 内部用于 tostring() 函数。__str__ 映射到这个函数
没有其他 lua 元函数被继承。例如，__gc 不是继承的，必须在每个类中重新定义。__tostring 受到特殊处理。如果在类和类库中不存在，SWIG 将提供一个默认值。
28.3.12 带有 %extend 的类扩展
SWIG 更有趣的特性之一是它可以使用新方法扩展结构和类。在上一节中，Complex 类将从 __str__() 方法以及对运算符重载的一些修复中受益匪浅。如果需要，它还可以用于向类添加其他功能。
原始的 Complex Class
class Complex {
private:
  double rpart, ipart;
public:
  Complex(double r = 0, double i = 0) : rpart(r), ipart(i) { }
  Complex(const Complex &c) : rpart(c.rpart), ipart(c.ipart) { }
  Complex &operator=(const Complex &c);
  Complex operator+(const Complex &c) const;
  Complex operator-(const Complex &c) const;
  Complex operator*(const Complex &c) const;
  Complex operator-() const;

  double re() const { return rpart; }
  double im() const { return ipart; }
};

现在我们用一些新代码扩展它
%extend Complex { 
  const char *__str__() { 
    static char tmp[1024]; 
    sprintf(tmp, "Complex(%g, %g)", $self->re(), $self->im()); 
    return tmp; 
  } 
  bool operator==(const Complex& c) { 
    return ($self->re()==c.re() && $self->im()==c.im()); 
  } 
};

现在，在 Lua
> c = Complex(3, 4) 
> d = Complex(7, 8) 
> e = c + d 
> print(e) -- print 使用 __str__ 得到字符串形式来打印
Complex(10, 12) 
> print( e==Complex(10, 12)) -- 测试 == 运算符
true 
> print(e!=Complex(12, 12)) -- != 使用 == 运算符
true

Extend 适用于 C 和 C++ 代码、类和结构。它不会以任何方式修改底层对象——扩展只显示在 Lua 界面中。唯一需要注意的是代码必须使用“$self”而不是“this”，并且您无法访问代码的受保护/私有成员（因为您不是该类的正式成员）。
28.3.13 使用 %newobject 释放内存
如果你有一个像这样分配内存的函数，
char *foo() {
  char *result = (char *) malloc(...);
  ...
  return result;
}

那么 SWIG 生成的包装器将发生内存泄漏——返回的数据将被复制到一个字符串对象中，而旧的内容将被忽略。
要修复内存泄漏，请使用 %newobject 指令。
%newobject foo; 
... 
char *foo();

这将释放分配的内存。
28.3.14 C++ 模板
C++ 模板不会给 SWIG 带来大问题。但是，为了创建包装器，您必须告诉 SWIG 为特定模板实例化创建包装器。为此，您可以使用模板指令。例如：
%module example
%{
#include "pair.h"
%}

template<class T1, class T2>
struct pair {
  typedef T1 first_type;
  typedef T2 second_type;
  T1 first;
  T2 second;
  pair();
  pair(const T1&, const T2&);
  ~pair();
};

%template(pairii) pair<int, int>;

在 Lua：
> p = example.pairii(3, 4) 
> print(p.first, p.second) 
3 4

显然，模板包装比这个例子中显示的更多。更多细节可以在 SWIG 和 C++ 章节中找到。稍后会出现一些更复杂的例子。
28.3.15 C++ 智能指针
在某些 C++ 程序中，通常使用由所谓的“智能指针”包装的类。通常，这涉及使用实现 operator->() 的模板类，如下所示：
template<class T> class SmartPtr { 
  ... 
  T *operator->(); 
  ... 
}

那么，如果你有这样的课程，
class Foo {
public:
  int x;
  int bar();
};

智能指针将在 C++ 中使用，如下所示：
SmartPtr<Foo> p = CreateFoo(); // 以某种方式创建（未显示）
... 
p->x = 3; // Foo::x 
int y = p->bar(); // Foo::bar

要包装它，只需将 SmartPtr 类和低级 Foo 对象告诉 SWIG。确保在必要时使用模板实例化 SmartPtr。例如：
%module example
... 
%template(SmartPtrFoo) SmartPtr<Foo>; 
...

现在，在 Lua 中，一切都应该“正常工作”：
> p = example.CreateFoo() -- 以某种方式创建一个智能指针
> px = 3 -- Foo::x 
> print(p:bar()) -- Foo::bar

如果您需要访问由 operator->() 本身返回的底层指针，只需使用 __deref__() 方法。例如：
> f = p:__deref__() -- 返回底层 Foo *

28.3.16 C++ 异常
Lua 本身不支持异常，但它有类似的 errors。当 Lua 函数因错误而终止时，它会向调用者返回一个值。SWIG 自动映射任何被抛出 Lua 错误的基本类型。因此对于函数：
int message() throw(const char *) {
  throw("I died.");
  return 1;
}

SWIG 会自动将其转换为 Lua 错误。
> message()
I died.
stack traceback:
        [C]: in function 'message'
        stdin:1: in main chunk
        [C]: ?
>

如果你想捕捉异常，你必须使用 pcall() 或 xpcall()，它们在 Lua 手册中有说明。使用 xpcall 将允许您获得额外的调试信息（例如堆栈跟踪）。
> function a() b() end -- function a() calls function b()
> function b() message() end -- function b() calls C++ function message(), which throws 
> ok, res=pcall(a)  -- call the function
> print(ok, res)
false   I died.
> ok, res=xpcall(a, debug.traceback)  -- call the function
> print(ok, res)
false   I died.
stack traceback:
        [C]: in function 'message'
        runme.lua:70: in function 'b'
        runme.lua:67: in function <runme.lua:66>
        [C]: in function 'xpcall'
        runme.lua:95: in main chunk
        [C]: ?

SWIG 能够毫无问题地抛出数字类型、枚举、字符、char* 和 std::string。它还为 std::exception 及其派生类编写了类型映射，将异常转换为错误字符串。
然而，抛出其他类型的对象并不是那么简单。抛出的对象在 'catch' 块之外无效。因此，它们不能退还给口译员。解决这个问题的显而易见的方法是返回对象的副本，或者将对象转换为字符串并返回。尽管执行前者似乎很明显，但在某些情况下这是不可能的，最明显的是当 SWIG 没有关于对象的信息，或者对象不可复制/创建时。
因此，默认情况下 SWIG 将所有抛出的对象转换为字符串并返回它们。所以给定一个函数：
void throw_A() throw(A*) { 
  throw new A(); 
}

SWIG 只会将其（不好地）转换为字符串并将其用作错误。（这不是很有用，但它总是有效）。
> throw_A()
object exception:A *
stack traceback:
        [C]: in function 'unknown'
        stdin:1: in main chunk
        [C]: ?
>

要从 SWIG 中获得更有用的行为，您必须：提供一种将异常转换为字符串的方法，或者抛出可以复制的对象。
如果您有自己的类想要输出为字符串，则需要添加如下类型的类型映射：
%typemap(throws) my_except 
%{ 
  lua_pushstring(L, $1.what()); // 假设 my_except::what() 返回一个 const char* 消息
  SWIG_fail; // 触发错误处理程序
%}

如果您希望将您的异常返回给解释器，它首先必须是可复制的。然后，您必须有一个额外的%apply 语句，以告诉 SWIG 将此对象的副本返回给解释器。例如：
%apply SWIGTYPE EXCEPTION_BY_VAL {Exc}; // tell SWIG to return Exc by value to interpreter

class Exc {
public:
  Exc(int c, const char *m) {
    code = c;
    strncpy(msg, m, 256);
  }
  int code;
  char msg[256];
};

void throw_exc() throw(Exc) {
  throw(Exc(42, "Hosed"));
} 

然后可以使用以下代码（注意：我们使用 pcall 来捕获错误以便我们可以处理异常）。
> ok, res=pcall(throw_exc) 
> print(ok) 
false 
> print(res) 
userdata: 0003D880 
> print(res.code, res.msg) 
42 Hosed 
>

注意：也有可能（虽然乏味）让一个函数抛出几种不同类型的异常。要处理这将需要一个 pcall，然后是一组检查错误类型的 if 语句。
所有这些代码都假定您的 C++ 代码使用异常规范（很多都没有）。如果它没有查阅“ 使用 %catches 进行异常处理”部分和“ 使用 %exception 进行异常处理”部分，以获取有关如何向函数或全局（分别）添加异常规范的更多详细信息。
28.3.17 命名空间
由于 SWIG-3.0.0 C++ 命名空间是通过 %nspace 功能支持的。
命名空间被映射到 Lua 表中。这些表中的每一个都包含在适当的命名空间中定义的名称。命名空间结构（又名嵌套命名空间）被保留。考虑以下 C++ 代码：
%module example
%nspace MyWorld::Nested::Dweller;
%nspace MyWorld::World;

int module_function() { return 7; }
int module_variable = 9;

namespace MyWorld {
  class World {
  public:
    World() : world_max_count(9) {}
    int create_world() { return 17; }
    const int world_max_count; // = 9
  };
  namespace Nested {
    class Dweller {
      public:
        enum Gender { MALE = 0, FEMALE = 1 };
        static int count() { return 19; }
    };
  }
}

现在，从 Lua 用法如下：
> print(example.module_function())
7
> print(example.module_variable)
9
> print(example.MyWorld.World():create_world())
17
> print(example.MyWorld.World.world_max_count)
9
> print(example.MyWorld.Nested.Dweller.MALE)
0
> print(example.MyWorld.Nested.Dweller.count())
19
>

28.3.17.1 兼容性说明
如果 SWIG 以向后兼容的方式运行，即没有 -no-old-metatable-bindings 选项，则会生成其他旧式名称（注意下划线）：
9
> print(example.MyWorld.Nested.Dweller_MALE)
0
> print(example.MyWorld.Nested.Dweller_count())
11
>

28.3.17.2 名称
如果 SWIG 在没有 -no-old-metatable-bindings 选项的情况下启动，则它进入向后兼容模式。在此模式下，它会尝试为静态函数、类静态常量和类枚举生成其他名称。这些名称采用 $classname_$symbolname 形式，并添加到类周围的范围中。如果启用了 %nspace，则类命名空间将作为作用域。如果没有命名空间，或者 %nspace 被禁用，则模块被视为类命名空间。
考虑以下 C++ 代码
%module example
%nspace MyWorld::Test;
namespace MyWorld {
class Test {
  public:
  enum { TEST1 = 10, TEST2 }
  static const int ICONST = 12;
};
class Test2 {
  public:
  enum { TEST3 = 20, TEST4 }
  static const int ICONST2 = 23;
}

在向后兼容模式下，除了通常的名称外，还会生成以下名称（注意下划线）：
9
> print(example.MyWorld.Test_TEST1) -- Test has %nspace enabled
10
> print(example.MyWorld.Test_ICONST) -- Test has %nspace enabled
12
> print(example.Test2_TEST3) -- Test2 doesn't have %nspace enabled
20
> print(example.Test2_ICONST2) -- Test2 doesn't have %nspace enabled
23
>

在 C 模式下，枚举与枚举略有不同。根据 C 标准，来自 C 结构的枚举被导出到周围的作用域，没有任何前缀。假装 Test2 是一个结构，而不是类，那就是：
> print(example.TEST3) -- NOT Test2_TEST3
20
>

28.3.17.3 继承
继承的内部组织发生了变化。考虑以下 C++ 代码：
%module example
class Base {
  public:
  int base_func()
};
class Derived : public Base {
  public:
  int derived_func()
}

让我们暂时假设类成员函数存储在 .fn 表中。以前，当在模块初始化期间将类导出到 Lua 时，对于每个派生类，所有服务表 ST（即“.fn”）都被压缩并添加到相应的派生类 ST 中：类 Base 的.fn表中的所有内容都被复制到.fn类派生表等。这是一个递归过程，所以最终派生类的整个继承树都被压缩成派生类。
这意味着在模块初始化后对 Base 类所做的任何更改都不会影响类 Derived：
base = example.Base() 
der = example.Derived() 
> print(base.base_func) 
function: 0x1367940 
> getmetatable(base)[".fn"].new_func = function (x) return x -- 添加新函数class Base（对于类，而不是实例！）
> print(base.new_func) -- 检查此函数
function 
> print(der.new_func) -- 不起作用。Derived 不再检查 Base。
nil
>

此行为已更改。现在，除非提供了 -squash-bases 选项，否则 Derived 会存储它的基数列表，如果在它自己的服务表中找不到某个符号，则会搜索它的基数。选项 -squash-bases 将有效地返回旧行为。
> print(der.new_func) -- Now it works
function
>

28.4 Typemap
本节解释什么是类型映射 typemap 以及如何使用它们。在大多数情况下，SWIG 的默认包装行为就足够了。然而，有时 SWIG 可能需要一些额外的帮助来了解应用哪种类型映射来提供最佳包装。本节将解释如何使用 typemaps 以达到最佳效果
28.4.1 什么是类型映射？
类型映射只不过是附加到特定 C 数据类型的代码生成规则。例如，要将整数从 Lua 转换为 C，您可以像这样定义一个类型映射：
%module example

%typemap(in) int {
  $1 = (int) lua_tonumber(L, $input);
  printf("Received an integer : %d\n", $1);
}
%inline %{
extern int fact(int n);
%}

注意：你不应该使用这个类型映射，因为 SWIG 已经有一个用于这个任务的类型映射。这纯粹是举例。
类型映射总是与代码生成的某些特定方面相关联。在这种情况下，“in”方法是指将输入参数转换为 C/C++。数据类型 int 是将应用类型映射的数据类型。提供的 C 代码用于转换值。在这段代码中，使用了许多以 $ 开头的特殊变量。$1 变量是 int 类型局部变量的占位符。$input 是要使用的值在 Lua 堆栈上的索引。
本例编译成Lua模块后，其操作如下：
> require "example"
> print(example.fact(6))
Received an integer : 6
720

28.4.2 使用类型映射
对于所有常见类型（int、float、short、long、char*、enum 等），SWIG 内置了许多现成的书面类型映射，SWIG 会自动使用这些类型映射，您无需付出任何努力。
然而，对于使用输入/输出参数或数组的更复杂的函数，您将需要使用 <typemaps.i>，它包含这些情况下的类型映射。例如，考虑以下函数：
void add(int x, int y, int *result) { 
  *result = x + y; 
} 

int sub(int *x1, int *y1) { 
  return *x1-*y1; 
} 

void swap(int *sx, int *sy) { 
  int t=*sx; 
  *sx=*sy; 
  *sy=t; 
}

程序员很清楚，'result'是一个输出参数，'x1'和'y1'是输入参数，'sx'和'sy'是输入/输出参数。但是对于 SWIG 来说并不明显，因此 SWIG 必须告知它们是哪种类型，以便它可以相应地进行包装。
一种方法是重命名参数名称以帮助 SWIG，例如 void add(int x, int y, int *OUTPUT)，但是使用 %apply 来实现相同的结果更容易，如下所示。
%include <typemaps.i>
%apply int* OUTPUT {int *result}; // int *result is output
%apply int* INPUT {int *x1, int *y1}; // int *x1 and int *y1 are input
%apply int* INOUT {int *sx, int *sy}; // int *sx and int *sy are input and output

void add(int x, int y, int *result);
int sub(int *x1, int *y1);
void swap(int *sx, int *sy);

包装后，它会给出以下结果：
> require "example"
> print(example.add(1, 2))
3
> print(demo.sub(1, 2))
-1
> a, b=1, 2
> c, d=demo.swap(a, b)
> print(a, b, c, d)
1       2       2       1

请注意，调用函数的参数中不需要“result”，因为它只是一个输出参数。对于 'sx' 和 'sy' 它们必须被传入（因为它们是输入），但原始值不会被修改（Lua 没有通过引用传递的特性）。然后将修改后的结果作为两个返回值返回。所有 INPUT/OUTPUT/INOUT 参数的行为方式类似。
注意：必须以完全相同的方式处理 C++ 引用。但是 SWIG 会自动将const int&包装为输入参数（因为它显然是输入）。
28.4.3 类型映射和数组
数组给 SWIG 带来了挑战，因为像指针一样，SWIG 不知道这些是输入值还是输出值，SWIG 也不知道数组应该有多大。然而，在适当的指导下，SWIG 可以轻松地包装数组以方便使用。
给定函数：
extern void sort_int(int* arr, int len); 
extern void sort_double(double* arr, int len);

SWIG 基本上有两种方法可以解决这个问题。第一种方法，使用 <carrays.i> 库在 C/C++ 中创建一个数组，然后可以在 Lua 中填充并传递给函数。它有效，但有点乏味。更多细节可以在 carrays.i 文档中找到。
第二种更直观的方法是将 Lua 表直接传递给函数，并让 SWIG 在 Lua 表和 C 数组之间自动转换。在<typemaps.i> 文件中，有已准备好的 typemap 规则来执行此任务。使用它们又是一个以正确方式使用 %apply 的问题。
下面的包装器文件显示了 carray 的使用以及使用 typemap 来包装数组。
// 使用 C 数组
%include <carrays.i> 
// 这声明了一批用于操作 C 整数数组的函数
%array_functions(int, int) 

extern void sort_int(int* arr, int len); // 要包装的函数

// 使用类型映射
%include <typemaps.i> // 这里面已经有一个从 double *INOUT,int 到 table 的 typemap
%apply (double *INOUT, int) {(double* arr, int len)}; // 为下面的函数应用 typemap

extern void sort_double(double* arr, int len); // 要包装的函数

包装后，这两个函数都可以被调用，但使用起来不同：
require "example" 
ARRAY_SIZE=10 

-- 将 C 数组传递给 sort_int() 
arr=example.new_int(ARRAY_SIZE) --
for i=0, ARRAY_SIZE-1 do -- 索引 0..9（就像C) 
    example.int_setitem(arr, i, math.random(1000)) 
end 
example.sort_int(arr, ARRAY_SIZE) -- 调用函数
example.delete_int(arr) -- 必须删除分配的内存

-- 使用typemap使用 Lua 表调用
-- 需要注意的一项：typemap 创建一个副本，而不是就地编辑
t={} -- 
for i=1, ARRAY_SIZE do  -- 索引 1..10 ( Lua style) 
    t[i]=math.random(1000)/10 
end 
t=example.sort_double(t) -- 用结果替换t

显然，通过编写 Lua 函数来执行从表到 C 数组的转换，第一个版本可以变得不那么乏味。在 ％luacode 指令是好这一点。有关此示例，请参阅 SWIG\Examples\lua\arrays。
警告：在 C 中索引从 0 开始，在 Lua 中索引从 1 开始。SWIG 期望 C 数组为 0..N-1 填充，Lua 表为 1..N，（索引遵循语言规范）。在类型映射中，当它将表转换为数组时，它会相应地悄悄更改索引。如果您有一个返回索引的 C 函数，请注意这种行为。
注意：SWIG 也可以以类似的方式支持指针数组。
28.4.4 类型映射和指针到指针函数
几个 C++ 库使用指针到指针函数来创建其对象。这些函数需要一个指向指针的指针，然后用指向新对象的指针填充该指针。Microsoft 的 COM 和 DirectX 以及许多其他库都有这种功能。下面给出一个例子：
struct iMath;    // some structure
int Create_Math(iMath** pptr); // its creator (assume it mallocs)

它将与以下 C 代码一起使用：
iMath* ptr;
int ok;
ok=Create_Math(&ptr);
// do things with ptr
//...
free(ptr); // dispose of iMath

SWIG 有一个现成的写好的类型映射来处理 <typemaps.i> 中的这种函数。它提供了正确的包装以及设置标志来通知 Lua 有问题的对象应该被垃圾收集。因此代码很简单：
%include <typemaps.i>
%apply SWIGTYPE** OUTPUT{iMath **pptr }; // tell SWIG it's an output

struct iMath;    // some structure
int Create_Math(iMath** pptr); // its creator (assume it mallocs)

用法如下：
ok, ptr=Create_Math() -- ptr is an iMath* which is returned with the int (ok)
ptr=nil -- the iMath* will be GC'ed as normal

28.5 编写类型映射
本节介绍如何使用 %typemap 指令修改 SWIG 对各种 C/C++ 数据类型的默认包装行为。这是一个高级主题，假定您熟悉 Lua C API 以及“类型映射 ”一章中的材料。
在继续之前，应该强调的是，很少需要编写类型映射，除非您想更改包装的某些方面，或者实现默认绑定无法实现的效果。
在继续之前，您应该阅读上一节关于使用 typemaps 的内容，并查看 luatypemaps.swg 和 typemaps.i 中现有的 typemaps。这些都有很好的文档记录并且相当容易阅读。在您阅读并理解这两个文件之前，您不应尝试编写自己的类型映射（它们也可能为您提供工作基础的想法）。
28.5.1 你可以写的类型映射
可以编写许多不同类型的类型映射，完整列表可以在“类型映射”一章中找到。但是，以下是最常用的。
- 在此是用于输入函数参数
- 出这是从函数的返回类型
- argout 这是一个函数参数，它实际上返回了一些东西
- typecheck 用于确定应该调用哪个重载函数（typecheck 的语法与 typemap 不同，有关详细信息，请参阅 typemaps）。
28.5.2 SWIG 的 Lua-C API
本节介绍 SWIG 特定的 Lua-C API。它不包括主要的 Lua-C api，因为这是有据可查的，不值得涵盖。
int SWIG_ConvertPtr(lua_State* L, int index, void** ptr, swig_type_info *type, int flags);

这是用于将 Lua 用户数据转换为 void* 的标准函数。它获取 Lua 状态中给定索引处的值并将其转换为用户数据。然后它将提供必要的类型检查，确认指针与“type”中给出的类型兼容。然后最后将 '*ptr' 设置为指针。如果 flags 设置为 SWIG_POINTER_DISOWN，这将清除对象上设置的任何所有权标志。
这将返回一个可以使用宏 SWIG_IsOK() 检查的值
void SWIG_NewPointerObj(lua_State* L, void* ptr, swig_type_info *type, int own);

这与 SWIG_ConvertPtr 相反，因为它推送一个新的用户数据，该用户数据包装了类型为 'type' 的指针 'ptr'。参数 'own' 指定对象是否被 Lua 拥有，如果它是 1，那么 Lua 将在处理用户数据时对对象进行 GC。
void* SWIG_MustGetPtr(lua_State* L, int index, swig_type_info *type, int flags, int argnum, const char* func_name);

这个函数是 SWIG_ConvertPtr() 的一个版本，除了它可以工作，或者它会触发一个带有文本错误消息的 lua_error() 。此功能很少使用，将来可能会被弃用。
SWIG_fail

此宏在 SWIG 包装函数的上下文中调用时，将跳转到错误处理程序代码。这将调用任何清理代码（释放任何临时变量），然后触发 lua_error。
此代码的常见用途是：
if (!SWIG_IsOK(SWIG_ConvertPtr( .....)){ 
 lua_pushstring(L, "发生了不好的事情"); 
 SWIG_fail; 
}

SWIG_fail_arg(char* func_name, int argnum, char* type)

此宏在 SWIG 包装函数的上下文中调用时，将显示错误消息并跳转到错误处理程序代码。错误消息的形式“ func_name (arg argnum ) 中的错误，预期'类型'得到'无论类型是'”
SWIG_fail_ptr(const char* fn_name, int argnum, swig_type_info* type);

与 SWIG_fail_arg 类似，不同之处在于它会显示 swig_type_info 信息。
28.6 自定义绑定
本节介绍向模块添加一些小的额外位以添加最后的收尾工作。
28.6.1 编写自己的自定义包装器
有时，可能需要添加您自己的特殊函数，绕过普通 SWIG 包装器方法，只使用原生 Lua API 调用。这些“本机”函数允许将您自己的代码直接添加到模块中。这是使用 %native 指令执行的，如下所示：
%native(my_func) int native_function(lua_State*L); // 使用 SWIG 注册 native_function() 
... 
%{ 
int native_function(lua_State*L) // 我的本机代码
{ 
 ... 
} 
%}

上面例子中的 %native 指令告诉 SWIG 有一个函数 int native_function(lua_State*L); 它将添加到名为“ my_func ”的模块中。除了将其添加到函数表中之外，SWIG 不会为此函数添加任何包装器。您如何编写代码完全取决于您。
28.6.2 添加额外的 Lua 代码
除了添加额外的 C/C++ 代码外，还可以将您自己的 Lua 代码添加到模块中。一旦调用了所有其他初始化，包括 %init 代码，就会执行此代码。
指令 %luacode 将代码添加到加载时执行的模块中。通常您会使用它来将您自己的功能添加到模块中。尽管您可以轻松执行其他任务。
%module example;

%luacode {
  function example.greet() 
    print "hello world" 
  end

  print "Module loaded ok"
}
...
%}

请注意，代码不是模块表的一部分。因此，对模块的任何引用都必须添加模块名称。
如果 Lua 代码中有错误，这不会 停止加载模块。SWIG 的默认行为是将错误消息打印到 stderr，然后继续。如果代码失败，可以使用 #define SWIG_DOSTRING_FAIL(STR) 定义不同的行为来更改此行为。
此功能的良好用途是添加新代码，或编写辅助函数来简化某些代码。有关此代码的示例，请参见示例/lua/arrays。
28.7 Lua绑定细节
在上一节中，介绍了 Lua 包装的高级视图。显然，很多事情发生在幕后才能实现。本节将解释有关如何实现的一些低级细节。
如果您只想使用 SWIG 并且不关心它是如何工作的，那么请停止阅读这里。这将进入代码的核心及其工作原理。它主要适用于需要了解代码中发生了什么的人。
28.7.1 将全局数据绑定到模块中。
假设您有一些想要在 C 和 Lua 之间共享的全局数据。SWIG 如何做到这一点？
%module example;
extern double Foo;

SWIG 将有效地生成这对函数
void Foo_set(double);
double Foo_get();

在初始化时，它会向解释器添加一个名为“example”的表，它代表模块。然后它将其所有功能添加到模块中。 （注意：旧版本的 SWIG 实际上添加了 Foo_set() 和 Foo_get() 函数，当前实现不再添加这些函数。）但它还向该表添加了一个元表，它有两个函数（__index 和 __newindex ）作为以及两个表（.get 和 .set） 下面的 Lua 代码将展示这些隐藏的功能。
> print(example)
table: 003F8F90
> m=getmetatable(example)
> table.foreach(m, print)
.set    table: 003F9088
.get    table: 003F9038
__index function: 003F8FE0
__newindex      function: 003F8FF8
> g=m['.get']
> table.foreach(g, print)
Foo     function: 003FAFD8
>

.get 和 .set 表是将变量名称 'Foo' 连接到访问器/修改器函数（Foo_set、Foo_get）的查找
__index和 __newindex代码的 Lua 等价物看起来有点像这样
function __index(mod, name)
        local g=getmetatable(mod)['.get'] -- gets the table
        if not g then return nil end
        local f=g[name] -- looks for the function
        -- calls it & returns the value
        if type(f)=="function" then return f() end
        return nil
end

function __newindex(mod, name, value)
        local s=getmetatable(mod)['.set'] -- gets the table
        if not s then return end
        local f=s[name] -- looks for the function
        -- calls it to set the value
        if type(f)=="function" then f(value)
        else rawset(mod, name, value) end
end

这样，当您调用 ' a=example.Foo ' 时，解释器查看表 'example' 发现没有字段 'Foo' 并调用 __index。这将依次检查 '.get' 表并找到 'Foo' 的存在，然后返回 C 函数调用 'Foo_get()' 的值。同样，对于代码“ example.Foo=10 ”，解释器将检查表，然后调用 __newindex，后者将检查“.set”表并调用 C 函数“Foo_set(10)”。
28.7.2 用户数据和元表
如前所述，类和结构都作为指针保存，使用 Lua 的“userdata”结构。这个结构实际上是一个指向 C 结构 'swig_lua_userdata' 的指针，它包含指向数据的指针、指向 swig_type_info（内部 SWIG 结构）的指针和一个标志，当解释器没有不再需要它。对象的实际访问是通过附加到此用户数据的元表完成的。
元表是 Lua 5.0 的特性（这也是 SWIG 不能包装 Lua 4.0 的原因）。它是一个包含函数、运算符和属性列表的表。这就是让用户数据感觉它是一个真实的对象而不仅仅是一块内存的原因。
给定一个班级
%module excpp;

class Point
{
public:
 int x, y;
 Point(){x=y=0;}
 ~Point(){}
 virtual void Print(){printf("Point @%p (%d, %d)\n", this, x, y);}
};

SWIG 将创建一个模块 excpp，其中包含所有各种功能。然而，为了直观地使用用户数据，SWIG 还创建了一组元表。正如上面关于全局变量的部分所见，元表的使用允许直观地使用包装器。为了省力，代码为每个类创建一个元表并将其存储在 Lua 的注册表中。然后当一个新对象被实例化时，在注册表中找到元表和与元表关联的用户数据。目前，派生类制作基类表的完整副本，然后添加自己的附加功能。
通过查看类的元表可以看到一些内部结构：
> p=excpp.Point()
> print(p)
userdata: 003FDB28
> m=getmetatable(p)
> table.foreach(m, print)
.type   Point
__gc    function: 003FB6C8
__newindex      function: 003FB6B0
__index function: 003FB698
.get    table: 003FB4D8
.set    table: 003FB500
.fn     table: 003FB528

'.type' 属性是类的名称。'.get' 和 '.set' 表的工作方式与模块类似，主要区别在于 '.fn' 表，它也包含所有成员函数。（'__gc' 函数是类的析构函数）
启用函数的代码的 Lua 等价物看起来有点像这样
function __index(obj, name)
        local m=getmetatable(obj) -- gets the metatable
        if not m then return nil end
        local g=m['.get'] -- gets the attribute table
        if not g then return nil end
        local f=g[name] -- looks for the get_attribute function
        -- calls it & returns the value
        if type(f)=="function" then return f() end
        -- ok, so it not an attribute, maybe it's a function
        local fn=m['.fn'] -- gets the function table
        if not fn then return nil end
        local f=fn[name] -- looks for the function
        -- if found the fn then return the function
        -- so the interpreter can call it
        if type(f)=="function" then return f end
        return nil
end

因此，当调用 'p:Print()' 时，__index 在对象元表上查找 'Print' 属性，然后查找 'Print' 函数。当它找到函数时，它返回函数，然后解释器可以调用'Point_Print(p)'
理论上，您可以使用此用户表并添加新功能，但请记住，它是一个类的所有实例之间的共享表，您很容易破坏所有实例中的功能。
注意：不透明结构（如 FILE*）和普通包装类/结构都使用相同的“swig_lua_userdata”结构。尽管不透明结构没有附加元表，或者在解释器完成它们后如何处理它们的任何信息。
注意：运算符重载基本上以相同的方式完成，通过向类的元表添加诸如“__add”和“__call”之类的函数。当前的实现有点粗糙，因为它会将任何以 '__' 开头的成员函数也添加到元表中，假设它是运算符重载。
28.7.3 内存管理
Lua 对内存管理很有帮助。'swig_lua_userdata' 完全由解释器本身管理。这意味着 C 代码和 Lua 代码都不能破坏它。一旦一条 userdata 没有引用它，它不会立即收集，而是在 Lua 认为需要时收集。（你可以通过调用 Lua 函数collectgarbage()强制收集 ）。一旦用户数据即将被释放，解释器将检查用户数据中的元表和函数“__gc”。如果存在，则调用此方法。对于所有完整类型（即普通包装类和结构），这应该存在。'__gc' 函数将检查 'swig_lua_userdata' 以检查 'own' 字段，如果这是真的（这将用于所有拥有的数据），它将调用指针上的析构函数。
目前不建议编辑此字段或添加一些用户代码来更改行为。虽然对于那些想尝试的人来说，这里是寻找的地方。
目前也无法更改数据的所有权标志（与大多数其他脚本语言不同，Lua 不允许从解释器内部访问数据）。


