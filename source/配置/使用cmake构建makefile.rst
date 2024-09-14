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

    -

        使用 ``clangd`` 时：

        ::
            
            cmake -G "MinGW Makefiles" -DCMAKE_EXPORT_COMPILE_COMMANDS=ON ...

Cmakelists
**************

    导出可执行程序：

    ::

        include_dir = ...

        link_dir = ...

        exe_name = ...

        cmake_minimum_required(VERSION 3.0)

        project(myproject)

        set(CMAKE_CXX_STANDARD 20)
        
        set(CMAKE_EXE_LINKER_FLAGS "-static-libgcc -static-libstdc++")

        include_directories(${include_dir})

        link_directories(${link_dir})

        file(GLOB_RECURSE src ${include_dir}/*.cpp)

        # GLOB_RECURSE参数遍历指定目录及其所有子目录，GLOB参数仅遍历指定目录。
        
        # 如果不加参数就必须指出具体文件地址。

        file(GLOB_RECURSE link ${link_dir}/*.a)

        add_executable(${exe_name} ${src})

        target_link_libraries(${exe_name} ${link})

    导出 ``DLL`` ：

    ::

        cmake_minimum_required(VERSION 3.0)

        # 项目名称
        project(myproject)

        # 设置 C++ 标准
        set(CMAKE_CXX_STANDARD 20)

        # 设置静态链接器标志
        set(CMAKE_SHARED_LINKER_FLAGS "-static-libgcc -static-libstdc++")

        # 变量定义
        set(include_dir "path/to/include")
        set(link_dir "path/to/libs")
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

make调用cmake
*****************

    ::

        source_dir = .

        build_dir = ${source_dir}\build

        DCMAKE_RC_COMPILER = C:/msys64/mingw64/bin/x86_64-w64-mingw32-windres

        DCMAKE_C_COMPILER = x86_64-w64-mingw32-gcc

        DCMAKE_CXX_COMPILER = x86_64-w64-mingw32-g++


        all:
            cmake -G "MinGW Makefiles" -DCMAKE_RC_COMPILER=${DCMAKE_RC_COMPILER} -DCMAKE_C_COMPILER=${DCMAKE_C_COMPILER} -DCMAKE_CXX_COMPILER=${DCMAKE_CXX_COMPILER} -S ${source_dir} -B ${build_dir}
            cd ${build_dir} & make

