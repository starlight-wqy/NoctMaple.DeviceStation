# 说明

- 仓库采用 Monorepo 方式管理代码
- 仓库所有项目基于 .NET10

---
# App应用部分
## NoctMaple.DeviceStation
- 主要功能：数据采集、数据存储、数据展示、接收指令、指令输出，传统的上位机开发
- 项目框架：采用 WPF + Prism + EFCore

---
# 工具部分
## NoctMaple.ProtocolForge
- 主要功能：处理 excel 通信协议，快速生成 dto、model、sql
- 项目框架：前期采用 WPF，后期采用 Avalonia 重构，实现跨平台 GUI

---
# SDK 部分
## NoctMaple.SDK
- Facade 模式
- 把 SDK 内部所有模块 “打包注册”，提供一个统一入口给用户
- 对外只暴露 AddNoctMaple () 等扩展方法
- 屏蔽内部复杂性（典型门面层）

## NoctMaple.SDK.Abstractions
- 存放接口（领域级）
- 按【模块】划分文件夹
- 如：ICanService、ICanLimiter
- 定义系统 “能力边界”，不包含实现
- 作用：解耦 + 支持替换实现（测试 / 跨平台）

## NoctMaple.SDK.Infrastructure
- 存放 Abstractions 中基础能力接口的实现
- 按【模块】划分文件夹
- 如：
    - CAN 通信
    - TCP / 串口
    - 数据库
    - 日志
- 偏 “底层技术实现”，无业务语义
- 作用：对接操作系统 / 硬件 / 第三方库

## NoctMaple.SDK.Core
- 存放 Abstractions 中业务接口的实现
- 按【模块】划分文件夹
- 负责使用 Infrastructure + Abstractions 进行功能拼装
- 包含：
    - 业务流程
    - 规则控制
    - 限流 / 状态机等逻辑
- 作用：承载核心业务逻辑（SDK 的 “大脑”）

## NoctMaple.SDK.Extensions
- 扩展能力层（非核心，但增强体验）
- 按【类型】划分文件夹、尽量减少文件夹
- 提供各种 “扩展方法（Extension Methods）”
- 典型内容：
    - IServiceCollection 扩展（模块注册拆分）
    - 配置绑定（Options Pattern）
    - 工具型增强封装（如 Retry、Polly 封装）
    

## NoctMaple.SDK.Primitives
- 基础类型定义层（跨模块共享，通用型，无领域）
- 使用 SDK 的用户回使用到的必要类型，如 CanMsg / UdpMsg
- 存放 “轻量级基础结构”，避免循环依赖
- 按【类型】划分文件夹
- 典型内容：
    - 基础 DTO / VO（数据结构）
    - 枚举（Enum）
    - Result / ErrorCode
    - 通用模型（如分页、响应结构）

## NoctMaple.SDK.Tests
- SDK 的测试

##  SDK 项目依赖关系
- Primitives 不依赖任何解决方案的项目
- Abstractions -> Primitives
- Core -> Abstractions + Primitives
- Infrastructure -> Abstractions + Primitives
- SDK -> Core + Infrastructure
- Extensions 可以依赖 Abstractions + Primitives