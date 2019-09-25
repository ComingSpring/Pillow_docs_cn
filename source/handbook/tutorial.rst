教程
========

使用Image类
---------------

Python Imaging Library中最重要的类是 :py:class:`~PIL.Image.Image`，该类定义在同名的模块中。你可以通过多种方式创建该类的实例；通过从文件加载图像，处理其他图像，或者从头创建图像。

为了从文件加载图像，使用 :py:mod:`~PIL.Image` 模块中的 :py:func:`~PIL.Image.open` 函数::

    >>> from PIL import Image
    >>> im = Image.open("hopper.ppm")

如果成功，这个函数返回一个 :py:class:`~PIL.Image.Image` 对象。你现在可以使用属性实例检查文件内容::

    >>> from __future__ import print_function
    >>> print(im.format, im.size, im.mode)
    PPM (512, 512) RGB

:py:attr:`~PIL.Image.Image.format` 属性识别图像的来源。如果图像不是从文件中读取，该属性被设置为 None。:py:attr:`~PIL.Image.Image.size` 属性是一个包含宽度和高度(以像素为单位)的元组。:py:attr:`~PIL.Image.Image.mode` 属性定义图像中频段的数量和名称，以及像素类型和深度。常见模式: 灰度图的模式为 “L”，真彩色图的模式为 “RGB”，预打印图的模式为 “CMYK”。

若文件打不开，则引发 :py:exc:`IOError` 异常。

一旦有了一个 :py:class:`~PIL.Image.Image` 类的实例，就可以使用该类定义的方法来处理和操作图像。例如，展示刚加载的图像::

    >>> im.show()

.. note::

    标准版本的 :py:meth:`~PIL.Image.Image.show` 效率不高，因为它将图像保存到临时文件并调用实用程序来显示图像。如果你没有安装适当的实用程序，它甚至将无法工作。然而当它能工作时，它对于调试和测试非常方便。

以下各节概述了此库中提供的各种功能。

读写图像
--------------------------

Python Imaging Library支持多种图像文件格式。要从磁盘中读取文件，请使用 :py:mod:`~PIL.Image` 模块中的 :py:func:`~PIL.Image.open` 函数。无需知道文件的格式即可打开文件。该库根据文件的内容自动确定格式。

要保存文件，请使用 :py:mod:`~PIL.Image` 模块中的 :py:func:`~PIL.Image.save` 函数。保存文件时，名称变得很重要。除非指定格式，否则库将使用文件扩展名来决定要使用哪种文件存储格式。

将文件转换为JPEG
^^^^^^^^^^^^^^^^^^^^^

::

    from __future__ import print_function
    import os, sys
    from PIL import Image

    for infile in sys.argv[1:]:
        f, e = os.path.splitext(infile)
        outfile = f + ".jpg"
        if infile != outfile:
            try:
                Image.open(infile).save(outfile)
            except IOError:
                print("cannot convert", infile)

可以给 :py:meth:`~PIL.Image.Image.save` 方法提供第二个参数，以显式指定文件格式。如果使用非标准扩展名，则必须始终以这种方式指定格式:

创建JPEG缩略图
^^^^^^^^^^^^^^^^^^^^^^

::

    from __future__ import print_function
    import os, sys
    from PIL import Image

    size = (128, 128)

    for infile in sys.argv[1:]:
        outfile = os.path.splitext(infile)[0] + ".thumbnail"
        if infile != outfile:
            try:
                im = Image.open(infile)
                im.thumbnail(size)
                im.save(outfile, "JPEG")
            except IOError:
                print("cannot create thumbnail for", infile)

值得注意的一点是，除非确实需要，否则库不会解码或加载光栅数据。当打开一个文件时，将读取文件头以确定文件格式，并提取诸如模式、大小以及其他对解码文件所需的属性之类的信息，但是文件的剩余内容随后才会处理。

这意味着打开图像文件是一个快速操作，与文件大小和压缩类型无关。下面是一个简单的脚本，用以快速识别一组图像文件:

