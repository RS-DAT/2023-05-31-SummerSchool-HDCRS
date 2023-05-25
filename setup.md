# Requirements/Installation  
## Remote Sensing Data Analysis using HTC/HPC Systems 

### MacOS*: 
- Terminal 
- Homebrew or an alternate package manager 
  - To install Homebrew: 
    ```
    /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
    ```
- Python  
  - You can test this using `python3 --version` – you require a minimum of python3.X  
  - To install python 3.X – we would recommend python 3.10 
    ```shell
    brew install python@3.X
    ``` 
- Python Packages: fabric and decorator 
  - To install: 
    ```
    pip install fabric decorator
    ```
- SSH
  - You can test this using `ssh` – this should output the usage of ssh 
  - To install SSH: 
    ```
    brew install openssh
    ``` 
 
### Linux*: 
- Terminal 
- Python  
  - You can test this using `python3 --version` – you require a minimum of python3.X  
  - To install python 3.X – we would recommend python 3.10 (use different package manager for distributions different from Ubuntu)
    ```shell
    sudo apt-get install python3.X
    ```
- Python Packages: fabric and decorator 
  - To install: 
    ```
    pip install fabric decorator
    ```
- SSH 
  - You can test this using `ssh` – this should output the usage of ssh 
  - To install ssh: 
    ```shell
    sudo apt install openssh-server
    ``` 
 
### Windows*: 
- Windows subsystem for linux 2 (WSL); To install WSL: 
  - Open PowerShell as administrator
  - Run `wsl --install`, by default Ubuntu distribution will be installed 
  - Restart PC 
  - After restarting, follow the instructions on the automatically opened Ubuntu terminal to setup ubuntu ID and password 
  - Follow above instructions as given for Linux starting from: 2) Python 

\* **If  you are familiar with conda or venv, please feel free to create an environment with the following dependencies: `python3.10`, `fabric`, `decorator`**
 
### For all systems: 
- Download and extract the following zip archive: [Link](https://github.com/RS-DAT/JupyterDaskOnSLURM/archive/refs/heads/workshops.zip) 
- Access the folder in your terminal using:
  ```
  cd JupyterDaskOnSLURM-workshops
  ```
- Run:
  ```shell
  python runJupyterDaskOnSLURM.py
  ```
  which should return: 
  ```
  usage: runJupyterDaskOnSLURM.py [-h] [--local_port LOCAL_PORT] [--wait_time WAIT_TIME] (--add_platform | --one_off | --uid UID) [--mode MODE] 
  runJupyterDaskOnSLURM.py: error: one of the arguments --add_platform/-a --one_off/-oo --uid/-u is required 
  ```
