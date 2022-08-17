### ***ROS2 Cheat Sheet***

---

#### References
1. [https://docs.ros.org/en/foxy/Tutorials.html](https://docs.ros.org/en/foxy/Tutorials.html)
2. [https://docs.ros2.org/latest/api/rclpy/index.html](https://docs.ros2.org/latest/api/rclpy/index.html)
3. [https://docs.ros.org/en/foxy/How-To-Guides/Launch-file-different-formats.html](https://docs.ros.org/en/foxy/How-To-Guides/Launch-file-different-formats.html)
4. [https://roboticsbackend.com/rclpy-params-tutorial-get-set-ros2-params-with-python](https://roboticsbackend.com/rclpy-params-tutorial-get-set-ros2-params-with-python/#Going_further_with_ROS2_params)

---

- [***ROS2 Cheat Sheet***](#ros2-cheat-sheet)
  - [References](#references)
  - [1. Environment](#1-environment)
  - [2. ROS2 Nodes](#2-ros2-nodes)
  - [3. ROS2 Topics](#3-ros2-topics)
  - [4. ROS2 Services](#4-ros2-services)
  - [5. ROS2 Parameters](#5-ros2-parameters)
  - [6. ROS2 Actions](#6-ros2-actions)
  - [7. ROS2 Launch](#7-ros2-launch)
  - [8. ROS2 Bags](#8-ros2-bags)
  - [9. Creating a Workspace](#9-creating-a-workspace)
  - [10. ROS2 Packages](#10-ros2-packages)
  - [11. Creating a Package](#11-creating-a-package)
  - [12. Creating a Publisher and a Subscriber in the Same Node](#12-creating-a-publisher-and-a-subscriber-in-the-same-node)
  - [13. Create Custom `msg` and `srv` Files](#13-create-custom-msg-and-srv-files)
  - [14. Using ROS2 parameters in Python](#14-using-ros2-parameters-in-python)

---

#### 1. Environment

> Ubuntu 20.04
> ROS2 Foxy 

#### 2. ROS2 Nodes

`$ ros2 node list`

`$ ros2 node info <node_name>`

`$ ros2 run <package_name> <executable_name>`  
`$ ros2 run <package_name> <executable_name> --ros-args --remap __node:=<my_node_name>`  
`$ ros2 run <package_name> <executable_name> --ros-args --params-file <file_name>`  

If some topics don't show up in `$ ros2 node list` use:

```bash
$ ros2 daemon stop
$ ros2 daemon start
```

#### 3. ROS2 Topics

`$ ros2 topic list`  
`$ ros2 topic list -t`  
`$ ros2 topic list --show-types`  

`$ ros2 topic info <topic_name>`  
`$ ros2 topic info -v <topic_name>`  
`$ ros2 topic info --verbose <topic_name>`  

`$ ros2 topic type <topic_name>`

`$ ros2 topic hz <topic_name>`

`$ ros2 topic echo <topic_name>`  
`$ ros2 topic echo --csv <topic_name>`

`$ ros2 topic pub <topic_name> <topic_type> <arguments>`  

If some topics don't show up in `$ ros2 topic list` use:

```bash
$ ros2 daemon stop
$ ros2 daemon start
```

#### 4. ROS2 Services

`$ ros2 service list`  
`$ ros2 service list -t`  
`$ ros2 service list --show-types`  

`$ ros2 service type <service_name>`  

`$ ros2 interface show <pkg_name>/srv/<service_type>`  
`$ ros2 interface show <service_type_name>.srv`  
Where: `<pkg_name>/srv/<service_type> = <service_type_name>`  

`$ ros2 service call <service_name> <service_type> <arguments>`  

#### 5. ROS2 Parameters

`$ ros2 param list`  

`$ ros2 param describe <node_name> <parameter_name>`  

`$ ros2 param get <node_name> <parameter_name>`  

`$ ros2 param set <node_name> <parameter_name> <value>`  

`$ ros2 param dump <node_name>`  

`$ ros2 param load <node_name> <parameter_file>`  

`$ ros2 run <package_name> <executable_name> --ros-args --params-file <file_name>`  

#### 6. ROS2 Actions

`$ ros2 action list`  
`$ ros2 action list -t`  
`$ ros2 action list --show-types`  

`$ ros2 action info <action_name>`  

`$ ros2 interface show <pkg_name>/action/<action_type>`  
`$ ros2 interface show <action_type_name>.action`  
Where: `<pkg_name>/action/<action_type> = <action_type_name>`  

`$ ros2 action send_goal <action_name> <action_type> <values>`  
`$ ros2 action send_goal <action_name> <action_type> <values> --feedback`  

#### 7. ROS2 Launch

- `$ ros2 launch <pkg_name> <launch_file_name>.launch.py`
- `$ ros2 launch <pkg_name> <launch_file_name>.launch.xml`
- `$ ros2 launch <pkg_name> <launch_file_name>.launch.yaml`

Using [Python vs. XML vs. YAML](https://docs.ros.org/en/foxy/How-To-Guides/Launch-file-different-formats.html) Launch files

#### 8. ROS2 Bags

`$ ros2 bag record <topic_name>`  
`$ ros2 bag record -o <output_file_name> <topic_name1> <topic_name2> <topic_nameN>`  
`$ ros2 bag record -o <output_file_name> -a` (all topics)  
If output bag name is not provided, it take the pattern: `rosbag2_year_month_day-hour_minute_second`  
`$ ros2 bag info <bag_file_name>`  
`$ ros2 bag play <bag_file_name>`  

#### 9. Creating a Workspace

1. Source the environment:  
   `$ source /opt/ros/$ROS_DISTRO/setup.bash`
2. Create a directory:  
   `$ mkdir -p ~/dev_ws/src && cd dev_ws/src`
3. Clone a repo of package(s) **OR** Create a package(s) from scratch (follow along)  
   `$ git clone https://github.com/ros/ros_tutorials.git -b foxy-devel`
4. Resolve Dependencies:  
   `$ rosdep install -i --from-path src --rosdistro $ROS_DISTRO -y`
5. Build the workspace with colcon:  
   `$ colcon build`
   - `--packages-up-to <pkg_name>`: builds the package you want, plus all its dependencies, not the whole workspace (to save time)
   - `--packages-select <pkg_name>`: build only the selected package(s)
   - `--symlink-install`: saves you from having to rebuild every time you tweak python scripts
   - `--event-handlers console_direct+`: shows console output while building (can otherwise be found in the log directory)
   ***<u>NOTE</u>*** : It’s good practice to run `rosdep` in the root of your workspace (`dev_ws`) to check for missing dependencies before building:
        ```bash
        # rosdep install -i --from-path <desired_path_to_check> --rosdistro $ROS_DISTRO
        $ rosdep install -i --from-path src --rosdistro foxy
        ```
6. Source the underlay (if it's not already sourced), then source the overlay  
   - ***underlay***: ROS2 installation
   - ***overlay***: current development workspace
     ```bash
     $ source /opt/ros/foxy/setup.bash  # if not already existing in .bashrc  
     $ cd ~/dev_ws
     $ . install/local_setup.bash  
     ```
     - You can modify and rebuild packages in the overlay separately from the underlay.
     - The overlay takes precedence over the underlay in case of name conflicts.
7. Modify the overlay  
   Modify $\longrightarrow$ Build (if **`src`** is modified) $\longrightarrow$ Source $\longrightarrow$ Run!

#### 10. ROS2 Packages

A ROS2 Package is a folder that contains your code in addition to specific files that define the folder as a package

- Python package
  - `package.xml` file containing meta information about the package
  - `setup.py` containing instructions for how to install the package
  - `setup.cfg` is required when a package has executables, so ros2 run can find them 
  - `/<package_name>` - a **Python package** with the same name as your ROS2 package, used by ROS2 tools to find your package, contains **`init.py`**
- CMake package
  - `package.xml` file containing meta information about the package
  - `CMakeLists.txt` file that describes how to build the code within the package

#### 11. Creating a Package
```bash
$ cd ~/dev_ws/src
```
- Python 
    ```bash
    $ ros2 pkg create --build-type ament_python <package_name>
    # optional: package with a specific node name
    $ ros2 pkg create --build-type ament_python --node-name <my_node_name> <my_package_name>
    ```
- CMake
    ```bash
    $ ros2 pkg create --build-type ament_cmake <package_name>
    # optional: package with a specific node name
    $ ros2 pkg create --build-type ament_cmake --node-name <my_node_name> <my_package_name>
    ```

#### 12. Creating a Publisher and a Subscriber in the Same Node
1. Create a package
   ```bash
   $ cd dev_ws/src
   # ros2 pkg create --build-type {cmake,ament_cmake,ament_python} <package_name>
   $ ros2 pkg create --build-type ament_python py_pubsub
   ```
2. Write the publisher and subscriber node
   ```bash
   # this directory is a Python package with the same name of the ROS2 package
   $ cd dev_ws/src/py_pubsub/py_pubsub
   $ touch publisher_subscriber_member_functions.py
   ```
   Edit the file to add the code
    2.1. **New Classes Method**
    ```python .numberLines
    # import ROS Python Client Library
    import rclpy                    

    # import Node class
    from rclpy.node import Node     

    # import string message type that will be used
    from std_msgs.msg import String

    # create a class that inherits Node class
    class MinimalPublisherSubscriber(Node):

        def __init__(self):
            # call the class' constructor and name it after my node
            super().__init__('minimal_publisher_subscriber')
            
            self.publisher_ = self.create_publisher(String, 'topic', 10)
            self.subscription = self.create_subscription(
                String, 'topic', self.listener_callback, 10)
            self.subscription  # prevent unused variable warning
            
            timer_period = 0.5  # seconds
            # create a timer that executes the cb fcn every <timer_period> seconds
            self.timer = self.create_timer(timer_period, self.timer_callback)
            self.i = 0

        def timer_callback(self):
            msg = String()
            msg.data = 'Hello World: %d' % self.i
            self.publisher_.publish(msg)
            self.get_logger().info('Publishing: "%s"' % msg.data)
            self.i += 1
        
        def listener_callback(self, msg):
            self.get_logger().info('I heard: "%s"' % msg.data)

    def main(args=None):
        rclpy.init(args=args)

        minimal_publisher_subscriber = MinimalPublisherSubscriber()

        rclpy.spin(minimal_publisher_subscriber)

        # Destroy the node explicitly
        # (optional - otherwise it will be done automatically
        # when the garbage collector destroys the node object)
        minimal_publisher_subscriber.destroy_node()
        rclpy.shutdown()

    if __name__ == '__main__':
        main()
    ```
    2.2. **Old School Method**
    ```python .numberLines
    import rclpy
    from std_msgs.msg import String
    from time import sleep

    g_node = None

    def chatter_callback(msg):
        global g_node
        g_node.get_logger().info(
            'I heard: "%s"' % msg.data)

    def main(args=None):
        global g_node
        rclpy.init(args=args)
        g_node = rclpy.create_node('minimal_subscriber')
        subscription = g_node.create_subscription(String, 'topic', chatter_callback, 10)
        subscription  # prevent unused variable warning
        publisher = g_node.create_publisher(String, 'topic', 10)
        msg = String()

        i = 0
        while rclpy.ok():
            msg.data = 'Hello World: %d' % i
            i += 1
            g_node.get_logger().info('Publishing: "%s"' % msg.data)
            publisher.publish(msg)
            sleep(0.5)  # seconds => 2 Hz

        # Destroy the node explicitly
        # (optional - otherwise it will be done automatically
        # when the garbage collector destroys the node object)
        g_node.destroy_node()
        rclpy.shutdown()

    if __name__ == '__main__':
        main()
    ```
3. Add an Entry Point
   - Navigate to your ROS2 package
        ```bash
        $ cd dev_ws/src/py_pubsub
        ```
   - Open `setup.py` and add the entry point for the node, the name of the entry point will be used to run the node later
        ```python .numberLines
        entry_points={
            'console_scripts': [
                'talker_listener = py_pubsub.publisher_subscriber_member_functions:main',
            ],
        },
        ```
4. Build and Run
   - Navigate to the root of your workspace
        ```bash
        $ cd dev_ws
        ```
   - Resolve dependencies
     It's good practice to run `rosdep` in the root of your workspace `(dev_ws)` to check for missing dependencies before building
        ```bash
        $ rosdep install -i --from-path src --rosdistro foxy -y
        ```
   - Build
        ```bash
        $ colcon build --packages-select py_pubsub
        ```
   - Source the setup files
        ```bash
        $ . install/setup.bash
        ```
    - Run
        ```bash
        $ ros2 run py_pubsub talker_listener
        ```
        The output should look like this ...
        ```bash
        ...
        [INFO] [1658726617.443270804] [minimal_publisher_subscriber]: Publishing: "Hello World: 2"
        [INFO] [1658726617.444563242] [minimal_publisher_subscriber]: I heard: "Hello World: 2"
        [INFO] [1658726617.943327820] [minimal_publisher_subscriber]: Publishing: "Hello World: 3"
        [INFO] [1658726617.944592867] [minimal_publisher_subscriber]: I heard: "Hello World: 3"
        [INFO] [1658726618.443164429] [minimal_publisher_subscriber]: Publishing: "Hello World: 4"
        [INFO] [1658726618.444557197] [minimal_publisher_subscriber]: I heard: "Hello World: 4"
        [INFO] [1658726618.943250358] [minimal_publisher_subscriber]: Publishing: "Hello World: 5"
        [INFO] [1658726618.944787046] [minimal_publisher_subscriber]: I heard: "Hello World: 5"
        [INFO] [1658726618.943250358] [minimal_publisher_subscriber]: Publishing: "Hello World: 6"
        [INFO] [1658726618.944787046] [minimal_publisher_subscriber]: I heard: "Hello World: 6"
        ...
        ```

#### 13. Create Custom `msg` and `srv` Files 
1. Create a CMake package 
   - There currently isn’t a way to generate a `.msg` or `.srv` file in a pure Python package. You can create a custom interface in a CMake package, and then use it in a Python node
        ```bash
        $ ros2 pkg create --build-type ament_cmake tutorial_interfaces
        ```
   - As a good practice, keep `.msg` and `.srv` files in their own directories within a package. 
     Create the directories in `dev_ws/src/tutorial_interfaces`:
        ```bash
        $ mkdir msg
        $ mkdir srv
        ```

2. Create custom definitions
   - `msg` definition
     ```bash
     $ cd dev_ws/src/tutorial_interfaces/msg
     $ touch Num.msg
     ```
     Edit the `Num.msg` file to add only one line of code declaring its data structure: 
     ```txt .numberLines
     int64 num
     ```
   - `srv` definition
     ```bash
     $ cd dev_ws/src/tutorial_interfaces/srv
     $ touch AddThreeInts.srv
     ```
     Edit the `AddThreeInts.srv` file to add the following request and response structure: 
     ```txt .numberLines
     int64 a
     int64 b
     int64 c
     ---
     int64 sum
     ```
     This is your custom service that requests three integers named a, b, and c, and responds with an integer called sum.

3. Edit `CMakeLists.txt`
   To convert the interfaces you defined into language-specific code (like C++ and Python) so that they can be used in those languages, add the following lines to `CMakeLists.txt`:
   ```cmake .numberLines
   find_package(rosidl_default_generators REQUIRED)
   
   rosidl_generate_interfaces(${PROJECT_NAME}
     "msg/Num.msg"
     "srv/AddThreeInts.srv"
   )
   ```
   <u>***NOTE***</u> : The library name must match `${PROJECT_NAME}`


<mark>**TODO** </mark>: using the newly generated messages and services

#### 14. Using ROS2 parameters in Python
We will use the code from [Creating a Publisher and a Subsciber](#12-creating-a-publisher-and-a-subscriber-in-the-same-node) to add some parameters

- **New Classes Method**
    ```python .numberLines
    # import ROS Python Client Library
    import rclpy                    

    # import Node class
    from rclpy.node import Node     

    # import string message type that will be used
    from std_msgs.msg import String

    # create a class that inherits Node class
    class MinimalPublisherSubscriber(Node):

        def __init__(self):
            # call the class' constructor and name it after my node
            super().__init__('minimal_publisher_subscriber')
            
            self.publisher_ = self.create_publisher(String, 'topic', 10)
            self.subscription = self.create_subscription(
                String, 'topic', self.listener_callback, 10)
            self.subscription  # prevent unused variable warning
            
            timer_period = 0.5  # seconds
            # create a timer that executes the cb fcn every <timer_period> seconds
            self.timer = self.create_timer(timer_period, self.timer_callback)
            self.i = 0

            # declare new parameters
            self.declare_parameter('my_str', 'default_value')
            self.declare_parameter('my_int', 7)
            self.declare_parameter('my_double_array', 1.253)
            
            # get parameters' values
            param_str = self.get_parameter('my_str').value
            param_int = self.get_parameter('my_int').value
            param_double_array = self.get_parameter('my_double_array').value
            self.get_logger().info("str: %s, int: %s, double[]: %s" %
                                   (str(param_str.value),
                                    str(param_int.value),
                                    str(param_double_array.value),))

        def timer_callback(self):
            msg = String()
            msg.data = 'Hello World: %d' % self.i
            self.publisher_.publish(msg)
            self.get_logger().info('Publishing: "%s"' % msg.data)
            self.i += 1
        
        def listener_callback(self, msg):
            self.get_logger().info('I heard: "%s"' % msg.data)

    def main(args=None):
        rclpy.init(args=args)

        minimal_publisher_subscriber = MinimalPublisherSubscriber()

        rclpy.spin(minimal_publisher_subscriber)

        # Destroy the node explicitly
        # (optional - otherwise it will be done automatically
        # when the garbage collector destroys the node object)
        minimal_publisher_subscriber.destroy_node()
        rclpy.shutdown()

    if __name__ == '__main__':
        main()
    ```
- **Old School Method**
    ```python .numberLines
    import rclpy
    from std_msgs.msg import String
    from time import sleep

    g_node = None

    def chatter_callback(msg):
        global g_node
        g_node.get_logger().info(
            'I heard: "%s"' % msg.data)

    def main(args=None):
        global g_node
        rclpy.init(args=args)
        g_node = rclpy.create_node('minimal_subscriber')
        subscription = g_node.create_subscription(String, 'topic', chatter_callback, 10)
        subscription  # prevent unused variable warning
        publisher = g_node.create_publisher(String, 'topic', 10)
        msg = String()
        
        # declare new parameters
        g_node.declare_parameter('my_str', 'default_value')
        g_node.declare_parameter('my_int', 7)
        g_node.declare_parameter('my_double_array', 1.253)
        
        # get parameters' values
        param_str = g_node.get_parameter('my_str').value
        param_int = g_node.get_parameter('my_int').value
        param_double_array = g_node.get_parameter('my_double_array').value
        g_node.get_logger().info("str: %s, int: %s, double[]: %s" %
                                (str(param_str.value),
                                str(param_int.value),
                                str(param_double_array.value),))

        i = 0
        while rclpy.ok():
            msg.data = 'Hello World: %d' % i
            i += 1
            g_node.get_logger().info('Publishing: "%s"' % msg.data)
            publisher.publish(msg)
            sleep(0.5)  # seconds
            rclpy.spin_once(g_node)

        # Destroy the node explicitly
        # (optional - otherwise it will be done automatically
        # when the garbage collector destroys the node object)
        g_node.destroy_node()
        rclpy.shutdown()

    if __name__ == '__main__':
        main()
    ```
