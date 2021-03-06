并行学习指南
=======================

这是一篇 LightGBM 的并行学习教程.

点击 `快速入门 <./Quick-Start.rst>`__ 来学习怎样使用 LightGBM.

选择合适的并行算法
-------------------------------------

LightGBM 现已提供了以下并行学习算法.

+--------------------------+---------------------------+
| **Parallel Algorithm**   | **How to Use**            |
+==========================+===========================+
| Data parallel            | ``tree_learner=data``     |
+--------------------------+---------------------------+
| Feature parallel         | ``tree_learner=feature``  |
+--------------------------+---------------------------+
| Voting parallel          | ``tree_learner=voting``   |
+--------------------------+---------------------------+

这些算法适用于不同场景,如下表所示:

+-------------------------+----------------------+----------------------+
|                         | **#data is small**   | **#data is large**   |
+=========================+======================+======================+
| **#feature is small**   | Feature Parallel     | Data Parallel        |
+-------------------------+----------------------+----------------------+
| **#feature is large**   | Feature Parallel     | Voting Parallel      |
+-------------------------+----------------------+----------------------+

在 `optimization in parallel learning <./Features.rst#optimization-in-parallel-learning>`__ 了解更多并行算法的细节.

构建并行版本
----------------------

默认的并行版本支持基于 socket 的并行学习.

如果你想构建基于 MPI 的并行版本, 请参考 `安装指南 <./Installation-Guide.rst#build-mpi-version>`__.

准备工作
-----------

Socket 版本
^^^^^^^^^^^^^^

它需要收集所有想要运行并行学习的机器的所有 IP 并且指定一个 TCP 端口号 (假设是 12345 ) ,
更改防火墙使得这个端口可以被访问 (12345). 然后把这些 IP 和端口写入一个文件中 (假设是 ``mlist.txt``), 如下所示:

.. code::

    machine1_ip 12345
    machine2_ip 12345

MPI 版本
^^^^^^^^^^^

它需要收集所有想要运行并行学习机器的 IP (或 hostname) .
然后把这些IP写入一个文件中 (例如 ``mlist.txt``) 如下所示:

.. code::

    machine1_ip
    machine2_ip

**Note**: 对于 windows 用户, 需要安装 "smpd" 来开启 MPI 服务. 更多细节点击 `here`_.

运行并行学习
---------------------

Socket 版本
^^^^^^^^^^^^^^

1. 在配置文件中编辑以下参数:

   ``tree_learner=your_parallel_algorithm``, 在这里编辑 ``your_parallel_algorithm`` (e.g. feature/data) .

   ``num_machines=your_num_machines``, 在这里编辑 ``your_num_machines`` (e.g. 4) .

   ``machine_list_file=mlist.txt``, ``mlist.txt`` 在 `准备工作 <#preparation>`__ 生成.

   ``local_listen_port=12345``, ``12345`` 在 `准备工作 <#preparation>`__ 分配.

2. 拷贝数据文件, 可执行文件, 配置文件和 ``mlist.txt`` 到所有机器.

3. 在所有机器上运行以下命令, 你需要更改 ``your_config_file`` 为真实配置文件.

   Windows: ``lightgbm.exe config=your_config_file``

   Linux: ``./lightgbm config=your_config_file``

MPI 版本
^^^^^^^^^^^

1. 在配置中编辑以下参数:

   ``tree_learner=your_parallel_algorithm``, 在这里编辑 ``your_parallel_algorithm`` (e.g. feature/data) .

   ``num_machines=your_num_machines``, 在这里编辑 ``your_num_machines`` (e.g. 4) .

2. 拷贝数据文件, 可执行文件, 配置文件和 ``mlist.txt`` 到所有机器.

   **Note**: MPI 需要运行在 **所有机器的相同路径上**.

3. 在机器上运行以下命令 (不需要运行所有机器), 需要更改 ``your_config_file`` 为真实的配置文件.

   Windows:
   
   .. code::

       mpiexec.exe /machinefile mlist.txt lightgbm.exe config=your_config_file

   Linux:

   .. code::

       mpiexec --machinefile mlist.txt ./lightgbm config=your_config_file

例子
^^^^^^^

-  `A simple parallel example`_

.. _here: https://blogs.technet.microsoft.com/windowshpc/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program/

.. _A simple parallel example: https://github.com/Microsoft/lightgbm/tree/master/examples/parallel_learning
