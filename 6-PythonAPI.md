# Python API

## Move robot with python


<details>
  <summary><strong>Python API functions and usage</strong></summary>

- `bot = InterbotixManipulatorXS("wx250", "arm", "gripper")` → create instance of `InterbotixManipulatorXS` (class defined [here](https://github.com/Interbotix/interbotix_ros_toolboxes/blob/main/interbotix_xs_toolbox/interbotix_xs_modules/src/interbotix_xs_modules/arm.py))
- `bot.arm.go_to_home_pose()` & `bot.arm.go_to_sleep_pose()`

---

- `bot.arm.set_ee_pose_components(x=0.2, y=0.1, z=0.2, roll=0, pitch=0, yaw=0)` → move end-effector in position x, y, z and orientation roll, pitch, jaw
- `bot.arm.set_ee_cartesian_trajectory(x=0.1, z=-0.16)` → add/substract to current position adn reach that end-effector
    - `bot.arm.set_ee_cartesian_trajectory(pitch=1.5)` → rotate in pitch but end-effector still
- `bot.arm.set_joint_positions(joint_positions)` → reach with the 6 joints, the 6 angles in `joint_positions` array
- `bot.dxl.robot_write_trajectory("group", "arm", "position", trajectory)` → reach a series of angles with the joint, in time
    - `trajectory = [
            {0.0: [0.0,  0.0, 0.0, 0.0, 0.0, 0.0]},
            {2.0: [0.0,  0.0, 0.0, 0.0, 0.5, 0.0]},
            {4.0: [0.5,  0.0, 0.0, 0.0, 0.5, 0.0]},
            {6.0: [-0.5, 0.0, 0.0, 0.0, 0.5, 0.0]}
        ]`
- `bot.arm.set_ee_pose_matrix(T_sd)` → move end-effector in position x, y, z and orientation roll, pitch, jaw using Rotation&Traslation Matrices
    - `T_sd = np.identity(4)
    T_sd[0,3] = 0.3
    T_sd[1,3] = 0.1
    T_sd[2,3] = 0.2`

---

- `bot.arm.set_single_joint_position("waist", np.pi/2.0)` → set angle in anly one joint
- `bot.gripper.open()` (`bot.gripper.open(0.2)`) (`bot.gripper.close()`) (`bot.gripper.set_pressure(0.3)`)


</details>


<details>
  <summary><strong>Examples</strong></summary>

Go to `cd interbotix_ws/src/interbotix_ros_manipulators/interbotix_ros_xsarms/examples/python_demos/` to find the Python [demos](https://github.com/Interbotix/interbotix_ros_manipulators/tree/main/interbotix_ros_xsarms/examples/python_demos).

>Note: u can have script where ever u want.. noo need to be in that directory

As we saw in [General.md](/General.md), run `roslaunch interbotix_xsarm_control xsarm_control.launch robot_model:=wx250s` to connect with real robot <span style="color:gray;"> or `roslaunch interbotix_xsarm_control xsarm_control.launch robot_model:=wx250s use_sim:=true` to simulate it. </span>

  <details>
    <summary>bartender.py</summary>
    <details>
      <summary>Code</summary>

```
from interbotix_xs_modules.arm import InterbotixManipulatorXS
import numpy as np
import sys

# This script makes the end-effector perform pick, pour, and place tasks
# Note that this script may not work for every arm as it was designed for the wx250
# Make sure to adjust commanded joint positions and poses as necessary
#
# To get started, open a terminal and type 'roslaunch interbotix_xsarm_control xsarm_control.launch robot_model:=wx250'
# Then change to this directory and type 'python bartender.py  # python3 bartender.py if using ROS Noetic'

def main():
    bot = InterbotixManipulatorXS("wx250", "arm", "gripper")

    if (bot.arm.group_info.num_joints < 5):
        print('This demo requires the robot to have at least 5 joints!')
        sys.exit()

    bot.arm.set_ee_pose_components(x=0.3, z=0.2)
    bot.arm.set_single_joint_position("waist", np.pi/2.0)
    bot.gripper.open()
    bot.arm.set_ee_cartesian_trajectory(x=0.1, z=-0.16)
    bot.gripper.close()
    bot.arm.set_ee_cartesian_trajectory(x=-0.1, z=0.16)
    bot.arm.set_single_joint_position("waist", -np.pi/2.0)
    bot.arm.set_ee_cartesian_trajectory(pitch=1.5)
    bot.arm.set_ee_cartesian_trajectory(pitch=-1.5)
    bot.arm.set_single_joint_position("waist", np.pi/2.0)
    bot.arm.set_ee_cartesian_trajectory(x=0.1, z=-0.16)
    bot.gripper.open()
    bot.arm.set_ee_cartesian_trajectory(x=-0.1, z=0.16)
    bot.arm.go_to_home_pose()
    bot.arm.go_to_sleep_pose()

if __name__=='__main__':
    main()
```

  </details>
    <details>
      <summary>Explanation of code</summary>

1. improt instance of `InterbotixManipulatorXS`
2. in main create instance of `InterbotixManipulatorXS` **(class defined [here](https://github.com/Interbotix/interbotix_ros_toolboxes/blob/main/interbotix_xs_toolbox/interbotix_xs_modules/src/interbotix_xs_modules/arm.py))**
    1. `bot = InterbotixManipulatorXS("wx250**s**", "arm", "gripper")`
3. set bot to go to 0.3 in x direction and 0.2 in z direction
    1. `bot.arm.set_ee_pose_components(x=0.3, z=0.2)`
    2. `(x=0.3, z=0.2)` is absolute position relative base link frame. ee_link frame with respect to base frame.
4. rotate weist of 90 deg
    1. `bot.arm.set_single_joint_position("waist", np.pi/2.0)`
5. **move end-effector forward of 0.1 meters relative to his current pos and lower it self of 0.16.**
    1. **`bot.arm.set_ee_cartesian_trajectory(x=-0.1, z=0.16)`**
6. rotate end-effector
    1. bot.arm.set_ee_cartesian_trajectory(pitch=1.5)

  </details>
    <details>
      <summary>Run code</summary>

1. `cd interbotix_ws/src/interbotix_ros_manipulators/interbotics_ros_xsarms/exaples/python_demos`
2. change model of robot is needed from wx250 to wx250s

    `nano bartender.py`

3. `python bartender.py` (or `python3 bartender.py`)

  </details>

  </details>

</details>


## Resources

- [Offical video tutorial](https://www.youtube.com/watch?v=KoqBEvz4GII&list=PL8X3t2QTE54sMTCF59t0pTFXgAmdf0Y9t&index=12)
