# waypoint_manager(自分用)
## 使い方例
```
roslaunch waypoint_server waypoint_server.launch 
```
```
roslaunch orne_navigation_executor record_waypoints_viz.launch map_file:=/home/ryusei/orne_ws/maps/tsudanuma/220825f3.yaml
```
***
## setup(確認用)
```
cd orne_ws/src/
git clone git@github.com:Ryusei-Baba/waypoint_manager.git
```
***
```
roscd orne_navigation_executor/launch/
code play_waypoints_nav_common.launch
```
```
<?xml version="1.0"?>

<launch>
  <arg name="robot_name"     default="alpha"/>
  <arg name="map_file"       default="$(find orne_navigation_executor)/maps/mymap"/>
  <arg name="init_pos_file"  default="$(find orne_navigation_executor)/initial_pose_cfg/initial_pose_$(arg robot_name).yaml"/>
  <arg name="waypoints_file" default="$(find orne_navigation_executor)/waypoints_cfg/waypoints_$(arg robot_name).yaml"/>
  <arg name="dist_err"       default="0.8"/>
  <arg name="suspend_file"       default="$(find orne_strategy)/suspend_cfg/suspend.yaml"/>

  <include file="$(find orne_navigation_executor)/launch/nav_static_map.launch">
    <arg name="robot_name"    value="$(arg robot_name)"/>
    <arg name="map_file"      value="$(arg map_file)"/>
    <arg name="init_pos_file" value="$(arg init_pos_file)"/>
  </include>
<!-- 
  <node name="waypoints_nav" pkg="fulanghua_waypoints_nav" type="waypoints_nav" output="screen">
    <param name="filename" value="$(arg waypoints_file)"/>
    <param name="dist_err" value="$(arg dist_err)"/>
  </node>
 -->
  <node name="tsukuba_challenge_strategy" pkg="orne_strategy" type="tsukuba_challenge.py" output="screen">
    <param name="filename"      value="$(arg suspend_file)"/>
  </node>
</launch>
```
***
```
roscd waypoint_server/config/routes/
cp test.yaml tsudanuma.yaml
cd ..
code waypoint_server.yaml
```
```
goal_topic: /move_base_simple/goal
is_reached_goal_topic: waypoint/is_reached
regist_goal_pose_topic: waypoint/regist_pose
regist_goal_point_topic: waypoint/regist_point
visualize_waypoints_topic: waypoints
robot_base_frame: base_link
global_frame: map
goal_publish_frequency: 0.5
goal_check_frequency: 1.0
default_goal_radius: 1.0
# waypoints_file: $(find waypoint_server)/config/waypoints/20220625_tsuda_ch.yaml
waypoints_file: $(find waypoint_server)/config/waypoints/tsudanuma.yaml
#waypoints_file: $(find waypoint_server)/config/waypoints/open_campus.yaml
#route_file: $(find waypoint_server)/config/routes/20220625_tsuda_ch.yaml
# route_file: $(find waypoint_server)/config/routes/open_campus.yaml
route_file: $(find waypoint_server)/config/routes/tsudanuma.yaml
enable_loop: true
debug: false
```
***
```
roscd waypoint_server/config/waypoints/
cp test.yaml tsudanuma.yaml
code tsudanuma.yaml
```
```
waypoint_server:
  scale: 1
  position:
    x: 1
    y: 2
    z: 3
  euler_angles:
    x: 0
    y: 0
    z: 1.57
  waypoints:
    - id: 1
      position:
        x: -2.384186e-07
        y: 0
        z: 0
      euler_angles:
        x: -0
        y: 0
        z: -0
      properties:
        - key: stop
          value: true
        - key: goal_radius
          value: 1.0
      connections:
        - 2
```
***
```
roscd orne_navigation_executor/launch/
code nav_static_map.launch
```
```
<?xml version="1.0"?>

<launch>
  <arg name="robot_name" default="alpha"/>
  <arg name="map_file"   default="$(find orne_navigation_executor)/maps/mymap"/>
  <arg name="init_pos_file" default="$(find orne_navigation_executor)/initial_pose_cfg/initial_pose.yaml"/>
  <arg name="odom_topic" default="combined_odom"/>

  <include file="$(find orne_navigation_executor)/launch/move_base.launch">
    <arg name="robot_name" value="$(arg robot_name)"/>
    <arg name="map_file" value="$(arg map_file)"/>
    <arg name="odom_topic" value="$(arg odom_topic)"/>
    <arg name="cmd_vel" value="icart_mini/cmd_vel"/>
  </include>

  <include file="$(find orne_navigation_executor)/launch/localization.launch">
    <arg name="robot_name" value="$(arg robot_name)"/>
    <arg name="map_file" value="$(arg map_file)"/>
    <arg name="init_pos_file" value="$(arg init_pos_file)"/>
  </include>

  <node pkg="rviz" type="rviz" name="rviz" args="-d $(find orne_navigation_executor)/rviz_cfg/nav_manager_v3.rviz"/>

</launch>
```
***
```
source ~/.bashrc
cd orne_ws/
catkin build
```
