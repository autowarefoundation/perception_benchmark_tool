# perception_benchmark_tool

This package contains a benchmark tool for testing the perception stack of Autoware.Universe with the Waymo dataset.

## Installation


For testing 3D Object Tracking with Waymo Dataset, follow the given steps.

1- Download the Waymo dataset validation segment(.tfrecord) files from the given link. Unpack it to the desired directory.

<https://waymo.com/open/download/>


```bash
cd ~/Downloads/
tar -xvf validation_validation_0000.tar
```

Change the dataset folder from "benchmark_runner.launch.py"

2- Install the Waymo Open Dataset Toolkit.

```bash
mkdir -p ~/perception_benchmark_ws/src
cd ~/perception_benchmark_ws/src
git clone https://github.com/autowarefoundation/perception_benchmark_tool.git
pip3 install protobuf==3.9.2 numpy==1.19.2
pip3 install waymo-open-dataset-tf-2-6-0
```

3- For running Autoware.Universe with the Waymo evaluation node,

Lidar point clouds and camera images are encoded in the .tfrecord file. It may take about ~60-90 seconds to decode
the data back.

Go to the Autoware folder and source it,
```bash
source install/setup.bash
```

Build perception benchmark tool,
```bash
cd ~/perception_benchmark_ws/
rosdep install -y --from-paths src --ignore-src --rosdistro $ROS_DISTRO
colcon build 
source install/setup.bash
```

Run perception benchmark tool,
```bash
ros2 launch perception_benchmark_tool benchmark_runner.launch.py
```

This command will run the perception stack with the Waymo Dataset. We will get the ground truth and prediction files in the file
paths we give as arguments to the launch file.

Lidar point clouds and camera images are encoded in the .tfrecord file. It may take about ~60-90 seconds to decode
the data back for each segment file.

4- Install Waymo Open Dataset toolkit for metric computation:

Follow the given command or instruction provided by the Waymo: <https://github.com/waymo-research/waymo-open-dataset/blob/master/docs/quick_start.md>

```bash
git clone https://github.com/waymo-research/waymo-open-dataset.git waymo-od
cd waymo-od
git checkout remotes/origin/master
sudo apt-get install --assume-yes pkg-config zip g++ zlib1g-dev unzip python3 python3-pip
BAZEL_VERSION=3.1.0
wget https://github.com/bazelbuild/bazel/releases/download/${BAZEL_VERSION}/bazel-${BAZEL_VERSION}-installer-linux-x86_64.sh
sudo bash bazel-${BAZEL_VERSION}-installer-linux-x86_64.sh
sudo apt install build-essential
./configure.sh
bazel clean
bazel build waymo_open_dataset/metrics/tools/compute_tracking_metrics_main
```

5- Evaluate tracking result

```bash
bazel-bin/waymo_open_dataset/metrics/tools/compute_tracking_metrics_main \
~/benchmark_result/predictions.bin ~/benchmark_result/gt.bin
```

## Result

The evaluation result of the Perception pipeline on the Waymo Dataset is presented below.

### lidar_centerpoint

![Screenshot from 2022-05-09 01-10-48](https://user-images.githubusercontent.com/12658936/167317879-fd1777c3-e7d4-4492-9108-673f9a2f774b.png)

### lidar_apollo_instance_segmentation

![Screenshot from 2022-05-09 02-05-03](https://user-images.githubusercontent.com/12658936/167319631-aa8a7f01-88d9-4db0-8e30-ae5013359a12.png)
