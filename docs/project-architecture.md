# MetroSim 项目架构说明文档

## 一、项目概述

MetroSim 是一款基于 C++17 + Qt6 的地铁站客流微观仿真系统，旨在通过计算机仿真技术模拟地铁站内行人流动行为，为地铁站设计优化和运营管理提供数据支撑。

### 1.1 核心功能模块

| 模块 | 职责描述 |
|------|----------|
| **站厅拓扑建模** | 定义地铁站的节点-边图结构，支持多种节点类型 |
| **乘客仿真引擎** | 离散时间步仿真，驱动乘客状态机转换 |
| **智能路径规划** | A* 算法实现多目标寻路，支持 Pareto 前沿计算 |
| **实时可视化** | 拓扑图、热力图、3D 视图的实时渲染 |
| **数据导出** | JSON/CSV/HTML 多格式仿真结果输出 |

---

## 二、目录结构与文件职责

### 2.1 目录组织

```
metro-simulation/
├── data/           # 运行时数据配置
├── external/       # 第三方依赖库
├── include/        # 头文件（接口定义）
├── resources/      # UI 资源文件
├── src/            # 源代码实现
└── output/         # 编译输出（运行时包）
```

### 2.2 数据层（data/）

| 文件路径 | 职责说明 | 关联模块 |
|----------|----------|----------|
| `data/params/default_params.json` | 默认仿真参数配置 | Simulation |
| `data/params/incident_params.json` | 突发事件场景参数 | Simulation |
| `data/params/rush_hour_params.json` | 高峰时段参数配置 | Simulation |
| `data/stations/sample_station.json` | 示例站点拓扑定义 | MetroGraph |
| `data/stations/interchange_station.json` | 换乘站拓扑定义 | MetroGraph |

### 2.3 第三方依赖（external/）

| 目录 | 说明 | 用途 |
|------|------|------|
| `external/nodeeditor/` | QtNodes 节点编辑器库 | 站厅拓扑可视化编辑 |

### 2.4 核心头文件（include/core/）

| 文件 | 类名 | 职责说明 | 核心方法/属性 |
|------|------|----------|--------------|
| `main.h` | - | 程序入口声明 | `runApplication()` |
| `mainwindow.h` | `MainWindow` | 主窗口框架 | UI 布局、工具栏、信号槽连接 |
| `metro_graph.h` | `MetroGraph` | 站厅拓扑图模型 | `addNode()`, `addEdge()`, `loadFromJsonFile()` |
| `passenger.h` | `Passenger` | 乘客 Agent 模型 | `PassengerState`, `path`, `speed`, `patience` |
| `event.h` | `Event` | 仿真事件记录 | 事件类型、时间戳、关联乘客 |
| `simulation.h` | `Simulation` | 仿真引擎核心 | `step()`, `loadScenario()`, `addPassenger()` |
| `path_planner.h` | `PathPlanner` | 路径规划器 | `findPath()`, `computePathMetrics()`, `findParetoFrontier()` |
| `statistics.h` | `Statistics` | 统计数据收集 | `recordPassengerCompleted()`, `averageTravelTime()` |
| `visualization.h` | `VisualizationWidget` | 2D 可视化组件 | 拓扑图、热力图、数据面板 |
| `station_3d_view.h` | `Station3DView` | 3D 站厅视图 | OpenGL 渲染、楼层切换 |
| `station_editor.h` | `StationEditor` | 拓扑编辑器 | 节点拖拽、属性编辑 |
| `result_export.h` | - | 结果导出模块 | `exportStep3Results()` |
| `report_writer.h` | - | HTML 报告生成 | `writeStep3HtmlReport()` |
| `asset_catalog.h` | `AssetCatalog` | 资源管理 | 图标、配置文件加载 |
| `utils.h` | - | 工具函数 | JSON 解析、路径处理 |

### 2.5 第三方头文件（include/thirdparty/）

| 文件 | 来源 | 用途 |
|------|------|------|
| `json.hpp` | nlohmann/json | JSON 序列化/反序列化 |
| `qcustomplot.h` | QCustomPlot | 图表绘制（统计曲线） |
| `waitingspinnerwidget.h` | QtWaitingSpinner | 加载动画 |

### 2.6 资源文件（resources/）

| 目录 | 内容 | 用途 |
|------|------|------|
| `resources/icons/` | 节点类型图标（SVG） | 拓扑图节点渲染 |
| `resources/ui/` | 应用图标、启动画面 | 程序界面资源 |
| `resources/diagrams/` | 流程图、示意图 | 帮助文档展示 |
| `resources/schematics/` | 站厅布局图 | 文档说明 |

