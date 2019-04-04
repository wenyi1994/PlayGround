## 1. How to control the turtlebot3 using our own laptops (bot as master)
**[IMPORTANT]** Since we have already finished the configuration for communication between laptop and bot, please skip this chapter and keep the file unchanged
1. Use `ifconfig` in terminal to get the ip-address of laptop, it should be something like `192.168.2.xx`.
2. Use `nano ~/.bashrc` to edit the configuration of our laptop, add following lines to it:
```shell
export ROS_MASTER_URI=http://198.168.2.14:11311
export ROS_HOSTNAME=192.168.2.xx
export TURTLEBOT3_MODEL=burger
```
3. Source the configuration file with `source ~/.bashrc` to get them into effect.
4. Use `ssh pnp@192.168.2.14` to login the bot, with password `ifl_pnp`. You will find the command line starts with `pnp@burger4:~$` if successfully login.
5. Use `nano ~/.bashrc` to edit the configuration of the bot, add following lines to it:
```shell
export ROS_MASTER_URI=http://198.168.2.14:11311
export ROS_HOSTNAME=192.168.2.14
export TURTLEBOT3_MODEL=burger
```
6. Source the configuration file with `source ~/.bashrc` to get them into effect.
## 2. How to set the turtulebot as master and control it with our laptops jointly
1. Use `ssh pnp@192.168.2.14` to login the bot, with password `ifl_pnp`. You will find the command line starts with `pnp@burger4:~$` if successfully login.
2. Run roscore on it with `roscore` command in the terminal.
3. Start a new terminal window and use `ssh pnp@192.168.2.14` to login the bot again.
> **Ctrl**+**Shift**+**T** to open a new terminal tab and **Ctrl**+**Alt**+**T** to open a new terminal window.
4. Execute the following command:
```shell
roslaunch turtlebot3_bringup turtlebot3_robot.launch
```
> We only need to start the `roscore` once and to launch the `bringup` package once, that means the two commands should be excuted only by one of us. Keep them running until we want to break the connection.
5. Start a new terminal window and source our workspace path to use [packages](https://git.scc.kit.edu/pnp_team4/our-turtlebot/tree/master/src/turtlebot3) provided by [Dziedzitz](mailto:jonathan.dziedzitz∂kit edu)
```shell
source ~/our-turtlebot/devel/setup.bash
```
6. Use `roslaunch` to run any package you would like to, following is a list of possible commands:
```shell
# receive messages sent by the bot and open a **rviz** window to monitor it
roslaunch turtlebot3_bringup turtlebot3_remote.launch
rosrun rviz rviz -d `rospack find turtlebot3_description`/rviz/model.rviz
# tele-operation
roslaunch turtlebot3_teleop turtlebot3_teleop_key.launch
# SLAM
roslaunch turtlebot3_slam turtlebot3_slam.launch slam_methods:=gmapping
# save generated map, the map will be saved in the root directory (`cd ~`)
rosrun map_server map_saver -f ~/map
```
> Read this [practical tutorial](http://emanual.robotis.com/docs/en/platform/turtlebot3/overview/) for further help.
> Each `roslaunch` command will start a `node`, the node cannot be started again. If we use same *launch-command* in two different terminals/laptops, the later launch will kick the former out.
## 3. How to use `git` and some common commands of it
1. Clone:
```shell
git clone <git-url>
```
> Always clone a repository **only once**. After then use `pull` to update it.
2. User configuration:
```shell
git config --global user.name "name"
git config --global user.email "email"
```
3. Check the status of local repository:
```shell
# cd to the git directory first!
git status
```
4. Upload new files to cloud repository:
```shell
git add -A
git commit -m "leave a comment here"
git push -u origin master
```
5. Update local repository:
```shell
# cd to the git directory first!
git checkout master
git pull origin master
```
6. Exclude the files that should not be uploaded:
```shell
# cd to the git directory first!
nano .gitignore
# add names of folders that would be excluded, also e.g. *.txt, *.jpg for specific file format
```
## 4. How to control the additional motor connected to the turtlebot3-platform
> **In `our-turtlebot` repository there is already needed package for motor controlling, so we don't need to clone them again. Just check your local repository folder `.../our-turtlebot/src/dynamixel_pkg`** 

> **Precondition** Ensure that the bot is running `roscore` and is brought up.
1. Set ros-environment using:
```shell
source ~/our-turtlebot/devel/setup.bash
```
2. Execute following commands in sequence:
```shell
rosrun dynamixel_pkg config_client_node.py
rosrun dynamixel_pkg command_pub_node.py
# followed is used to get infomation of motor
rosrun dynamixel_pkg state_sub_node.py
```
3. About those python scripts:
    - config_client_node.py
    ```python
    ...
    NUMBER_OF_DYNAMIXELS = 1 # number of connected motors
    MY_DYNAMIXEL_ID = 6 # ID of motor, written on the label
                        # Use an array when connecting 2 motors, like [5,6]
    POSIITON_CONTROL_MODE = 3
    MY_PROFIL_VELOCITY = 50 # control rotating velocity
    MY_MIN_POSITION = 0 # limit of position value
    MY_MAX_POSITION = 6000 # limit of position value
    ...
    ```
    - command_pub_node.py
    ```python
    ...
    NUMBER_OF_DYNAMIXELS = 1 # number of connected motors
    MY_DYNAMIXEL_ID = 6 # ID of motor, written on the label
                        # Use an array when connecting 2 motors, like [5,6]
    TORQUE_ON = 1
    MY_GOAL_POSITION = 5000 # target position, should be given in the interval set in config_client_node.py
    LED_ON = 1
    ...
    ```
    - state_sub_node.py
    ```python
    ...
    state_present_velocity = xl_state_msg.present_velocity[i]

    #print(a) # comment this line, there isn't a variable named 'a'

    #feedback on recieved values
    ...
    ```
## 5. How to drive the motor of conveyor
1. Use `ssh pnp@192.168.2.24` to login the raspberry pi, with password `ifl_pnp`. You will find the command line starts with `pnp@Station4:~$` if successfully login.
2. Use `git clone https://git.scc.kit.edu/ifl_pnp_2019/ifl_pnp_conveyor.git` to get necessary files.
3. Change directory (`cd`) to `/ifl_pnp_conveyor/MotorShield`, run python script with `python3 Test_Motor.py`. That is a testing programm for the motor.
## 6. If we encounter any error/problem ...
1. Can't find ros package when we try to use `roslaunch`:
    * Try to source `~/.bashrc` and then `~/our-turtlebot/devel/setup.bash`, also try to add `--extend` to keep them both in effect.
    * Update the local repository using `git pull origin master` to get up to date packages.
2. Can't source `~/our-turtlebot/devel/setup.bash` or it doesn't work after sourcing it:
    * Try to compile the package again (in our-turtlebot folder `catkin_make`, sometimes also need to use `catkin_init_workspace` first in src folder) and generate a new `setup.bash`. Don't forget to source it again.