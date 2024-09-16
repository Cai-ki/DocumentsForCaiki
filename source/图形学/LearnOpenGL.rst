LearnOpenGL
=====================

    欢迎来到图形学！！！

    接下来我开始跟着 ``LearnOpenGL`` 教程正式入坑图形学啦~

    `LearnOpenGL 中文 <https://learnopengl-cn.github.io/>`_

Hello Window
*********************

    配置部分解决完成的话，运行这个示例代码应该很简单。
    
    代码整体不难理解，都是对各自函数的解释，我们这里就continue了。

    `HelloWindow <../_static/图形学/LearnOpenGL/HelloWindow.html>`_

    .. image:: ../_static/图形学/LearnOpenGL/HelloWindow.png
        :alt: Hello Window
        :align: center

Hello Triangle
********************

    说实话，有点上强度了，不过读好几遍代码终于搞懂了。
    
    编译着色器和链接部分比葫芦画瓢就行。
    
    VAO，VBO，EBO，的理解就十分重要了。
    
    教程本来就是翻译的刚开始有点看不懂，复制一些代码让GPT解释了一下才勉强理解了它们的概念。
    
    简单来讲，你每一次让GPU绘制的任务都应该由VAO，VBO，EBO组合，当然EBO可以不需要。
    
    组合的时候就照抄给的代码就行。VAO存储的是你对该任务的各种配置，VBO就啥都不管仅仅存储顶点的数据就行了，EBO存到就是你任务里的每个个体都是由VBO的那几个顶点组成的。
    
    刚开始要给它们绑定到一块，代表它们是一家人了，之后调用这个任务时，就只找它们家长VAO就行了。
    
    对于绑定的代码部分也可以通俗理解成，我们有一个小屋，刚开始人们都不在屋里，所以要把人们叫过来，到一个屋子里就是一家人了。
    
    绑定0意味着，让这个家庭的某个职位的成员滚蛋。

    `HelloTriangle <../_static/图形学/LearnOpenGL/HelloTriangle.html>`_

    .. image:: ../_static/图形学/LearnOpenGL/HelloTriangle1.png
        :alt: Hello Triangle1
        :align: center

    .. image:: ../_static/图形学/LearnOpenGL/HelloTriangle2.png
        :alt: Hello Triangle2
        :align: center   

    #. `HelloTriangle_homework1 <../_static/图形学/LearnOpenGL/HelloTriangle_homework1.html>`_     
    #. `HelloTriangle_homework2 <../_static/图形学/LearnOpenGL/HelloTriangle_homework2.html>`_
    #. `HelloTriangle_homework3 <../_static/图形学/LearnOpenGL/HelloTriangle_homework3.html>`_

Shaders
****************

    这一节难度较低，开始教了一部分`GLSL`的用法，接下来展示了如何让程序调用函数来与着色器交互。
    
    之后就更简单了，封装一个着色器类便于使用，毕竟着色器编译链接部分很板。

    `Shaders1 <../_static/图形学/LearnOpenGL/Shaders1.html>`_

    .. image:: ../_static/图形学/LearnOpenGL/Shaders1.png
        :alt: Shaders1
        :align: center

    `Shaders2 <../_static/图形学/LearnOpenGL/Shaders2.html>`_

    .. image:: ../_static/图形学/LearnOpenGL/Shaders2.png
        :alt: Shaders2
        :align: center

    `Shaders3 <../_static/图形学/LearnOpenGL/Shaders3.html>`_
    `Shaders4 <../_static/图形学/LearnOpenGL/Shaders4.html>`_

    .. image:: ../_static/图形学/LearnOpenGL/Shaders34.png
        :alt: Shaders34
        :align: center

    #. `Shaders_homework1 <../_static/图形学/LearnOpenGL/Shaders_homework1.html>`_     
    #. `Shaders_homework2 <../_static/图形学/LearnOpenGL/Shaders_homework2.html>`_
    #. `Shaders_homework3 <../_static/图形学/LearnOpenGL/Shaders_homework3.html>`_

Textures
***************

    代码实际运行效果不是下方图片哦。

    我自己改了一部分代码，教程的图片好难看(⊙﹏⊙)，还是我的流萤好看。

    `Textures1 <../_static/图形学/LearnOpenGL/Textures1.html>`_

    `Textures2 <../_static/图形学/LearnOpenGL/Textures2.html>`_

    .. image:: ../_static/图形学/LearnOpenGL/Textures_流萤.png
        :alt: Textures_流萤
        :align: center
    
    `原画链接 <../_static/图形学/LearnOpenGL/流萤.jpg>`_

    #. `Textures_homework1 <../_static/图形学/LearnOpenGL/Textures_homework1.html>`_     
    #. `Textures_homework2 <../_static/图形学/LearnOpenGL/Textures_homework2.html>`_
    #. `Textures_homework3 <../_static/图形学/LearnOpenGL/Textures_homework3.html>`_
    #. `Textures_homework4 <../_static/图形学/LearnOpenGL/Textures_homework3.html>`_

Transformations
**************************

    这一节讲了不少线性代数的知识。

    有一说一，我不是很喜欢，但没办法还得学。

    作业我没怎么看，之后用到再翻找吧。。。

    `Transformations <../_static/图形学/LearnOpenGL/Transformations.html>`_

    .. image:: ../_static/图形学/LearnOpenGL/Transformations.png
        :alt: Transformations
        :align: center

    #. `Transformations_homework1 <../_static/图形学/LearnOpenGL/Transformations_homework1.html>`_     
    #. `Transformations_homework2 <../_static/图形学/LearnOpenGL/Transformations_homework2.html>`_

Coordinate Systems
**************************

    这一个个系数矩阵有点迷糊呀，当然我是指具体实现方面。

    它们各自的功能还是很好理解的。

    .. math::

        V_{clip} = M_{projection} \cdot M_{view} \cdot M_{model} \cdot V_{local}


    `CoordinateSystems1 <../_static/图形学/LearnOpenGL/CoordinateSystems1.html>`_

    .. image:: ../_static/图形学/LearnOpenGL/CoordinateSystems1.png
        :alt: Coordinate Systems1
        :align: center

    `CoordinateSystems2 <../_static/图形学/LearnOpenGL/CoordinateSystems2.html>`_

    .. image:: ../_static/图形学/LearnOpenGL/CoordinateSystems2.png
        :alt: Coordinate Systems2
        :align: center

    `CoordinateSystems3 <../_static/图形学/LearnOpenGL/CoordinateSystems3.html>`_

    .. image:: ../_static/图形学/LearnOpenGL/CoordinateSystems3.png
        :alt: Coordinate Systems3
        :align: center

    #. `CoordinateSystems_homework <../_static/图形学/LearnOpenGL/CoordinateSystems_homework.html>`_ 

Camera
*****************

    逻辑不难理解，所以代码直接跳到最后一部分了。

    `Camera <../_static/图形学/LearnOpenGL/Camera.html>`_

    `Camera_class <../_static/图形学/LearnOpenGL/Camera_class.html>`_
    
    `Camera_useclass <../_static/图形学/LearnOpenGL/Camera_useclass.html>`_
    
    #. `Camera_homework1 <../_static/图形学/LearnOpenGL/Camera_homework1.html>`_     
    #. `Camera_homework2 <../_static/图形学/LearnOpenGL/Camera_homework2.html>`_

Review
***************************

    `教程链接 <https://learnopengl-cn.github.io/01%20Getting%20started/10%20Review/>`_  
