# IsaacGym Installation 
This is a step-by-step tutorial to install `IsaacGym` on Ubuntu 22.04. After each section, I provide information about my current system configuration.
## Install the Nvidia drivers
First of all, you need to install the Nvidia driver following this [tutorial](https://linuxhint.com/install-nvidia-drivers-on-ubuntu/)  

### 💻 My current system configuration

```console
uname -a
Linux iiticublap261 6.2.0-33-generic #33~22.04.1-Ubuntu SMP PREEMPT_DYNAMIC Thu Sep  7 10:33:52 UTC 2 x86_64 x86_64 x86_64 GNU/Linux
```

 
```
+---------------------------------------------------------------------------------------+
| NVIDIA-SMI 535.113.01             Driver Version: 535.113.01   CUDA Version: 12.2     |
|-----------------------------------------+----------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |         Memory-Usage | GPU-Util  Compute M. |
|                                         |                      |               MIG M. |
|=========================================+======================+======================|
|   0  NVIDIA GeForce GTX 1650 Ti     Off | 00000000:01:00.0 Off |                  N/A |
| N/A   49C    P0               7W /  50W |      5MiB /  4096MiB |      0%      Default |
|                                         |                      |                  N/A |
+-----------------------------------------+----------------------+----------------------+
                                                                                         
+---------------------------------------------------------------------------------------+
| Processes:                                                                            |
|  GPU   GI   CI        PID   Type   Process name                            GPU Memory |
|        ID   ID                                                             Usage      |
|=======================================================================================|
|    0   N/A  N/A      2724      G   /usr/lib/xorg/Xorg                            4MiB |
+---------------------------------------------------------------------------------------+
```


## Install IsaacGym

You can install IsaacGym via `conda`. Please download [Isaac Gym Preview 4](https://istitutoitalianotecnologia.sharepoint.com/:f:/r/sites/ArtificialandMechanicalIntelligence/Documenti%20condivisi/Software%20Engineering/Software?csf=1&web=1&e=4tbjux) from IIT sharepoint, or if you wish join the [preview release](https://developer.nvidia.com/isaac-gym) and download the `IsaacGym_Preview_4_Package.tar.gz`.
Once downloaded, extract the compressed folder, which should contain the following files:

> **Warning**
> The content is for **internal use only** and should not to be distributed to external parties.

```
.
├── assets
├── create_conda_env_rlgpu.sh
├── docker
├── docs
├── licenses
├── python
└── README.txt
```
> **Warning**
>  I strongly recommend using `mamba` to install `IsaacGym`, as the default `conda` installation process can be slow. If you decide to use mamba, apply the following patch to `create_conda_env_rlgpu.sh`:
```patch
24c24
<     conda env create -f ./rlgpu_conda_env.yml
---
>     mamba env create -f ./rlgpu_conda_env.yml
49c49
< echo "SUCCESS"
---
> echo "SUCCESS"
\ No newline at end of file
```
Assuming you save the above snippet in a file called `mamba.patch`, you can apply the patch with the following command:
```console
patch create_conda_env_rlgpu.sh mamba.patch
```

Moreover the `rlgpu_conda_env.yml` contains an environment with `python 3.7` which is deprecated. I suggest to change it to `python 3.8`. To do so you can apply the following patch to `./python/rlgpu_conda_env.yml`:
```patch
5d4
<   - defaults
7,10c6,9
<   - python=3.7
<   - pytorch=1.8.1
<   - torchvision=0.9.1
<   - cudatoolkit=11.1
---
>   - python=3.8
>   - pytorch=1.12.1
>   - torchvision=0.13.0
>   - cudatoolkit=11.1.1
```
Assuming you save the above snippet in a file called `env.patch`, you can apply the patch with the following command:
```console
patch ./python/rlgpu_conda_env.yml env.patch
```
At this point you can ran the `create_conda_env_rlgpu.sh`. The script will will create a new conda environment called `rlgpu` containing IsaacGym 

Finally you need to install the `isaacgym` package. To do so, navigate to the `isaacgym/python` folder and run the following command:
```console
pip install -e .
```

### 💻 System 
I'm currently using `Mambaforge 23.3.1-1` (`conda 23.3.1`)

## Test IsaacGym
Navigate to the `isaacgym/python/examples` and run the following command
```console
python 1080_balls_of_solitude.py
```

### 🎮 Here the final result

https://github.com/ami-iit/element_rl-for-locomotion/assets/16744101/deb21200-f799-4cad-af9d-8b70883533f0

## Troubleshooting

### ImportError: libpython

```console
ImportError: libpython3.7m.so.1.0: cannot open shared object file: No such file or directory
```

In your conda env run:

```sh
conda env config vars set LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$CONDA_PREFIX
```

### Error: failed to solve Docker

```sh
--------------------
   1 | >>> FROM nvcr.io/nvidia/pytorch:21.09-py3
   2 |     ENV DEBIAN_FRONTEND=noninteractive 
   3 |     
--------------------
ERROR: failed to solve: failed to fetch anonymous token: unexpected status: 401 Unauthorized
```

1. Log in to https://ngc.nvidia.com/.
2. In the top right corner, click your user account icon and select Setup.
3. Click **Get API key** to open the Setup > API Key page.
4. The API Key is the mechanism used to authenticate your access to the NGC container registry.
5. Click **Generate API Key** to generate your API key. 
6. Click **Confirm** to generate the key.
7. Run:
```sh
docker login nvcr.io
```
9. Log in using your API Key from ngc.nvidia.com:
```sh
Username: $oauthtoken # literally, it is the same for all users
Password: <enter-NGC-API-key>
```

### Error: FDX Library failed to load
```sh
Error: FBX library failed to load - importing FBX data will not succeed. Message: No module named 'fbx'
FBX tools must be installed from https://help.autodesk.com/view/FBX/2020/ENU/?guid=FBX_Developer_Help_scripting_with_python_fbx_installing_python_fbx_html
```
From [help.autodesk.com](https://help.autodesk.com/view/FBX/2020/ENU/?guid=FBX_Developer_Help_scripting_with_python_fbx_installing_python_fbx_html):
1. Go to [http://www.autodesk.com/fbx](http://www.autodesk.com/fbx)
2. Navigate to Downloads page and scroll down to the FBX Python Bindings section
3. Find the version of Python Binding for your development platform.
4. Download the file and install the Python Binding following the instructions on the extracted `install_FbxPythonBindings.txt`.
Let’s call the directory where you installed the Python Binding for FBX as `<yourFBXPythonSDKpath>`.
5. Go to `<yourFBXPythonSDKpath>` and type the command:
```sh
python -m pip install name_of_the_wheel_file.whl
```
Note that pip will handle the location where the wheel file is extracted. If you do not have the appropriate permissions when running pip you may get the following message: "Defaulting to user installation because normal site-packages is not writeable" .
 
Optional: Copy the sample programs for Python FBX to a suitable location. The samples are in `<yourFBXPythonSDKpath>/<version>`.

I wasn't able to find any wheel package in the specified folder, but it seems to work anyway stopping at step 4!

### RuntimeError: CUDA unknown error

```sh 
RuntimeError: CUDA unknown error - this may be due to an incorrectly set up environment, e.g. changing env variable CUDA_VISIBLE_DEVICES after program start. Setting the available devices to be zero.
```
Try first to check if CUDA is available:
```python
import torch
torch.cuda.is_available()
```

If you get `False`, then:
```bash
sudo rmmod nvidia_uvm
sudo modprobe nvidia_uvm
```
should solve the error. This might be due to a specific Nvidia driver version.
