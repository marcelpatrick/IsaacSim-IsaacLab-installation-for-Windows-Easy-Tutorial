# IsaacSim-IsaacLab-installation-for-Windows-Easy-Tutorial


## Ground rules
- Do not use WSL for Isaac Sim/Isaac Lab. Run everything in Windows Terminal or PowerShell.
- Use Python 3.11 for Isaac Sim 5.x. Other versions will fail. 

-> This tutorial is inspired by the official tutorial (Isaac Sim: https://isaac-sim.github.io/IsaacLab/main/source/setup/installation/pip_installation.html) but explain all the steps in a more beginner-friendly way.

# IsaacSIM

## Step 0 — Verify your GPU and driver 
- Why: Isaac Sim 5.1 was tested on Windows driver 580.88. Older drivers can fail or disable RTX features. 
- How (on windows PowerShell): ```nvidia-smi```

- Check the Driver Version in the output. If it’s < 580.88, update via GeForce Experience or NVIDIA’s driver page. Then reboot.
  
- Optional on laptops with Intel + NVIDIA: force high‑performance GPU for Python and Isaac Sim in Settings → System → Display → Graphics by adding the app and choosing High performance. (Microsoft Learn)

## Step 1 — Enable long file paths (Windows one‑time setup) 
- Why: Long paths can break installs. 
- How (on PowerShell as Admin): click on Windows search option, search for powershell, don't open it. Instead, click on the app with the right button and click on open as admin. 
```New-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\FileSystem" ```

``` -Name "LongPathsEnabled" -Value 1 -PropertyType DWORD -Force```

Reboot if this is your first time enabling it.


## Step 2 — Install the prerequisites

### 2.1 Git (for cloning Isaac Lab): 
- Download and install Git for Windows or use winget:
```winget install --id Git.Git -e```
- update pip install
``` python -m pip install --upgrade pip```
 
### 2.2 A Python env manager:
- Download “Miniconda3 Windows x86_64” and install: https://www.anaconda.com/docs/getting-started/miniconda/install. Accept defaults.

- official instructions. ([Isaac Sim](https://isaac-sim.github.io/IsaacLab/main/source/setup/installation/pip_installation.html))

## Step 3 — Create a clean Python 3.11 environment

- Conda: (on a conda cli: click on Windows search option, type “anaconda prompt”, click on it to open the cli)
```
conda create -n env_isaaclab python=3.11 -y 
conda activate env_isaaclab 
```

## Step 4 — Install Isaac Sim 5.1 
- In the conda activated env:
```
pip install "isaacsim[all,extscache]==5.1.0" --extra-index-url https://pypi.nvidia.com
``` 

Source from the docs. ([Isaac Sim](https://isaac-sim.github.io/IsaacLab/main/source/setup/installation/pip_installation.html))

## Step 5 — Install PyTorch with CUDA for Windows
- In the same env:
```
pip install -U torch==2.7.0 torchvision==0.22.0 --index-url https://download.pytorch.org/whl/cu128
``` 

- Quick test:
```
python -c "import torch; print(torch.__version__, torch.cuda.is_available())"
```

If everything went as expected up to here: 
- PyTorch installed correctly (import torch worked).
- CUDA runtime is active (otherwise it wouldn’t detect the GPU).
- Windows driver + PyTorch are communicating properly with your GPU.
-> you don’t need to install the standalone CUDA Toolkit — the cu128 wheel already included the required runtime.

## Step 6 — Validate Isaac Sim before running Isaac Lab

### 6.1 Run the Compatibility Checker app :
```isaacsim isaacsim.exp.compatibility_check``` 

Expected output (depends on your machine configuration)

- Driver Version (✅ green)
 your Windows NVIDIA driver is new enough and recognized.

- GPU: It will show your GPU version (✅ green)
Isaac Sim can see and talk to your GPU correctly through Vulkan + CUDA.

- VRAM (light green)
GPU memory detected and usable; “light green” just shows a normal range for mobile 4090s.

- RAM GB (🟧 orange)
Informational warning — the check recommends ≥ 64 GB for very heavy RTX simulations, but 32–34 GB is still fine for learning, tutorials, and medium workloads. It’s not an error.

- CPU Governor “not available”
Windows doesn’t expose Linux-style CPU governors; this field is only relevant on Linux. Safe to ignore.

If all these are checked, your environment (GPU, driver, Vulkan, CUDA, Isaac Sim core) are working correctly.

Close it after the checks. 

### 6.2 Launch the full app once to pull extensions and accept the EULA:
```Isaacsim``` 

- On first launch, reply Yes to the Omniverse EULA prompt (one time)

- The first run can take several minutes while extensions download and cache. This is expected.  

# IsaacLab

## Step 7 — Install Isaac Lab from source

### 7.1 Clone the repo:
```
cd %USERPROFILE%
git clone https://github.com/isaac-sim/IsaacLab.git 
cd IsaacLab
```
Source: ([Isaac Sim](https://isaac-sim.github.io/IsaacLab/main/source/setup/installation/pip_installation.html))

### 7.2 Install Isaac Lab extras using the Windows helper:
```
isaaclab.bat --install
pip install click==8.1.7
``` 

This installs Isaac Lab’s extensions and optional RL libraries.

## Step 8 — Smoke tests for Isaac Lab

### 8.1 Minimal sim window:
```
isaaclab.bat -p scripts\tutorials\00_sim\create_empty.py
``` 
- A black viewport window should appear. Close with Ctrl+C or stop in the terminal.

 Source: ([Isaac Sim](https://isaac-sim.github.io/IsaacLab/main/source/setup/installation/pip_installation.html))

Expected outputs
- it opens the isaacsim application window showing just a black screen on the view port.
- anaconda prompt logs : 
```
[22.704s] app ready
[27.287s] Simulation App Startup Complete
[27.386s] [ext: omni.physx.fabric-107.3.26] startup
[INFO]: Setup complete...
```

### 8.2 Run Quick RL example 

Navigate to ```C:\Users\[YOUR USER]\IsaacLab```

Run it headless: It will run the RL script but not open the IsaacSim application 
```
isaaclab.bat -p scripts\reinforcement_learning\rsl_rl\train.py --task=Isaac-Ant-v0 --headless
```
It will run the RL training (1000 iterations). You can stop it with Ctrl+c

Run it full: It will run the RL script AND open the IsaacSim application showind the progress there. Takes longer
```
isaaclab.bat -p scripts\reinforcement_learning\rsl_rl\train.py --task=Isaac-Ant-v0
```

Run other demos available in the repo:
```
python scripts\tutorials\05_controllers\run_diff_ik.py
python scripts\tutorials\01_assets\run_deformable_object.py
python scripts\tutorials\02_scene\create_scene.py
python scripts\tutorials\03_envs\create_quadruped_base_env.py
isaaclab.bat -p scripts\demos\h1_locomotion.py
```
