# launch学习

node属性

（1）pkg=“mypackage”
节点的package，相当于 rosrun 命令后面的第一个参数。

（2）type=“nodetype”
rosrun 命令的第二个参数；节点类型。必须有一个同名的对应可执行文件。

（3）name=“nodename”
该节点的名字，相当于代码中 ros::init() 中设置的信息，有了它代码中的名称会被覆盖。注意：名称不能包含命名空间。请改用ns属性。

（4）args=“arg1 arg2 arg3”(optional)
启动参数，将参数传递给节点。

（5）machine=“machine-name”(optional, see )
在指定的计算机上启动节点。

（6）respawn=“true”(optional, default: False)
如果退出，则自动重新启动节点。

（7）respawn_delay=“30” (optional, default 0) New in ROS indigo
如果reshawn为true，则在检测到节点故障后等待reshawn_delay秒，然后再尝试重新启动。

（8）required=“true”(optional)
如果节点终止，终止整个roslaunch。

（9）ns=“foo”(optional)
在“foo”命名空间中启动节点。

（10）clear_params=“true/false”(optional)
在启动之前删除节点私有命名空间中的所有参数。

（12）output=“log/screen”(optional)
如果“screen”，则节点的stdout/stderr将发送到屏幕。
如果是“log”，则stdout/stderr输出将被发送到$ros_home/log中的日志文件，并且stderr将继续发送到屏幕。默认值为“log”。

（13）cwd=“ROS_HOME/node”(optional)
如果是“node”，则该节点的工作目录将设置为与该节点的可执行文件相同的目录。

（14）launch-prefix=“prefix arguments”(optional)
在节点的启动参数前添加命令/参数。这是一个强大的特性，使您能够启用gdb、valgrind、xterm、nice或其他方便的工具。

