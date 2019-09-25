安装
====

警告
----

.. warning:: Pillow 和 PIL不能共存。在安装 Pillow 之前，请先卸载 PIL。

.. warning:: Pillow >= 1.0不再支持 "import Image"。请用"from PIL import Image"代替。

.. warning:: Pillow >= 2.1.0 不再支持"import _imaging"。请用"from PIL.Image import core as _imaging"代替。

注解
--------

.. note:: 以下 Python 版本支持 Pillow

+---------------------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+
|**Python**           |**2.4**|**2.5**|**2.6**|**2.7**|**3.2**|**3.3**|**3.4**|**3.5**|**3.6**|**3.7**|
+---------------------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+
|Pillow < 2.0.0       |  Yes  |  Yes  |  Yes  |  Yes  |       |       |       |       |       |       |
+---------------------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+
|Pillow 2.x - 3.x     |       |       |  Yes  |  Yes  |  Yes  |  Yes  |  Yes  |  Yes  |       |       |
+---------------------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+
|Pillow 4.x           |       |       |       |  Yes  |       |  Yes  |  Yes  |  Yes  |  Yes  |       |
+---------------------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+
|Pillow 5.0.x - 5.1.x |       |       |       |  Yes  |       |       |  Yes  |  Yes  |  Yes  |       |
+---------------------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+
|Pillow 5.2.x - 5.4.x |       |       |       |  Yes  |       |       |  Yes  |  Yes  |  Yes  |  Yes  |
+---------------------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+
|Pillow 6.x           |       |       |       |  Yes  |       |       |       |  Yes  |  Yes  |  Yes  |
+---------------------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+
|Pillow >= 7.0.0      |       |       |       |       |       |       |       |  Yes  |  Yes  |  Yes  |
+---------------------+-------+-------+-------+-------+-------+-------+-------+-------+-------+-------+

基本安装
--------

.. note::

    以下指导将会安装支持大多数常见图像格式的 Pillow 库,请参阅 :ref:`外部库` 以获取支持的外部库的完整列表。

使用 :command:`pip` 安装 Pillow::

    $ pip install Pillow

在Windows下安装
^^^^^^^^^^^^^^^^^^

对于 Windows，我们提供了wheel，egg，exe类型的 Pillow 二进制文件，这些二进制文件针对受支持的 Python 编译了32位和64位版本。除了 raqm 和 libimagequant，这些二进制文件包含了所有的可选库::

  > pip install Pillow

在mac操作系统下安装
^^^^^^^^^^^^^^^^^^^^^

对于 mac 操作系统，我们提供了受支持的 Python 的 wheel 格式的二进制文件。除了 libimagequant，这些二进制文件包含所有可选库。要获得 Raqm 支持，必须单独安装 libraqm，fribidi 和 harfbuzz::

  $ pip install Pillow

在Linux上安装
^^^^^^^^^^^^^^^^

对于 Linux，我们针对每个受支持的 Python 提供了 manylinux wheel 格式的二进制文件。除了 libimagequant，这些二进制文件包含所有可选库。要获得 Raqm 支持，必须单独安装 libraqm，fribidi 和 harfbuzz::

  $ pip install Pillow

大多数主要的 Linux 发行版，包括 Fedora，Debian / Ubuntu 和 ArchLinux，在之前包含 PIL 的包中也包含 Pillow。比如 ``python-imaging``。

在FreeBSD上安装
^^^^^^^^^^^^^^^

在 FreeBSD 上，可以通过官方的 Ports 或者 Packages 系统安装 Pillow。

**Ports**::

  $ cd /usr/ports/graphics/py-pillow && make install clean

**Packages**::

  $ pkg install py27-pillow

.. note::

    `Pillow FreeBSD port <https://www.freshports.org/graphics/py-pillow/>`_ 和所有的包已由 ports 团队针对所有受支持的 FreeBSD 版本和 Python 2.7 和 3.x 进行了测试。

从源代码编译
----------------

下载并提取 `compressed archive from PyPI`_.

.. _compressed archive from PyPI: https://pypi.org/project/Pillow/

.. _外部库:

外部库
^^^^^^^^^

.. note::

    为了使用 Pillow 的基本功能，你 **不需要** 安装所有受支持的外部。默认情况下需要 **Zlib** 和 **libjpeg**。

平台支持
----------

旧版本
----------

你可以从 `PyPI发行历史 <https://pypi.org/project/Pillow/#history>`_ 下载旧的发行版，或者直接通过 URL 链接，比如 https://pypi.org/project/Pillow/1.0/。