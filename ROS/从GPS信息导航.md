[参考博客地址](https://answers.ros.org/question/12663/gps-navigation/#:~:text=The%20ROS%20navigation%20stack%20provides,the%20odometry%20required%20for%20navigation.)

导航需要

* sensor stream
* odometry information
* transform

GPS只是一部分odometry。

我们的问题：

1. 需要`nav_msgs/Odometry`消息，从`gps_common`节点将Lat/Long对转化为Odometry。
2. `nav_msgs/Odometry`还需要`geometry_msgs/Twist`，
3. `robot_pose_ekf`方法融合多个传感器

# Node的理解

roscore 

查看所有node： rosnode list

启动一个node： rosrun <package-name> <node-name>

例如：rosrun turtlesim turtlesim_node

> /turtlesim

也可以重新命名你的节点名字

ping来查看节点的链接情况：rosnode ping <node-name>

# Topic的理解

启动两个节点，turtlesim_node和turtlesim_teleop_key；

分别是乌龟节点和控制乌龟节点，乌龟节点订阅名为/turtle1/cmd_vel的话题，如果别的节点把消息发布的这个话题，马上，乌龟节点会做出反应。这里teleop_turtle节点向话题发布了消息。

![rqt_graph_turtle_key.png](http://wiki.ros.org/ROS/Tutorials/UnderstandingTopics?action=AttachFile&do=get&target=rqt_graph_turtle_key.png)

新建节点订阅一个消息: rostopic echo <topic name>

查看所有topic： rostopic list

实际传输的是msg，而不是topic；

rostopic pub <topic> <msg type> <args>

其中带-1和-r参数

## 编写publisher与subscriber

roscore 

1. 记得publisher和subscriber最上面加上 #!/usr/bin/env python

2. 写好py程序，给运行权限

3. 在CMakeLists中注册

4. workspace中重新catkin_make
5. source ~/ros_ws/devel/setup.bash

- [ ] 怎么收数据？

