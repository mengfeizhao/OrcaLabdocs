# OrcaSim安装与配置指南

OrcaSim的安装需完成环境准备、资源下载、解压合并、启动调试及后续配置（分支切换、机器人替换、摄像头参数设置），以下为详细步骤，确保操作流程与原意一致。

## 一、环境与工具准备

在开始安装前，需确认基础环境与必要工具已就绪。

### 1. 基础环境要求

- 操作系统：**Ubuntu 22.04**（需确保系统正常运行，无依赖冲突）。
- 传输工具：**FTP客户端（如FileZilla）**（用于从远程服务器下载OrcaSim及Assets资源）。
  
  ### 2. FTP工具连接配置（以FileZilla为例）
  
  打开FileZilla，在顶部输入远程服务器信息并连接，界面如下：
- **Host**：远程服务器地址（示例：`ftp://lijianan@47.116.64.88:20080`）。
- **Username**：登录用户名（示例：`lijianan`）。
- **Password**：登录密码（根据实际分配填写）。
- **Port**：端口号（示例：`20080`）。
- 点击「Quickconnect」建立连接，首次连接可能提示“Insecure FTP connection”，确认后即可进入远程目录。
  
  
  ![](C:\Users\lhvst\AppData\Roaming\marktext\images\2025-10-23-10-00-24-image.png)![](C:\Users\lhvst\AppData\Roaming\marktext\images\2025-10-23-10-00-27-image.png)
  
  ## 二、安装资源下载
  
  需下载两类核心资源：`OrcaSim`主程序包与`Assets`资产包，均通过FTP从远程服务器获取。
  
  ### 1. OrcaSim主程序下载
1. 在FileZilla远程站点中，导航至资源路径（示例：`/2025.10.10`），找到`OrcaSim.tar.gz`文件（大小约5.7 GB）。
2. 在本地电脑指定下载路径（示例：`/home/user/yanshi/`），右键选择“下载”或双击文件开始传输。
3. 查看底部“Queue”队列面板，监控下载进度，等待传输完成。
   
   
   ![](C:\Users\lhvst\AppData\Roaming\marktext\images\2025-10-23-10-00-42-image.png)
   
   ### 2. Assets资产包下载
   
   在远程服务器中找到`Assets`相关压缩包（或文件夹），按上述相同方式下载至本地（路径建议与OrcaSim主程序放在同一目录，如`/home/user/yanshi/`）。
   
   
   ![](C:\Users\lhvst\AppData\Roaming\marktext\images\2025-10-23-10-00-51-image.png)
   
   ## 三、资源解压与文件夹合并
   
   下载完成后，需将`Assets`资产合并至`OrcaSim`主程序目录，确保资产文件正常加载。
   
   ### 1. 解压压缩包
- 打开Ubuntu终端，进入下载目录（示例：`cd /home/user/yanshi/`）。
- 解压`OrcaSim.tar.gz`：执行`tar -zxvf OrcaSim.tar.gz`，生成`OrcaSim`文件夹。
- 解压`Assets`压缩包（若为压缩格式）：执行对应解压命令（如`unzip Assets.zip`），生成`Assets`文件夹。
  
  ### 2. 合并Assets至OrcaSim
1. 打开文件管理器，进入解压后的`Assets`文件夹，全选所有子文件（如`AssetBundles`、`Levels`、`Shaders`等）。
2. 复制选中文件，粘贴至`OrcaSim`主目录（路径：`/home/user/yanshi/OrcaSim/`）。
3. 系统会提示“Merge Folder”（文件夹重名，如`Levels`），选择「Merge」确认合并（合并前会自动保留冲突文件，无需手动删除原有内容）。
   
   
   ![](C:\Users\lhvst\AppData\Roaming\marktext\images\2025-10-23-10-01-05-image.png)![](C:\Users\lhvst\AppData\Roaming\marktext\images\2025-10-23-10-01-09-image.png)![](C:\Users\lhvst\AppData\Roaming\marktext\images\2025-10-23-10-01-12-image.png)
   
   ## 四、OrcaSim启动与依赖问题解决
   
   启动OrcaSim需执行脚本，若遇到“缺失依赖包”错误，需通过配置软件源修复。
   
   ### 1. 启动OrcaSim（首次尝试）
4. 进入OrcaSim主目录：
   
   ```bash
   cd /home/yanshi/OrcaSim
   ```
