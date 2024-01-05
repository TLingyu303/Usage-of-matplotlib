# Definition of plot

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

# Official Interpretation

(method) def plot(
    self: Self@Axes,
    *args: Any,
    **kwargs: Any
) -> list

Plot y versus x as lines and/or markers.

Call signatures:

    plot([x], y, [fmt], data=None, **kwargs)
    plot([x], y, [fmt], [x2], y2, [fmt2], ..., **kwargs)

The coordinates of the points or line nodes are given by *x*, *y*.

The optional parameter *fmt* is a convenient way for defining basic formatting like color, marker and linestyle. It's a shortcut string notation described in the *Notes* section below.

>>> plot(x, y)        # plot x and y using default line style and color
>>> plot(x, y, 'bo')  # plot x and y using blue circle markers
>>> plot(y)           # plot y using x as index array 0..N-1
>>> plot(y, 'r+')     # ditto, but with red plusses
You can use .Line2D properties as keyword arguments for more control on the appearance. Line properties and *fmt* can be mixed. The following two calls yield identical results:

>>> plot(x, y, 'go--', linewidth=2, markersize=12)
>>> plot(x, y, color='green', marker='o', linestyle='dashed',
        linewidth=2, markersize=12)
When conflicting with *fmt*, keyword arguments take precedence.

**Plotting labelled data**

There's a convenient way for plotting objects with labelled data (i.e. data that can be accessed by index obj['y']). Instead of giving the data in *x* and *y*, you can provide the object in the *data* parameter and just give the labels for *x* and *y*:

>>> plot('xlabel', 'ylabel', data=obj)
All indexable objects are supported. This could e.g. be a dict, a pandas.DataFame or a structured numpy array.

**Plotting multiple sets of data**

There are various ways to plot multiple sets of data.

The most straight forward way is just to call plot multiple times. Example:
>>> plot(x1, y1, 'bo')
>>> plot(x2, y2, 'go')
Alternatively, if your data is already a 2d array, you can pass it directly to *x*, *y*. A separate data set will be drawn for every column.
  Example: an array a where the first column represents the *x* values and the other columns are the *y* columns:

  >>> plot(a[0], a[1:])
The third way is to specify multiple sets of *[x]*, *y*, *[fmt]* groups:
  >>> plot(x1, y1, 'g^', x2, y2, 'g-')

  In this case, any additional keyword argument applies to all
  datasets. Also this syntax cannot be combined with the *data*
  parameter.
By default, each line is assigned a different style specified by a 'style cycle'. The *fmt* and line property parameters are only necessary if you want explicit deviations from these defaults. Alternatively, you can also change the style cycle using the 'axes.prop_cycle' rcParam.

Parameters
x, y : array-like or scalar
    The horizontal / vertical coordinates of the data points. *x* values are optional. If not given, they default to [0, ..., N-1].

    Commonly, these parameters are arrays of length N. However, scalars are supported as well (equivalent to an array with constant value).

    The parameters can also be 2-dimensional. Then, the columns represent separate data sets.

fmt : str, optional
    A format string, e.g. 'ro' for red circles. See the *Notes* section for a full description of the format strings.

    Format strings are just an abbreviation for quickly setting basic line properties. All of these and more can also be controlled by keyword arguments.

data : indexable object, optional
    An object with labelled data. If given, provide the label names to plot in *x* and *y*.

Other Parameters
scalex, scaley : bool, optional, default: True
    These parameters determined if the view limits are adapted to the data limits. The values are passed on to autoscale_view.

**kwargs : .Line2D properties, optional
    *kwargs* are used to specify properties like a line label (for auto legends), linewidth, antialiasing, marker face color. Example:

    >>> plot([1,2,3], [1,2,3], 'go-', label='line 1', linewidth=2)
    >>> plot([1,2,3], [1,4,9], 'rs',  label='line 2')

    If you make multiple lines with one plot command, the kwargs
    apply to all those lines.

    Here is a list of available `.Line2D` properties:

    %(Line2D)s
Returns
lines
    A list of .Line2D objects representing the plotted data.

See Also
scatter : XY scatter plot with markers of variing size and/or color (
    sometimes also called bubble chart).

Notes
**Format Strings**

A format string consists of a part for color, marker and line:

    fmt = '[color][marker][line]'
Each of them is optional. If not provided, the value from the style
cycle is used. Exception: If line is given, but no marker, the data will be a line without markers.

**Colors**

The following color abbreviations are supported:

|character |color | 
|---|---| 
|'b' |blue| 
|'g' |green| 
|'r' |red| 
|'c' |cyan| 
|'m' |magenta| 
|'y' |yellow| 
|'k' |black| 
|'w' |white|

If the color is the only part of the format string, you can additionally use any matplotlib.colors spec, e.g. full names ('green') or hex strings ('#008000').

**Markers**

|character |description |
| ---| --- |
|'.' |point marker|
|',' |pixel marker|
|'o' |circle marker|
|'v' |triangle_down marker|
|'^' |triangle_up marker|
|'<' |triangle_left marker|
|'>' |triangle_right marker|
|'1' |tri_down marker|
|'2' |tri_up marker|
|'3' |tri_left marker|
|'4' |tri_right marker|
|'s' |square marker|
|'p' |pentagon marker|
|'*' |star marker|
|'h' |hexagon1 marker|
|'H' |hexagon2 marker|
|'+' |plus marker|
|'x' |x marker|
|'D' |diamond marker|
|'d' |thin_diamond marker|
|'|' |vline marker|
|'_' |hline marker|

**Line Styles**

|character |description |
|---| ---|
|'-' |solid line style|
|'--' |dashed line style|
|'-.' |dash-dot line style|
|':' |dotted line style|

Example format strings:

    'b'    # blue markers with default shape
    'ro'   # red circles
    'g-'   # green solid line
    '--'   # dashed line with default color
    'k^:'  # black triangle_up markers connected by a dotted line

