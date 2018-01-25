当前系统环境：
```
HP-ProDesk-480-G2-MT-TPM:~$ lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 14.04.5 LTS
Release:	14.04
Codename:	trusty
```
查看当前系统中有什么版本的python:
```
HP-ProDesk-480-G2-MT-TPM:~$ ls /usr/bin/python*
/usr/bin/python   /usr/bin/python2.7         /usr/bin/python2-config  /usr/bin/python3.4   /usr/bin/python3m
/usr/bin/python2  /usr/bin/python2.7-config  /usr/bin/python3         /usr/bin/python3.4m  /usr/bin/python-config

```

使用alternatives  机制：
执行下面的命令
```
sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 100
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 150
```

执行下面的命令手动去选择python 版本：
 sudo update-alternatives --config python
```
HP-ProDesk-480-G2-MT-TPM:~$ sudo update-alternatives --config python
有 2 个候选项可用于替换 python (提供 /usr/bin/python)。

  选择       路径            优先级  状态
------------------------------------------------------------
  0            /usr/bin/python3   150       自动模式
* 1            /usr/bin/python2   100       手动模式
  2            /usr/bin/python3   150       手动模式

要维持当前值[*]请按回车键，或者键入选择的编号：

```
查询当前设置完毕的python版本：
```
HP-ProDesk-480-G2-MT-TPM:~$ python --version
Python 2.7.6

```
