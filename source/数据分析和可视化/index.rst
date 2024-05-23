数据分析和可视化
=================

小提琴图
*********

    .. code:: python

        import seaborn as sns
        import matplotlib.pyplot as plt

        df = sns.load_dataset('iris')

        sns.violinplot(x=df["species"], y=df["sepal_length"])
        plt.show()

    ::

        小提琴图可以将一组或多组数据的数值变量分布可视化。
        相比有时会隐藏数据特征的箱形图相比，小提琴图值得更多关注。

    .. image:: ../_static/数据分析和可视化/小提琴图.png
        :alt: 小提琴图
        :align: center

核密度估计图
*************

    .. code:: python

        import seaborn as sns
        import matplotlib.pyplot as plt

        df = sns.load_dataset('iris')

        sns.kdeplot(df['sepal_width'])
        plt.show()

    ::

        核密度估计图其实是对直方图的一个自然拓展。
        可以可视化一个或多个组的数值变量的分布，非常适合大型数据集。

    .. image:: ../_static/数据分析和可视化/核密度估计图.png
        :alt: 核密度估计图
        :align: center

直方图
***********

    .. code:: python

        import seaborn as sns
        import matplotlib.pyplot as plt

        df = sns.load_dataset('iris')

        sns.histplot(df, x="sepal_length", bins=20, kde=True, color="blue")
        plt.show()

    ::

        直方图，可视化一组或多组数据的分布情况。

    .. image:: ../_static/数据分析和可视化/直方图.png
        :alt: 直方图
        :align: center

箱形图
***********

    .. code:: python

        import seaborn as sns
        import matplotlib.pyplot as plt

        df = sns.load_dataset('iris')

        sns.boxplot(x=df["species"], y=df["sepal_length"])
        plt.show()

    ::

        箱形图，可视化一组或多组数据的分布情况。
        可以快速获得中位数、四分位数和异常值，但也隐藏数据集的各个数据点。

    .. image:: ../_static/数据分析和可视化/箱形图.png
        :alt: 箱形图
        :align: center

散点图
*********

    .. code:: python

        import seaborn as sns
        import matplotlib.pyplot as plt

        df = sns.load_dataset('iris')

        sns.regplot(x=df["sepal_length"], y=df["sepal_width"])
        plt.show()

    ::

        散点图，显示2个数值变量之间的关系。

    .. image:: ../_static/数据分析和可视化/散点图.png
        :alt: 散点图
        :align: center

矩形热力图
***********

    .. code:: python

        import seaborn as sns
        import matplotlib.pyplot as plt
        import pandas as pd
        import numpy as np

        df = pd.DataFrame(np.random.random((5,5)), columns=["a","b","c","d","e"])
        
        sns.heatmap(df)
        plt.show()

    ::

        矩形热力图，矩阵中的每个值都被表示为一个颜色数据。

    .. image:: ../_static/数据分析和可视化/矩形热力图.png
        :alt: 矩形热力图
        :align: center

相关性图
***********

    .. code:: python

        import seaborn as sns
        import matplotlib.pyplot as plt

        df = sns.load_dataset('iris')

        sns.pairplot(df)
        plt.show()

    ::

        相关性图或相关矩阵图，分析每对数据变量之间的关系。
        相关性可视化为散点图，对角线用直方图或密度图表示每个变量的分布。

    .. image:: ../_static/数据分析和可视化/相关性图.png
        :alt: 相关性图
        :align: center

气泡图
***********

    .. code:: python

        import matplotlib.pyplot as plt
        import seaborn as sns
        from gapminder import gapminder

        data = gapminder.loc[gapminder.year == 2007]

        sns.scatterplot(data=data, x="gdpPercap", y="lifeExp", size="pop", legend=False, sizes=(20, 2000))
        plt.show()

    ::

        气泡图其实就是一个散点图，其中圆圈大小被映射到第三数值变量的值。图表示每个变量的分布。

    .. image:: ../_static/数据分析和可视化/气泡图.png
        :alt: 气泡图
        :align: center

连接散点图
***********

    .. code:: python

        import matplotlib.pyplot as plt
        import numpy as np
        import pandas as pd

        df = pd.DataFrame({'x_axis': range(1, 10), 'y_axis': np.random.randn(9) * 80 + range(1, 10)})

        plt.plot('x_axis', 'y_axis', data=df, linestyle='-', marker='o')
        plt.show()

    ::

        连接散点图就是一个线图，其中每个数据点由圆形或任何类型的标记展示。

    .. image:: ../_static/数据分析和可视化/连接散点图.png
        :alt: 连接散点图
        :align: center