识别图像文件
^^^^^^^^^^^^^^^^^^^^

::

    from __future__ import print_function
    import sys
    from PIL import Image

    for infile in sys.argv[1:]:
        try:
            with Image.open(infile) as im:
                print(infile, im.format, "%dx%d" % im.size, im.mode)
        except IOError:
            pass

剪切、粘贴和融合图像
------------------------------------

:py:class:`~PIL.Image.Image` 类包含一些方法，这些方法让你可以操纵图像中的区域。 要从图像中提取子矩形，请使用 :py:meth:`~PIL.Image.Image.crop` 方法。

从图像中复制子矩形
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    box = (100, 100, 400, 400)
    region = im.crop(box)

区域由一个四元组定义，坐标为(左，上，右，下)。Python Imaging Library使用的坐标系的左上角为 (0,0)。另外请注意，坐标指的是像素之间的位置，因此上面的例子中的区域恰好是 300x300 像素。

现在可以用某种方式处理该区域并将其粘贴回去。

处理子矩形，并将其粘贴回去
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    region = region.transpose(Image.ROTATE_180)
    im.paste(region, box)

当将区域粘贴回去的时候，区域的大小必须与给定区域完全匹配。另外，该区域不能延伸到图像之外。然而，原始图像和区域的模式不需要匹配。如果不匹配，在粘贴之前区域会被自动转换。(详细信息，请参考 :ref:`颜色变换` )

下面是另一个例子：

滚动图像
^^^^^^^^^^^^^^^^

::

    def roll(image, delta):
        """Roll an image sideways."""
        xsize, ysize = image.size

        delta = delta % xsize
        if delta == 0: return image

        part1 = image.crop((0, 0, delta, ysize))
        part2 = image.crop((delta, 0, xsize, ysize))
        image.paste(part1, (xsize-delta, 0, xsize, ysize))
        image.paste(part2, (0, 0, xsize-delta, ysize))

        return image

对于更高级的技巧， :py:meth:`~PIL.Image.Image.paste` 方法可以将透明掩码作为可选参数。在此掩码中，255 表示粘贴的图像在该位置是不透明的（也就是说，粘贴的图像应按原样使用）。0 表示粘贴的图像是完全透明的。0-255 之间的值表示不同级别的透明度。例如，粘贴一个 RGBA 图像并将其用作掩码，将会粘贴图像的不透明部分，但不粘贴其透明背景。

Python Imaging Library还允许你使用多频段图像（例如RGB图像）的各个频段。 :py:meth:`~PIL.Image.Image.split` 方法将创建一组新图像，每个图像包含原始多频段图像中的一个频段。 :py:meth:`~PIL.Image.Image.merge` 函数采用一种模式，将一组图像组合成一个新图像。下面的例子交换了 RGB 图像的三个频段：

拆分和合并频段
^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    r, g, b = im.split()
    im = Image.merge("RGB", (b, g, r))

注意，对于单频段的图像， :py:meth:`~PIL.Image.Image.split` 返回图像本身。要使用各个频段，你可能需要先将图像转换为 “RGB” 模式。

几何变换
----------------------

:py:class:`PIL.Image.Image` 类包含重新调整图像大小的 :py:meth:`~PIL.Image.Image.resize` 方法和旋转图像的 :py:meth:`~PIL.Image.Image.rotate` 方法。前者参数为指定新尺寸的元组，后者参数为逆时针旋转的角度。

简单的几何变换
^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    out = im.resize((128, 128))
    out = im.rotate(45) # degrees counter-clockwise

要以 90 度为单位旋转图像，可以使用 :py:meth:`~PIL.Image.Image.rotate` 方法或者 :py:meth:`~PIL.Image.Image.transpose` 方法。后者还可用于围绕图像的水平或垂直轴翻转图像。

转置图像
^^^^^^^^^^^^^^^^^^^^

