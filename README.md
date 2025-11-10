# IsaacSim-IsaacLab-installation-for-Windows-Easy-Tutorial


Steps below mirror the official guide and add Windows‑specific checks. (Isaac Sim)

Ground rules
Do not use WSL for Isaac Sim/Isaac Lab. Run everything in Windows Terminal or PowerShell.


Use Python 3.11 for Isaac Sim 5.x. Other versions will fail. (Isaac Sim)


First run may fetch many extensions. A blank window and heavy network activity are normal on first launch. (Isaac Sim)



## Step 0 — Verify your GPU and driver 
Why: Isaac Sim 5.1 was tested on Windows driver 580.88. Older drivers can fail or disable RTX features. (Isaac Sim Documentation)
How (PowerShell):
```nvidia-smi```

Check the Driver Version in the output. If it’s < 580.88, update via GeForce Experience or NVIDIA’s driver page. Then reboot. (Isaac Sim Documentation)
Optional on laptops with Intel + NVIDIA: force high‑performance GPU for Python and Isaac Sim in Settings → System → Display → Graphics by adding the app and choosing High performance. (Microsoft Learn)

## Step 1 — Enable long file paths (Windows one‑time setup) 
Why: Long paths can break installs. (Microsoft Learn)
How (PowerShell as Admin):
```New-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\FileSystem" ```

``` -Name "LongPathsEnabled" -Value 1 -PropertyType DWORD -Force```

Reboot if this is your first time enabling it. (Microsoft Learn)


## Step 2 — Install the small prerequisites
### 2.1 Git (for cloning Isaac Lab): 
Download and install Git for Windows or use winget:

```winget install --id Git.Git -e```
 (Git)


### 2.2 A Python env manager (choose one):
Recommended: Miniconda


Download “Miniconda3 Windows x86_64” and install. Accept defaults.
 (Anaconda) 


official instructions. (Isaac Sim)

## Step 3 — Create a clean Python 3.11 environment

Conda 
(on a conda cli: click on windows search option, type “anaconda prompt”)
```
conda create -n env_isaaclab python=3.11 -y 
conda activate env_isaaclab 
python -m pip install --upgrade pip 
```

Python 3.11 is mandatory for Isaac Sim 5.x. (Isaac Sim)

## Step 4 — Install Isaac Sim 5.1 (pip)
In the activated env:
```pip install "isaacsim[all,extscache]==5.1.0" --extra-index-url https://pypi.nvidia.com``` 

This is the exact command from the docs. (Isaac Sim)

## Step 5 — Install PyTorch with CUDA for Windows
In the same env:
```pip install -U torch==2.7.0 torchvision==0.22.0 --index-url https://download.pytorch.org/whl/cu128``` 


Quick test:
```
Python 
import torch 
print("Torch", torch.__version__, "CUDA available:", torch.cuda.is_available())
if torch.cuda.is_available():
    print("GPU:", torch.cuda.get_device_name(0)) 
```

Expected output: 
```GPU: NVIDIA GeForce RTX 4090 Laptop GPU```

Interpretation: 
PyTorch installed correctly (import torch worked).
CUDA runtime is active (otherwise it wouldn’t detect the GPU).
Windows driver + PyTorch are communicating properly with your RTX 4090.
So you don’t need to install the standalone CUDA Toolkit — the cu128 wheel already included the required runtime.



## Step 6 — Validate Isaac Sim before touching Isaac Lab
### 6.1 Run the Compatibility Checker app (fast, no projects needed):
```isaacsim isaacsim.exp.compatibility_check``` 

Expected output (depends on your machine configuration)


Field
Your Status
Meaning
Driver 581.57 (✅ green)
Perfect — your Windows NVIDIA driver is new enough and recognized.


GPU: RTX 4090 Laptop GPU (✅ green)
Isaac Sim can see and talk to your GPU correctly through Vulkan + CUDA.


VRAM 17 GB (light green)
GPU memory detected and usable; “light green” just shows a normal range for mobile 4090s.


RAM 34 GB (🟧 orange)
Informational warning — the check recommends ≥ 64 GB for very heavy RTX simulations, but 32–34 GB is still fine for learning, tutorials, and medium workloads. It’s not an error.


CPU Governor “not available”
Windows doesn’t expose Linux-style CPU governors; this field is only relevant on Linux. Safe to ignore.



Conclusion:
 Your environment (GPU, driver, Vulkan, CUDA, Isaac Sim core) is working correctly.

Close it after the checks. (Isaac Sim Documentation)

### 6.2 Launch the full app once to pull extensions and accept the EULA:
```Isaacsim``` 

On first launch, reply Yes to the Omniverse EULA prompt (one time)

The first run can take several minutes while extensions download and cache. This is expected. (Isaac Sim)

## Step 7 — Install Isaac Lab from source
### 7.1 Clone the repo:
```
cd %USERPROFILE%
git clone https://github.com/isaac-sim/IsaacLab.git 
cd IsaacLab
```
(Isaac Sim)
### 7.2 Install Isaac Lab extras using the Windows helper:
```isaaclab.bat --install``` 

This installs Isaac Lab’s extensions and optional RL libraries.. (Isaac Sim)

## Step 8 — Smoke tests for Isaac Lab
### 8.1 Minimal sim window:
```isaaclab.bat -p scripts\tutorials\00_sim\create_empty.py``` 

A black viewport window should appear. Close with Ctrl+Break or stop in the terminal. (Isaac Sim)

Expected outputs
“it opens the isaacsim application window showing just a black screen on the view port. my power shell logs were: 
"[22.704s] app ready
[27.287s] Simulation App Startup Complete
[27.386s] [ext: omni.physx.fabric-107.3.26] startup
[INFO]: Setup complete..."
“
### 8.2 Quick RL example (headless):
```isaaclab.bat -p scripts\reinforcement_learning\rsl_rl\train.py --task=Isaac-Ant-v0 --headless```

This confirms Python bindings, PyTorch, and sim are wired correctly. (Isaac Sim)

Expected outputs:
Runs RL training on your anaconda cli.
