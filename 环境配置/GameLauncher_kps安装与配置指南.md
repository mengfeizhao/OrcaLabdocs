# GameLauncher_kps安装与配置指南

GameLauncher_kps是OrcaSim场景加载与数据采集的核心工具，需配合OrcaGym完成环境配置、资源初始化及程序启动。以下为详细安装步骤，严格遵循原操作逻辑，确保功能正常运行。

## 一、环境准备

安装前需确认基础系统环境与硬件驱动满足要求，避免后续依赖冲突。

### 1. 系统与驱动要求

- **操作系统**：Ubuntu 22.04（需确保系统已更新至最新稳定版本）。
- **显卡驱动**：NVIDIA显卡驱动版本≥550（需支持Vulkan，确保仿真渲染正常）。
  
  ### 2. 验证显卡驱动
  
  打开终端，执行以下命令查看显卡驱动信息，确认版本符合要求：
  
  ```bash
  vulkaninfo --summary
  ```
- 输出结果中需包含“NVIDIA GPU Driver Version: 550.xx.xx”或更高版本，若版本过低需先升级驱动。
  
  ## 二、安装资源下载与目录配置
  
  需下载GameLauncher_kps与OrcaGym两大核心资源，并完成特定目录的文件复制，确保程序加载路径正确。
  
  ### 1. GameLauncher_kps下载与解压
  
  #### 步骤1：下载压缩包
  
  通过FTP工具（如FileZilla）下载`GameLauncher_kps.zip`压缩包至本地（建议保存至主文件夹目录，如`/home/admin01/`）。
  
  #### 步骤2：解压与目录整理
1. 右键点击`GameLauncher_kps.zip`，选择“提取到此处”，生成`GameLauncher_kps`文件夹。
2. 进入`GameLauncher_kps`文件夹，将内部的`GameLauncher`子文件夹移动至**主文件夹根目录**（如`/home/admin01/GameLauncher/`）。
3. 删除空文件夹`GameLauncher_kps`（避免冗余目录）。
   
   
   ![](C:\Users\lhvst\AppData\Roaming\marktext\images\2025-10-23-10-12-16-image.png)
   
   #### 步骤3：Cache目录配置
   
   需建立特定目录并复制Cache文件，确保GameLauncher加载资源时路径正确：
4. 执行命令创建目标目录：
   
   ```bash
   mkdir -p /home/orca/{99AFD77A-00FA-41C7-8F51-0E1E744AADF7}/Cache/linux
   ```
5. 复制`GameLauncher`中的Cache文件至上述目录：
   
   ```bash
   cp -r /home/admin01/GameLauncher/Cache/linux/* /home/orca/{99AFD77A-00FA-41C7-8F51-0E1E744AADF7}/Cache/linux/
   ```
   
   ![](C:\Users\lhvst\AppData\Roaming\marktext\images\2025-10-23-10-12-52-image.png)
   
   ### 2. OrcaGym下载（从GitHub克隆）
   
   OrcaGym提供数据采集与机械臂控制脚本，需通过Git克隆仓库至本地。
   
   #### 步骤1：安装Git（若未安装）
   
   ```bash
   sudo apt install git
   ```
   
   #### 步骤2：克隆OrcaGym仓库
   
   执行以下命令克隆GitHub仓库（仓库地址：https://github.com/openverse-orca/OrcaGym.git）：
   
   ```bash
   git clone https://github.com/openverse-orca/OrcaGym.git
   ```
- 克隆完成后，本地会生成`OrcaGym`文件夹（默认路径：`/home/admin01/OrcaGym/`）。
  
  ## 三、OrcaGym配置
  
  OrcaGym需完成资源初始化、conda环境创建及第三方依赖安装，确保脚本可正常运行。操作需参考`OrcaGym/README.md`与`OrcaGym/3rd_party/README.md`的说明。
  
  ### 1. 初始化OrcaGym资源（含Git LFS与子模块）
  
  #### 步骤1：安装Git LFS（用于加载大文件资源）
  
  ```bash
  sudo apt install git-lfs
  ```
  
  #### 步骤2：初始化Git LFS并拉取资源
1. 进入OrcaGym目录：
   
   ```bash
   cd /home/admin01/OrcaGym/
   ```
2. 初始化Git LFS：
   
   ```bash
   git lfs install
   ```
3. 拉取LFS管理的大文件资源：
   
   ```bash
   git lfs pull
   ```
   
   #### 步骤3：初始化Git子模块（加载第三方依赖）
   
   ```bash
   git submodule update --init --recursive
   ```
   
   ### 2. 安装Miniconda与创建conda环境
   
   OrcaGym依赖特定Python版本（3.12），需通过conda创建独立环境隔离依赖。
   
   #### 步骤1：下载Miniconda安装脚本
   
   ```bash
   wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
   ```
   
   #### 步骤2：执行Miniconda安装
   
   ```bash
   bash Miniconda3-latest-Linux-x86_64.sh
   ```
