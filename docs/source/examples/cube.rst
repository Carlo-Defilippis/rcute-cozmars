Cozmars 的玩伴——魔方
=======================

连接魔方
----------

魔方是独立于 Cozmars 机器人的配件，要控制魔方或者获取魔方的传感器数据，需要与魔方建立连接。方法和 :class:`Robot` 十分类似。

按下电源开关后，首先导入 :class:`rcute_cozmars.Cube`

.. code:: python

    from cozmars import Cube

.. raw:: html

    <style> .red {color:#E74C3C;} </style>

.. role:: red

然后与魔方建立连接，可以使用 :red:`with` 语法：

.. code:: python

    from rcute_cozmars import Cube
    import time

    # 假设魔方的序列号是 556a，你需要把它换成你的魔方的序列号！
    with Cube('556a') as cube:
        cube.color = 'red' # 亮红灯3秒钟
        time.sleep(3)

也可以显示地调用 :meth:`connect` 和 :meth:`disconnect` 方法来建立和断开连接：


    >>> from rcute_cozmars import Cube
    >>> cube = Cube('556a')
    >>> cube.connet()
    >>> cube.color = 'red'
    >>> cube.disconnect()  # 最后记得断开连接


.. note::

    魔方也是同时只能与一个程序连接

几个属性
---------------

我们可以通过几个简单的属性或方法来控制魔方或查询魔方的状态：

- :data:`color` 属性能查询或改变魔方 LED 的 GBR 颜色
- :data:`static` 属性用来指示魔方是否在静止
- :data:`last_action` 属性可以查询魔方的上一个动作
- :data:`top_face` 当魔方静止时，该属性用来查询魔方哪个面朝上，返回值是朝上一面的二维码对应的颜色，当魔方不是静止状态时返回 `None`

..
    - :data:`acc` 属性用来查询魔方的加速度/重力的矢量


手势识别
-----------

魔方内置运动传感器，支持丰富的手势识别，对应有以下许多不同的回调函数：

- :data:`when_flipped` 在魔方被翻转90度或180度时调用（带有角度参数）
- :data:`when_pushed` 在魔方被平移时调用（带有方向参数，用颜色表示）
- :data:`when_rotated` 在魔方被顺/逆时针旋转时调用（带有方向参数）
- :data:`when_shaked` 在魔方被摇晃时调用
- :data:`when_tilted` 在魔方倾斜时调用（带有方向参数，用颜色表示）
- :data:`when_tapped` 在轻敲魔方时调用
- :data:`when_fall` 在魔方失重/自由落体时调用
- :data:`when_moved` 在魔方被移动时调用（包括以上动作）
- :data:`when_static` 在魔方恢复静止时调用

下面的程序分别连接魔方和 Cozmars 机器人，当魔方顺时针转动时让机器人右转，当魔方逆时针转动时让机器人左转：

.. code:: python

    from rcute_cozmars import Cube, Cozmars

    with Cube('556a') as cube, Cozmars('0a3c') as robot:

        def turn(direction):
            if direction == 'CW': # 顺时针旋转
                robot.turn_right(2)
            elif direction == 'CCW': # 逆时针旋转
                robot.turn_left(2)

        cube.when_rotated = turn
        input('回车结束程序')

再看一个例子，使用 tilted 倾斜手势：

.. code:: python

    from rcute_cozmars import Cube, Cozmars

    with Cube('556a') as cube, Cozmars('0a3c') as robot:

        # 当魔方向不同颜色的面倾斜时，机器人做出不同的动作
        def move_robot(dir):
            if dir == 'red':
                robot.head.angle = 20
            elif dir == 'green':
                robot.head.angle = -20
            elif dir == 'blue':
                robot.lift.height = 1
            elif dir == 'yellow':
                robot.lift.height = 0

        cube.when_tilted = move_robot
        input('回车结束程序')

.. note::

    看到了吧，魔方和 Cozmars 的序列号并不是同一个!
    以上程序分别与 Cozmars 和 魔方都建立了连接


.. seealso::

    `rcute_cozmars.Cube <../api/cube.html>`_