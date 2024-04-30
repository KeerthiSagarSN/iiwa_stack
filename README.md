#### Follow the steps mentioned below in the Forked IIWA Stack
####### Go through step by step procedure in the wiki page- It contains all information for installation.
This repository is just for my reference, please follow the steps at your own risk.

### Tips to get it working and issues I faced to get it working

#### Backup process
1. First take a backup with a KUKA provided back-up pend drive before performing any of the below options
2. Empty the image folder in the KUKA Pendrive using an external laptop
3. Power down the Sunrise Controller completely
4. Plug-in the Sunrise controller to an external monitor and then plug-in the KUKA backup pendrive to the sunrise controller
5. Now switch-on the controller, and you must observe that directly control goes to the external monitor and wait till it goes black again, meaning it is taking a backup of the full controller
6. After the backup is complete, the fans in the controller will be turned down, indicating that the backup is complete. Turn-off the controller, remove the KUKA pendrive.
7. Remove the HDMI cable connected to the external monitor and restart

#### Controller side Tips
1. IFL-CAMP works with KUKA Smart Servoing, so "do not mix" and "do not use" the KONI interface procedure given by KHANSARI (https://bitbucket.org/khansari/iiwa/src), use the X66 port on the kuka sunrise controller.
2. Due to SMART Servoing package, you may experience a minor delay, if you are using the ROS package for teleoperation.
3. Install the Latest Sunrise Workbench on your laptop.
4. Using the ROSJAVA packages provided in the below IIWA-STACK and the procedure explicitly provided to upload  the application.
5. Sometimes you would be required to setup the PC's IP address to be in the range of the X66 port controller, mine was in the 172.31.1.150, you can check with the network configuration on your teach pendant.
6. Once the package is uploaded to Sunrise Controller, you will find the ROSServo in the Application tab of the teach pendant.
7. This means that the JAVA Side installation has been successfully completed.

I also used the following repository for visual aids to complete the ROSJAVA installation, but all JAVA packages are from the IIWA_STACK:
[https://ahundt.github.io/grl/howto/iiwaKukaRobotSetup.html](https://ahundt.github.io/grl/howto/iiwaKukaRobotSetup.html)

#### ROS side Tips
### I tried to integrate the controller with Unity-Robotics-Hub repository for teleoperation
#### This needed a master and then a slave PC -> Where Master -> Runs the ROS controller, whereas the Slave -> Runs the Unity Application on Windows.

1. Follow the ROS-tips from IIWA_STACK to build the repository from IIWA_STACK in your catkin_ws
2. Importantly, carry-out the following steps correctly to both receive and also send commands to the IIWA Controller -> I faced difficulty, if I did this step incorrectly, as I was able to receive the joint states but was unable to send the move commands to the controller. If you cant send commands it is mainly only due tothe network configurations, do sanity check of all the IPs and bash
3. Connect an ethernet cable between the ROS-PC to the Sunrise controller to the X66 port.
4. Configure the ethernet IPV4 settings of the ROS-PC Laptop -> IP Address: 172.31.1.150 Netmask:255.255.0.0

In a terminal
```
$ cd ~/catkin_ws/src/
$ gedit ~/.bashrc
$ export ROS_MASTER_URI=http://172.31.1.150:11311
$ export ROS_IP=172.31.1.150
$ source ~/.bashrc
$ source ~/.bashrc
```
In a terminal - Source the installation and bash
```
$ cd ~/catkin_ws/src/
$ source ~/.bashrc
$ source devel/setup.bash
```

5. Once this is done, normally you can launch

In a terminal - Source the installation and bash
```
$ roscore
```

6. Now set the IIWA to AUT using the small key in the teach-pendant
7. Click-> Application -> ROSSERVO -> Press the green play button while holding the enabling switch
8. The applicaiton should become green and enabled

In a terminal - Source the installation and bash
```
$ rostopic list
```
9. YOu should be able to see all the iiwa/state/JointPositions
10. Be sure to build propoerly the repository, and then you can send command - /iiwa/command/JointPosition
11. I generally move the last 7th axis joint to see if it is working

### To further extend to Unity-Robotics-Hub, 

Most cases, this wouldnt be required, but if the ROS-PC is not connected to a network switch, but the routing is happening through two network ports on the same ROS-PC, it is essential to do port forwarding

Do this only if you are not using a network switch:

In a terminal
```
$ sudo iptables -t nat -A PREROUTING -p tcp --dport 10000 -j DNAT --to-destination 172.31.1.150:10000
$ sudo iptables -t nat -A POSTROUTING -j MASQUERADE
```

Do not worry, if this messes with your system network configuration, restart your PC/Laptop and this port forwarding is reset and back to the original settings.

Follow the installation procedure from [https://github.com/Unity-Technologies/Unity-Robotics-Hub](https://github.com/Unity-Technologies/Unity-Robotics-Hub)

Change the Unity PC- Windows IP Address to 172.31.2.153; Subnet Mask: 255.255.255.0
In a terminal - To establish connection from Linux to Windows Unity PC
First need to change the ROS_IP param in the config
1. Go to niryo_moveit/config/params.yaml
```
ROS_IP: 172.31.1.150
```

2. Save the params file

In a terminal:
```
roslaunch niryo_moveit part_1.launch 
```
3. If you can visualize all the ros-topics in Unity, then the connection is successful and you can perform teleoperation.

![KUKA-IIWA-TELEOP](./Images_Readme/TELEOP_IIWA.png)
 

## IIWA STACK
ROS Indigo/Kinetic/Noetic metapackage for the KUKA LBR IIWA R800/R820 (7/14 Kg).

**Current version : v-1.3.0 for Sunrise 1.10 - 1.16**    
[Using a previous version of Sunrise?](https://github.com/SalvoVirga/iiwa_stack/wiki/FAQ#which-version-of-sunriseossunrise-workbench-is-supported)    

[![Build Status](https://dl.circleci.com/status-badge/img/gh/IFL-CAMP/iiwa_stack/tree/master.svg?style=svg)](https://dl.circleci.com/status-badge/redirect/gh/IFL-CAMP/iiwa_stack/tree/master)

___
### Features
- Native ROSJava nodes running on the robot as a Sunrise RoboticApplication: supports ROS parameters, topics, services, etc.
- Integration of KUKA's SmartServo and PTP motions:
  - joint position, joint velocity and cartesian position control via simple ROS messages. 
  - online configuration of JointImpedance, CartesianImpedance, DesiredForce and Sine(Force)Pattern via ROS service.
  - online configuration of joint/cartesian velocity and acceleration via ROS service.
  - updates on the time left to reach the commanded destination via ROS service.
- Selection of the tool and endframe to use via ROS parameters and services.
- 'Fake' hand-guidance mode.
- NTP synchronization with a server running on the ROS master
- full MoveIt! integration
- Gazebo support

___
### Usage
__The features and usage of the stack are described in depth on its  [WIKI][8].__  
We **_strongly_** suggest to have a look at the wiki to have a better understanding of the code, both for its use and its extension.     
Do you have problems? First, please check the [**FAQs**](https://github.com/SalvoVirga/iiwa_stack/wiki/FAQ). Issues or emails are always welcome!

___
### Citation

If you use iiwa_stack for reseach, you could cite the following work. It is the first publication where it was used.

    @article{hennersperger2017towards,
      title={Towards MRI-based autonomous robotic US acquisitions: a first feasibility study},
      author={Hennersperger, Christoph and Fuerst, Bernhard and Virga, Salvatore and Zettinig, Oliver and Frisch, Benjamin and Neff, Thomas and Navab, Nassir},
      journal={IEEE transactions on medical imaging},
      volume={36},
      number={2},
      pages={538--548},
      year={2017},
      publisher={IEEE}
    }

___
### Acknowledgements
This repository takes inspiration from the work of :
- _Centro E. Piaggio_ and their [ROS interface for the KUKA LBR 4+][1]
- _Mohammad Khansari_ and his [IIWA-ROS communication inteface][2] 
- _Robert Krug_ and his [IIWA URDF and Gazebo package][7]      

Most of the original files were completely refactored though.


### Contacts

[![TUM](http://campar.in.tum.de/files/goeblr/TUM_Web_Logo_blau.png "TUM Logo")](http://www.tum.de)      
[Chair for Computer Aided Medical Procedures](http://campar.in.tum.de/)      
[Technical University of Munich](http://www.tum.de), Germany.      

<b>Salvatore Virga</b> : [salvo.virga@tum.de](mailto:salvo.virga@tum.de)      
<b>Marco Esposito</b> : [marco.esposito@tum.de](mailto:marco.esposito@tum.de) 

[1]: https://github.com/CentroEPiaggio/kuka-lwr
[2]: https://bitbucket.org/khansari/iiwa.git
[3]: https://bitbucket.org/khansari/iiwa/src/c4578460d79d5d24f58bf94bd97fb6cb0b6f280f/msg/IIWAMsg.msg
[4]: https://bitbucket.org/khansari/iiwa/wiki/Home
[5]: https://bitbucket.org/khansari/iiwa/src/c4578460d79d5d24f58bf94bd97fb6cb0b6f280f/JavaNode/?at=master
[6]: http://git.lcsr.jhu.edu/cgrauma1/kuka_iiwa_shared
[7]: https://github.com/rtkg/lbr_iiwa
[8]: https://github.com/SalvoVirga/iiwa_stack/wiki