- 安装过程中按提示操作：输入`yes`确认许可协议，指定安装路径（默认`/home/admin01/miniconda3/`），最后选择`yes`初始化conda。
  
  #### 步骤3：激活conda并创建“orca”环境
1. 初始化conda（仅首次安装后需执行）：
   
   ```bash
   source ~/miniconda3/bin/activate
   ```
2. 创建Python 3.12的conda环境：
   
   ```bash
   conda create -n orca python=3.12 -y
   ```
3. 激活“orca”环境：
   
   ```bash
   conda activate orca
   ```
   
   ### 3. 安装OrcaGym核心依赖
   
   ```bash
   # 在OrcaGym目录下执行
   pip install -e .
   ```
   
   ### 4. 安装第三方依赖（3rd_party）
   
   参考`OrcaGym/3rd_party/README.md`，安装子模块依赖：
4. 进入3rd_party目录：
   
   ```bash
   cd /home/admin01/OrcaGym/3rd_party/
   ```
5. 安装3rd_party基础依赖：
   
   ```bash
   pip install -e .
   ```
6. 安装robomimic依赖：
   
   ```bash
   cd robomimic/
   pip install -r requirements.txt
   ```
   
   ![](C:\Users\lhvst\AppData\Roaming\marktext\images\2025-10-23-10-14-21-image.png)
   
   ## 四、切换OrcaGym分支（kpsCollection）
   
   需切换至特定功能分支（`kpsCollection`）以支持数据采集功能：
7. 返回OrcaGym根目录：
   
   ```bash
   cd /home/admin01/OrcaGym/
   ```
8. 查看当前分支（确认是否为目标分支）：
   
   ```bash
   git branch
   ```
9. 切换至`kpsCollection`分支：
   
   ```bash
   git checkout kpsCollection
   ```
10. 拉取分支最新代码：
    
    ```bash
    git pull
    ```
    
    ## 五、启动程序（GameLauncher与数据采集）
    
    ### 1. 启动GameLauncher（加载场景）
    
    #### 步骤1：进入脚本目录
    
    ```bash
    cd /home/admin01/OrcaGym/examples/openpi/
    ```
    
    #### 步骤2：执行启动命令（以“shop”场景为例）
    
    ```bash
    python run_gamelauncher.py --datalink_auth_config ~/GameLauncher/orcasim.json --orcasim_path ~/GameLauncher/OrcaStudio_2409.GameLauncher --level shop
    ```
- **参数说明**：
  - `--datalink_auth_config`：指定数据链路认证配置文件路径。
  - `--orcasim_path`：指定OrcaStudio的GameLauncher路径。
  - `--level`：指定加载的场景名称（此处为`shop`，可根据需求替换）。
    
    ### 2. 启动数据采集（OrcaGym）
    
    需**另起一个终端**执行，避免与GameLauncher进程冲突：
    
    #### 步骤1：激活conda环境
    
    ```bash
    conda activate orca
    ```
    
    #### 步骤2：安装额外依赖（pymediainfo）
    
    ```bash
    pip install pymediainfo
    ```
    
    #### 步骤3：执行数据采集脚本（以“shop”场景为例）
    
    ```bash
    python run_dual_arm_sim.py --task_config shop_task.yaml --action_type end_effector_osc --level 'shop'
    ```
- **参数说明**：
  - `--task_config`：指定场景配置文件（`shop_task.yaml`）。
  - `--action_type end_effector_osc`：设置机械臂控制方式为“末端执行器阻抗控制”。
  - `--level 'shop'`：指定数据存储目录（与GameLauncher场景名称一致）。
    
    ## 六、调整Mujoco版本（兼容要求）
    
    若出现Mujoco版本不兼容问题，需安装指定版本（3.3.3）：
1. 查看当前Mujoco版本：
   
   ```bash
   python -c "import mujoco; print(mujoco.__version__)"
   ```
2. 卸载旧版本：
   
   ```bash
   pip uninstall mujoco -y
   ```
3. 安装Mujoco 3.3.3：
   
   ```bash
   pip install mujoco==3.3.3
   ```
   
   ## 七、修改yaml配置文件（可选）
   
   仅当使用**OrcaGym 10.0版本之前**的版本时，需修改场景yaml配置文件（10.0及以后版本无需修改）。以`shop_task.yaml`为例，关键配置项如下：
   
   
   ![](C:\Users\lhvst\AppData\Roaming\marktext\images\2025-10-23-10-13-47-image.png)
   
   ### 关键配置项说明
- `level_name`：场景名称，需与`--level`参数一致。
- `object_bodys`/`object_sites`/`object_joints`：待操作物体的物理体、位点与关节名称（顺序需严格匹配）。
- `goal_bodys`/`goal_sites`：目标物体（如篮子）的物理体与位点名称。
- `random_light`/`num_lights`：灯光随机生成配置，控制场景光照多样性。
