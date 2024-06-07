# Errors

## Errosrs to avoid

### ATTENTION:
- When powering off the robot or disabling the torque, be aware that the robot will collapse. Ensure it is securely supported to prevent damage.
- Connect power before USB signal
- Connect correct power suply to the robot. ()

## Fix errors during installation

1. If during installation, yo get error because a package didn't compile:
    - download it manually:
        - `apt-cache search <package_name>` → `sudo apt install <the ros-noetic-.. package>` → `rm -rf ~/interbotix_ws/`

## Fix motor mulfctioning

When motor start falshing red once every seconds (more or less):
1. Unplug/replug power from from the motor hub and try again.
2. Use [reboot_motors](https://docs.trossenrobotics.com/interbotix_xsarms_docs/ros_interface/ros1/overview/xs_msgs.html#reboot) service
    - Where cmd_type: single/group
    - Where name: name group (ex: arm), or the signle joint (ex: elbow (ID4)/shoulder (ID2)) find it in config file [here](https://github.com/Interbotix/interbotix_ros_manipulators/blob/main/interbotix_ros_xsarms/interbotix_xsarm_control/config/wx250s.yaml)

### Resources
- [Fix motor down](https://docs.trossenrobotics.com/interbotix_xsarms_docs/troubleshooting.html)
- [Reboot motor](https://docs.trossenrobotics.com/interbotix_xsarms_docs/ros_interface/ros1/overview/xs_msgs.html#reboot)