::

    out = im.transpose(Image.FLIP_LEFT_RIGHT)
    out = im.transpose(Image.FLIP_TOP_BOTTOM)
    out = im.transpose(Image.ROTATE_90)
    out = im.transpose(Image.ROTATE_180)
    out = im.transpose(Image.ROTATE_270)

也可以用 :py:meth:`~PIL.Image.Image.rotate` 来实现与 ``transpose(ROTATE)`` 相同的操作，``expand`` 标志设置为True，用于提供对图像大小的相同的修改。

通过 :py:meth:`~PIL.Image.Image.transform` 方法执行更加通用形式的图像变换。

.. _颜色变换:

颜色变换
----------------

Python Imaging Library允许你使用 :py:meth:`~PIL.Image.Image.convert` 方法在不同像素表示之间转换图像。

模式之间的转换
^^^^^^^^^^^^^^^^^^^^^^^^

::

    from PIL import Image
    im = Image.open("hopper.ppm").convert("L")

该库支持每种受支持的模式与 “L” 和 “RGB” 模式之间的转换。要在其他模式之间转换，必须使用中间图像（通常是 “RGB” 模式图像）。

图像增强
-----------------

Python Imaging Library提供了许多可用于增强图像的方法和模块。

滤波器
^^^^^^^^^

:py:mod:`~PIL.ImageFilter` 模块包含许多可以与 :py:meth:`~PIL.Image.Image.filter` 方法一起使用的预定义增强滤波器。

应用滤波器
~~~~~~~~~~~~~~~~

::

    from PIL import ImageFilter
    out = im.filter(ImageFilter.DETAIL)

点操作
^^^^^^^^^^^^^^^^

:py:meth:`~PIL.Image.Image.point` 方法可用于转换图像的像素值（例如，图像对比度操纵）。大多数情况下，需要一个参数的函数对象可以传递给该方法。根据该函数处理每个像素：

应用点变换
~~~~~~~~~~~~~~~~~~~~~~~~~

::

    # multiply each pixel by 1.2
    out = im.point(lambda i: i * 1.2)

使用上述技术，你可以快速将任何简单表达式应用于图像。你还可以结合 :py:meth:`~PIL.Image.Image.point` 和 :py:meth:`~PIL.Image.Image.paste` 方法来选择性地修改图像：


处理各个频段
~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    # split the image into individual bands
    source = im.split()

    R, G, B = 0, 1, 2

    # select regions where red is less than 100
    mask = source[R].point(lambda i: i < 100 and 255)

    # process the green band
    out = source[G].point(lambda i: i * 0.7)

    # paste the processed band back, but only where red was < 100
    source[G].paste(out, None, mask)

    # build a new multiband image
    im = Image.merge(im.mode, source)

注意创建掩码的语法::

    imout = im.point(lambda i: expression and 255)

Python根据确定结果的必要，仅评估逻辑表达式的部分，并返回最后检查的值作为表达式的结果。因此，如果上面的表达式为 false(0)，Python 不会查看第二个操作数，因此返回 0。否则返回 255。

增强
^^^^^^^^^^^

要进行更高级的图像增强，可以使用 :py:mod:`~PIL.ImageEnhance` 中的类。一旦从图像中创建，可以使用增强对象快速尝试不同的设置。

你可以通过这种方式调整对比度，亮度，色彩平衡和清晰度。

增强图像
~~~~~~~~~~~~~~~~

::

    from PIL import ImageEnhance

    enh = ImageEnhance.Contrast(im)
    enh.enhance(1.3).show("30% more contrast")

图像序列
---------------

Python Imaging Library包含对图像序列（也称为动画格式）的一些基本支持。支持的格式包含 FLI/FLC，GIF和一些实验性格式。TIFF 文件也可以包含多帧。

当打开序列文件时，PIL 自动加载序列的第一帧。你可以使用 :py:func:`~PIL.Image.seek` 和 :py:func:`~PIL.Image.tell` 方法在不同帧之间移动：

读取序列
^^^^^^^^^^^^^^^^^

