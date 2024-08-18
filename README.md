# DeepStream6.4_docker_setup_on_Pc
All the steps to install and configure DeepStream6.4 on a Pc with debian12 and a nvidia gpu 
## My setup:
Debian12 with nvidia 3070 GPU

## Host installation
Install the latest nvidia gpu driver

## Install host nvidia software
https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html
```
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
  && curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
    sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
    sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
```

Optionally, configure the repository to use experimental packages:
```
sed -i -e '/experimental/ s/^#//g' /etc/apt/sources.list.d/nvidia-container-toolkit.list
```
Update the packages list from the repository:
```
sudo apt-get update
```
Install the NVIDIA Container Toolkit packages:
```
sudo apt-get install -y nvidia-container-toolkit
```


## Configure docker

Configure the container runtime by using the nvidia-ctk command:
```
sudo nvidia-ctk runtime configure --runtime=docker
```

The nvidia-ctk command modifies the /etc/docker/daemon.json file on the host. The file is updated so that Docker can use the NVIDIA Container Runtime.
```
nvidia-ctk runtime configure --runtime=docker --config=$HOME/.config/docker/daemon.json
```

Restart the Rootless Docker daemon:
```
systemctl --user restart docker
```

Configure /etc/nvidia-container-runtime/config.toml by using the sudo nvidia-ctk command:
```
sudo nvidia-ctk config --set nvidia-container-cli.no-cgroups --in-place
```




Configure the container runtime by using the nvidia-ctk command:
```
sudo nvidia-ctk config --set nvidia-container-cli.no-cgroups --in-place
```



## Download the docker container

```
docker pull nvcr.io/nvidia/deepstream:6.4-gc-triton-devel
```

## make shared folder

My folder will be:
```
mkdir /media/data/shared_with_docker
```

## Downlaod the samples:
```
cd /media/data/shared_with_docker
git clone https://github.com/NVIDIA-AI-IOT/deepstream_python_apps.git
cd deepstream_python_apps

```

## Run the docker
```
# Step to run the docker
export DISPLAY=:0 &&  xhost +  && xhost local:root && docker run -it  --mount src=/media/data/shared_with_docker,target=/shared_with_docker/,type=bind --privileged --rm --net=host --gpus all -e DISPLAY=$DISPLAY --device /dev/snd -v /tmp/.X11-unix/:/tmp/.X11-unix nvcr.io/nvidia/deepstream:6.4-gc-triton-devel

```


## Inside the docker 
```
cd /opt/nvidia/deepstream/deepstream-6.4
chmod +x user_deepstream_python_apps_install.sh
./user_deepstream_python_apps_install.sh --build-bindings -r master
pip3 install cuda-python
wget https://github.com/NVIDIA-AI-IOT/deepstream_python_apps/releases/download/v1.1.11/pyds-1.1.11-py3-none-linux_x86_64.whl
pip3 install ./pyds-1.1.*.whl


/opt/nvidia/deepstream/deepstream/user_additional_install.sh

```


## Run the example (Inside the docker)
```
cd /shared_with_docker/deepstream_python_apps/apps/deepstream-test1/
python3 deepstream_test_1.py /opt/nvidia/deepstream/deepstream/samples/streams/sample_720p.h264
```

