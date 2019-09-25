移植
=======

**将现有的基于 PIL 的代码移植到 Pillow**

Pillow 是对 Python Imaging Library 的功能性替代。要想用 Pillow 运行现有的与 PIL 兼容的代码，需要从 ``PIL`` 命名空间导入 ``Image`` 模块，而不是全局的命名空间。

修改: ::

    import Image

为: ::

    from PIL import Image

:py:mod:`_imaging` 模块已经被移除。可以用以下方式导入::

    from PIL.Image import core as _imaging

图像插件加载机制已更改。Pillow 不再自动导入 Python 路径中名称以 :file:`ImagePlugin.py` 结尾的任何文件。你将需要手动导入图像插件。

如果由于任何原因（包括 Python 和扩展程序代码之间版本不匹配）无法加载核心扩展程序，Pillow 将会引发异常。之前，如果核心扩展不可用，则 PIL 仅允许 Python 代码运行。