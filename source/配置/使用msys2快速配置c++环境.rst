使用msy32快速配置c++环境
==========================

#. `下载msys2 <https://www.msys2.org/>`_

#. 下载GCC和Clang

    #. 打开MSYS2 MSYS
    
        .. code-block::
            :caption: 输入以下命令

            pacman -Syu

            pacman -S mingw-w64-x86_64-gcc

            pacman -S mingw-w64-i686-gcc

            pacman -S mingw-w64-x86_64-clang

            pacman -S mingw-w64-i686-clang
    
    #. 根据个人需求将相应的编译器的bin目录加入环境变量中
    
        .. code-block::
            :caption: 默认路径

            "C:\msys64"

    #. 打开powershell测试安装是否完成

        ::

            g++ --version

            clang++ --version



