# Installation

Check [Official documentation](https://docs.trossenrobotics.com/interbotix_xsarms_docs/ros_interface/ros1/software_setup.html) for more details on installations

## Full Installation
<details>
  <summary><strong>Procedure</strong></summary>

  (requirements: Ubuntu Linux 18.04, **20.04**, or 22.04)

Full descktop installaition:

```
sudo apt install curl
curl 'https://raw.githubusercontent.com/Interbotix/interbotix_ros_manipulators/main/interbotix_ros_xsarms/install/amd64/xsarm_amd64_install.sh' > xsarm_amd64_install.sh
chmod +x xsarm_amd64_install.sh
./xsarm_amd64_install.sh -d noetic
```

→  end up with this [Repo](https://github.com/Interbotix)

→ Correct installation checks:

    ```
    source /opt/ros/$ROS_DISTRO/setup.bash
    source ~/interbotix_ws/devel/setup.bash
    rospack list | grep interbotix
    ```
  Specific packages you should confirm have been built are interbotix_xs_sdk, interbotix_xs_msgs, and interbotix_xs_modules. These serve as the fundamental core of the ROS 1 Interface and are required to use it. If these are missing, check the installation script’s output for errors.

</details>

<br>

**Resurces**:

- [Official Trossen Robotics documentation](https://docs.trossenrobotics.com/interbotix_xsarms_docs/ros_interface/ros1/software_setup.html#requirements)
- [Official installation video tutorial](https://www.youtube.com/watch?v=kZx2tNVfQAQ&list=PL8X3t2QTE54sMTCF59t0pTFXgAmdf0Y9t&index=4&t=31s)
- [Wiki](http://wiki.ros.org/xseries_arms)


<br>
<br>

## Raspberry Pi Installation

<details>
  <summary><strong>Procedure</strong></summary>

1. Format SD card of RPi

2. Flush Ubuntu Server 20.04.5 LTS 64 bits in to the SD card of RPi
  > I used [Raspberry Pi imager](https://www.raspberrypi.com/software/):
  >> Chose OS → Other general Purpose OS → Ubuntu → Ubuntu Server 20.04.5 LTS 64 bits

3. Plug in SD card into the RPi and wait for booting

4. Run this comands:

    ```
    sudo apt update && sudo apt upgrade
    sudo reboot

    sudo apt install ubuntu-mate-desktop
    sudo reboot
    ```

5. At boot, the monitor should now display a login screen (instead of the terminal prompts from before). Before logging in, click the Ubuntu sign next to the username text box, and select ‘MATE’ as the desktop environment.

>6. Modify max CPU clock frequency from 1.5GHz to 2GHz
>
>    ```
>    cd /boot/firmware/
>    sudo nano usercfg.txt
>
>    over_voltage=6
>    arm_freq=2000
>    sudo reboot
>    ```

>7. Give a user sudo privileges without requiring a password
>
>    ```
>    sudo visudo
>
>    At bottom add: pibot ALL=(ALL) NOPASSWD:ALL
>    ```

> 8. Fix Bluetooth issues → the Bluetooth module on the Pi 4 is by default disabled ⇒ sudo apt install pi-bluetooth

9. INSTALL ROS & PACKAGES ON PI ([Raspberry Pi 4B (ARM64 Architecture)](https://docs.trossenrobotics.com/interbotix_xsarms_docs/ros_interface/ros1/software_setup.html#raspberry-pi-4b-arm64-architecture))

  ```
  $sudo apt install curl
  $curl 'https://raw.githubusercontent.com/Interbotix/interbotix_ros_manipulators/main/interbotix_ros_xsarms/install/rpi4/xsarm_rpi4_install.sh' > xsarm_rpi4_install.sh
  $chmod +x xsarm_rpi4_install.sh
  $./xsarm_rpi4_install.sh -d noetic
  ```

10. INSTALL ROS & PACKAGES ON PERSONAL LINUX COMPUTER ([Remote Install](https://docs.trossenrobotics.com/interbotix_xsarms_docs/ros_interface/ros1/software_setup.html#remote-install)):

  - Be aware that the installation script will export the ROS_MASTER_URI environment variable in your personal computer’s ~/.bashrc file to http://<hostname>.local:11311. Make sure to comment out this line when done monitoring or your personal computer will complain about not being able to find its ROS Master.

11. Check that the Interbotix ROS packages were installed correctly. The command and example output are below:

  ```
  $source /opt/ros/$ROS_DISTRO/setup.bash
  $source ~/interbotix_ws/devel/setup.bash
  $rospack list | grep interbotix
  ```

  remember to comment out the .bashrc

</details>

<br>

**Resurces**:

- [Official documentation installation](https://docs.trossenrobotics.com/interbotix_xsarms_docs/ros_interface/ros1/software_setup.html#raspberry-pi-4b-arm64-architecture)
- [Official documentation setup for RPi](https://docs.trossenrobotics.com/interbotix_xsarms_docs/ros_interface/ros1/raspberry_pi_setup.html)
- [Official installation video tutorial](https://www.youtube.com/watch?v=kZx2tNVfQAQ&list=PL8X3t2QTE54sMTCF59t0pTFXgAmdf0Y9t&index=4&t=31s)

