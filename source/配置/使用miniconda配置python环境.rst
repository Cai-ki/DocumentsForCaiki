使用miniconda配置python环境
==============================

`下载miniconda <https://docs.anaconda.com/miniconda/>`_
***********************************************************

    下载完成后记得将 ``miniconda`` 添加进环境变量。

    之后在终端中输入 ``conda --version``。

    如果终端中显示出 ``conda`` 版本号则为成功。

初始化 ``conda``
*******************

    在 ``powershell`` 输入 ``Get-ExecutionPolicy`` 。

    如果显示 ``Restricted`` 或者 ``AllSigned`` 请接着在终端中输入 ``Set-ExecutionPolicy RemoteSigned`` 或者 ``Set-ExecutionPolicy Unrestricted`` 。

    再次输入 ``Get-ExecutionPolicy`` 查看是否修改成功脚本执行策略。

    接下来输入 ``conda init`` 。

    为了加快下载速度和稳定性我们可以换成国内源（以下为清华源）。

    在终端中输入：

    ::

        conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
        conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/
        conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
        conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/menpo/
        conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/bioconda/
        conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/msys2/
        conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/        
        conda config --set show_channel_urls yes
        conda config --show

    至此，你的 ``conda`` 初始化完毕。

    ps：如果直接用 ``cmd`` 就不需要关心执行策略，直接输入 ``conda init`` 即可。恢复默认源需要在终端中键入 ``conda config --remove-key channels`` 。

创建 ``python`` 环境
**********************

    打开终端，输入 ``conda create -n env_name python=3.12`` （记得按提示输入 ``y`` ），这里的 ``env_name`` 是你的环境名称吗， ``3.12`` 是你的 ``python`` 版本号，请根据个人需求自行更改。

    接下来输入 ``conda activate env_name`` ，这时候你的终端最新一行前会出现 ``(env_name)`` ，代表已激活环境。

安装 ``pytorch``
**********************

    在终端中输入 ``conda install pytorch`` ，一路 ``y`` 后，敬请等待。

    下载完成后输入 ``conda list`` 可以看到 ``torch`` 已安装完毕。

    ps：下载像 ``pytorch`` 这样大型库建议使用 ``conda install`` ，其它情况则建议用 ``pip install``。而且请注意不要经常混用 ``conda install`` 和 ``pip install`` 。
    

