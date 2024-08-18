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
