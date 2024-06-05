# Perception Pakage

## Robot - USB camera - Linux computer


<details>
  <summary><strong>Use Arm vision kit</strong></summary>

<details>
  <summary><strong>Configuration</strong></summary>

1. Localize robot in space:
  `roslaunch interbotix_xsarm_perception xsarm_perception.launch robot_model:=wx250s use_armtag_tuner_gui:=true use_pointcloud_tuner_gui:=true`

    - parameters explenation:
        - `use_armtag_tuner_gui:=true` → GUI to help localize rebot to respect with camera
        - `use_pointcloud_tuner_gui:=true` → tune parameters of cloud vision of camera

2. when RViz open, you see camera view

3. torque off arm:
    `rosservice call /wx250s/torque_enable "cmd_type: 'group' name: 'arm' enable: false"`

4. Put robot tag in camera view

5. torque on arm:

    `rosservice call /wx250s/torque_enable "cmd_type: 'group' name: 'arm' enable: true"`

6. bring up `use_armtag_tuner_gui`:
    - capture arm
        1. num_samples: from 1 to 10. set to 10. 10 snapshots will be taken from the camera will come up with 10mslitly diffrent poses of where the arm is. Average all poses togheter.
            1. CLICK `Snap Pose` and capture robot-camera position sistem
            2. poses will be saved in jamal files. every time move arm and camera you have to calibrate again

---

7. Bring up `pointcloud_tuner_gui`:
    - Apply filters to workspace

      > Change frame of reference: TF→Frames→camera_color_depth_optical=true & camera_color_optical=false

      1. Crop Box Filter

          Green semi trasperent box in the 6 fields. ⇒ get data that we want: table and small object on table ⇒ cut parts you dont want out of the cloud

          ⇒ Fewer point to pass at Voxel Grid Filter ⇒ FASTER

      2. Voxel Grid Filter

          the cloud left by Crop Box Filter is filled by many small cubes (Leaf Size[m]). Any point in the point cloud will be average in to one ⇒ reduce number of points

          Smaller⇒ a lot of points to next filter + more accuracy + SLOWER

          Increase⇒ sparse points + less acurate + less point

          ⇒ 0.004 would do

      3. Segmenation Filter
          1. Uncheck filterPointCloud and Check ObjectPointCloud

          Crap out plate the objects are on from point cloud (mathematical model)

          - max interations: leave at 50… not too much effect
          - dist. thresh[m]: any data in to a defined distance are craped out as part of plane. Any point that falls in; +- 5mm (0.005); from the mathematical model of the plane; are craped out and classified as plane
              - if too small ⇒ see part of the plane
              - if too large you crap out base of objects

                  ⇒ rase untile stop see plane

      4. Radius Outlier Removal Filter

          If I have a lot of noise, I can crap it out.

          - min neighbors & Radius search: Take a point; search with a x radius arround the point; if I found n neighbours; Keep the point, if not trwo it out.
              - Decrese noise ⇒ increase min neigh. or decrease redius search
      5. Cluster Filter

          At this point we have a PointCloud that should the objects.

          Decide how many clusters are there? As any point in within a group of points such that any point is in a certain tollerance[m]. If so, consider same object/cluster.

          Minsize → at least n point to be a cluster (increase if midpoint is blinking)

          maxsize → at most n point to be a cluster (object with too much points)

          Center point of each cluster is RGB average value and position


    Once done: SAVE CONFIG button. the launch files, will take filter_params argument automaticaly from this yaml ⇒ SAVE ⇒ REPLACE


</details>

<details>
  <summary><strong>Move robot with Computer Vision</strong></summary>

Go to `cd interbotix_ws/src/interbotix_ros_manipulators/interbotix_ros_xsarms/interbotix_xsarm_perception/scripts/` to find the demos.

We will walk trough pick_place.py deamo. Check out also the others for curiosity.

<details>
  <summary>pick_place.py</summary>

> SETUP: arm on left of camera, cubes in fron of camera, and box in fornt of arm
<details>
  <summary>Code</summary>

```
import time
from interbotix_xs_modules.arm import InterbotixManipulatorXS
from interbotix_perception_modules.armtag import InterbotixArmTagInterface
from interbotix_perception_modules.pointcloud import InterbotixPointCloudInterface

# This script uses a color/depth camera to get the arm to find objects and pick them up.
# For this demo, the arm is placed to the left of the camera facing outward. When the
# end-effector is located at x=0, y=-0.3, z=0.2 w.r.t. the 'wx200/base_link' frame, the AR
# tag should be clearly visible to the camera. A small basket should also be placed in front of the arm.
#
# To get started, open a terminal and type 'roslaunch interbotix_xsarm_perception xsarm_perception.launch robot_model:=wx200'
# Then change to this directory and type 'python pick_place.py'

def main():
    # Initialize the arm module along with the pointcloud and armtag modules
    bot = InterbotixManipulatorXS("wx200", moving_time=1.5, accel_time=0.75)
    pcl = InterbotixPointCloudInterface()
    armtag = InterbotixArmTagInterface()

    # set initial arm and gripper pose
    bot.arm.set_ee_pose_components(x=0.3, z=0.2)
    bot.gripper.open()

    # get the ArmTag pose
    bot.arm.set_ee_pose_components(y=-0.3, z=0.2)
    time.sleep(0.5)
    armtag.find_ref_to_arm_base_transform()
    bot.arm.set_ee_pose_components(x=0.3, z=0.2)

    # get the cluster positions
    # sort them from max to min 'x' position w.r.t. the 'wx200/base_link' frame
    success, clusters = pcl.get_cluster_positions(ref_frame="wx200/base_link", sort_axis="x", reverse=True)

    # pick up all the objects and drop them in a virtual basket in front of the robot
    for cluster in clusters:
        x, y, z = cluster["position"]
        bot.arm.set_ee_pose_components(x=x, y=y, z=z+0.05, pitch=0.5)
        bot.arm.set_ee_pose_components(x=x, y=y, z=z, pitch=0.5)
        bot.gripper.close()
        bot.arm.set_ee_pose_components(x=x, y=y, z=z+0.05, pitch=0.5)
        bot.arm.set_ee_pose_components(x=0.3, z=0.2)
        bot.gripper.open()
    bot.arm.go_to_sleep_pose()

if __name__=='__main__':
    main()
```
</details>

