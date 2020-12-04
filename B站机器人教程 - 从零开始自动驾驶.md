# B站机器人教程 - 从零开始自动驾驶

## 01--05

### 1， Introduction

目标：用gazebo模拟一个自动驾驶

### 2， 环境搭建

安装ros，安装gazebo（略）

### 3， 空白世界

##### 3,1 用命令行打开gazebo

```bash
$ gazebo
```

有 ground， sun

##### 3, 2 用ros的方式打开gazebo

~~~bash
$ roslauch gazebo_ros empty_world.launch
~~~

##### 3,3 查看launch文件里的内容

~~~bash
$ roscd gazebo_ros
$ cd launch 
$ vi empty_world.launch
~~~

### 4， 往世界里放一个立方体

urdf文件，统一机器人描述文件

##### 4, 1 创建一个workspace

~~~bash
$ cd catkin_ws
$ mkdir src
$ catkin_make
~~~

##### 4,2 把东西放到src目录里面去

<u>从04_intro_to_urdf文件夹拷贝到src目录里面</u>

##### 4,3 创建package

~~~bash
$ catkin_create_pkg box
~~~

##### 4,4 把下载下来的launch和urdf文件夹拷贝到box里面

##### 4,5 看效果

~~~bash
# compile
$ cd ~/temp/catkin_ws
$ catkin_make

# run gazebo
$ roslaunch gazebo_ros empty_world.launch

# new terminal 
$ cd ~/temp/catkin_ws
$ source devel/setup.bash
$ roslaunch box spawn_box_urdf.launch
~~~

gazebo中可以看到多了一个方块

（之后解释了urdf文件以及xacro文件）

### 5， 基本小车模型

##### 5,1 新建package， car_model

~~~bash
$ catkin_create_pkg car_model
~~~

##### 5,2 将05_basic_car里的launch和urdf文件拷贝到car_model文件夹里面

~~~bash
$ catkin_make
$ source devel/setup.bash
$ roslaunch car_model spawn_car.launch
~~~



## 06 美化小车

用blender画的小车

和往常一样，将github文件夹覆盖到car_model里面

运行一个空白世界，运行spawn_car之后看到gazebo里面的小车了

gazebo里面可以修改小车的后轮速度，看看小车在空白世界里行驶；



## 07 用ros topic控制车轮

### !!!bug： 打开gazebo，出现Err REST.cc: 205 Error in REST request

参考csdn上的blog.csdn.net/thkfighter/article/details/90214525

### !!!bug： 出现failed to load ...

根据视频下面的置顶评论，安装对应的包https://www.bilibili.com/video/BV1J7411F75T

## 08 控制速度与方向

本次我们要实现gazebo里通过rqt_gui调试车的速度与方向

 根据以往的方法：

* 打开空白新世界
* 打开小车模型

首先，查看一下现有的topic：

~~~bash
$ rostopic list
~~~

从中找到`/smart/cmd_vel`topic，它是`geometry_msg/Twist`类型的。

之后，试图用`rqt_gui`工具发布消息给`/smart/cmd_vel`topic：

> 首次打开rqt_gui之后什么都不显示，用plugin->topics->message publisher启动topic发布方法；
>
> 从topic中选择/smart/cmd_vel， 点击`+`号。分别配置linear的x，以及angular的z；
>
> 勾选复选框，发布topic，可以让gazebo里的小车移动

代码在`car_model/cmdvel2gazebo.py`，可以看看具体的解释

### !!!bug： gazebo启动空白世界的时候出现没有pid的错误

在`car_model/config/smart_control_config.yaml`中的最后有一段注释代码，是配置pid的代码。

去掉这段代码之后，小车启动或者停止的时候，变化是逐渐变化。

## 09 获取小车定位于速度

导入文件夹

> 从09_simple_localization中把所有东西复制到car_model里面，里面的东西除了CMakeLists和package.xml全部都删掉

打开新世界

~~~bash
# terminal 1
$ roslaunch gazebo_ros empty_world.launch
~~~

运行spawn car

~~~bash
# terminal 2
$ roslaunch car_model spawn_car.launch
~~~

查看所有topic，多了几个topic

1. /smart/center_pose:  中心点的位置
2. /smart/rear_pose： 后轴中心点的位置
3. /smart/volocity： 小车的速度

~~~bash
# terminal 3
$ rostopic echo /smart/center_pose
~~~

center_pose：

position:

x, y, z

orientation:

x, y, z, w： roll pitch yaw的另一种表示方式；参考<u>哔哩哔哩四元数的链接</u>

velocity： 线速度，角速度（Twist）

