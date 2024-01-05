# plot函数定义
本文仅节选官方文档的部分内容，故一些函数定义不明确，详见官方文档。
```python
    def plot(self, *args, **kwargs):
        
        scalex = kwargs.pop('scalex', True)
        scaley = kwargs.pop('scaley', True)

        if not self._hold:
            self.cla()
        lines = []

        kwargs = cbook.normalize_kwargs(kwargs, _alias_map)

        for line in self._get_lines(*args, **kwargs):
            self.add_line(line)
            lines.append(line)

        self.autoscale_view(scalex=scalex, scaley=scaley)
        return lines

```

---

# 使用方法解读

## 概览及简单示例

### 函数方法定义：

(method) def plot(
    self: Self@Axes,
    *args: Any,
    **kwargs: Any
) -> list

作用绘制二维平面上y关于x的线图或点图。

### 调用方法：

```python
plot([x], y, [fmt], data=None, **kwargs)
plot([x], y, [fmt], [x2], y2, [fmt2], ..., **kwargs)
# plot([1-D array], [1-D array], [fmt], [1-D array], [1-D array], [fmt], ..., **kwargs)
# 第一个一维数组作为x轴坐标值，第二个作为y轴坐标值，fmt为格式要求，
# 第三个一维数组作为x轴坐标值，第四个作为y轴坐标值，后面同上
```


点的坐标由(x, y)构成。

可选参数`fmt`：定义一些简单的格式，例如颜色，绘图标记，线型。本质为缩写的字符串标记。
```python
>>> plot(x, y)        # plot x and y using default line style and color
>>> plot(x, y, 'bo')  # plot x and y using blue circle markers
>>> plot(y)           # plot y using x as index array 0..N-1
>>> plot(y, 'r+')     # ditto, but with red plusses # 同上，但使用红色加号
```

`.Line2D`作为`keyword arguments`可以控制更多的表现形式。线的属性和`fmt`可以混合使用。下面两种调用方式等效。

```python
>>> plot(x, y, 'go--', linewidth=2, markersize=12)
>>> plot(x, y, color='green', marker='o', linestyle='dashed',
        linewidth=2, markersize=12)
```
当参数与`fmt`冲突时，`keyword arguments`具有优先权。

## 绘制标签数据

绘制标签数据的一种简单方式（i.e. 数据可以通过索引值获取）。可以给数据集(字典为例)`data={'a':[1, 2, 3, ],'b':[3, 4, 5, ]}`，然后仅给出数据的标签`labels`。
支持所有可索引的数据类型（包括 dict, padans.DataFame, numpy array等）。
```python
>>> obj={'xlabel':[1, 2, 3, 4, 5,], 'ylabel':[2, 4, 7, 9, 1]}
>>> plot('xlabel', 'ylabel', data=obj)
# <==>
>>> plot([1, 2, 3, 4, 5,], [2, 4, 7, 9, 1])
```

## 绘制多组数据

### 方法一：多次调用`plot`函数

    ```python
    >>> plot(x1, y1, 'bo')
    >>> plot(x2, y2, 'go')
    ```

方法二：对于二维数组，直接传输不同维度的数据分别作为`x`, `y`

例：一个数组，第一维作为`x`， 后面的作为`y`。

    ```python
    >>> plot(a[0], a[1:])
    ```

方法三：明确指定多组数据相应的 `[x], y, [fmt]`

    ```python
    >>> plot(x1, y1, 'g^', x2, y2, 'g-')
    ```
在这种情况下，任何附加的`keyword arguments`均应用于所有的数据集。而且这种语法（即指定多组x,y时）不能与`data`参数一起使用。

默认情况下，每条线都会使用不同的风格，该风格由'style cycle'指定。使用`[fmt]`和`线属性参数`(line property parameters)可以为你的线条指定特定的风格。或者，也可以使用`axex.prop_cycle`rcParam来改变风格循环('style cycle')。

## 参数

### 主要参数

