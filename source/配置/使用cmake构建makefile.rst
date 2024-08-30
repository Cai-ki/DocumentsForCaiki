使用cmake构建makefile
========================

mingw32-make
**************

    #. :doc:`配置c/c++的环境 <使用msys2快速配置c++环境>`

    #. 打开MSYS2终端并运行以下命令：

        ::

            pacman -S mingw-w64-i686-make    # 对于32位工具链
            pacman -S mingw-w64-x86_64-make  # 对于64位工具链

            pacman -S mingw-w64-x86_64-toolchain # 可以直接安装整个工具链，它包括了 make 和其他编译工具

    #. 更新环境变量    

    #. 检查一下

        ::

            gcc --version
            g++ --version
            mingw32-make --version

CMake 生成器
************

    在运行 ``CMake`` 时，明确指定 ``MinGW Makefiles`` 生成器，并手动指定编译器。

    -

        基本用法：

        ::

            cmake -G "MinGW Makefiles" -DCMAKE_C_COMPILER=gcc -DCMAKE_CXX_COMPILER=g++ -S <source_dir> -B <build_dir>

    -

        不添加环境变量的用法：

        ::

            cmake -G "MinGW Makefiles" -DCMAKE_MAKE_PROGRAM=C:/path/to/mingw/bin/mingw32-make.exe -DCMAKE_C_COMPILER=C:/path/to/mingw/bin/gcc.exe -S <source_dir> -B <build_dir>

    -

        生成 ``64`` 位程序时：

        ::

            cmake -G "MinGW Makefiles" -DCMAKE_C_COMPILER=x86_64-w64-mingw32-gcc -DCMAKE_CXX_COMPILER=x86_64-w64-mingw32-g++ -S <source_dir> -B <build_dir>
