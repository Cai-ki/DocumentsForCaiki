尝试使用Python来调用CPP的动态链接库
=======================================

目录结构
**********

    ::

        root:.
        ├── Cmakelists.txt
        ├── Makefile
        └── src
            ├── mydynamiclib.cpp
            └── test.py

各文件内容
***********

    .. code-block:: cmake
        :caption: Cmakelists.txt

        cmake_minimum_required(VERSION 3.0)

        # 项目名称
        project(myproject)

        # 设置 C++ 标准
        set(CMAKE_CXX_STANDARD 20)

        # 设置静态链接器标志
        set(CMAKE_SHARED_LINKER_FLAGS "-static-libgcc -static-libstdc++")

        # 变量定义
        set(include_dir "./src")
        set(link_dir "./src")
        set(library_name "mylibrary")

        # 包含目录
        include_directories(${include_dir})

        # 链接目录
        link_directories(${link_dir})

        # 查找所有源文件（.cpp 文件）并将它们添加到 SRC 变量
        file(GLOB_RECURSE src_files ${include_dir}/*.cpp)

        # 查找所有链接库（.a 文件），并将它们添加到 LIBS 变量
        file(GLOB_RECURSE lib_files ${link_dir}/*.a)

        # 添加动态链接库目标
        add_library(${library_name} SHARED ${src_files})

        # 设置导出 DLL 时的目标属性
        set_target_properties(${library_name} PROPERTIES
            OUTPUT_NAME "mydynamiclib"  # 生成的 DLL 文件名将是 mydynamiclib.dll
        )

        # 链接外部库
        target_link_libraries(${library_name} ${lib_files})

    .. code-block:: make
        :caption: Makefile

        source_dir = .

        build_dir = ${source_dir}\build

        DCMAKE_RC_COMPILER = C:/msys64/mingw64/bin/x86_64-w64-mingw32-windres

        DCMAKE_C_COMPILER = x86_64-w64-mingw32-gcc

        DCMAKE_CXX_COMPILER = x86_64-w64-mingw32-g++


        all:
            cmake -G "MinGW Makefiles" -DCMAKE_RC_COMPILER=${DCMAKE_RC_COMPILER} -DCMAKE_C_COMPILER=${DCMAKE_C_COMPILER} -DCMAKE_CXX_COMPILER=${DCMAKE_CXX_COMPILER} -S ${source_dir} -B ${build_dir}
            cd ${build_dir} & make & python ../src/test.py

    .. code-block:: CPP
        :caption: mydynamiclib.cpp

        #include <iostream>

        extern "C" {
        void say_hello() { std::cout << "Hello from the DLL!" << std::endl; }
        void cout(char *s) { std::cout << s << std::endl; }
        int add(int a, int b) { return a + b; }
        }

    .. code-block:: Python
        :caption: test.py

        import ctypes

        lib = ctypes.CDLL("../build/libmydynamiclib.dll")

        lib.say_hello.argtypes = None
        lib.say_hello.restype = None

        lib.say_hello()

        lib.add.argtypes = [ctypes.c_int, ctypes.c_int]
        lib.add.restype = ctypes.c_int

        print(lib.add(1, 2))

        lib.cout.argtypes = [ctypes.c_char_p]
        lib.cout.restype = None

        lib.cout(str(lib.add(1, 2)).encode("ASCII"))
        lib.cout(b"hello world!")
        lib.cout("你好，世界！".encode("OEM"))

切换到 ``root`` 目录下，在终端中输入 ``make`` ，得到以下输出。

    .. code-block:: bash

        Hello from the DLL!
        3
        3
        hello world!
        你好，世界！

至此，我们通过 ``Python`` 的 ``ctypes`` 库调用成功 ``CPP`` 的 ``dll`` 。

当我们既需要 ``Python`` 的便捷性但还有部分高性能需求时，不妨把高性能模块抽离出来通过 ``CPP`` 来代替。

编译 ``imgui`` 项目为 ``dll``
*********************************

    `try_ctypes <https://github.com/MiliLong/my-try/tree/main/try_ctypes>`_

借助 ``pybind11`` 将代码导出为 ``module``
*******************************************

    `try_pybind11 <https://github.com/MiliLong/my-try/tree/main/try_pybind11>`_