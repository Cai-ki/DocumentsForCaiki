C/CPP项目搭建过程
================================

``Step 1`` ：配置环境
************************

``IDE`` 和代码编辑器
--------------------

        我个人是使用 `Visual Studio Code <https://code.visualstudio.com/Download>`_。 

        之后教程也是以 ``Visual Studio Code`` 作为代码编辑器。

        当然，使用其它 ``IDE`` 或者代码编辑器也可以。

``C/CPP`` 编译器
--------------------

        目前主流的 ``C/CPP`` 编译器有 ``3`` 种，分别是 ``GCC`` ， ``Clang`` ， ``MSVC`` 。

        在这里我建议这 ``3`` 种编译器都下载。

        单纯做题的话可以任选一个下载，比如大家常用的 ``GCC`` 。

        * 下载 ``GCC`` 和 ``Clang``

            我们需要借助 `MSYS2 <https://www.msys2.org/>`_ 来下载它们。

            打开 ``MSYS2 MSYS`` ，输入以下命令：

            .. code-block:: bash
                :caption: 更新

                pacman -Syu

            .. code-block:: bash
                :caption: ``GCC``

                pacman -S mingw-w64-x86_64-gcc

            .. code-block:: bash
                :caption: ``Clang``

                pacman -S mingw-w64-x86_64-clang

            这里下载的都是 ``64`` 位的，如果需要 ``32`` 位可以把命令里的 ``x86_64`` 改成 ``i686`` 。

            添加完环境变量后执行以下命令测试安装是否成功：

            .. code-block:: bash
                :caption: 查询版本

                g++ --version
                clang++ --version

        * 下载 ``MSVC``

            安装 `Visual Studio <https://visualstudio.microsoft.com/zh-hans/>`_。

            打开安装程序后，可以看到多个选项，选择“使用 ``C++`` 的桌面开发”，之后无脑安装即可。
            
            实在迷糊可以网上搜索教程，这里不多做赘述。

            微软官方不建议直接将 ``MSVC`` 加入环境变量，这里我们打开刚刚下载的 ``Developer PowerShell for VS 2022`` ， 在终端中输入：

            .. code-block:: bash
                :caption: 验证

                cl      

        至此，编译环境配置完成。

``CMake`` ， ``make`` ， ``ninja``
------------------------------------

        `CMake <https://cmake.org/download/>`_

        `make <https://ftp.gnu.org/gnu/make/>`_

        `ninja <https://github.com/ninja-build/ninja/releases>`_

        下载完成记得添加到环境变量。


``Step 2`` ：构建项目
*************************

项目参考
-----------

        `my-try <https://github.com/MiliLong/my-try>`_。

        `template <https://github.com/MiliLong/my-try/tree/main/template>`_

``Makefile`` 模板
--------------------

        ``Cmakelists.txt`` 如何书写，可以网上搜索。
        
        .. code-block:: cmake
            :caption: 常用的

            cmake_minimum_required(VERSION 3.20)

            project(project)

            set(CMAKE_CXX_STANDARD 20)

            set(CMAKE_CXX_STANDARD_REQUIRED ON)

            add_subdirectory()

            include_directories()

            link_directories()

            file(GLOB_RECURSE)

            add_library()

            add_executable()

            target_link_libraries()

        以下是我构建项目时用的 ``Makefile`` 。

        .. code-block:: Makefile
            :caption: MSVC

            source_dir = .
            build_dir = ${source_dir}/build

            all:
                cmake -G "Ninja" \
                -DCMAKE_EXPORT_COMPILE_COMMANDS=ON \
                -DCMAKE_C_COMPILER=cl \
                -DCMAKE_CXX_COMPILER=cl \
                -S ${source_dir} -B ${build_dir}

                ninja -C ${build_dir}
        
        特殊的，对于需要 ``MSVC`` 编译的项目，记得打开 ``x64 Native Tools Command Prompt for VS 2022`` 来运行它。

        ``x64 Native Tools Command Prompt for VS 2022`` 是下载 ``Visual Studio`` 后自带的终端，它预设了许多环境变量和路径。

        这里的编译器可以根据个人的需要来更换，只需要更改：

        .. code-block:: Makefile
            :caption: GCC

            source_dir = .
            build_dir = ${source_dir}/build

            all:
                cmake -G "Ninja" \
                -DCMAKE_EXPORT_COMPILE_COMMANDS=ON \
                -DCMAKE_C_COMPILER=gcc \
                -DCMAKE_CXX_COMPILER=g++ \
                -S ${source_dir} -B ${build_dir}

                ninja -C ${build_dir}

        .. code-block:: Makefile
            :caption: Clang

            source_dir = .
            build_dir = ${source_dir}/build

            all:
                cmake -G "Ninja" \
                -DCMAKE_EXPORT_COMPILE_COMMANDS=ON \
                -DCMAKE_C_COMPILER=clang \
                -DCMAKE_CXX_COMPILER=clang++ \
                -S ${source_dir} -B ${build_dir}

                ninja -C ${build_dir}

``Step 3`` ： ``Visual Studio Code`` 配置
************************************************

插件
------------

        下载 ``clangd`` ， ``CMake`` 。


使用 ``clangd`` 规整代码
-----------------------------

        打开 ``MSYS2 MSYS`` ，输入 ``pacman -S mingw-w64-x86_64-clang-tools-extra`` ， 下载 ``clangd`` 。

        切换到 ``Visual Studio Code`` ，在 ``settings.json`` 里添加 ``"clangd.path": "/path/to/clangd.exe"`` ， ``clangd`` 路径。

        在项目根目录下执行 ``clang-format --style=Google --dump-config | Out-File -Encoding utf8 ./.clang-format`` 得到 ``.clang-format`` 文件。

        可以更改 ``.clang-format`` 文件内容，建议设置 ``IndentWidth`` （缩进）为 ``4`` 。

        至此，在 ``clangd`` ， ``clangd`` 插件 ， ``.clang-format`` 文件的配合下，我们可以享受：代码格式化，语法检查和错误提示，代码补全，代码导航等功能。

        注意，上文中 ``-DCMAKE_EXPORT_COMPILE_COMMANDS=ON`` 选项十分重要。

        有了它，在你构建项目后，项目 ``build`` 目录下会生成 ``compile_commands.json`` 文件。

        这个文件与 ``clangd`` 配合下才能进行正常的语法检查和错误提示以及代码导航功能。