An ultra fast cross-platform multiple screenshots module in pure python using ctypes
====================================================================================

Very basic, it will grab one screen shot by monitor or a screen shot of all monitors and save it to a PNG file, Python 2.6/3.5 compatible & PEP8 compliant.
It could be easily embedded into games and other softwares which require fast and plateforme optimized methods to grab screenshots.

**MSS** stands for **M**ultiple **S**creen**S**hots.

It's under zlib/libpng licence.


Installation
------------

You can install it with pip:

```shell
$ pip install --upgrade mss
```


Support
-------

Legend:
* :star: fully functional (latest stable version of Python)
* :star2: fully functional (old version of Python)
* :question: no machine to test ([reports needed](https://github.com/BoboTiG/python-mss/issues) :smiley:)

Python | GNU/Linux | MacOS X | Windows
:---: | :---: | :---: | :---:
**3.5.1** | :star: | :question: | :star:
3.4.4 | :star2: | :question: | :star2:
3.3.6 | :star2: | :question: | :star2:
3.2.6 | :star2: | :question: | :star2:
3.1.5 | :star2: | :question: | :star2:
3.0.1 | :star2: | :question: | :star2:
**2.7.11** | :star: | :star: | :star:
2.6.9 | :star2: | :star2: | :star2:

Feel free to try MSS on a system we had not tested, and let report us by creating an [issue](https://github.com/BoboTiG/python-mss/issues).


Testing
-------

You can try the MSS module directly from the console:

```shell
$ python tests.py
```


Instance the good class
-----------------------

So MSS can be used as simply as:

```python
from mss import mss


# Then ...
with mss() as screenshotter:
    # ...
```

Or import the good one (choose one):

```python
# MacOS X
from mss.darwin import MSS

# GNU/Linux
from mss.linux import MSS

# Microsoft Windows
from mss.windows import MSS


# Then ...
with MSS() as screenshotter:
    # ...
```

Of course, you can use it the old way:

```python
from mss import mss
# or from mss.linux import MSS as mss


# Then ...
screenshotter = mss()
# ...
```


Errors
------

If an error occures, the `ScreenshotError` exception is raised.


Examples
--------

One screenshot per monitor:

```python
for filename in screenshotter.save():
    print(filename)
```

Screenshot of the monitor 1:

```python
print(next(screenshotter.save(mon=1)))
```

Screenshot of the monitor 1, with callback:

```python
def on_exists(fname):
    ''' Callback example when we try to overwrite an existing
        screenshot.
    '''

    from os import rename
    from os.path import isfile

    if isfile(fname):
        newfile = fname + '.old'
        print('{0} -> {1}'.format(fname, newfile))
        rename(fname, newfile)
    return True

print(next(screenshotter.save(mon=1, callback=on_exists)))
```

A screenshot to grab them all:

```python
print(next(screenshotter.save(mon=-1, output='fullscreen.png')))
```

Example into the Python's console
---

```python
>>> from mss import mss
>>> sct = mss(display=b':0')

# Retrieve monitors informations
>>> displays = sct.enum_display_monitors()
>>> displays
[{'width': 1920, 'top': 0, 'height': 1080, 'left': 0}, {'width': 1920, 'top': 0, 'height': 1080, 'left': 0}]
# You can access monitors list via `monitors`:
>>> sct.monitors
[{'width': 1920, 'top': 0, 'height': 1080, 'left': 0}, {'width': 1920, 'top': 0, 'height': 1080, 'left': 0}]

# Retrieve pixels from the first monitor
>>> pixels = sct.get_pixels(displays[1])
>>> pixels
<ctypes.c_char_Array_6220800 object at 0x7fe82e9007a0>
# You can access pixels data via `image`:
>>> sct.image
<ctypes.c_char_Array_6220800 object at 0x7fe82e9007a0>

# Save pixels to a PNG file: option 1
>>> files = sct.save(mon=1)
>>> next(files)
'monitor-1.png'
>>> next(files)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration

# Save pixels to a PNG file: option 2
>>> mon = displays[1]
>>> sct.to_png(data=pixels, width=mon[b'width'], height=mon[b'height'], output='monitor-1.png')
```

---

API
===

**enum_display_monitors** => list of dicts

```python
>>> enum_display_monitors(force=False)
''' Get positions and dimensions of monitors.
    If `force` is set to `True`, it will rescan for monitors informations.
    It stocks monitors informations into `monitors` and returns it.
    `monitors[0]` is a dict of all monitors together
    `monitors[N]` is a dict of the monitor N (with N > 0)
'''
```

**get_pixels** => array of ctypes.c_char

```python
>>> get_pixels(monitor)
''' Retrieve screen pixels for a given monitor.
    `monitor` is a dict generated by `enum_display_monitors()`.
    It stocks pixels data into `image` (RGB) and returns it.
'''
```

**save** => generator

```python
>>> save(mon=0, output='monitor-%d', callback=lambda *x: True)
''' Grab a screenshot and save it to a file.

    `mon` is an integer:
        -1: grab one screenshot of all monitors
         0: grab one screenshot by monitor
         N: grab the screenshot of the monitor N

    `output` is a string:
        The output filename.
        %d, if presents, will be replaced by the monitor number.

    `callback` is a method:
        Callback called before saving the screenshot to a file.
        Takes `output` argument as parameter.

    This is a generator which returns created files.
'''
```

**to_png**

```python
>>> to_png(data, width, height, output)
''' Dump raw `data` into PNG `output` file. '''
```
