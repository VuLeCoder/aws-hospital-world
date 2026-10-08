# AWS RoboMaker Hospital World ROS package

**Visit the [AWS RoboMaker website](https://aws.amazon.com/robomaker/) to learn more about building intelligent robotic applications with Amazon Web Services.**

![Model: Hospital World](docs/images/hospital_world.jpg)
### Supported versions of Gazebo
7.14.0+ | 9.16.0+

Note: `python3` and `python3-pip` is required to run this world.

## 3D Models included in this Gazebo World

| Model (/models)       | Picture           |
| :------------- |:-------------:|
| **aws_robomaker_hospital_elevator_01_car, aws_robomaker_hospital_elevator_01_door, aws_robomaker_hospital_elevator_01_portal**     | ![Model: Elevator](docs/images/elevator.png) |
| **aws_robomaker_hospital_curtain_closed_01, aws_robomaker_hospital_curtain_half_open_01, aws_robomaker_hospital_curtain_open_01**     | ![Model: Curtains](docs/images/curtains.png) |
| **aws_robomaker_hospital_nursesstation_01**    | ![Model: Nurses Station](docs/images/nurses_station.png)
| **aws_robomaker_hospital_hospitalsign_01**    | ![Model: Hospital Sign](docs/images/hospital_sign.png)
| **aws_robomaker_hospital_floor_01_floor**    | ![Model: Hospital Floor](docs/images/hospital_floor.png)
| **aws_robomaker_hospital_floor_01_walls**    | ![Model: Hospital Walls and Layout](docs/images/hospital_walls.png)
| **aws_robomaker_hospital_floor_01_ceiling**    | ![Model: Ceiling](docs/images/hospital_ceiling.png)

We also reference the following models from https://app.ignitionrobotics.org/fuel/models:

*XRayMachine, IVStand, BloodPressureMonitor, BPCart, BMWCart, CGMClassic, StorageRack, Chair, InstrumentCart1, Scrubs, PatientWheelChair, WhiteChipChair, TrolleyBed, SurgicalTrolley, PotatoChipChair, VisitorKidSit, FemaleVisitorSit, AdjTable, MopCart3, MaleVisitorSit, Drawer, OfficeChairBlack, ElderLadyPatient, ElderMalePatient, InstrumentCart2, MetalCabinet, BedTable, BedsideTable, AnesthesiaMachine, TrolleyBedPatient, Shower, SurgicalTrolleyMed, StorageRackCovered, KitchenSink, Toilet, VendingMachine, ParkingTrolleyMin, PatientFSit, MaleVisitorOnPhone, FemaleVisitor, MalePatientBed, StorageRackCoverOpen, ParkingTrolleyMax*


# Include the world from another package

* Update .rosinstall to clone this repository and run `rosws update`

```
- git: {local-name: src/aws-robomaker-hospital-world, uri: 'https://github.com/aws-robotics/aws-robomaker-hospital-world.git', version: ros2}
```
* Add the following to your launch file:
* Add the following include to the ROS2 launch file you are using:
```python
    import os
    from ament_index_python.packages import get_package_share_directory
    from launch import LaunchDescription
    from launch.actions import IncludeLaunchDescription
    from launch.launch_description_sources import PythonLaunchDescriptionSource
    def generate_launch_description():
        hospital_pkg_dir = get_package_share_directory('aws_robomaker_hospital_world')
        hospital_launch_path = os.path.join(warehouse_pkg_dir, 'launch')
        hospital_world_cmd = IncludeLaunchDescription(
            PythonLaunchDescriptionSource([hospital_launch_path, '/hospital.launch.py'])
        )
        ld = LaunchDescription()
        ld.add_action(hospital_world_cmd)
        return ld
```

# Load directly into Gazebo (without ROS2)
```bash
chmod +x setup.sh
./setup.sh
export GAZEBO_MODEL_PATH=`pwd`/models:`pwd`/fuel_models
gazebo worlds/hospital.world
```

# ROS2 Launch with Gazebo viewer (without a robot)
For Gazebo Harmonic, use the standalone instructions below. The existing ROS2
launch files use Gazebo Classic.

```bash
# build for ROS2
rosdep install --from-paths . --ignore-src -r -y
colcon build

# run in ROS2
source install/setup.sh
ros2 launch aws_robomaker_hospital_world view_hospital.launch.py
```

# Gazebo Harmonic (without ROS2)

The following world variants have been checked with Gazebo Harmonic 8.15.0.
They use distinct world names and Fuel URLs for Sun and Ground Plane.
Portrait textures available in `photos/` are also bundled inside their models,
with mesh references corrected to use `../materials/textures/`.

Run from the repository root. If `fuel_models/` has not been populated yet:

```bash
python3 -m venv /tmp/hospital-venv
source /tmp/hospital-venv/bin/activate
bash setup.sh
```

Set the model search path in each terminal, then run one world:

```bash
export GZ_SIM_RESOURCE_PATH="$PWD/models:$PWD/fuel_models${GZ_SIM_RESOURCE_PATH:+:$GZ_SIM_RESOURCE_PATH}"

# One floor
gz sim -r worlds/hospital_harmonic.world

# Two floors
gz sim -r worlds/hospital_two_floors_harmonic.world

# Three floors
gz sim -r worlds/hospital_three_floors_harmonic.world
```

The first run may need network access to download Sun and Ground Plane from Fuel.
For a bounded server-only check, add `-s --iterations 1000` to a command above.
All three variants completed this check. Collada material warnings and DART mesh
collision debug messages remain; robot collision behavior has not been validated.

# Building
Include this as a .rosinstall dependency in your SampleApplication simulation workspace. `colcon build` will build this repository.

To build it outside an application, note there is no robot workspace. It is a simulation workspace only.

```bash
$ rosws update
$ rosdep install --from-paths . --ignore-src -r -y
$ chmod +x setup.sh
$ ./setup.sh
$ colcon build
```
