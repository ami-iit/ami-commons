# NVIDIA Driver Upgrade

It is suggested to start from a clean environment:

```sh
sudo apt remove --purge nvidia-*
sudo apt autoremove --purge
sudo apt clean
```

Then you can find the available drivers with `ubuntu-drivers list`, for example:
```
nvidia-driver-525-server, (kernel modules provided by linux-modules-nvidia-525-server-generic-hwe-22.04)
nvidia-driver-525, (kernel modules provided by linux-modules-nvidia-525-generic-hwe-22.04)
nvidia-driver-530, (kernel modules provided by nvidia-dkms-530)
nvidia-driver-470-server, (kernel modules provided by linux-modules-nvidia-470-server-generic-hwe-22.04)
nvidia-driver-515, (kernel modules provided by nvidia-dkms-515)
nvidia-driver-520, (kernel modules provided by nvidia-dkms-520)
nvidia-driver-535-server, (kernel modules provided by linux-modules-nvidia-535-server-generic-hwe-22.04)
nvidia-driver-550, (kernel modules provided by nvidia-dkms-550)
nvidia-driver-525-open, (kernel modules provided by linux-modules-nvidia-525-open-generic-hwe-22.04)
nvidia-driver-550-open, (kernel modules provided by nvidia-dkms-550-open)
nvidia-driver-535, (kernel modules provided by linux-modules-nvidia-535-generic-hwe-22.04)
nvidia-driver-535-server-open, (kernel modules provided by linux-modules-nvidia-535-server-open-generic-hwe-22.04)
nvidia-driver-545, (kernel modules provided by linux-modules-nvidia-545-generic-hwe-22.04)
nvidia-driver-535-open, (kernel modules provided by linux-modules-nvidia-535-open-generic-hwe-22.04)
nvidia-driver-545-open, (kernel modules provided by linux-modules-nvidia-545-open-generic-hwe-22.04)
nvidia-driver-470, (kernel modules provided by linux-modules-nvidia-470-generic-hwe-22.04)
```

To check what drivers are suggested for your graphics card use `sudo ubuntu-drivers devices`:
```
== /sys/devices/pci0000:00/0000:00:01.0/0000:01:00.0 ==
modalias : pci:v000010DEd000025A0sv00001028sd00000B19bc03sc02i00
vendor   : NVIDIA Corporation
model    : GA107M [GeForce RTX 3050 Ti Mobile]
driver   : nvidia-driver-550-open - third-party non-free
driver   : nvidia-driver-525 - third-party non-free
driver   : nvidia-driver-535-server-open - distro non-free
driver   : nvidia-driver-520 - third-party non-free
driver   : nvidia-driver-525-server - distro non-free
driver   : nvidia-driver-525-open - distro non-free
driver   : nvidia-driver-545-open - distro non-free
driver   : nvidia-driver-530 - third-party non-free
driver   : nvidia-driver-535 - third-party non-free
driver   : nvidia-driver-470-server - distro non-free
driver   : nvidia-driver-545 - third-party non-free
driver   : nvidia-driver-550 - third-party non-free
driver   : nvidia-driver-535-server - distro non-free
driver   : nvidia-driver-515 - third-party non-free
driver   : nvidia-driver-535-open - distro non-free
driver   : nvidia-driver-470 - distro non-free recommended
driver   : xserver-xorg-video-nouveau - distro free builtin
```

here we can see that the `recommended` driver is `470`, to install it just run `sudo ubuntu-drivers install`.

If for some reason you want to install a different driver wrt the recommended one you can run `sudo ubuntu-drivers install nvidia:535` for example.


<details>
<summary>Eventual Troubleshooting</summary>

