基于Sphinx构建自己的个人文档
============================================================================
Sphinx
****************************************************************************

* `Sphinx 网站 <https://www.sphinx-doc.org/>`_

* `Sphinx 使用手册 <https://zh-sphinx-doc.readthedocs.io/en/latest/index.html>`_

reStructuredText
********************************

* `reStructuredText 网站 <https://docutils.sourceforge.io/rst.html>`_

环境搭建
********************************

确保电脑已经安装 `python` ， `make` 

在终端输入以下命令：

::

    pip install -U Sphinx
    pip install sphinx-autobuild
    pip install sphinx_rtd_theme

安装后我们得到以下命令：

:: 

    sphinx-apidoc
    sphinx-autobuild
    sphinx-autogen
    sphinx-build
    sphinx-quickstart

创建项目
********************************

创建项目文件夹，假设为 `mydoc` 。
切换到当前目录下并执行 `sphinx-quickstart` 。

:: 

    欢迎使用 Sphinx 7.3.7 快速配置工具。

    请输入接下来各项设定的值（如果方括号中指定了默认值，直接
    按回车即可使用默认值）。

    已选择根路径：.

    有两种方式来设置用于放置 Sphinx 输出的构建目录：
    一是在根路径下创建“_build”目录，二是在根路径下创建“source”
    和“build”两个独立的目录。
    > 独立的源文件和构建目录（y/n） [n]:

为了便于之后的管理，我们选择 `y` 。创建 `source` 和 `build` 两个独立的目录。

:: 

    项目名称将会出现在文档的许多地方。
    > 项目名称: mydoc
    > 作者名称: caiki
    > 项目发行版本 []: v1

    如果用英语以外的语言编写文档，
    你可以在此按语言代码选择语种。
    Sphinx 会把内置文本翻译成相应语言的版本。

    支持的语言代码列表见：
    https://www.sphinx-doc.org/en/master/usage/configuration.html#confval-language。
    > 项目语种 [en]: zh_CN

根据自己需要填写信息，之后可以在 `conf.py` 文件中更改。    

::

    build：生成的文件的输出目录。
    source/_static：静态文件目录。
    source/_templates：模板目录。
    source/conf.py：存放 Sphinx 的配置，包括在 sphinx-quickstart 时填写的那些配置信息，可以自行定义其他的配置信息。
    source/index.rst：文档项目起始文件。
    make.bat：Windows 脚本文件。
    Makefile：make的配置文件。

在 `mydoc` 目录下执行 `make html` 将在我们的 `build/html` 目录下生成 `html` 文件，用浏览器打开 `index.html` 即可渲染我们的页面。

也可以直接执行 `sphinx-autobuild source build/html` 一键启动HTTP服务。

index.rst
********************************

全文

::

    .. mydoc documentation master file, created by
    sphinx-quickstart on Sat May 18 19:58:32 2024.
    You can adapt this file completely to your liking, but it should at least
    contain the root `toctree` directive.

    Welcome to mydoc's documentation!
    =================================

    .. toctree::
    :maxdepth: 2
    :caption: Contents:



    Indices and tables
    ==================

    * :ref:`genindex`
    * :ref:`modindex`
    * :ref:`search`

* 注释使用

    ::

        .. mydoc documentation master file, created by
        sphinx-quickstart on Sat May 18 19:58:32 2024.
        You can adapt this file completely to your liking, but it should at least
        contain the root `toctree` directive.


* 标题使用

    ::

        Welcome to mydoc's documentation!
        =================================

    ::

        Indices and tables
        ==================

* 创建内容树

    ::

        .. toctree::
        :maxdepth: 2
        :caption: Contents:

* 无序列表和超链接

    ::

      * :ref:`genindex`
      * :ref:`modindex`
      * :ref:`search`

rst具体语法请自行学习。

主题
********************************

在 `conf.py` 文件中更改

::

    html_theme = 'alabaster'

为

::

    html_theme ='sphinx_rtd_theme'

可由默认主题切换至我们 `sphinx_rtd_theme` 主题。