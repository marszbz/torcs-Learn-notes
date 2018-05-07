# torcs-Learn-notes
torcs-Learn notes
Torcs介绍手册

1.	Torcs是什么
TORCS是一个赛车游戏，也是一个开源的赛车仿真模拟器

2.	Gym_torcs是什么
Gym_torcs是基于torcs的扩展
Gym-torcs是一个模仿Open-AI接口的TORCS的python封装，用于在TORCS上测试增强学习算法。
（就是说你自己的车开的要比你对手快，然后调整算法，这里用的是增强学习算法，应该还能用别的，暂时没研究。）

3.	Gym_torcs安装
Ctrl+Alt+t 打开终端 （前四步及五的第一步为安装包，后面才是正式配置编译gym_torcs）
3.1	安装pip
sudo apt-get install python-pip
sudo pip install --upgrade pip
3.2	安装python 3
3.3	安装xautomation
sudo apt-get install xautomation
3.4	安装OpenAI-Gym 
sudo apt-get install -y python-numpy python-dev cmake zlib1g-dev libjpeg-dev xvfb libav-tools xorg-dev python-opengl libboost-all-dev libsdl2-dev swig
3.5	安装Gym_torcs
3.5.1	sudo apt-get install libglib2.0-dev libgl1-mesa-dev libglu1-mesa-dev freeglut3-dev libplib-dev libopenal-dev libalut-dev libxi-dev libxmu-dev libxrender-dev libxrandr-dev libpng12-dev 
3.5.2	./configure
3.5.3	make
3.5.4	sudo make install
3.5.5	sudo make datainstall

4.	Gym_torcs操作
4.1	如何训练车
在Gym_torcs(客户端 – 服务器模块)仅支持两种模式：
1）练习模式(一次允许单个机器人竞赛)
2）快速竞赛模式(允许多个机器人竞争)
4.1.1	打开终端1，输入sudo torcs
Quick Race -> Configure Race(选择赛道，车辆，圈数及是否直接显示训练结果)(可省略) -> 点击New Race，服务器等待
4.1.2	打开终端2
 
4.2	赛前配置
1）选择赛道
2）选择比赛参与者
3）确定圈数及公里数
4）正常模式/选择直接显示结果
注：2中，TORCS中有几种类型的汽车具有不同的功能，您需要确保相同类型汽车才能对抗。请注意，scr_server使用car1-trb1，而使用同一辆车的其他机器是：tita 3、berniw 3、olethros 3、lliaw 3、inferno 3、bt 3
在游戏GUI界面配置/直接编辑xml配置文件

5.	Gym_torcs架构概述
torcs的架构：C/S模式（机器人作为外部进程运行，通过UDP连接连接到比赛服务器进行测试）其中机器人被编译为单独的模块，当发生比赛时被加载到主内存中。机器人和模拟引擎之间没有分离，机器人可以完全访问定义轨道和比赛当前状态的所有数据结构。因此，每个机器人可以使用不同的信息来实现其驾驶策略。
gym_torcs在三个方面扩展了原始的TORCS体系结构。
首先，它将TORCS构建为客户端 - 服务器应用程序：机器人作为外部进程运行，通过UDP连接连接到比赛服务器。 
第二，它实时增加：每个游戏tic（大致对应于20ms的模拟时间），服务器将当前感觉输入发送到每个机器人，然后等待10ms（实时）从机器人接收动作。 如果没有动作到达，则继续进行模拟，并使用上一次执行的动作。 
最后，竞赛软件在驱动程序代码和竞赛服务器之间建立物理上的分离，构建抽象层，传感器和执行器模型，（i）给出了用于机器人的编程语言的完全自由选择，（ii）限制只能访问设计师定义的信息。
 
比赛软件的架构如图所示。游戏引擎与原始的TORCS相同，主要修改在于scr-server，它可以管理游戏与client-bot（你写的测试代码）之间的UDP连接。
服务器是通过提供一种名为scr服务器的特定机器人驱动程序开发的，该服务器不是具有自己的智能，而是将游戏状态发送到客户端模块并等待回复，即由控制器（包含在客户端中）执行的动作。

比赛涉及每个客户端的server-bot; 每个服务器机器人在比赛服务器的单独端口上侦听。
一开始，每个client-bot将其自身识别为相应的server-bot建立连接。然后，随着比赛开始，每个server-bot将当前的感官信息发送给其客户端，并等待动作，然后服务器更新比赛的状态，发送回客户端。
Ps：客户端可以通过向服务器发送特殊操作来请求比赛重新启动。

