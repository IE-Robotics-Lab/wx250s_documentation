# General


## Robot - Linux computer general knowledge


<details>
  <summary><strong>General knowledge</strong></summary>

1. Source the terminal

```
(surce /opt/ros/noetic/setup.bash)
source interbotics_ws/devel/setup.bash
```

2. Don't worry if you still don't see the topics after running `rostopic list`. Untill you don't `roslaunch` some packages you won't see them. Alos, don't worry about `roscore`, it will automaticaly run when you `roslaunch`.

3. Play arround with the pakages at your disposal, get to kn ow them. Check this comand out: `roscd → to see where a package is` and `rospack list`

4. github → interbotix_ros_manipulators → interbotix_ros_xsarms → interbotix_ros_xsarms_control → config → wx250s.yaml ⇒ config file to see joint order, names, sleep_position,  groups (such as arm), etc…

</details>

<details>
  <summary><strong>First comands</strong></summary>

7. To have model loaded and play arround in RViz (play with model):
    - Use description package: `roslaunch interbotix_xsarm_description xsarm_descriptions.launch robot_model:=wx250s use_joint_pub_gui:=true`
      - note for better rviz visuals: RobotModel → alpha → set transparency to 0.5
      - note for better rviz visuals: TF → Frames → show/not show axes in joint
8. To talk to the robot and move it in real world:
    - `roslaunch interbotix_xsarm_control xsarm_control.launch robot_model:=wx250s`
      - In the playground box, write _wx250s_ under **Robot Namespace** (light becae green). Now you are connected to real robot
      - Important paramenters: **use_sim** → to simulate the robot moovments. It will be usefull later, when we will code and mouve the robot to test our code before breaking the robot!!!!!!!!

    <details>
      <summary>Topics</summary>

    `rostopic list`

    Now you can see interesting topics. Such `/wx250s/commands/joint_group` (pub to a group of joint), `/wx250s/commands/joint_single` (pub to one joint), `/wx250s/commands/joint_trajectory` (pub joint trajectory message).

    - Publish to **`/wx250s/commands/joint_group`**:

      1. Home position:`rostopic pub -1 /wx250s/comands/joint_group interbotix_ws_sdk/JointGroupComand “name: ‘arm’ cmd: [0,0,0,0,0,0]”`
        - where `name: ‘arm’`is the arm_group (everithing eccept the gripper
        - where `cmd: [0,0,0,0,0,0]` are joint angles (→ rect position (home))
      2. Sleep position: `rostopic pub -1 /wx250s/comands/joint_group interbotix_ws_sdk/JointGroupComand “name: ‘arm’ cmd: [<set_of_positions>]”`
        - to **find the `<set of positions>`** go to github → interbotix_ros_manipulators → interbotix_ros_xsarms → interbotix_ros_xsarms_control → **config → wx250s.yaml** ⇒ config file to see joint order, names, sleep_position, groups (such as `arm`), etc…

    </details>

    <details>
      <summary>Services</summary>

    `rosservice list`

    Now you can see interesting services. Such `/wx250s/get_robot_info` (see infos about robot)and `/wx250s/torque_enable` (to enable or desable torque).

    1. `rosservice call /wx250s/get_robot_info "cmd_type: 'group' name: 'arm'"`
        - where `cmd_type: 'group'` says u want informations from a certain group of motors
        - where `name: 'arm'` says the group of motors u want to get infos
        ⇒ see mode, profile_type (velocity vs time), joint infos & joint limits, etc…
    2. `rosservice call /wx250s/torque_enable "cmd_type: 'group' name: 'arm' enable: false"`
        - when `enable: false`, the robot collapse. So hold it before running the comand
        - Usefull because you can turn off the torque, manualy move the robot in the desire position and turn on torque again (`enable: true`). At this point you can `rostopic echo wx250s/joint_states` to see informations about current desired position.
    </details>

</details>



<br>
<br>

## Robot+Raspberry Pi - Personal Computer general knowledge

<details>
  <summary><strong>Procedure</strong></summary>

Still not working... comming soon


</details>

<br>

