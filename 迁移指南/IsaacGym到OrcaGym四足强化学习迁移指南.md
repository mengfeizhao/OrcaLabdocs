# Isaac Gym 到 OrcaGym 四足强化学习迁移指南

[请至钉钉文档查看附件《Framework_Comparison_Summary.md》](https://alidocs.dingtalk.com/i/nodes/PwkYGxZV3ZjlD2XXTyL7rLlkWAgozOKL?iframeQuery=anchorId%3DX02mh1hblgxx3qd8klfhmg)
[请至钉钉文档查看附件《Isaac_Gym_Migration_Guide.md》](https://alidocs.dingtalk.com/i/nodes/PwkYGxZV3ZjlD2XXTyL7rLlkWAgozOKL?iframeQuery=anchorId%3DX02mh1hbld9s7ew48q1yg)
讲解视频链接：[https://shanji.dingtalk.com/app/transcribes/76327569643136303939343739375f3534313730373734395f30](https://shanji.dingtalk.com/app/transcribes/76327569643136303939343739375f3534313730373734395f30)

## 框架对比分析

1. **代码设计对比**：Isaac Gym 采用面向对象的类配置模式，使用自定义 VecEnv 接口及 PyTorch Tensor（GPU）数据结构，支持同步向量化并行；OrcaGym 结合面向对象与字典配置，采用 Gymnasium 标准化接口，基于 NumPy Array（CPU/GPU 混合），可实现异步分布式并行。

2. **架构对比**：Isaac Gym 的架构流程为训练入口→Task Registry→LeggedRobot Environment（包含 Isaac Gym 物理引擎等）→RSL-RL PPO Runner；OrcaGym 的架构流程为训练入口→Gymnasium 环境注册→LeggedGymEnv（包含 Mujoco 等物理引擎等）→Stable-Baselines3/RLlib。

3. **核心依赖对比**：物理引擎上，Isaac Gym 依赖 NVIDIA 的 Isaac Gym，OrcaGym 则使用 Mujoco 等；深度学习库方面，Isaac Gym 必须使用 PyTorch，OrcaGym 可选择 PyTorch 或 TensorFlow；RL 算法库中，Isaac Gym 内置 RSL-RL，OrcaGym 采用 Stable-Baselines3/RLlib 等。
   
   ## 核心架构差异

4. **配置系统差异**：Isaac Gym 采用嵌套类进行配置，OrcaGym 则使用字典配置。

5. **数据流对比**：Isaac Gym 的操作基于 GPU Tensor，OrcaGym 的操作基于 NumPy。
   
   ## 移植核心思路

6. **总体策略**：需完成三层转换，分别是物理引擎层（从 Isaac Gym Physics 转换为 Mujoco 等）、环境接口层（从 VecEnv 转换为 Gymnasium AsyncEnv）、RL 算法层（从 RSL-RL 转换为 Stable-Baselines3/RLlib）。

7. **迁移路线图**：包含六个步骤，即理解项目结构、准备物理模型、重写环境类、转换核心逻辑、适配 RL 算法、测试与调优。

8. **关键转换点**：明确了 Isaac Gym 各组件对应的转换目标、难度及说明，例如将 `LeggedRobot(VecEnv)` 转换为 `LeggedGymEnv(OrcaGymAsyncEnv)` 的难度较高。
   
   ## 详细移植步骤

9. **环境分析与准备**：分析原有 Isaac Gym 代码结构，识别关键代码；搭建 OrcaGym 项目的标准结构。

10. **模型与场景准备**：复制并转换机器人模型（从 URDF 格式转换为 MJCF 格式），在 OrcaStudio 中配置场景，生成高度图。

11. **创建配置文件**：将机器人配置和环境配置转换为字典格式。

12. **其他步骤**：还包括创建训练脚本、进行测试与调试等。
    
    ## 关键代码转换示例
    
    涵盖环境初始化、观测计算、奖励计算、环境重置、配置格式等方面，展示了 Isaac Gym 与 OrcaGym 的代码对比。
    
    ## 训练流程对比
    
    Isaac Gym 为一体化流程，采用自定义 PPO 实现，环境和算法存在强耦合；OrcaGym 为模块化设计，使用标准 RL 库，实现了环境和算法的解耦。
    
    ## 性能、适用场景等

13. **性能对比**：Isaac Gym 单机性能极强，OrcaGym 本地模式性能约为其 50%，但分布式模式可显著提升吞吐量。

14. **适用场景**：Isaac Gym 适用于单机训练、追求极致速度等场景；OrcaGym 适用于多机扩展、需要多种 RL 算法等场景。

15. **迁移成本**：小型项目需 1-2 周，中型项目需 2-4 周，大型项目需 1-2 月，迁移难度随项目规模递增。
    
    ## 总结与建议
    
    包含移植建议、检查清单、资源链接和社区支持等内容，助力顺利完成迁移工作。