```console
flferretti@iiticubws065:~$ sudo apt install nvidia-driver-535
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Some packages could not be installed. This may mean that you have
requested an impossible situation or if you are using the unstable
distribution that some required packages have not yet been created
or been moved out of Incoming.
The following information may help to resolve the situation:

The following packages have unmet dependencies:
 nvidia-driver-535 : Depends: libnvidia-gl-535 (= 535.129.03-0ubuntu0.20.04.1) but it is not going to be installed
                     Depends: libnvidia-extra-535 (= 535.129.03-0ubuntu0.20.04.1) but it is not going to be installed
                     Depends: libnvidia-decode-535 (= 535.129.03-0ubuntu0.20.04.1) but it is not going to be installed
                     Depends: libnvidia-encode-535 (= 535.129.03-0ubuntu0.20.04.1) but it is not going to be installed
                     Depends: xserver-xorg-video-nvidia-535 (= 535.129.03-0ubuntu0.20.04.1) but it is not going to be installed
                     Depends: libnvidia-cfg1-535 (= 535.129.03-0ubuntu0.20.04.1) but it is not going to be installed
                     Depends: libnvidia-fbc1-535 (= 535.129.03-0ubuntu0.20.04.1) but it is not going to be installed
                     Recommends: libnvidia-compute-535:i386 (= 535.129.03-0ubuntu0.20.04.1)
                     Recommends: libnvidia-decode-535:i386 (= 535.129.03-0ubuntu0.20.04.1)
                     Recommends: libnvidia-encode-535:i386 (= 535.129.03-0ubuntu0.20.04.1)
                     Recommends: libnvidia-fbc1-535:i386 (= 535.129.03-0ubuntu0.20.04.1)
                     Recommends: libnvidia-gl-535:i386 (= 535.129.03-0ubuntu0.20.04.1)
E: Unable to correct problems, you have held broken packages.
```

Yet, there were no held packages:

```console
flferretti@iiticubws065:~$ dpkg --get-selections | grep hold
flferretti@iiticubws065:~$ 
```

So I tried

```sh
sudo apt install -f
```

that notified of some actually held broken packages that were not remove before, so after 
```sh
sudo apt autoremove
```

I could install the new drivers:
```sh
sudo apt install nvidia-driver-535
```

I verified the installation with:
```console
flferretti@iiticubws065:~$ dkms status
nvidia, 535.129.03, 5.13.0-44-generic, x86_64: installed
```

Yet `nvidia-smi` was not working, so I checked a couple more things:
```console
flferretti@iiticubws065:~$ nvidia-xconfig --query-gpu-info

ERROR: Unable to query GPU information

flferretti@iiticubws065:~$ sudo systemctl status display-manager
● gdm.service - GNOME Display Manager
     Loaded: loaded (/lib/systemd/system/gdm.service; static; vendor preset: enabled)
     Active: active (running) since Mon 2023-11-06 17:46:17 CET; 5min ago
    Process: 1784 ExecStartPre=/usr/share/gdm/generate-config (code=exited, status=0/SUCCESS)
    Process: 1786 ExecStartPre=/usr/lib/gdm3/gdm-wait-for-drm (code=exited, status=0/SUCCESS)
   Main PID: 2010 (gdm3)
      Tasks: 3 (limit: 462654)
     Memory: 5.5M
     CGroup: /system.slice/gdm.service
             └─2010 /usr/sbin/gdm3

nov 06 17:46:07 iiticubws065 systemd[1]: Starting GNOME Display Manager...
nov 06 17:46:17 iiticubws065 systemd[1]: Started GNOME Display Manager.
nov 06 17:46:17 iiticubws065 gdm-launch-environment][2015]: pam_unix(gdm-launch-environment:session): s>
nov 06 17:46:17 iiticubws065 gdm-launch-environment][2015]: pam_unix(gdm-launch-environment:session): s>
nov 06 17:46:17 iiticubws065 gdm3[2010]: GdmDisplay: Session never registered, failing
nov 06 17:46:17 iiticubws065 gdm3[2010]: Child process -2029 was already dead.
nov 06 17:46:18 iiticubws065 gdm3[2164]: modprobe: FATAL: Module nvidia not found in directory /lib/mod>
nov 06 17:46:28 iiticubws065 gdm3[2195]: modprobe: FATAL: Module nvidia not found in directory /lib/mod>
nov 06 17:46:38 iiticubws065 gdm3[2010]: Child process -2029 was already dead.
nov 06 17:46:38 iiticubws065 gdm-launch-environment][2140]: pam_unix(gdm-launch-environment:session): 
```

Therefore I started again purging packages related to nvidia and adding kernel headers that were deleted during the purge.

</details>

Remember to reinistall linux-headers as they might get cancelled:
```sh
sudo apt-get install linux-headers-$(uname -r)
```

