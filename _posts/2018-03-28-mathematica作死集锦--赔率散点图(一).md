---
layout: post
tags: Mathematica作死集锦
---

![](/images/00048.jpg)

<!--more-->

好的话不多说，作死开始。

---

我这次其实只是想学习一下MMA的绘制散点图功能。。只是恰好我这赔率数据比较多而已。。(别问为什么这不是重点)

原始数据是这样的：

> 3,3.4,2.3,1
> 2.5,3.25,2.8,-1
> 2.1,3.25,3.6,-1
> 2.7,3.4,2.5,0
> 2.5,3.25,2.8,0
> 1.73,3.8,4.5,0
> 1.29,5.5,9.5,1
> 4.33,3.6,1.8,-1
> 2,3.4,3.75,-1
> 1.62,3.8,5.5,1
> 1.5,4,7,1
> 4.75,3.6,1.73,-1
> ......

为了方便，我就用1,0,-1表示主胜，平局和客胜了，作为最后一项。

我决定用sqlite保存数据，所以把数据导入odds.db

[python]

>  import sqlite3
>  import csv
>  
>  f=open('bet365.csv')
>  reader = csv.reader(f)
>  odds = list(reader)
>  f.close()
>  
>  conn = sqlite3.connect('odds.db')
>  c = conn.cursor()
>  c.execute('''CREATE TABLE bet365(
>      ZHU REAL,
>      PING REAL,
>      KE REAL,
>      RES INT
> ); ''' )
> 
> for odd in odds:
>     c.execute('''INSERT INTO jingcai VALUES({},{},{},{})'''.format(*odd))
> conn.commit()
> conn.close()

接下来用MMA就很简单了。

先连接odds.db

[wolfram]

> Needs["DatabaseLink`"];
> conn = OpenSQLConnection[JDBC["SQLite", StringJoin[NotebookDirectory[], "odds.db"]]];

先不绘制颜色，所以只取每个列表的前三项

[wolfram]

>data = SQLExecute[conn, "SELECT ZHU,PING,KE FROM bet365"];

然后直接绘制散点图

[wolfram]

>ListPointPlot3D[data, ImageSize -> Full, AxesLabel -> {"Home", "Draw", "Away"}]

看👇👇

![](/images/00039.jpg)

三维的图象当然可以转动，就像这样

![](/images/00040.jpg)

这样

![](/images/00041.jpg)

这样

![](/images/00042.jpg)

我们发现，胜赔和负赔好像有某种熟悉的关系。 。所以不妨作出胜赔-负赔的散点图。

这回取每个列表的第1项和第3项

[wolfram]

>data2d = {#[[1]], #[[3]]} & /@ data;

再次作出图象

[wolfram]

>ListPlot[data2d, ImageSize -> Full, AxesLabel -> {"Home", "Away"}]

![](/images/00043.jpg)

这回更加熟悉了吧。。应该和反比例函数有关系

对散点做一下回归分析

得出关系大概是这样的：

>y=2.7/(x-1)+1

(MMA不太靠谱用肉眼辅助观察的)

把拟合曲线和散点作在同一个图象上

![](/images/00044.jpg)

完美。

对残差作出图象

[wolfram]

cancha = (#[[2]] - 1 - 2.7/(#[[1]] - 1)) & /@ data2d;
ListPlot[cancha, PlotRange -> {-1.5, 1.5}, ImageSize -> Full]

![](/images/00045.jpg)

看起来不错。

就是这样。

反观赔率其他组合，就像这样：

![](/images/00046.jpg)

![](/images/00047.jpg)

并没有什么明显规律了。。

这节先到这里了，下节再说一说散点和赛果的关系吧。

**完整源码和原始数据**:

请联系作者本人[滑稽]

---

假如这篇有人看的话，谢谢阅读哦。

比心.

![比心](/images/bixin.jpg)


