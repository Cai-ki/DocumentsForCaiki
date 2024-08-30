使用cmake构建makefile
========================

mingw32-make
**************

    #. :doc:`配置c/c++的环境 <使用msys2快速配置c++环境>`

    #. 打开MSYS2终端并运行以下命令：

        ::

            pacman -S mingw-w64-i686-make    # 对于32位工具链
            pacman -S mingw-w64-x86_64-make  # 对于64位工具链

    #. 更新环境变量    

    #. 检查一下

        ::

            gcc --version
            g++ --version
            mingw32-make --version

CMake 生成器
************

    在运行 ``CMake`` 时，明确指定 ``MinGW Makefiles`` 生成器，并手动指定编译器。

    ::

        cmake -G "MinGW Makefiles" -DCMAKE_C_COMPILER=gcc -DCMAKE_CXX_COMPILER=g++ -S path/to/source -B path/to/build

    当然，不添加环境变量直接指定路径也可以。

    ::

        cmake -G "MinGW Makefiles" -DCMAKE_MAKE_PROGRAM=C:/path/to/mingw/bin/mingw32-make.exe -DCMAKE_C_COMPILER=C:/path/to/mingw/bin/gcc.exe -S path/to/source -B path/to/build

