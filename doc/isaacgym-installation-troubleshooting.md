# IsaacGym Installation Troubleshooting

Using Ubuntu 22.04 which is not officially supported yet.

## ImportError: libpython

```plain
ImportError: libpython3.7m.so.1.0: cannot open shared object file: No such file or directory
```

In your conda env run:

```plain
conda env config vars set LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/xyz/mambaforge/envs/rlgpu/lib
```
where `mambaforge` should be substituted with `anaconda3` if you are using Anaconda and `xyz` is your username.

## Error: failed to solve Docker

```plain
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
```bash
docker login nvcr.io
```
9. Log in using your API Key from ngc.nvidia.com:
```bash
Username: $oauthtoken #literally, it is the same for all users
Password: <enter-NGC-API-key>
```

## Error: FDX Library failed to load
```bash
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
```bash
python -m pip install name_of_the_wheel_file.whl
```
Note that pip will handle the location where the wheel file is extracted. If you do not have the appropriate permissions when running pip you may get the following messsage: "Defaulting to user installation because normal site-packages is not writeable" .
 
Optional: Copy the sample programs for Python FBX to a suitable location. The samples are in `<yourFBXPythonSDKpath>/<version>`.

I wasn't able to find any wheel package in the specified folder, but it seems to work anyway stopping at step 4!

## RuntimeError: CUDA unknown error

```plain 
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
