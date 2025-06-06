# NVidia Jetson Orin Nano

[Documentation](https://docs.nvidia.com/jetson/archives/r36.4/DeveloperGuide/index.html)

## Update for Max Performance

**Ubuntu 22.04 host machine required**

[Firmware Update](https://developer.nvidia.com/embedded/learn/get-started-jetson-orin-nano-devkit#firmware)

Install SDK Manager

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo apt-get update
sudo apt-get -y install sdkmanager
```

You will need to create a developer account.

### Using NVidia SDK Manager on Ubuntu 22.04 on the laptop in the lab

- Connect FC_REC and GND on the board
- Connect the board to the laptop using the USB-C cable
- Run SDK Manager, select Orin Nano 8Gb Developer Kit, uncheck the Host Machine
- Select Linux Image and other checkboxes
- Select NVMe, set username and password "phrec" and flash it.
*This will take about an hour*

### Install NVidia Libraries

- Boot Jetson normally and after initial setup connect to the laptop
- In SDK Manager check everything but not Linux Image

### Necessary

```bash
echo 'export PATH=/home/phrec/.local/bin:$PATH' >> .bashrc
sudo apt update && sudo apt upgrade -y
sudo apt install git wget curl python3-pip
sudo apt install libelf-dev build-essential pkg-config v4l-utils
# install jtop
sudo pip3 install -U jetson-stats
# setup inputs (for cameras)
sudo /opt/nvidia/jetson-io/jetson-io.py
# Configure Jetson 24pin CSI Connector - Configure for compatible hw
# V3 - IMX477, V2 - IMX219
```

### Build OpenCV with CUDA

[Using this script](https://github.com/AastaNV/JEP/blob/master/script/install_opencv4.10.0_Jetpack6.1.sh)

```bash
jtop # then press 7 to check status
wget https://raw.githubusercontent.com/AastaNV/JEP/refs/heads/master/script/install_opencv4.10.0_Jetpack6.1.sh
chmod +x install_opencv4.10.0_Jetpack6.1.sh
./install_opencv4.10.0_Jetpack6.1.sh
```

*This will take about an hour*

### Install PyTorch and TorchVision compiled with CUDA

```bash
pip install https://pypi.jetson-ai-lab.dev/jp6/cu126/+f/6ef/f643c0a7acda9/torch-2.7.0-cp310-cp310-linux_aarch64.whl#sha256=6eff643c0a7acda92734cc798338f733ff35c7df1a4434576f5ff7c66fc97319
pip install https://pypi.jetson-ai-lab.dev/jp6/cu126/+f/daa/bff3a07259968/torchvision-0.22.0-cp310-cp310-linux_aarch64.whl#sha256=daabff3a0725996886b92e4b5dd143f5750ef4b181b5c7d01371a9185e8f0402
```

[More Libraries compiled with CUDA if needed](https://pypi.jetson-ai-lab.dev/jp6/cu126)

### Test CSI Camera

```bash
# check camera
ls -la /dev/video*
v4l2-ctl --list-devices
# to display video output
gst-launch-1.0 nvarguscamerasrc sensor-id=0 ! nvvidconv ! nveglglessink
```

## Wireguard (not Necessary)

```bash
sudo apt-get install libelf-dev build-essential pkg-config
# use custom repo for 5.15.148-tegra
git clone https://github.com/MrVasquez96/wireguard-linux-compat
git clone https://git.zx2c4.com/wireguard-tools
make -C wireguard-linux-compat/src -j$(nproc)
sudo make -C wireguard-linux-compat/src install
make -C wireguard-tools/src -j$(nproc)
sudo make -C wireguard-tools/src install
```
