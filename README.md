# Simulation

NOTE: This is a **simulation** only branch. To flash the PX4 firmware, see the `firmware` branch.

## Quickstart

### Default Simulation

```
make px4_sitl gz_x500
```

### Default Drone with Camera

To run with the default drone with bottom facing camera and ArUco Tag:

```
make px4_sitl gz_x500_mono_cam_down

```

To see camera topic in gz sim:

```
ros2 run ros_gz_bridge parameter_bridge /camera@sensor_msgs/msg/Image@gz.msgs.Image
```

### Our Drone

To run with our drone:

1. Generate a `.sdf` file from the `.urdf` file.

```
cd PX4-Autopilot-Firmware/Tools/simulation/gz/models/uav2_description
gz sdf -p urdf/jellyfish2_v1.urdf > ../jellyfish2_v1/model.sdf
```

2. Start simulation.

```
make px4_sitl gz_jellyfish2_v1
```

3. To see camera topic in gz sim:

```
ros2 run ros_gz_bridge parameter_bridge /camera@sensor_msgs/msg/Image@gz.msgs.Image
```

## Known issues

Sorry we not good enough, we will work harder

### Baylands

* Starting baylands world screws up the sim. The sim does not auto load when running other commands. To run the sim again, need to run ```gz sim``` manually but the world would still be in baylands

### ros_gz_bridge

Summary:
GZ messages not being converted to ROS messages / converted ROS messages are not published.

Steps to reproduce:
1. Run `make px4_sitl gz_x500_mono_cam_down` in one terminal.
2. Run `ros2 run ros_gz_bridge parameter_bridge /camera@sensor_msgs/msg/Image@gz.msgs.Image` in another terminal.

Expected:
When subscribing to the image topic (e.g. `/camera`), no messages are received.

Debug:
Run the `ros_gz_bridge` `parameter_bridge` node and publish your GZ messages. Get info of the GZ messages to be converted using `gz topic --info -t <TOPIC>`. If you see two different message type versions (e.g. `ignition.msgs.Image` and `gz.msgs.Image` shown below), there is a mismatch between your Gazebo version and `ros_gz_bridge` version.

![image](https://github.com/user-attachments/assets/fe3841d8-ce0a-40fe-a8a5-df7f58d78f3c)

Fix:

1. Uninstall `ros-gz-bridge`.

```
sudo apt remove ros-humble-ros-gz-bridge
```

2. Reinstall the bridge by installing the Gazebo version that you are using.

```
sudo apt install ros-humble-ros-gzgarden
```