### 2.7 源代码实现（src/core/）

| 文件 | 对应头文件 | 实现内容 |
|------|------------|----------|
| `main.cpp` | `main.h` | 程序入口，初始化仿真和 GUI |
| `mainwindow.cpp` | `mainwindow.h` | 主窗口 UI 构建、仿真控制逻辑 |
| `metro_graph.cpp` | `metro_graph.h` | 图数据结构实现、JSON 读写 |
| `passenger.cpp` | `passenger.h` | 乘客状态转换、路径追踪 |
| `event.cpp` | `event.h` | 事件记录与管理 |
| `simulation.cpp` | `simulation.h` | 仿真循环、乘客生成与更新 |
| `path_planner.cpp` | `path_planner.h` | A* 算法、路径缓存、Pareto 计算 |
| `statistics.cpp` | `statistics.h` | 统计数据聚合与计算 |
| `visualization.cpp` | `visualization.h` | QCustomPlot 图表、热力图渲染 |
| `station_3d_view.cpp` | `station_3d_view.h` | OpenGL 3D 渲染、相机控制 |
| `station_editor.cpp` | `station_editor.h` | QtNodes 集成、节点编辑交互 |
| `result_export.cpp` | `result_export.h` | JSON/CSV 格式输出 |
| `report_writer.cpp` | `report_writer.h` | HTML 报告模板生成 |
| `asset_catalog.cpp` | `asset_catalog.h` | 资源路径管理、缓存 |
| `utils.cpp` | `utils.h` | 通用工具函数实现 |

---

## 三、模块间依赖关系

### 3.1 核心依赖图

```
┌─────────────────────────────────────────────────────────────────┐
│                        MainWindow                              │
│  (UI 入口，管理仿真状态、协调各可视化组件)                       │
└───────────────────────────────┬─────────────────────────────────┘
                                │
        ┌───────────────────────┼───────────────────────┐
        ▼                       ▼                       ▼
┌───────────────┐     ┌─────────────────┐     ┌─────────────────┐
│ Visualization │     │  Station3DView  │     │  StationEditor  │
│   Widget      │     │   (OpenGL 3D)   │     │  (QtNodes)      │
│ (2D可视化面板)│     └────────┬────────┘     └────────┬────────┘
└───────┬───────┘              │                      │
        │                      │                      │
        └──────────────────────┼──────────────────────┘
                               ▼
                    ┌───────────────────┐
                    │    Simulation     │
                    │   (仿真引擎核心)   │
                    └─────────┬─────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        ▼                     ▼                     ▼
┌───────────────┐     ┌───────────────┐     ┌───────────────┐
│  MetroGraph   │     │   Passenger   │     │  PathPlanner  │
│  (站厅拓扑)   │     │   (乘客模型)   │     │  (路径规划)   │
└───────────────┘     └───────────────┘     └───────────────┘
        │                     │                     │
        └─────────────────────┼─────────────────────┘
                               ▼
                    ┌───────────────────┐
                    │   Statistics      │
                    │   (统计模块)       │
                    └───────────────────┘
                              │
                              ▼
                    ┌───────────────────┐
                    │   ResultExport    │
                    │   ReportWriter    │
                    │   (数据导出)       │
                    └───────────────────┘
```

### 3.2 数据流向

```
1. 配置加载阶段
   JSON 文件 ──► MetroGraph ──► Simulation
                 (站点拓扑)        (仿真初始化)

2. 仿真运行阶段
   Simulation ──► Passenger ──► PathPlanner
     (时间步进)    (状态更新)      (路径计算)
       │              │
       └──────────────┼──────────────┐
                      ▼              ▼
                 Statistics    Visualization
                 (数据统计)    (实时渲染)

3. 结果导出阶段
   Simulation ──► ResultExport ──► JSON/CSV
                    └──► ReportWriter ──► HTML
```

---

## 四、关键类设计

### 4.1 Simulation（仿真引擎）

**核心职责**：管理仿真时钟、乘客生命周期、事件队列

**关键成员**：
- `currentTime_`：当前仿真时间（秒）
- `passengers_`：活跃乘客列表
- `events_`：事件日志
- `statistics_`：统计数据
- `pathPlanner_`：路径规划器引用

**核心流程**：
```
Simulation::step()
  ├── 生成新乘客（根据客流率）
  ├── 更新每位乘客状态
  ├── 处理拥堵事件
  ├── 记录统计数据
  └── 触发可视化更新
```

### 4.2 Passenger（乘客 Agent）