二维密度图
***********

    .. code:: python

        import numpy as np
        import matplotlib.pyplot as plt
        from scipy.stats import kde

        data = np.random.multivariate_normal([0, 0], [[1, 0.5], [0.5, 3]], 200)
        x, y = data.T

        fig, axes = plt.subplots(ncols=3, nrows=2, figsize=(25, 10))

        axes[0][0].set_title('Scatterplot')
        axes[0][0].plot(x, y, 'ko')

        nbins = 20
        axes[0][1].set_title('Hexbin')
        axes[0][1].hexbin(x, y, gridsize=nbins, cmap=plt.cm.BuGn_r)

        axes[0][2].set_title('2D Histogram')
        axes[0][2].hist2d(x, y, bins=nbins, cmap=plt.cm.BuGn_r)

        k = kde.gaussian_kde(data.T)
        xi, yi = np.mgrid[x.min():x.max():nbins * 1j, y.min():y.max():nbins * 1j]
        zi = k(np.vstack([xi.flatten(), yi.flatten()]))

        axes[1][0].set_title('Calculate Gaussian KDE')
        axes[1][0].pcolormesh(xi, yi, zi.reshape(xi.shape), shading='auto', cmap=plt.cm.BuGn_r)

        axes[1][1].set_title('2D Density with shading')
        axes[1][1].pcolormesh(xi, yi, zi.reshape(xi.shape), shading='gouraud', cmap=plt.cm.BuGn_r)

        axes[1][2].set_title('Contour')
        axes[1][2].pcolormesh(xi, yi, zi.reshape(xi.shape), shading='gouraud', cmap=plt.cm.BuGn_r)
        axes[1][2].contour(xi, yi, zi.reshape(xi.shape))

        plt.show()

    ::

        二维密度图或二维直方图，可视化两个定量变量的组合分布。
        它们总是在X轴上表示一个变量，另一个在Y轴上，就像散点图。
        然后计算二维空间特定区域内的次数，并用颜色渐变表示。
        形状变化：六边形a hexbin chart，正方形a 2d histogram，核密度2d density plots或contour plots。
                
    .. image:: ../_static/数据分析和可视化/二维密度图.png
        :alt: 二维密度图
        :align: center

条形图
***********

    .. code:: python

        import numpy as np
        import matplotlib.pyplot as plt

        height = [3, 12, 5, 18, 45]
        bars = ('A', 'B', 'C', 'D', 'E')
        y_pos = np.arange(len(bars))

        plt.bar(y_pos, height)
        plt.xticks(y_pos, bars)
        plt.show()

    ::

        条形图表示多个明确的变量的数值关系。每个变量都为一个条形。条形的大小代表其数值。

    .. image:: ../_static/数据分析和可视化/条形图.png
        :alt: 条形图
        :align: center

雷达图
***********

    .. code:: python

        import matplotlib.pyplot as plt
        import pandas as pd
        from math import pi
        
        df = pd.DataFrame({
            'group': ['A', 'B', 'C', 'D'],
            'var1': [38, 1.5, 30, 4],
            'var2': [29, 10, 9, 34],
            'var3': [8, 39, 23, 24],
            'var4': [7, 31, 33, 14],
            'var5': [28, 15, 32, 14]
        })

        categories = list(df)[1:]
        N = len(categories)

        angles = [n / float(N) * 2 * pi for n in range(N)]
        angles += angles[:1]

        ax = plt.subplot(111, polar=True)

        ax.set_theta_offset(pi / 2)
        ax.set_theta_direction(-1)

        plt.xticks(angles[:-1], categories)
        ax.set_rlabel_position(0)
        plt.yticks([10, 20, 30], ["10", "20", "30"], color="grey", size=7)
        plt.ylim(0, 40)

        values = df.loc[0].drop('group').values.flatten().tolist()
        values += values[:1]
        ax.plot(angles, values, linewidth=1, linestyle='solid', label="group A")
        ax.fill(angles, values, 'b', alpha=0.1)

        values = df.loc[1].drop('group').values.flatten().tolist()
        values += values[:1]
        ax.plot(angles, values, linewidth=1, linestyle='solid', label="group B")
        ax.fill(angles, values, 'r', alpha=0.1)

        plt.legend(loc='upper right', bbox_to_anchor=(0.1, 0.1))

        plt.show()

    ::

        雷达图，可以可视化多个定量变量的一个或多个系列的值。
        每个变量都有自己的轴，所有轴都连接在图形的中心。

    .. image:: ../_static/数据分析和可视化/雷达图.png
        :alt: 雷达图
        :align: center

