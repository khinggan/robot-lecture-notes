[TOC]
# Beginner level  tutorial（初学者ROS教程）

## 1. Installing and Configuring Ros Environment（安装与环境配置）
根据[Installing tutorial](http://wiki.ros.org/Installation/Ubuntu)选择适合自己ubuntu系统的ROS版本（我的是ubuntu18.04LTS，所以选了Melodic）

安装注意事项：

> 1. ubuntu下载的时候有很多镜像，我选了东北大学镜像
> 2. 安装好ubuntu（无论是双系统还是虚拟机），在 软件更新 里选择源为aliyun；

### 1.1 Install ROS（安装ROS）

原文在[Installing tutorial](http://wiki.ros.org/Installation/Ubuntu)，

安装前，根据[Stack Overflow](https://askubuntu.com/questions/141512/how-to-resolve-failed-to-download-repository-information#:~:text=If%20you%20still%20have%20issues,the%20Select%20Best%20Server%20button.&text=Now%20try%20updating%2Fupgrading%20again.)所示博客，运行完三条命令就可以安装了。不然可能找不到ros安装文件

1. 安装源，或者点击下面的mirror，安装国内的镜像源，如清华镜像源

```bash
$ sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
```

2. 设置秘钥

```bash
$ sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
```

3. apt update

```bash
$ sudo apt update
```

4. 安装ros对应版本

```bash
$ sudo apt install ros-melodic-desktop-full
```

### 1.2 Managing Your Environment（配置环境变量）

将环境变量写到.bashrc里面，每次开机运行

```bash
$ echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc
$ source ~/.bashrc
```

其中，安装好之后需要单独安装rosdep

```bash
$ sudo rosdep init
$ rosdep update
```

注意：

安装过程中第一个命令出现问题，请参考[博客](https://blog.csdn.net/u013468614/article/details/102917569)，或者根据[博客](https://zhuanlan.zhihu.com/p/77483614)第四部分，手动建立目录以及文件，将内容复制到文件中

第二个命令出现问题，请参考[博客](https://blog.csdn.net/qq_38649880/article/details/87903654?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param)

如果更新时候出现网络问题，切换到手机热点

### 1.3 Create a ROS Workspace（新建ROS catkin工作区域）

create a *catkin*  workspace
```bash
$ mkdir -p ~/catkin_ws/src
$ cd ~/catkin_ws/
$ catkin_make
```

------



## 2 Navigating the ROS Filesystem（ROS文件系统的介绍）

一些ROS专有命令介绍以及文件结构的介绍

首先，进入ros workspace，并激活该workspace

```bash
cd ~/catkin_ws
source devel/setup.bash
```


以下是几个常见的ROS命令：rospack, roscd, roscd log, rosls, 

### rospack

rospack可以获取package的信息；这里只测试find命令

```bash
$ rospack find roscpp
/opt/ros/melodic/share/roscpp
```

### roscd

roscd是bash的cd命令的扩展版

```
$ roscd roscpp
$ pwd
/opt/ros/melodic/share/roscpp
```

### roscd log

进入日志所在目录

### rosls

rosls是bash的ls的扩展版



------



## 3. Creating a ROS Package（新建ROS package）

### 3.1 What makes up a catkin Package?（catkin package由什么组成？）

常说的package是catkin package，他们是由以下部分组成

* package.xml

* CMakeLists.txt
* own folder
```
my_package/
  CMakeLists.txt
  package.xml
```

### 3.2 catkin workspace（catkin工作区域）

经常工作的不是catkin package，而是workspace；workspace 的包结构如下

```
workspace_folder/        -- WORKSPACE
  src/                   -- SOURCE SPACE
    CMakeLists.txt       -- 'Toplevel' CMake file, provided by catkin
    package_1/
      CMakeLists.txt     -- CMakeLists.txt file for package_1
      package.xml        -- Package manifest for package_1
    ...
    package_n/
      CMakeLists.txt     -- CMakeLists.txt file for package_n
      package.xml        -- Package manifest for package_n
```

### 3.3 Creating a catkin Package（创建catkin package）

1. cd to workspace src folder
```bash
$ cd ~/catkin_ws/src
```
2. use `catkin_create_pkg` to create a catkin package

   命令模式：catkin_create_pkg name [dependencies [dependencies ...]]

```bash
$ catkin_create_pkg beginner_tutorials std_msgs rospy roscpp
```



------



## 4. Building Packages（自己创建ros package）
1. create workspace folder and src folder
2. catkin_make

以上两种方式可以在文件夹里面生成ros package；

运行完命令之后会有 build  devel  src三个文件夹



------



## 5. ROS Nodes（ROS节点）

### 5.1 Quick overview of Graph Concepts（ROS的一些基本概念）
* Nodes: A node is an executable that uses ROS to communicate with other nodes.

* Messages: ROS data type used when subscribing or publishing to a topic.

* Topics: Nodes can publish messages to a topic as well as subscribe to a topic to receive messages.

* Master: Name service for ROS (i.e. helps nodes find each other)

* rosout: ROS equivalent of stdout/stderr

* roscore: Master + rosout + parameter server (parameter server will be introduced later)

  Node ----------message------------> topic                 (publish)
  Node<---------message--------------topic                  (subscribe)

### 5.2 Nodes（节点）
ROS package 里的一个可执行文件
Node可以使用或者提供一个服务（Service）

### 5.3 Client Libraries（自带的库）
rospy
roscpp

### 5.4 roscore
roscore是你用ros的时候第一个运行的程序，用于启动ros
```
$ roscore
... logging to ~/.ros/log/9cf88ce4-b14d-11df-8a75-00251148e8cf/roslaunch-machine_name-13039.log
Checking log directory for disk usage. This may take awhile.
Press Ctrl-C to interrupt
Done checking log file disk usage. Usage is <1GB.

started roslaunch server http://machine_name:33919/
ros_comm version 1.4.7

SUMMARY
======

PARAMETERS
 * /rosversion
 * /rosdistro

NODES

auto-starting new master
process[master]: started with pid [13054]
ROS_MASTER_URI=http://machine_name:11311/

setting /run_id to 9cf88ce4-b14d-11df-8a75-00251148e8cf
process[rosout-1]: started with pid [13067]
started core service [/rosout]
```
### 5.5 rosnode
另外开终端，打开同一个workspace之后运行
```
$ rosnode list
```
查看正在运行的node；这里可以看到（/rosout)



应用系统自带的node

rosrun [package_name] [node_name]

```
$ rosrun turtlesim turtlesim_node
```



------



## 6. ROS Topics（ROS 话题）

ROS topics: rostopic, rqt_plot

### 6.1 用方向键控制小乌龟
1. start up the roscore
```bash
$ roscore
```

2. run a turtlesim in new terminal
```bash
$ rosrun turtlesim turtlesim_node
```

3. control turtle using keyboard
```bash
$ rosrun turtlesim turtle_teleop_key
```
用上下左右键控制小乌龟的方向

### 6.2 ROS topics

之前的turtlesim_node node和turtlesim_teleop_key node分别是两个节点，他们通过ROS topic进行交互；

1. rqt_graph： 显示当前运行的nodes 和 topics

```bash
$ rosrun rqt_graph rqt_graph
```

显示当前运行的nodes和topics

> 拿turtlesim_teleop_key 节点和turtlesim_node节点之间的通讯举例，/teleop_turtle node publish message on /turtle1/cmd_velocity topic which subscribed by the /turtlesim node

2. rostopic
`rostopic` 命令的其他参数可以获取topic的信息

```bash
$ rostopic
bw  echo  find  hz  info  list  pub  type
```
3. rostopic有很多命令，list，echo等等

新打开一个terminal

```bash
$ rostopic list
/rosout
/rosout_agg
/turtle1/cmd_vel
/turtle1/color_sensor
/turtle1/pose
```
rostopic list会列出所有的topic名字；

rostopic的其他命令加topic名字操作

rostopic echo [topic]
```bash
$ rostopic echo /turtle1/com_vel
```
之后topic上的都显示出来

### 6.3 ROS Message
Publisher: turtle_teleop_key
Subscriber: turtlesim_node

Publisher和Subscriber发布和接受同一类型的message，意味着topic的类型是由message类型决定的



查看一个topic中发送和接收的message的类型，也是topic类型

```bash
$ rostopic type /turtle1/cmd_vel 
geometry_msgs/Twist
```



显示一类message的信息

```bash
$ rosmsg show geometry_msgs/Twist 
geometry_msgs/Vector3 linear
  float64 x
  float64 y
  float64 z
geometry_msgs/Vector3 angular
  float64 x
  float64 y
  float64 z
```



### 6.4 rostopic continued（发布具体message）

1. 用rostopic pub命令，在当前topic上发布一个message

rostopic pub [topic] [msg_type] [args]

```bash
$ rostopic pub -1 /turtle1/cmd_vel geometry_msgs/Twist -- '[2.0, 0.0, 0.0]' '[0.0, 0.0, 1.8]'
```
在topic /turtle1/cmd_vel上发布一个geometry_msgs/Twist类型的message '[2.0, 0.0, 0.0]' '[0.0, 0.0, 1.8]'

-1: 只发布一个并退出



也可以发布连续的message，需要带-r参数

```bash
$ rostopic pub /turtle1/cmd_vel geometry_msgs/Twist -r 1 -- '[2.0, 0.0, 0.0]' '[0.0, 0.0, -1.8]'
```



------



## 7. ROS Service and Parameters（ROS 服务与参数）

重点介绍ROS Service和ROS Parameters，并且介绍rosservice, rosparam两个命令行工具



### 7.1 ROS Service（ROS服务）

> Service is Another way that nodes can communicate with each other. 
> Service allow nodes to send **request** and receive a **response**

Service是node之间交流的另一种方式，可以给另一个node发送request，并对request回复一个response


### 7.2 Using rosservice

`rosservice`可以很容易实现ROS的client/server模式，它的具体命令有：

```bash
rosservice list         print information about active services
rosservice call         call the service with the provided args
rosservice type         print service type
rosservice find         find services by service type
rosservice uri          print service ROSRPC uri
```

1. rosservice list显示当前所有的service

```bash
$ rosservice list
/clear
/kill
/reset
/rosout/get_loggers
/rosout/set_logger_level
/spawn
/teleop_turtle/get_loggers
/teleop_turtle/set_logger_level
/turtle1/set_pen
/turtle1/teleport_absolute
/turtle1/teleport_relative
/turtlesim/get_loggers
/turtlesim/set_logger_level
```

2. rosservice call

```bash
$ rosservice call [service] [args]
```

调用该命令执行rosservice，比如

```bash
$ rosservice call /spawn 2 2 0.2 ""
```

### 7.3 Using rosparam

rosparam有很多命令，包括 set， get等等；

```bash
rosparam set            set parameter
rosparam get            get parameter
rosparam load           load parameters from file
rosparam dump           dump parameters to file
rosparam delete         delete parameter
rosparam list           list parameter names
```



1. list： 列出所有node可用的rosparam

```bash
$ rosparam list
/rosdistro
/roslaunch/uris/host_nxt__43407
/rosversion
/run_id
/turtlesim/background_b
/turtlesim/background_g
/turtlesim/background_r
```

turtlesim有三个参数，background_r, background_g, background_b；

可以通过set， get命令改变他们的值

2. set&get

```bash
rosparam set [param_name]
rosparam get [param_name]
```

```bash
$ rosparam get /turtlesim/background_g 
$ rosparam set /turtlesim/background_r 150
```

melodic好像要重启乌龟程序

```bash
$ rosrun turtlesim turtlesim_node
```

之后才可以看到背景色的变化

3. dump&load

将参数写进yaml文件或者从yaml文件获取参数

```bash
rosparam dump [file_name] [namespace]
rosparam load [file_name] [namespace]
```



------



## 8. rqt_console and roslaunch

首先介绍了`rqt_console`和`rqt_logger_level`命令来调试，之后介绍`roslaunch`命令启动多个节点（但是现在我还没有成功）

> This tutorial introduces ROS using rqt_console and rqt_logger_level for debugging and roslaunch for starting many nodes at once 

预先要安装好一些包

```bash
$ sudo apt-get install ros-<distro>-rqt ros-<distro>-rqt-common-plugins ros-<distro>-turtlesim 
```



rqt_console显示日志；rqt_console attaches to ROS's logging framework to display output from nodes 

rqt_logger_level 是不同等级的日志；rqt_logger_level allows us to change the verbosity level (DEBUG, WARN, INFO, and ERROR) of nodes as they run. 



新开两个terminal之后分别运行

```bash
$ rosrun rqt_console rqt_console
```

```bash
$ rosrun rqt_logger_level rqt_logger_level
```

logger级别是从Fatal到Debug下降的；Fatal has the highest priority and Debug has the lowest 

 Fatal
 Error
 Warn
 Info
 Debug 

## 9. ROS msg and srv（msg, srv)

### 9.1 msg 和 srv的介绍

msg文件： 描述ROS message的字段信息的txt文件；每行是 field type       field name

srv文件： 描述ROS service的文件

其中， field type（字段类型）包括

```
int8, int16, int32, int64 (plus uint*)
float32, float64
string
time, duration
other msg files
variable-length array[] and fixed-length array[C]
```

srv文件和msg文件类似，但是它是由两部分组成的： request 和 response，他们俩通过“--------”隔开

### 9.2 msg文件的使用

#### 创建msg

```bash
# 1. make a new catkin package    beginner_tutorial
$ cd ~/catkin_ws/src
$ catkin_create_pkg beginner_tutorials std_msgs rospy roscpp
新建一个catkin package，叫beginner_tutorials，它依赖std_msgs，rospy， roscpp
$ cd ~/catkin_ws/
$ catkin_make
build the catkin package


# 2. Write a new msg file
$ roscd beginner_tutorial
$ echo "int64 num" > msg/Num.msg
这个时候在msg/Num.msg里有一条msg了，类型和名字
```

### 9.3 使用rosmsg

`rosmsg`命令可以查看我已经建立的msg文件

> msg文件的特点是：
>
> * 在catkin package的msg文件夹里的.msg文件
> * 格式化的：msg类型    msg名字

```bash
$ rosmsg show Num

[beginner_tutorials/Num]:
int64 num
```

### 9.4 使用srv

比起自己新建一个srv文件，我们这里从rospy中用roscp命令复制了一个已有的文件来用，AddTwoInts.srv

之后修改package.xml，CMakeList.txt



之后， 可以用rossrv命令查看我们的srv是否已经建好了

```bash
$ rossrv show AddTwoInts
$ rossrv show AddTwoInts
[beginner_tutorials/AddTwoInts]:
int64 a
int64 b
---
int64 sum

[rospy_tutorials/AddTwoInts]:
int64 a
int64 b
---
int64 sum
```



### 9.5 Common Step for msg and srv

修改CMakeLists.txt

```bash
# generate_messages(
#   DEPENDENCIES
# #  std_msgs  # Or other packages containing msgs
# )
```

这部分**去掉**注释

之后到workspace根目录下重新build

```bash
$ cd ~/catkin_ws
$ catkin_make
```

如果出错了按照错误提示修改；

> 我这里在上面步骤中，没有正确修改CMakeLists.txt文件，导致build的时候出错了

## 10. Python写一个publisher & subscriber

分别下载好talker.py和listener.py文件，之后将他们分别给权限，修改CMakeLists.txt中的catkin_install_python的参数；

之后分别运行这两个script之后可以观察到publisher和subscriber了；

```bash
$ roscd beginner_tutorials
$ mkdir scripts
$ cd scripts

$ wget https://raw.github.com/ros/ros_tutorials/melodic-level/rospy_tutorials/001_talker_listener/talker.py
# 这里有不同的版本melodic还是其他，如果下载不了的话复制新建文件并粘贴也一样
$ chmod +x talker.py

# 同样方法得到listener.py
$ wget https://raw.github.com/ros/ros_tutorials/melodic-devel/rospy_tutorials/001_talker_listener/listener.py
$ chmod +x listener.py
```

之后到beginner_tutorial 目录里的CMakeLists.txt， 修改成

```bash
catkin_install_python(PROGRAMS scripts/talker.py scripts/listener.py
  DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)
```

最后搞这个

```bash
$ cd ~/catkin_ws
$ catkin_make
```

## 11. 运行Publisher&Subscriber

开启ros

```bash
$ roscore
```

启动workspace

```bash
# In your catkin workspace
$ cd ~/catkin_ws
$ source ./devel/setup.bash
```

运行talker.py，生成publisher

```bash
$ rosrun beginner_tutorials talker      (C++)
$ rosrun beginner_tutorials talker.py   (Python) 
[INFO] [WallTime: 1314931831.774057] hello world 1314931831.77
[INFO] [WallTime: 1314931832.775497] hello world 1314931832.77
[INFO] [WallTime: 1314931833.778937] hello world 1314931833.78
[INFO] [WallTime: 1314931834.782059] hello world 1314931834.78
[INFO] [WallTime: 1314931835.784853] hello world 1314931835.78
[INFO] [WallTime: 1314931836.788106] hello world 1314931836.79
```

运行listener.py，生成subscriber

```bash
$ rosrun beginner_tutorials listener     (C++)
$ rosrun beginner_tutorials listener.py  (Python) 
[INFO] [WallTime: 1314931969.258941] /listener_17657_1314931968795I heard hello world 1314931969.26
[INFO] [WallTime: 1314931970.262246] /listener_17657_1314931968795I heard hello world 1314931970.26
[INFO] [WallTime: 1314931971.266348] /listener_17657_1314931968795I heard hello world 1314931971.26
[INFO] [WallTime: 1314931972.270429] /listener_17657_1314931968795I heard hello world 1314931972.27
[INFO] [WallTime: 1314931973.274382] /listener_17657_1314931968795I heard hello world 1314931973.27
[INFO] [WallTime: 1314931974.277694] /listener_17657_1314931968795I heard hello world 1314931974.28
[INFO] [WallTime: 1314931975.283708] /listener_17657_1314931968795I heard hello world 1314931975.28
```



## 12. Python写一个Service & Client并验证

1. 和写Publisher&Susbscriber一样，在scripts文件夹中新建add_two_ints_server.py和add_two_ints_client.py，并将tutorial中的代码复制进去
2. 将权限改为可运行文件
3. 在CMakeLists.txt中将这两个文件加进去
4. catkin_make

首先，打开server；在新的终端上

```bash
$ source ~/catkin_ws/devel/setup.bash
$ rosrun beginner_tutorials add_two_ints_server.py
```



```bash
$ source ~/catkin_ws/devel/setup.bash
$ rosrun beginner_tutorials add_two_ints_client.py 1 3
```

client会传入参数

## 13. Recording and playing back data（实际是第17个tutorial）

* 从正在运行的ROS系统记录数据到.bag文件中
* 将.bag文件导入到ROS系统，运行ROS

### 13.1 Recording Data（将数据写进.bag文件）

这里的例子是保存turtlesim节点和turtle_teleop_key节点之间的信息

首先，在两个终端上打开这两个节点

```bash
$ roscore
```

```bash
$ rosrun turtlesim turtlesim_node
```

```bash
$ rosrun turtlesim turtle_teleop_key
```



之后，新打开一个终端，记录目前topic的信息

```bash
$ mkdir ~/bagfiles
$ cd ~/bagfiles
$ rosbag record -a
```

这样会新建一个名为时间戳的.bag文件，在turtle_teleop_key终端中操作记下之后，回到bag终端，用Ctrl+C停止，ls之后会看到这个文件

### 13.2 Examing and Playing（用.bag文件复现之前的轨迹）

用play命令复现.bag文件中的动作

```bash
$ rosbag play <bag-file>.bag
```

这样turtlesim_node上的小乌龟就按照刚才的操作执行了

### 13.3 只记录一部分topic的数据

有时候不是要用到所有的topic数据，这个时候把要记录的topic名称后加在命令后面

```bash
$ rosbag record TOPIC1 [TOPIC2 TOPIC3 ...]
```