怎么获取定位信息？

~~~bash
$ rostopic echo /gazebo/model_states
~~~

> name: [ground_planne, smart]
>
> pose:
>
> twist:



查看代码`vehicle_pose_and_velocity_updater.py`

1. 新建一个类，里面发布三个topic：/smart/center_pose, /smart/rear_pose, /smart/velocity;
2. 订阅一个topic： /gazebo/model_states, 调用一个函数
3. 用`tf.transformations.euler_from_quaternion`将小车位置的四元组转换为航向角yaw
4. 发布center_pose
5. 发布rear_pose；通过center pose与rear_pose之间的距离和刚刚得到的yaw计算
6. 发布速度(TwistStampede())

## 10 可视化工具rviz的使用

### rviz是什么？

ros系统可视化工具，相当于一个节点，去订阅很多topic，并显示出来，对原有的系统不会产生影响

  rviz和gazebo的区别：

* rviz相当于一个订阅的结点，它对ros系统没有影响，只是将ros显示而已
* gazebo是一个物理引擎，会模拟真实世界

打开rviz之后是不显示小车的，需要打开smart.rviz之后才显示；

左边栏可以看到`smart` model，可以删除之后再添加。

这个时候可以注意到Robot Description参数是robot_description，这是来自于参数服务器上来。

### 调用关系

spawn_car.launch中包含了spawn_xacro.launch，spawn_xacro.launch中有一个param参数robot_description， 调用了spawn_car.launch中的arg参数urdf_robot_file， 这个调用了urdf/smart.xacro文件，这个是小车模型



### 用rqt_gui调试

rqt_gui选择`/smart/cmd_vel`， 配置参数`linear:x`, `angular:z`之后，可以在rviz中让汽车轮子转起来了

但是不会往前走，因为坐标系还是原来的base_link，需要用一个world全局坐标

> 从launch/config.launch中，去掉最后一行的注释，就可以在rviz中跑了。具体是将gazebo中的center_pose实时发布出来，让两个坐标系一致



## 11 path tracking （1）将全局路径点显示到rviz上

删掉`car_model`级文件夹之后，将github里的三个文件夹复制到src目录里面，将`self-driving-vehicle-101`文件夹复制到catkin_ws文件夹里，与`build`, `devel`, `src`同一级别

* styx_msgs: 一种消息类型，自己定义的
* waypoint_loader: 一个加载全局路径点的结点

步骤：

1. empty_world
2. spawn_car.launch
3. **roslaunch waypoint_loader waypoint_loader.launch**
4. rviz
5. 添加path，选择topic： /base_path

这样在rviz看到我们的全局路径了

### 代码解释

全局路径信息是通过运行waypoint_loader包里的waypoint_loader.launch文件之后运行的

> 该文件首先运行了scripts/waypoint_loader.py，建立一个节点，定义了两个param
>
> * path： waypoints/waypoints.csv，这里的提前规划好的路径点
> * velocity： 10km/h

### !!!question

waypoints.csv格式，waypoint_loader.py的格式转换方法

## 12 path tracking（2）找到向前一段距离，发布到topic

目标： 把小车的前一路径距离提取出来发布到topic

步骤：

1. empty world
2. spawn_car.launch
3. source devel/setup.bash, rviz
4. roslaunch waypoint_loader waypoint_loader.launch
5. roslaunch waypoint_updater waypoint_updater.launch

这个时候应该能看见rviz里的小车路径前一小部分是粗的，如果没有，查看报的错

很可能是waypoint_updater/scripts/waypoint_updater.py中调用了scipy，但是ubuntu环境没有安装。

我试图用pip安装，但是没成功，

最终用ubuntu命令去安装

~~~bash
$ sudo apt-get install python-scipy
~~~

从路径中找到最近点的index， 在它上面加上前视距离（lookahead_wps）作为下一个跟踪的点，

20Hz的频率去更新上一个步骤

小技巧：从waypoints中找点的时候，用的是KDtree，这样一次初始化，waypoint中的点很多的时候查找速度会非常快

## 13 path tracking（3）pure pursuit

具体算法解释根据 [知乎专栏](https://zhuanlan.zhihu.com/p/48117381)

运行代码步骤：

1. self-driving-vehicle-101文件夹复制到catkin_ws中
2. 第13个文件夹复制到src中
3. catkin_make
4. source devel/setup.bash, roslaunch gazebo_ros empty_world.launch
5. source devel/setup.bash, roslaunch car_model spawn_car.launch
6. source devel/setup.bash, rviz
7. source devel/setup.bash, roslaunch pure_pursuit pure_pursuit.launch



