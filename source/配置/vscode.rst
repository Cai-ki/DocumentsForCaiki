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
            "code-runner.executorMap": {
                "javascript": "node",
                "java": "cd $dir && javac -d ../bin $fileName && java -cp ../bin $fileNameWithoutExt",
                "c": "cd $dir && gcc $fileName -o $fileNameWithoutExt && $dir$fileNameWithoutExt",
                "zig": "zig run",
                "cpp": "cd $dir && g++ -std=c++20 $fileName -o ./$fileNameWithoutExt && ./$fileNameWithoutExt",
                // "cpp": "cd $dir && clang++ -std=c++20 $fileName -o C:/Users/Caiki/Desktop/MyProject/bin/$fileNameWithoutExt && Get-Content 'C:/Users/Caiki/Desktop/MyProject/in_out/in.txt' | C:/Users/Caiki/Desktop/MyProject/bin/$fileNameWithoutExt | Out-File -FilePath 'C:/Users/Caiki/Desktop/MyProject/in_out/out.txt'",
                "objective-c": "cd $dir && gcc -framework Cocoa $fileName -o $fileNameWithoutExt && $dir$fileNameWithoutExt",
                "php": "php",
                "python": "cd $dir && set PYTHONIOENCODING=utf8 ;python -u $fullFileName",
                // "python": "set PYTHONIOENCODING=utf8 ; Get-Content \"./.vscode/oi/in.txt\" | python -u $fullFileName | Out-File -FilePath \"./.vscode/oi/out.txt\" -Encoding utf8",
                "perl": "perl",
                "perl6": "perl6",
                "ruby": "ruby",
                "go": "go run",
                "lua": "lua",
                "groovy": "groovy",
                "powershell": "powershell -ExecutionPolicy ByPass -File",
                "bat": "cmd /c",
                "shellscript": "bash",
                "fsharp": "fsi",
                "csharp": "cd $dir && csc /nologo -out:../bin/$fileNameWithoutExt.exe $fileName && ../bin/$fileNameWithoutExt",
                "vbscript": "cscript //Nologo",
                "typescript": "ts-node",
                "coffeescript": "coffee",
                "scala": "scala",
                "swift": "swift",
                "julia": "julia",
                "crystal": "crystal",
                "ocaml": "ocaml",
                "r": "Rscript",
                "applescript": "osascript",
                "clojure": "lein exec",
                "haxe": "haxe --cwd $dirWithoutTrailingSlash --run $fileNameWithoutExt",
                "rust": "cd $dir && rustc $fileName && $dir$fileNameWithoutExt",
                "racket": "racket",
                "scheme": "csi -script",
                "ahk": "autohotkey",
                "autoit": "autoit3",
                "dart": "dart",
                "pascal": "cd $dir && fpc $fileName && $dir$fileNameWithoutExt",
                "d": "cd $dir && dmd $fileName && $dir$fileNameWithoutExt",
                "haskell": "runghc",
                "nim": "nim compile --verbosity:0 --hints:off --run",
                "lisp": "sbcl --script",
                "kit": "kitc --run",
                "v": "v run",
                "sass": "sass --style expanded",
                "scss": "scss --style expanded",
                "less": "cd $dir && lessc $fileName $fileNameWithoutExt.css",
                "FortranFreeForm": "cd $dir && gfortran $fileName -o $fileNameWithoutExt && $dir$fileNameWithoutExt",
                "fortran-modern": "cd $dir && gfortran $fileName -o $fileNameWithoutExt && $dir$fileNameWithoutExt",
                "fortran_fixed-form": "cd $dir && gfortran $fileName -o $fileNameWithoutExt && $dir$fileNameWithoutExt",
                "fortran": "cd $dir && gfortran $fileName -o $fileNameWithoutExt && $dir$fileNameWithoutExt",
                "sml": "cd $dir && sml $fileName",
                "mojo": "mojo run",
                "erlang": "escript",
                "spwn": "spwn build",
                "pkl": "cd $dir && pkl eval -f yaml $fileName -o $fileNameWithoutExt.yaml",
                "gleam": "gleam run -m $fileNameWithoutExt"
            },
            "code-runner.executorMapByFileExtension": {
                ".vb": "cd $dir && vbc /nologo $fileName && $dir$fileNameWithoutExt",
                ".vbs": "cscript //Nologo",
                ".scala": "scala",
                ".jl": "julia",
                ".cr": "crystal",
                ".ml": "ocaml",
                ".zig": "zig run",
                ".exs": "elixir",
                ".hx": "haxe --cwd $dirWithoutTrailingSlash --run $fileNameWithoutExt",
                ".rkt": "racket",
                ".scm": "csi -script",
                ".ahk": "autohotkey",
                ".au3": "autoit3",
                ".kt": "cd $dir && kotlinc $fileName -include-runtime -d $fileNameWithoutExt.jar && java -jar $fileNameWithoutExt.jar",
                ".kts": "kotlinc -script",
                ".dart": "dart",
                ".pas": "cd $dir && fpc $fileName && $dir$fileNameWithoutExt",
                ".pp": "cd $dir && fpc $fileName && $dir$fileNameWithoutExt",
                ".d": "cd $dir && dmd $fileName && $dir$fileNameWithoutExt",
                ".hs": "runhaskell",
                ".nim": "nim compile --verbosity:0 --hints:off --run",
                ".csproj": "dotnet run --project",
                ".fsproj": "dotnet run --project",
                ".lisp": "sbcl --script",
                ".kit": "kitc --run",
                ".v": "v run",
                ".vsh": "v run",
                ".sass": "sass --style expanded",
                ".cu": "cd $dir && nvcc $fileName -o $fileNameWithoutExt && $dir$fileNameWithoutExt",
                ".ring": "ring",
                ".sml": "cd $dir && sml $fileName",
                ".mojo": "mojo run",
                ".erl": "escript",
                ".spwn": "spwn build",
                ".pkl": "cd $dir && pkl eval -f yaml $fileName -o $fileNameWithoutExt.yaml",
                ".gleam": "gleam run -m $fileNameWithoutExt"
            },
            "explorer.confirmDelete": false,
            "workbench.settings.applyToAllProfiles": [],
            "editor.inlayHints.fontSize": 14,
            "editor.fontSize": 21,
            "redhat.telemetry.enabled": true,
            "explorer.confirmDragAndDrop": false,
            "tabnine.experimentalAutoImports": true,
            "tabnine.disableFileRegex": [],
            "cmake.showOptionsMovedNotification": false,
            "cmake.configureOnOpen": true,
        }

    ::

        以上我是复制我本地的配置文件内容。
        第二个基本上不用改，最多就是改改编译器，比如你用的是`Clang`，就把`g++`换成`clang++`。
        注释部分能看懂就可以按自己所需更改，看不懂不要用。
    
    ::

        由于该配置文件是在你某个文件夹下创建，所以只能对该文件夹作用域中使用，有需要你可以在全局设置。
        注：其中有些部分并不重要，嫌过于冗余，可以浏览器搜索对应键值的含义，自行思索删除。