5. 打开终端，执行启动脚本：
   
   ```bash
   bash start.sh
   ```
   
   ### 2. 常见问题：缺失依赖包
   
   启动时终端可能提示“缺少xxx包”，原因是系统软件源配置无效，无法下载依赖。
   
   #### 解决步骤：
   
   ##### ① 检查当前软件源配置
   
   ```bash
   # 查看主软件源列表
   cat /etc/apt/sources.list
   # 查看额外软件源配置（可选）
   ls /etc/apt/sources.list.d/
   ```
   
   ##### ② 备份并替换为有效软件源
   
   ```bash
   # 备份当前软件源配置（防止误操作）
   sudo cp /etc/apt/sources.list /etc/apt/sources.list.backup
   # 创建新的软件源配置（使用Ubuntu官方源，适配22.04（jammy））
   sudo tee /etc/apt/sources.list > /dev/null <<'EOF'
   deb http://archive.ubuntu.com/ubuntu/ jammy main restricted universe multiverse
   deb http://archive.ubuntu.com/ubuntu/ jammy-updates main restricted universe multiverse
   deb http://archive.ubuntu.com/ubuntu/ jammy-security main restricted universe multiverse
   deb http://archive.ubuntu.com/ubuntu/ jammy-backports main restricted universe multiverse
   EOF
   ```
   
   ##### ③ 清理缓存并更新软件包
   
   ```bash
   # 清理本地缓存（避免旧源干扰）
   sudo apt clean
   sudo apt autoclean
   # 更新软件包列表（加载新源）
   sudo apt update
   # 升级已安装包（可选，确保系统组件兼容）
   sudo apt upgrade -y
   ```
   
   ##### ④ 重新下载依赖并启动
6. 再次执行启动脚本，系统会自动下载缺失的依赖包：
   
   ```bash
   bash start.sh
   ```
7. **注意**：首次启动时，OrcaSim会自动导入资产，过程耗时较长（界面会显示“analyzing jobs remaining xxx”“processing jobs remaining xxx”），需耐心等待。
8. 若部分资产导入失败（界面标注“Failed”），需手动从其他完整环境的`OrcaSim/Assets`目录复制对应资产文件，粘贴至本地`Assets`后，重新执行`bash start.sh`。
   
   ## 五、分支切换（OrcaGym）
   
   若需使用特定功能分支（如`kpsCollection`），需在`OrcaGym`目录下执行分支切换操作。
9. 进入目标目录：
   
   ```bash
   cd /path/to/OrcaGym/examples/openpi/ # 替换为实际OrcaGym路径
   ```
10. 查看当前分支：
    
    ```bash
    git branch # 列表中带“*”的为当前分支
    ```
11. 切换至`kpsCollection`分支：
    
    ```bash
    git checkout kpsCollection
    ```
12. 拉取分支最新代码：
    
    ```bash
    git pull
    ```
    
    ## 六、机器人替换配置
    
    由于机器人模型更新，需替换场景中的旧机器人，并确保位置与参数一致。
    
    ### 操作步骤：
13. **导入新机器人资源**：将新机器人的`mujoco_wrap`文件夹从本地路径拖入OrcaSim的“资产浏览”面板。
14. **添加新机器人至场景**：在资产浏览中找到新机器人模型，将其拖入场景画布。
15. **匹配坐标与旋转参数**：
    - 选中新机器人，打开“组件属性”面板，查看`Transform`（变换）参数（`Translate`平移、`Rotate`旋转）。
    - 选中旧机器人，复制其`Translate`（如X=0.0m, Y=0.0m, Z=0.0m）和`Rotate`（如X=0.0deg, Y=0.0deg, Z=0.0deg）参数，粘贴到新机器人的对应位置，确保两者位置完全重合。
16. **删除旧机器人**：选中旧机器人的`mujoco_wrap`组件，按`Delete`键删除。
    
    
    ![](C:\Users\lhvst\AppData\Roaming\marktext\images\2025-10-23-10-02-11-image.png)
    
    ![](C:\Users\lhvst\AppData\Roaming\marktext\images\2025-10-23-10-02-16-image.png)![](C:\Users\lhvst\AppData\Roaming\marktext\images\2025-10-23-10-02-19-image.png)![](C:\Users\lhvst\AppData\Roaming\marktext\images\2025-10-23-10-02-23-image.png)
    
    ## 七、摄像头参数配置
    
    为确保数据采集一致性，需保持新机器人的摄像头配置与旧配置完全一致，主要涉及**左右手腕摄像头**与**头部摄像头**。
    
    ### 1. 左右手腕摄像头（以`camera_wrist_l`为例）
    
    ![](C:\Users\lhvst\AppData\Roaming\marktext\images\2025-10-23-10-04-24-image.png)
    
    ### 2. 头部摄像头（`camera_head`）
    
    ![](C:\Users\lhvst\AppData\Roaming\marktext\images\2025-10-23-10-04-27-image.png)