**状态机定义**（passenger.h:5-12）：
```cpp
enum class PassengerState {
    Enter,     // 进入站厅
    Security,  // 安检
    Ticket,    // 购票/检票
    Wait,      // 候车
    Board,     // 上车
    Exit,      // 出站
    Finished   // 完成
};
```

**关键属性**：
- `path`：规划的路径节点序列
- `speed`：行走速度（1.2m/s 基准）
- `patience`：耐心值（超时退出概率）
- `progress`：当前边的行进进度（0-1）

### 4.3 PathPlanner（路径规划器）

**寻路目标策略**（path_planner.h:8-13）：
```cpp
enum class PathObjective {
    MinTime,        // 最短时间
    MinDistance,    // 最短距离
    MinCongestion,  // 最小拥堵
    MinZoneSwitches,// 最少区域切换
    WeightedSum     // 加权综合
};
```

**核心方法**：
- `findPath()`：基于 A* 的单目标寻路
- `findParetoFrontier()`：多目标优化的 Pareto 前沿计算
- `computePathMetrics()`：计算路径指标（时间、距离、拥堵等）

### 4.4 MetroGraph（站厅图模型）

**节点类型**（metro_graph.h:8-10）：
| 类型 | 说明 | 典型容量 |
|------|------|----------|
| entrance | 入口 | 50人 |
| security | 安检区 | 40人 |
| ticket | 售票区 | 50人 |
| gate | 闸机区 | 不限 |
| corridor | 走廊 | 100人 |
| hall | 大厅 | 200人 |
| stairs | 楼梯 | 30人 |
| escalator | 扶梯 | 60人 |
| platform | 站台 | 300人 |
| exit | 出口 | 50人 |
| waiting | 候车区 | 100人 |

**边属性**（metro_graph.h:13-22）：
- `length`：路径长度（米）
- `width`：通道宽度（米）
- `capacity`：通行能力（人/秒）
- `transferTime`：通过时间（秒）
- `bidirectional`：是否双向通行

---

## 五、可视化架构

### 5.1 2D 可视化组件

**VisualizationWidget** 包含以下子组件：
| 组件 | 功能 | 依赖 |
|------|------|------|
| 拓扑图 | 节点-边渲染，拥堵颜色映射 | MetroGraph + Simulation |
| 热力图 | 基于密度的热度展示 | Simulation |
| 统计曲线 | 历史趋势图表 | Statistics + QCustomPlot |
| 数据面板 | 实时指标数字显示 | Statistics |
| 事件日志 | 仿真事件时序记录 | Simulation |

### 5.2 3D 可视化组件

**Station3DView** 功能：
- 基于 OpenGL 的三维站厅渲染
- 分层显示（支持楼层切换）
- 乘客位置实时更新
- 视角旋转、缩放交互

---

## 六、编译与部署

### 6.1 构建依赖

| 依赖 | 版本要求 | 获取方式 |
|------|----------|----------|
| Qt | 6.5+ | Qt 官方安装包 |
| CMake | 3.20+ | Qt Tools 或独立安装 |
| MSVC | 2022 | Visual Studio 2022 |

### 6.2 构建流程

```
build_and_deploy.bat
  ├── 检查 Qt 安装
  ├── 配置 MSVC 环境
  ├── 清理旧构建目录
  ├── CMake 配置 (Release)
  ├── MSBuild 编译
  └── windeployqt 部署
```

### 6.3 输出结构

```
output/
├── metro_sim.exe           # 主程序
├── Qt6Core.dll             # Qt 核心库
├── Qt6Gui.dll              # Qt GUI 库
├── Qt6Widgets.dll          # Qt Widgets 库
├── Qt6OpenGL.dll           # Qt OpenGL 库
├── QtNodes.dll             # 节点编辑器库
├── data/                   # 运行时数据
├── resources/              # UI 资源
└── platforms/              # 平台插件
```

---

## 七、扩展建议

### 7.1 功能扩展方向

1. **多站联动仿真**：支持多条线路、多个站点的协同仿真
2. **AI 优化模块**：基于强化学习的客流调度优化
3. **VR 沉浸体验**：支持 VR 设备的沉浸式站厅漫游
4. **云端部署**：Web 端仿真服务，支持远程访问

### 7.2 性能优化建议

1. **并行仿真**：利用多线程加速大规模客流仿真
2. **LOD 渲染**：3D 视图的细节层次优化
3. **增量更新**：仅更新变化部分的可视化渲染

---

**文档版本**: v1.0  
**生成日期**: 2026年6月  
**项目地址**: https://github.com/SUNMOONHD/metro-simulation