and finally install the chosen driver:

```sh
sudo apt-get install nvidia-driver-535
```

After rebooting it should work fine:

```console
flferretti@iiticubws065:~$ nvidia-smi
Mon Nov  6 20:19:04 2023       
+---------------------------------------------------------------------------------------+
| NVIDIA-SMI 535.129.03             Driver Version: 535.129.03   CUDA Version: 12.2     |
|-----------------------------------------+----------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |         Memory-Usage | GPU-Util  Compute M. |
|                                         |                      |               MIG M. |
|=========================================+======================+======================|
|   0  Quadro RTX 6000                Off | 00000000:AF:00.0 Off |                  Off |
| 40%   63C    P8               9W / 260W |     15MiB / 24576MiB |      0%      Default |
|                                         |                      |                  N/A |
+-----------------------------------------+----------------------+----------------------+
                                                                                         
+---------------------------------------------------------------------------------------+
| Processes:                                                                            |
|  GPU   GI   CI        PID   Type   Process name                            GPU Memory |
|        ID   ID                                                             Usage      |
|=======================================================================================|
|    0   N/A  N/A      1503      G   /usr/lib/xorg/Xorg                           10MiB |
|    0   N/A  N/A      1915      G   /usr/bin/gnome-shell                          3MiB |
+---------------------------------------------------------------------------------------+
```

## NVIDIA CUDA Toolkit 12.3

As installing from Ubuntu repository might not end up with the latest CUDA version, a possibility is to install CUDA from the NVIDIA repository following the [steps reported on their website](https://developer.nvidia.com/cuda-downloads):

```sh
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/cuda-ubuntu2004.pin
sudo mv cuda-ubuntu2004.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget https://developer.download.nvidia.com/compute/cuda/12.3.0/local_installers/cuda-repo-ubuntu2004-12-3-local_12.3.0-545.23.06-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu2004-12-3-local_12.3.0-545.23.06-1_amd64.deb
sudo cp /var/cuda-repo-ubuntu2004-12-3-local/cuda-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install cuda-toolkit-12-3
```

As in some cases cuDNN is also needed, it is possible to install it following a guide on [NVIDIA Docs](https://docs.nvidia.com/deeplearning/cudnn/archives/cudnn-831/install-guide/index.html#download).

Download the `tar` file from the [cuDNN homepage](https://developer.nvidia.com/cudnn) (you should be registered in the NVIDIA Developer Program):

```sh
tar -xvf cudnn-linux-x86_64-8.9.6.50_cuda12-archive.tar.xz
sudo cp cudnn-*-archive/include/cudnn*.h /usr/local/cuda/include 
sudo cp -P cudnn-*-archive/lib/libcudnn* /usr/local/cuda/lib64 
sudo chmod a+r /usr/local/cuda/include/cudnn*.h /usr/local/cuda/lib64/libcudnn*
```

and add it to the system-wide `.bashrc`:

> [!WARNING] 
> This may potentially lead to conflicts on a shared machine, so it's worth considering the option to revert these changes. If a user requires cuDNN, the following commands, executed without the need for `sudo`, can be used to enable its functionality

```sh
echo 'export PATH="/usr/local/cuda-12.3/bin:$PATH"' | sudo tee -a /etc/bash.bashrc
echo 'export LD_LIBRARY_PATH="/usr/local/cuda-12.3/lib64:$LD_LIBRARY_PATH"' | sudo tee -a /etc/bash.bashrc
```

cuDNN is now installed 🎉 

```console
flferretti@iiticubws065:~$ nvcc --version
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2023 NVIDIA Corporation
Built on Fri_Sep__8_19:17:24_PDT_2023
Cuda compilation tools, release 12.3, V12.3.52
Build cuda_12.3.r12.3/compiler.33281558_0
```

You may also need to re-install **NVIDIA Container Toolkit** as it gets purged from the driver installation. Again, it is recommended to follow the guide on [NVIDIA Container Toolkit homepage](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html):

```sh
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg

curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
    sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
    sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list 

sudo apt-get update
sudo apt-get install -y nvidia-container-toolkit
```

and then configure Docker:

```sh
sudo nvidia-ctk runtime configure --runtime=docker
sudo systemctl restart docker
```
