使用vscode进行编程
=======================

    `下载vscode <https://code.visualstudio.com/Download>`_

c/c++
*******************

    #. :doc:`配置c/c++的环境 <使用msys2快速配置c++环境>` 

    #. 下载必要插件
        
        #. Chinese (Simplified) (简体中文) Language Pack for Visual Studio Code
        
        #. C/C++

        #. C/C++ Extension Pack

        #. Code Runner

    #. 目录结构
        
        ::

            root
            ├─.vscode
            │  └─settings.json
            ├─bin
            ├─io
            │  ├─in.txt
            │  └─out.txt
            └─code
                └─main.cpp

    #. 配置文件

        .. code-block:: json
            :caption: settings.json

            {
            "editor.fontSize": 21, // 编辑器中的字体大小
            "editor.inlayHints.fontSize": 14, // 编辑器中内嵌提示的字体大小
            "editor.formatOnSave": true,
            "editor.formatOnPaste": true, // 保存或者粘贴前自动格式化
            "terminal.integrated.enableMultiLinePasteWarning": "never", // 去除粘贴多行时警告
            "security.workspace.trust.untrustedFiles": "open", //  允许打开不受信任的文件
            "explorer.confirmDelete": false, // 当你在文件资源管理器中删除文件或文件夹时，不会显示确认对话框，直接删除
            "code-runner.saveFileBeforeRun": true, // 运行前自动保存
            "code-runner.preserveFocus": false, // 焦点自动切换到终端
            "code-runner.saveAllFilesBeforeRun": true, // 运行前自动保存所有文件
            "code-runner.runInTerminal": true, // 在terminal中运行
            "code-runner.executorMap": {
                "c": "cd $dir && gcc $fileName -o ../bin/$fileNameWithoutExt && ../bin/$dir$fileNameWithoutExt",
                "cpp": "cd $dir && g++ -std=c++20 $fileName -o ../bin/$fileNameWithoutExt && ../bin/$fileNameWithoutExt",
                // "cpp": "cd $dir && g++ -std=c++20 $fileName -o ../bin/$fileNameWithoutExt && Get-Content '../io/in.txt' | ../bin/$fileNameWithoutExt | Out-File -FilePath '../io/out.txt' -Encoding utf8",
                "python": "cd $dir && set PYTHONIOENCODING=utf8 ;python -u $fullFileName",
            }, // 用于指定如何运行不同语言的代码
            "code-runner.executorMapByFileExtension": {}, // 用于指定如何运行特定文件扩展名的代码
            "clangd.path": "C:/msys64/mingw32/bin/clangd.exe", // clangd路径
            }

    #. 测试代码

        ::

            Ctrl+Alt+N