<details>
  <summary>Code explenation</summary>

1. istance of robot → (robot_model, moving_time, accel_time)
    1. check if need to change robot model
    2. Moving time is how much each motion will take
    3. accel time time accellerating and decellerationg (with moving_time = 1.5 and accel_time = 0.75 ⇒ half time spend accelleraing and half decellerating ⇒ smooth motion)
2. instantiate pointCloud adn arm tag (modules in interbotics perception toolbox)
    1. pointCloud → get location of objects seen by camera
    2. armtag → determin where arm is respect to camera
        1. Comment line 18 (`armtag = InterbotixArmTagInterface()`)and from 24 (`# get the ArmTag pose`) to 28 (`bot.arm.set_ee_pose_components(x=0.3, z=0.2)`) if u already did the set up of armtag
        2. ⇒ Move arm so that air tag in in view of camera adn then call `armtag.find_ref_to_arm_base_transform()` to register position to respect to camera
3. move robot from sleep pose and open gripper

    ```python
     	  # set initial arm and gripper pose
        bot.arm.set_ee_pose_components(x=0.3, z=0.2)
        bot.gripper.open()
    ```

4. get cluster positions function

    `success, clusters = pcl.**get_cluster_positions**(ref_frame="wx200/base_link", sort_axis="x", reverse=True)`

    - ⇒Return:

        ```python
        {'name': 'cluster_4', 'position': [0.12605651975031112, -0.37287103605565386, 0.015619998457338302], 'yaw': 0, 'color': [173.2, 137.20000000000002, 35.400000000000006], 'num_points': 124.2}
        {'name': 'cluster_5', 'position': [0.0468508013847061, -0.2212722444096395, 0.008733008036610979], 'yaw': 0, 'color': [145.2, 46.0, 45.400000000000006], 'num_points': 119.6}
        {'name': 'cluster_3', 'position': [0.2250253260504927, -0.39883325169098516, 0.014425192517960761], 'yaw': 0, 'color': [32.599999999999994, 56.2, 34.2], 'num_points': 115.00000000000001}
        {'name': 'cluster_6', 'position': [0.18161164834594629, -0.18549951294510453, 0.0044959433110706715], 'yaw': 0, 'color': [93.0, 58.8, 78.4], 'num_points': 114.2}
        {'name': 'cluster_2', 'position': [0.00603318185314719, -0.40130624634685086, 0.012654474935686777], 'yaw': 0, 'color': [14.8, 40.599999999999994, 70.8], 'num_points': 109.80000000000001}
        ```


    Documentation for this function: `interbotix_perception_toolbox/interbotix_perception_modules/src/interbotix_perception_modules/pointcloud.py`

    PARAMETERS: `ref_frame` base link frame of the arm. because specify cluster pos relative to base link frame ⇒ IK frame/Origine ⇒ Know where arm should move to pick up those clusters. Each cluster has position relative to link frame. We will order them based on `sort_axis` . the closest to that axes is the first cluster in the dictionary (`sort_axis="x"` → last cluster in the list have maximum x value). `reverse` → reverse to true, reverse list of dictionaries. (By default get_cluster_position find clusters. if u want to pick object from top, you need to know the top of object ⇒ `is_parallel` = true ⇒ define where top is. if is false, we get average. x,y dont change, but z change, form center of cluster to most high pont in the clusetr).

    → return list of dictionaries inside clusters. With infos about each cluster. Name cluster, size of cluster, position, average RGB, etc…

    → success is varible to see if function was able to find all of clusters

5. go trough list of dictionaries in the for loop.  `for cluster in clusters:`
6. get position of that one cluster. `x, y, z = cluster["position"]`
7. say to arm to rach that cluster `bot.arm.set_ee_pose_components(x=x, y=y, z=z+0.05, pitch=0.5)` (5cm above so there is bit clearence)
8. `bot.arm.set_ee_pose_components(x=x, y=y, z=z, pitch=0.5)` → actualy lower gripper to reach the high of cluster
9. close gripper and then do oppisite
10. after doing that for each cluster, go to sleep

</details>
<details>
  <summary>Run code</summary>

1. `roslaunch interbotix_xsarm_perception xsarm_perception.launch robot_model:=wx250s enable_pipeline:=true`

    `enable_pipeline:=true` → run all filter we set before with the gui. used for `get_cluster_position` function

2. new terminla
3. `cd interbotix_ros_xsarms/interbotix_xsarm_perception/scripts/`
4. `python pick_place.py`

</details>
</details>

</details>


</details>



### Resurces:

- [Official video tutorial 1](https://www.youtube.com/watch?v=UesfMYM4qcc&list=PL8X3t2QTE54sMTCF59t0pTFXgAmdf0Y9t&index=5&t=557s)
- [Official video tutorial 2](https://www.youtube.com/watch?v=03BZ6PLFOac&list=PL8X3t2QTE54sMTCF59t0pTFXgAmdf0Y9t&index=7)

<br>
<br>

## Robot+Raspberry Pi - Personal Computer

<details>
  <summary><strong>Procedure</strong></summary>

Still not working... comming soon


</details>

<br>

