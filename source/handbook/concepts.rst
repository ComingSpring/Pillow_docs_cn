概念
========

Python Imaging Library处理 *光栅* 图像，也就是矩形像素数据。

.. _concept-bands:

频段
--------------

图像可以包含一个或多个频段的数据。 Python Imaging Library可让你在单个图像中存储多个频段，只要它们都具有相同的维度和深度。例如，对于红色，绿色，蓝色和 alpha 透明度值，PNG图像可能具有 ‘R’，‘G’，‘B’，‘A’ 频段。许多操作分别作用于每个频段，例如直方图。通常认为每个像素对于每个频段具有一个值。

要获取图像中频段的数量和名称，请使用 :py:meth:`~PIL.Image.Image.getbands` 方法。

.. _concept-modes:

模式
----------------

图像的 ``mode`` 定义了图像中像素的类型和深度。每个像素使用整个位深度范围。因此，1 位的像素的范围是 0-1，8 位像素的范围是 0-255，以此类推。当前版本支持以下标准模式：


    * ``1`` (1-bit 像素, 黑和白, 一个像素占用一个byte)
    * ``L`` (8-bit 像素, 黑和白)
    * ``P`` (8-bit 像素, 使用调色板映射到任何其他模式)
    * ``RGB`` (3x8-bit 像素, 真彩色)
    * ``RGBA`` (4x8-bit 像素, 带透明度掩码的真彩色)
    * ``CMYK`` (4x8-bit 像素, 分色)
    * ``YCbCr`` (3x8-bit 像素, 颜色视频格式)
    
      * 注意，这里指的是JPEG，不是ITU-R BT.2020标准

    * ``LAB`` (3x8-bit 像素, L*a*b 颜色空间)
    * ``HSV`` (3x8-bit 像素, 色相，饱和度，明度颜色空间)
    * ``I`` (32-bit 有符号整数像素)
    * ``F`` (32-bit 浮点像素)

Pillow 还为一些特殊模式提供有限的支持，包括：

    * ``LA`` (L with alpha)
    * ``PA`` (P with alpha)
    * ``RGBX`` (true color with padding)
    * ``RGBa`` (true color with premultiplied alpha)
    * ``La`` (L with premultiplied alpha)
    * ``I;16`` (16-bit unsigned integer pixels)
    * ``I;16L`` (16-bit little endian unsigned integer pixels)
    * ``I;16B`` (16-bit big endian unsigned integer pixels)
    * ``I;16N`` (16-bit native endian unsigned integer pixels)
    * ``BGR;15`` (15-bit reversed true colour)
    * ``BGR;16`` (16-bit reversed true colour)
    * ``BGR;24`` (24-bit reversed true colour)
    * ``BGR;32`` (32-bit reversed true colour)

然而，Pillow 不支持用户定义的模式；如果你需要处理上面未列出的频段的组合，请使用 Image 对象序列。

你可以通过 :py:attr:`~PIL.Image.Image.mode` 属性读取图像的模式。这是一个包含以上值之一的字符串。

尺寸
---------

你可以通过 :py:attr:`~PIL.Image.Image.size` 属性读取图像尺寸。这是一个2元组，包含以像素为单位的水平和垂直尺寸。

.. _coordinate-system:

坐标系统
-----------------

Python Imaging Library使用笛卡尔像素坐标系，左上角为（0，0）。注意，坐标是指像素角；地址为（0，0）的像素中心实际上位于（0.5，0.5）。

坐标通常以2元组(x，y)的形式传递给库。矩形用4元组表示，左上角在前。例如，将覆盖 800x600 像素图像的矩形写作（0，0，800，600）。

调色板
-------

调色板模式（``P``)使用调色板定义每个像素的实际颜色。

信息
--------

你可以使用 :py:attr:`~PIL.Image.Image.info` 属性将辅助信息附加到图像。它是一个字典对象。

加载和保存图像文件时，如何处理这类信息取决于文件格式处理程序（请参考 :ref:`image-file-formats`）。大多数处理程序在加载图像时将属性添加到 :py:attr:`~PIL.Image.Image.info` 属性，但在保存图像时将其忽略。

.. _concept-filters:

滤波器
------------

对于可能将多个输入像素映射到单个输出像素的几何运算，Python Imaging Library提供了不同的重采样 *滤波器* 。

``NEAREST``

    从输入图像中选择最近的像素，忽略所有其他输入像素。

``BOX``

    源图像的每个像素以相同的权重贡献到目标图像的一个像素。For upscaling is equivalent of ``NEAREST``。该滤波器只能与 :py:meth:`~PIL.Image.Image.resize` 和 :py:meth:`~PIL.Image.Image.thumbnail` 方法使用。

    .. versionadded:: 3.4.0

``BILINEAR``

    若要调整大小，对所有可能影响输出值的像素使用线性插值计算输出像素的值。对于其他变换，在输入图像中2x2邻域上进行线性插值。

``HAMMING``

    生成比 ``BILEAR`` 更清晰的图像，并且在局部不会有像 ``BOX`` 那样的错位。该滤波器只能与 :py:meth:`~PIL.Image.Image.resize` 和 :py:meth:`~PIL.Image.Image.thumbnail` 方法使用。

    .. versionadded:: 3.4.0

``BICUBIC``

    若要调整大小，对所有可能影响输出值的像素使用三次插值计算输出像素的值。对于其他变换，在输入图像中4x4邻域上进行三次插值。

``LANCZOS``

    对所有可能影响输出值的像素使用高质量的 Lanczos 滤波器计算输出像素的值。该滤波器只能与 :py:meth:`~PIL.Image.Image.resize` 和 :py:meth:`~PIL.Image.Image.thumbnail` 方法使用。

    .. versionadded:: 1.1.3


滤波器对比表
~~~~~~~~~~~~~~~~~~~~~~~~

+------------+-------------+-----------+-------------+
| Filter     | Downscaling | Upscaling | Performance |
|            | quality     | quality   |             |
+============+=============+===========+=============+
|``NEAREST`` |             |           | √√√√√       |
+------------+-------------+-----------+-------------+
|``BOX``     | √           |           | √√√√        |
+------------+-------------+-----------+-------------+
|``BILINEAR``| √           | √         | √√√         |
+------------+-------------+-----------+-------------+
|``HAMMING`` | √√          |           | √√√         |
+------------+-------------+-----------+-------------+
|``BICUBIC`` | √√√         | √√√       | √√          |
+------------+-------------+-----------+-------------+
|``LANCZOS`` | √√√√        | √√√√      | √           |
+------------+-------------+-----------+-------------+