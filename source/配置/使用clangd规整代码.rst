使用clangd规整代码
====================

    #. 下载msys2和vscode，配置好相应环境

    #. 安装clangd

        ::

            pacman -S mingw-w64-x86_64-clang-tools-extra

            pacman -S mingw-w64-i686-clang-tools-extra


    #. 打开powershell测试安装是否完成

        ::

            clangd --version     

    #. 安装clangd插件

    #. setting.json

        .. code-block:: json
            :caption: 添加新的内容

            "clangd.path": "C:/msys64/mingw32/bin/clangd.exe", // clangd路径

    #. 执行命令

        ::

            clang-format --style=Google --dump-config | Out-File -Encoding utf8 ./.clang-format

        可以更改.clang-format内容，建议设置IndentWidth（缩进）为4

    #. 目录结构
        
        ::

            root
            ├─.vscode
            │  └─settings.json
            ├─bin
            ├─io
            │  ├─in.txt
            │  └─out.txt
            ├─.clang-format
            └─code
                └─main.cpp