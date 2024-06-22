vscode
============

快速配置你的vscode
*******************

    ::

        先把相应语言必备的插件，和其编译器或者解释器装了。
        插件比较简单，你可以浏览器搜索，也可以直接在拓展里面搜相应语言，把下标带`Microsoft`的都装了就行。
        这里不多做赘述。
        编译器或者解释器可以在我其它文章找下载链接，或者依旧浏览器搜索。

    ::

        为了开箱即用，少一些配置环节，我直接选用`Code Runner`作为运行代码的插件。
        如果喜欢其它方式，自行搜索学习。
        `Code Runner`记得在拓展中下载。
        以下为`C/C++`快速配置步骤。

    ::

        在你的代码文件夹里，或者工作区根目录下创建`.vscode`文件夹。
        之后在该文件夹下分别创建两个文件，分别是：
        `c_cpp_properties.json`和`settings.json`
        其中，`c_cpp_properties.json`是`C/C++`的配置文件，`settings.json`是`VS Code`的配置文件。
        具体内容如下：
    
    .. code:: json

        {
            "configurations": [
                {
                    "name": "Win32",
                    "includePath": [
                        "${workspaceFolder}/**",
                        "C:/msys64/mingw64/include" // 换成自己编译器文件夹里的include目录
                    ],
                    "defines": [
                        "_DEBUG",
                        "UNICODE",
                        "_UNICODE"
                    ],
                    "windowsSdkVersion": "10.0.22621.0",
                    "compilerPath": "C:/msys64/mingw64/bin/clang++.exe", // 换成自己编译器相应的目录，GCC就换成`g++.exe`
                    "cStandard": "c17",
                    "cppStandard": "c++20"
                }
            ],
            "version": 4
        }

    .. code:: json

        {
            // 运行前自动保存
            "code-runner.saveFileBeforeRun": true,
            // 焦点自动切换到终端
            "code-runner.preserveFocus": false,
            // 运行前自动保存所以文件
            "code-runner.saveAllFilesBeforeRun": true,
            // 在terminal中运行
            "code-runner.runInTerminal": true,
            // 保存或者粘贴前自动格式化
            "editor.formatOnSave": true,
            "editor.formatOnPaste": true,
            // 去除粘贴多行时警告
            "terminal.integrated.enableMultiLinePasteWarning": "never",
            //  允许打开不受信任的文件
            "security.workspace.trust.untrustedFiles": "open",
            // 用于指定如何运行不同语言的代码
            "code-runner.executorMap": {
                "c": "cd $dir && gcc $fileName -o $fileNameWithoutExt && $dir$fileNameWithoutExt",
                "cpp": "cd $dir && g++ -fopenmp -O1 -O2 -O3 -std=c++20 $fileName -o ./$fileNameWithoutExt && ./$fileNameWithoutExt",
                // "cpp": "cd $dir && clang++ -std=c++20 $fileName -o C:/Users/Caiki/Desktop/MyProject/bin/$fileNameWithoutExt && Get-Content 'C:/Users/Caiki/Desktop/MyProject/in_out/in.txt' | C:/Users/Caiki/Desktop/MyProject/bin/$fileNameWithoutExt | Out-File -FilePath 'C:/Users/Caiki/Desktop/MyProject/in_out/out.txt'",
                "python": "cd $dir && set PYTHONIOENCODING=utf8 ;python -u $fullFileName",
                // "python": "set PYTHONIOENCODING=utf8 ; Get-Content \"./.vscode/oi/in.txt\" | python -u $fullFileName | Out-File -FilePath \"./.vscode/oi/out.txt\" -Encoding utf8",
            },
            // 用于指定如何运行特定文件扩展名的代码
            "code-runner.executorMapByFileExtension": {},
            // 当你在文件资源管理器中删除文件或文件夹时，不会显示确认对话框，直接删除
            "explorer.confirmDelete": false,
            // 指定工作台的颜色主题为 "Ayu Mirage Bordered"
            "workbench.colorTheme": "Ayu Mirage Bordered",
            // 设置编辑器中内嵌提示（inlay hints）的字体大小为 14 像素
            "editor.inlayHints.fontSize": 14,
            // 设置编辑器中的字体大小为 21 像素
            "editor.fontSize": 21,
            // 当你在文件资源管理器中拖放文件或文件夹时，不会显示确认对话框，直接执行拖放操作
            "explorer.confirmDragAndDrop": false,
            // 固定 CMake 扩展中的命令。这里固定了两个命令
            "cmake.pinnedCommands": [
                "workbench.action.tasks.configureTaskRunner",
                "workbench.action.tasks.runTask"
            ],
        }

    ::

        以上我是复制我本地的配置文件内容。
        第二个基本上不用改，最多就是改改编译器，比如你用的是`Clang`，就把`g++`换成`clang++`。
        注释部分能看懂就可以按自己所需更改，看不懂不要用。
    
    ::

        由于该配置文件是在你某个文件夹下创建，所以只能对该文件夹作用域中使用，有需要你可以在全局设置。
        注：其中有些部分并不重要，嫌过于冗余，可以浏览器搜索对应键值的含义，自行思索删除。