LearnOpenGL
=====================

    ::

        欢迎来到图形学！！！

    ::

        接下来我开始跟着`LearnOpenGL`教程正式入坑图形学啦~

    `LearnOpenGL 中文 <https://learnopengl-cn.github.io/>`_

Hello Window
*********************

    ::

        配置部分解决完成的话，运行这个示例代码应该很简单。
        代码整体不难理解，都是对各自函数的解释，我们这里就continue了。

    `代码链接 <../_static/图形学/LearnOpenGL/HelloWindow.html>`_

    .. image:: ../_static/图形学/LearnOpenGL/HelloWindow.png
        :alt: Hello Window
        :align: center

Hello Triangle
********************

    ::

        说实话，有点上强度了，不过读好几遍代码终于搞懂了。
        着色器链接部分比葫芦画瓢就行。
        VAO，VBO，EBO，的理解就十分重要了。
        教程本来就是翻译的刚开始有点看不懂，复制一些代码让GPT解释了一下才勉强理解了它们的概念。
        简单来讲，你每一次让GPU绘制的任务都应该由VAO，VBO，EBO组合，当然EBO可以不需要。
        组合的时候就照抄给的代码就行。VAO存储的是你对该任务的各种配置，VBO就啥都不管仅仅存储顶点的数据就行了，EBO存到就是你任务里的每个个体都是由VBO的那几个顶点组成的。
        刚开始要给它们绑定到一块，代表它们是一家人了，之后调用这个任务时，就只找它们家长VAO就行了。
        对于绑定的代码部分也可以通俗理解成，我们有一个小屋，刚开始人们都不在屋里，所以要把人们叫过来，到一个屋子里就是一家人了。
        绑定0意味着，让这个家庭的某个职位的成员滚蛋。

    `代码链接 <../_static/图形学/LearnOpenGL/HelloTriangle.html>`_

    .. image:: ../_static/图形学/LearnOpenGL/HelloTriangle1.png
        :alt: Hello Triangle1
        :align: center

    .. image:: ../_static/图形学/LearnOpenGL/HelloTriangle2.png
        :alt: Hello Triangle2
        :align: center   

    #. `作业1代码链接 <../_static/图形学/LearnOpenGL/HelloTriangle_homework1.html>`_     
    #. `作业2代码链接 <../_static/图形学/LearnOpenGL/HelloTriangle_homework2.html>`_
    #. `作业3代码链接 <../_static/图形学/LearnOpenGL/HelloTriangle_homework3.html>`_