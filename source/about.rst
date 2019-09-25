关于Pillow
===================

目标
----------

分支作者的目标是通过以下方式促进和支持 PIL 积极发展：

- 通过 `Travis CI`_ 和 `AppVeyor`_ 进行持续集成测试
- 在 `GitHub`_ 上公开的开发活动
- 定期发布到 `Python Package Index`_

.. _Travis CI: https://travis-ci.org/python-pillow/Pillow
.. _AppVeyor: https://ci.appveyor.com/project/Python-pillow/pillow
.. _GitHub: https://github.com/python-pillow/Pillow
.. _Python Package Index: https://pypi.org/project/Pillow/

许可
-------

同 PIL 一样，Pillow 许可为 `open source PIL Software License <https://raw.githubusercontent.com/python-pillow/Pillow/master/LICENSE>`_

为什么建立分支
------------------

PIL 和 setuptools 不兼容。详细解释请参看 `this Image-SIG post`_ 。另外，PIL 当前每两年（或更长时间）一次的发行频率太低，不能容纳报告问题的数量和频率。。

.. _this Image-SIG post: https://mail.python.org/pipermail/image-sig/2010-August/006480.html

PIL现状
---------------

.. note::

    在 Pillow 2.0.0 之前，很少更改图像代码。Pillow 2.0.0 增加了对 Python 3 的支持，并包括许多贡献者提交的错误修复。

距离上一个 PIL 版本（2009年的 1.1.7 ）已有相当长的时间，发行新版本的 PIL 的可能性也随时间降低。但是，我们尚未听到官方宣称要终结 PIL。因此，如果你任然希望支持 PIL，首先 `报告问题`_，然后开放 `Pillow tickets`_。

.. _报告问题: https://bitbucket.org/effbot/pil-2009-raclette/issues

.. _Pillow tickets: https://github.com/python-pillow/Pillow/issues

请提供第一个 ticket 的链接以方便我们在上游跟踪问题。