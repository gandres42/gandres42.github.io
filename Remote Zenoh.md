Turns out that while DDS remains a nightmare to set up over a network, Zenoh makes life a little easier by allowing you to use a centralized, non-distributed server/client model.  Here's a guide for connecting to a Turtlebot's ROS topics over a network.
## Setup
### Turtlebot
First install the Zenoh middleware:
```
sudo apt install ros-kilted-rmw-zenoh-cpp
```
Then make it the default and make it easy to start a Zenoh router by adding the following to your `~/.bashrc`:
```
export RMW_IMPLEMENTATION=rmw_zenoh_cpp
alias roscore="ros2 run rmw_zenoh_cpp rmw_zenohd"
```
Close all your ssh sessions and open new ones to ensure proper env setup.
### Client
Again, install the Zenoh middleware:
```
sudo apt install ros-kilted-rmw-zenoh-cpp
```
## Use
### Turtlebot
First start the Zenoh router.  Open a terminal with ssh and run:
```
roscore
```
**Note: because we set Zenoh as the default middleware implementation in `.bashrc` you must always run `roscore` first on the Turtlebot, even when running code directly on the Turtlebot**.
Now run your Turtlebot bringup in another ssh session:
```
ros2 launch turtlebot3_bringup robot.launch.py
```
### Client
First, make sure you are on the `robotics` network, I haven't gotten this working on eduroam yet.
Open a new terminal, making sure you sourced your ROS install and run:
```
export RMW_IMPLEMENTATION=rmw_zenoh_cpp
export ZENOH_CONFIG_OVERRIDE='mode="client";connect/endpoints=["tcp/TURTLEBOT_HOST:7447"]'
ros2 daemon stop && ros2 daemon start
```
where `TURTLEBOT_HOST` is your hostname, e.g. `burgerbot1.engr.oregonstate.edu`.  Now if you run `ros2 topic list` you should see topics from the Turtlebot.