* x, y : array-like or scalar

    数据点的横纵坐标。`x`值可选，未指定时默认为[0, ..., N-1]。

    一般而言，这些参数都是长度为N的数组。然而，标量亦可（等价于常值数组）。

    `[x], y`参数也可以是二维的。这样的话，列代表分离的数据集（the columns represent separate data sets）。
    个人理解是`x`的每一列与`y`的每一列相对应。

    **Note**:***x的第一维和y的第一维必须相同***，例如x.shape=(5,), 则y.shape=(5, n), (n>=1且为整数)；
    此外，x可以是多维数组，例x.shape=(4, 3), y.shape=(4, 6)![多维x轴](<屏幕截图 2024-01-04 004349.png>)

* fmt : str, optional

    格式字符串，e.g. 'ro'表示红色圆点。更多相关定义可以参考下面的`Note`部分

    格式字符串仅是快速定义基本的线属性的简写。更多线属性的参数设置可以通过`keyword arguments`。

* data : indexable object, optional

    具有标签数据的可索引的对象（例：字典型数据）。如果给定，在`plot`中将标签的名字分别放在`x`, `y`的位置。详见上面**绘制标签数据**部分。

### 其他参数

* scalex, scaley : bool, optional, default: True

    这两个参数决定了视图横纵坐标是否根据数据的范围进行变化。当他们的值为`False`时，横纵坐标轴的变化范围为[0.0, 1.0]。这些参数被传递到`autoscale_view`。

* **kwargs : .Line2D properties, optional

该参数用于指定一个线的标签(for auto legends)*此处不是很理解，如有高见，还请及时告知*，线宽，抗锯齿，绘图标记颜色。例：
```python
>>> plot([1,2,3], [1,2,3], 'go-', label='line 1', linewidth=2)
>>> plot([1,2,3], [1,4,9], 'rs',  label='line 2')
```
如果多条线用一个`plot`命令，则这些参数应用于所有的线。


## Return 函数返回

返回一组线条，一组表示数据的`.Line2D`对象。

## See also

散点图：有不同大小和颜色的XY散点图(也叫气泡图)。

## Note

### 格式字符串

格式字符串包括颜色，绘图标记，线型组成。

    fmt = '[color][marker][line]'

这些参数均为可选，如果未指定，则默认使用`style cycle`。此外，如果给了线型的设定，但是未指定绘图标记，则将无绘图标记。

### 颜色

`plot`支持以下几种字符。

|character|color|
|-|-|
|'b' |blue| 
|'g' |green| 
|'r' |red| 
|'c' |cyan(蓝绿色)| 
|'m' |magenta(洋红色)| 
|'y' |yellow| 
|'k' |black| 
|'w' |white|

如果`[fmt]`中只含有颜色参数，则可以使用`matplotlib.colors spec`, e.g. 全称 ('green') or 十六进制字符串 ('#008000').


### 绘图标记

个人对绘图标记的理解：数据点的标记样式。

|character |description |类型|
| ---| --- | --- |
|'.' |point marker|点|
|',' |pixel marker|像素标记(比点标记小)|
|'o' |circle marker|圆标记(比点大)|
|'v' |triangle_down marker|下三角|
|'^' |triangle_up marker|上三角|
|'<' |triangle_left marker|左三角|
|'>' |triangle_right marker|右三角|
|'1' |tri_down marker|三角星向下|
|'2' |tri_up marker||
|'3' |tri_left marker||
|'4' |tri_right marker||
|'s' |square marker|方形|
|'p' |pentagon marker|五边形|
|'*' |star marker|五角星|
|'h' |hexagon1 marker|竖六边形|
|'H' |hexagon2 marker|横六边形|
|'+' |plus marker|加号|
|'x' |x marker|叉号|
|'D' |diamond marker|方块|
|'d' |thin_diamond marker|竖菱形|
|'\|' |vline marker|竖线|
|'_' |hline marker|横线|

### 线的类型

|character |description |类型|
|---| ---| --- |
|'-' |solid line style|实线|
|'--' |dashed line style|虚线|
|'-.' |dash-dot line style|点划线|
|':' |dotted line style|点虚线|

### 示例

Example format strings:

    'b'    # blue markers with default shape
    'ro'   # red circles
    'g-'   # green solid line
    '--'   # dashed line with default color
    'k^:'  # black triangle_up markers connected by a dotted line