::

    from PIL import Image

    im = Image.open("animation.gif")
    im.seek(1) # skip to the second frame

    try:
        while 1:
            im.seek(im.tell()+1)
            # do something to im
    except EOFError:
        pass # end of sequence

如本例所示，当序列结束时，你将受到 :py:exc:`EOFError` 异常。

下面的类可以让你使用 for 语句遍历序列：

使用ImageSequence Iterator类
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    from PIL import ImageSequence
    for frame in ImageSequence.Iterator(im):
        # ...do something to frame...


Postscript 打印
-------------------

Python Imaging Library包含用于在 Postscript 打印机上打印图像，文本和图形的函数。下面是一个简单示例：

绘制Postscript
^^^^^^^^^^^^^^^^^^

::

    from PIL import Image
    from PIL import PSDraw

    im = Image.open("hopper.ppm")
    title = "hopper"
    box = (1*72, 2*72, 7*72, 10*72) # in points

    ps = PSDraw.PSDraw() # default is sys.stdout
    ps.begin_document(title)

    # draw the image (75 dpi)
    ps.image(box, im, 75)
    ps.rectangle(box)

    # draw title
    ps.setfont("HelveticaNarrow-Bold", 36)
    ps.text((3*72, 4*72), title)

    ps.end_document()

更多关于读取图像
----------------------

如前所述， :py:mod:`~PIL.Image` 模块中的 :py:func:`~PIL.Image.open` 函数用于打开图像文件。大多数情况下，只需将文件名作为参数传递给它：::

    from PIL import Image
    im = Image.open("hopper.ppm")

如果一切顺利，结果是一个 :py:class:`PIL.Image.Image` 对象。否则引发 :exc:`IOError` 异常。

你可以使用类似文件的对象代替文件名。该对象必须实现 :py:meth:`~file.read`, :py:meth:`~file.seek` 和 :py:meth:`~file.tell` 方法，并以二进制模式打开。

从打开的文件中读取
^^^^^^^^^^^^^^^^^^^^^^^^^

::

    from PIL import Image
    with open("hopper.ppm", "rb") as fp:
        im = Image.open(fp)

要想从字符串数据中读取图像，使用 :py:class:`~StringIO.StringIO` 类：

从字符串中读取
^^^^^^^^^^^^^^^^^^^^^

::

    from PIL import Image
    import StringIO
    im = Image.open(StringIO.StringIO(buffer))

注意，在读取图像头之前，库会倒退文件（使用 ``seek(0)``）。另外，在读取图像数据时，也可以使用 seek。如果图像文件嵌在较大的文件中（比如 tar 文件），你可以使用 :py:class:`~PIL.ContainerIO` 或者 :py:class:`~PIL.TarIO` 模块来访问它。

从tar文档中读取
^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    from PIL import Image, TarIO

    fp = TarIO.TarIO("Tests/images/hopper.tar", "hopper.jpg")
    im = Image.open(fp)

控制解码器
-----------------------

一些解码器允许你在从文件中读取图像时对其进行操作。在创建缩略图（通常速度比质量重要）和打印到单色激光打印机（仅需要灰度版本的图像）时，
通常可以使用它来加速解码。

:py:meth:`~PIL.Image.Image.draft` 方法处理已经打开但尚未加载的图像，因此它尽可能匹配给定的模式和大小。这是通过重新配置图像解码器来完成的。

以 draft 模式读取
^^^^^^^^^^^^^^^^^^^^^

仅适用于 JPEG 和 MPO 文件。

::

    from PIL import Image
    from __future__ import print_function
    im = Image.open(file)
    print("original =", im.mode, im.size)

    im.draft("L", (100, 100))
    print("draft =", im.mode, im.size)

打印信息如下::

    original = RGB (512, 512)
    draft = L (128, 128)

注意，生成的图像可能与要求的模式和尺寸不完全匹配。为确保图像不大于给定的大小，请使用 :py:func:`~PIL.Image.thumbnail` 方法。