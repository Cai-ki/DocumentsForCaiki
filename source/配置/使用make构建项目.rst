使用make构建项目
================

make
************

#. `source <https://gnuwin32.sourceforge.net/packages/make.htm>`_
#. 将bin目录添加到环境变量里。

Makefile
***********

.. code-block:: cpp
    :caption: main.cpp

    #include <stdio.h>

    int main() {
        printf("this is code main.cpp\n");
        return 0;
    }

.. code-block:: makefile
    :caption: Makefile

    all:
        g++ main.cpp -o main
        main

    clean:
        del main.exe

.. code-block:: bash
    :caption: bash

    $ make
    g++ main.cpp -o main
    main
    this is code main.cpp
    $ make clean
    del main.exe

使用make来编译运行你的代码。以上是最最基础的部分。

接下来我们编译包含多个源文件的 C++ 程序。

.. code-block:: cpp
    :caption: main.cpp

    #include <stdio.h>

    #include "function.h"

    int main() {
        printf("this is code main.cpp\n");
        call();
        return 0;
    }

.. code-block:: cpp
    :caption: function.h

    #include <stdio.h>
    void call();

.. code-block:: cpp
    :caption: function.cpp

    #include "function.h"
    void call() { printf("the function is called"); }

.. code-block:: makefile
    :caption: Makefile

    CODES = main.cpp function.cpp
    EXE = main

    all: compile
        ${EXE}
        
    compile:
        g++ ${CODES} -o ${EXE}

    clean:
        del ${EXE}.exe

.. code-block:: bash
    :caption: bash

    $ make
    g++ main.cpp function.cpp -o main
    main
    this is code main.cpp
    the function is called
    $ make clean
    del main.exe

让我们解释一下上面的Makefile内容。

我们可以自定义变量，这个可以类比成c/c++的宏（我个人是这么理解的），
调用时只需要按 `${变量名}` 的格式，就可以替换内容。

后面的部分都是以

::

    target：need ...
        command
        
        ...

的格式书写。

其实很好理解，你需要执行某个指令，需要一些前置条件，就比如我上面的运行代码需要先编译出可执行文件。

这里的need其实是另一个target罢了。

command就是正常的指令而已啦。

很显然make的执行是一个递归调用的过程。

其默认执行第一个target，也就是说如果你不指定make target，仅仅make的话就只会执行第一个target。
