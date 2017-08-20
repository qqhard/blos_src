title: matplotlib中文问题
date: 2016-03-27 22:25:12
tags:
- python
- matplotlib
- font
categories:
- python
---
一个中文乱码问题，让我搞的好苦，总结一下。

<!-- more -->

# 安装matplotlib
pip安装失败,使用`apt-get install matplotlib`安装成功

# 简单程序
```
import matplotlib.pyplot as pl
from matplotlib.ticker import MultipleLocator, FuncFormatter
x = [1,2,3,4]
y = [1,2,3,4]
pl.figure(figsize=(8,4))
pl.plot(x, y)
pl.title('标题')
pl.xlabel('x轴')
pl.ylabel('y轴')
ax = pl.gca()
def pi_formatter(x, pos):
    return str(int(x))+'月'
# 设置两个坐标轴的范围
pl.ylim(0,10)
pl.xlim(0,12)
# 设置图的底边距
pl.subplots_adjust(bottom = 0.15)
pl.grid() #开启网格
# 主刻度为1
ax.xaxis.set_major_locator( MultipleLocator(1))
# 主刻度文本用pi_formatter函数计算
ax.xaxis.set_major_formatter( FuncFormatter( pi_formatter ))
# 设置刻度文本的大小
for tick in ax.xaxis.get_major_ticks():
    tick.label1.set_fontsize(14)
pl.show()
```
运行了发现，所有的中文都变成了一个小方框，简单尝试了在开头加`#-*- coding: UTF-8 -*-`，和在｀'标题'｀前加`u`，都不好使

# 动态加载字体

```
from matplotlib.font_manager import FontProperties
font = FontProperties(fname=r"/usr/share/fonts/truetype/arphic/ukai.ttc", size=14)
pl.title('测试程序', fontproperties=font)
pl.xlabel('x轴', fontproperties=font)
pl.ylabel('y轴', fontproperties=font)
```
`ukai`是系统里的中文字体。

系统里的中文字体可以通过命令`fc-list :lang=zh`列出。

尝试后可以发现title和label的中文显示都正常了，但坐标轴的刻度文字依然不能正常显示。

# 配置文件matplotlibrc
这个配置文件在`/etc/`下，有的在`~/.matplotlib/`下

可以通过`matplotlib.matplotlib_fname()`打印获得这个路径

编辑该配置文件，找到`font.family`,将注释去掉,在下面的`font.sans-serif     : Bitstream Vera Sans, Lucida Grande, Verdana, Geneva, Lucid, Arial, Helvetica, Avant Garde, sans-serif`加上中文字体`SimHei`

```
font.family         : sans-serif
font.sans-serif     : SimHei, Bitstream Vera Sans, Lucida Grande, Verdana, Geneva, Lucid, Arial, Helvetica, Avant Garde, sans-serif
```

或者直接改为`font.family         : SimHei`

SimHei可以到`http://fontzone.net/download/simhei`下载，我尝试过使用上面提到的ukai，没有效果。

下载的SimHei放到下面任何一个路径均可:
```
/usr/share/fonts/truetype
/usr/share/matplotlib/mpl-data/fonts/ttf
~/.fonts
```
# 清除缓存

做了上面的更改，发现并没有什么卵用。

到`~/.cache/matplotlib`下的文件删掉，就ok了。

# 另一种方式，动态配置

```
from matplotlib import rcParams
rcParams['font.sans-serif'] = ['SimHei']
rcParams['font.family']='sans-serif'
```
这样可以不通过配置文件，动态配置
