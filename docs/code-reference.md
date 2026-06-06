# MetroSim 代码参考文档

---

## 概述

本文档详细介绍 MetroSim 项目中每个 `.h` 和 `.cpp` 文件的类、函数及职责。项目采用 C++17 + Qt6 技术栈，核心模块包括仿真引擎、路径规划、可视化等。

---

## 一、核心头文件（include/core/）

### 1.1 metro_graph.h

**文件职责**：定义站厅拓扑图数据结构，提供图的增删改查操作

#### 结构体定义

| 结构体 | 说明 | 成员字段 |
|--------|------|----------|
| `StationNode` | 站点节点 | `id`, `name`, `type`, `floor`, `x`, `y`, `capacity`, `width` |
| `GraphEdge` | 图边 | `from`, `to`, `length`, `width`, `capacity`, `transferTime`, `lineIndex`, `bidirectional` |

#### 类 MetroGraph

| 方法 | 返回类型 | 说明 |
|------|----------|------|
| `setStationName(const std::string&)` | `void` | 设置站点名称 |
| `stationName()` | `const std::string&` | 获取站点名称 |
| `setFloors(const std::vector<int>&)` | `void` | 设置楼层列表 |
| `floors()` | `const std::vector<int>&` | 获取楼层列表 |
| `addNode(const StationNode&)` | `bool` | 添加节点 |
| `addEdge(const GraphEdge&)` | `bool` | 添加边 |
| `clear()` | `void` | 清空图数据 |
| `loadFromJsonFile(const std::string&, std::string*)` | `bool` | 从 JSON 文件加载 |
| `nodeCount()` | `std::size_t` | 获取节点数量 |
| `edgeCount()` | `std::size_t` | 获取边数量 |
| `nodes()` | `const std::unordered_map<std::string, StationNode>&` | 获取所有节点 |
| `edges()` | `const std::vector<GraphEdge>&` | 获取所有边 |
| `adjacency()` | `const std::unordered_map<std::string, std::vector<std::size_t>>&` | 获取邻接表 |

---

### 1.2 passenger.h

**文件职责**：定义乘客 Agent 模型及状态机

#### 枚举类型

| 枚举 | 值 | 说明 |
|------|-----|------|
| `PassengerState` | `Enter`, `Security`, `Ticket`, `Wait`, `Board`, `Exit`, `Finished` | 乘客状态 |

#### 类 Passenger

| 成员变量 | 类型 | 说明 |
|----------|------|------|
| `id` | `int` | 乘客唯一标识 |
| `startNode` | `std::string` | 起始节点 ID |
| `endNode` | `std::string` | 目标节点 ID |
| `speed` | `double` | 行走速度（默认 1.2 m/s） |
| `patience` | `double` | 耐心值（超时退出概率） |
| `familiarity` | `double` | 熟悉度（影响购票时间） |
| `state` | `PassengerState` | 当前状态 |
| `currentNode` | `std::string` | 当前所在节点 |
| `targetNode` | `std::string` | 目标节点 |
| `path` | `std::vector<std::string>` | 规划路径节点序列 |
| `pathIndex` | `std::size_t` | 当前路径索引 |
| `progress` | `double` | 当前边的行进进度 (0-1) |
| `nodeWaitRemaining` | `double` | 节点等待剩余时间 |
| `edgeTravelRemaining` | `double` | 边行进剩余时间 |
| `edgeTravelTotal` | `double` | 边行进总时间 |
| `waitedSeconds` | `double` | 已等待时长 |
| `onEdge` | `bool` | 是否在边上移动 |
| `edgeFrom` | `std::string` | 当前边起点 |
| `edgeTo` | `std::string` | 当前边终点 |
| `edgeIndex` | `int` | 当前边索引 |
| `arrivalTime` | `int` | 到达时间 |
| `exitTime` | `int` | 离开时间 |

---

### 1.3 simulation.h

**文件职责**：仿真引擎核心，管理仿真时钟、乘客生命周期、事件队列

#### 结构体定义

| 结构体 | 说明 | 关键字段 |
|--------|------|----------|
| `ProcessingConfig` | 处理时间配置 | `securityTime`, `ticketTimeBase`, `gateTime`, `boardingTime`, `trainHeadway`, `trainCapacity` |
| `SimulationConfig` | 仿真参数配置 | `timeStep`, `peakLambda`, `offpeakLambda`, `peakHours`, `maxPatience`, `pathObjective`, `pathWeights` |

#### 类 Simulation

| 方法 | 返回类型 | 说明 |
|------|----------|------|
| `Simulation()` | - | 构造函数 |
| `reset()` | `void` | 重置仿真状态 |
| `step()` | `void` | 执行单步仿真 |
| `setTimeStep(int)` | `void` | 设置时间步长 |
| `timeStep()` | `int` | 获取时间步长 |
| `currentTime()` | `int` | 获取当前仿真时间 |
| `setConfig(const SimulationConfig&)` | `void` | 设置仿真配置 |
| `config()` | `const SimulationConfig&` | 获取仿真配置 |
| `loadConfigFromJsonFile(const std::string&, std::string*)` | `bool` | 从 JSON 加载配置 |
| `loadScenario(const std::string&, const std::string&, std::string*)` | `bool` | 加载场景（站点+参数） |
| `setGraph(const MetroGraph&)` | `void` | 设置拓扑图 |
| `graph()` | `const MetroGraph&` | 获取拓扑图 |
| `addPassenger(const Passenger&)` | `void` | 添加乘客 |
| `passengers()` | `const std::vector<Passenger>&` | 获取所有乘客 |
| `events()` | `const std::vector<Event>&` | 获取事件列表 |
| `statistics()` | `const Statistics&` | 获取统计数据 |
| `nodeOccupancy()` | `const std::unordered_map<std::string, int>&` | 获取节点占用 |
| `edgeOccupancy()` | `const std::unordered_map<std::string, int>&` | 获取边占用 |

---

### 1.4 path_planner.h

**文件职责**：路径规划器，实现 A* 算法和多目标优化

#### 枚举类型

| 枚举 | 值 | 说明 |
|------|-----|------|
| `PathObjective` | `MinTime`, `MinDistance`, `MinCongestion`, `MinZoneSwitches`, `WeightedSum` | 寻路目标策略 |

#### 结构体定义

| 结构体 | 说明 | 关键字段 |
|--------|------|----------|
| `PathWeights` | 加权策略权重 | `wTime=0.4`, `wDistance=0.2`, `wCongestion=0.3`, `wZoneSwitch=0.1` |
| `PathMetrics` | 路径指标 | `totalTime`, `totalDistance`, `avgCongestion`, `zoneSwitches` |

#### 类 PathPlanner

| 方法 | 返回类型 | 说明 |
|------|----------|------|
| `findPath(const MetroGraph&, const std::string&, const std::string&, PathObjective, const PathWeights&, ...)` | `std::vector<std::string>` | 查找路径 |
| `computePathMetrics(const MetroGraph&, const std::vector<std::string>&, ...)` | `PathMetrics` | 计算路径指标 |
| `findParetoFrontier(const MetroGraph&, const std::string&, const std::string&, ...)` | `std::vector<std::vector<std::string>>` | 查找 Pareto 前沿路径 |
| `clearCache()` | `void` | 清空路径缓存 |
| `cacheSize()` | `size_t` | 获取缓存大小 |
| `setCacheEnabled(bool)` | `void` | 设置缓存启用状态 |
| `setUseAStar(bool)` | `void` | 设置是否使用 A* 算法 |
| `getCacheEnabled()` | `bool` | 获取缓存状态 |
| `getUseAStar()` | `bool` | 获取 A* 启用状态 |

---

### 1.5 event.h

**文件职责**：定义仿真事件类型和事件记录结构

#### 枚举类型

| 枚举 | 值 | 说明 |
|------|-----|------|
| `EventType` | `PassengerArrived`, `PassengerExited`, `CongestionTriggered`, `TimeoutReached`, `PeakHourStarted`, `PeakHourEnded`, `TrainArrived`, `PassengerSurge` | 事件类型 |

#### 结构体 Event

| 成员 | 类型 | 说明 |
|------|------|------|
| `type` | `EventType` | 事件类型 |
| `time` | `int` | 事件发生时间 |
| `nodeId` | `std::string` | 关联节点 ID |
| `passengerId` | `int` | 关联乘客 ID |
| `message` | `std::string` | 事件描述消息 |

---

### 1.6 statistics.h

**文件职责**：统计数据收集与计算

#### 类 Statistics

| 方法 | 返回类型 | 说明 |
|------|----------|------|
| `reset()` | `void` | 重置统计 |
| `recordPassengerCompleted(int)` | `void` | 记录完成乘客 |
| `recordPassengerTimedOut()` | `void` | 记录超时乘客 |
| `recordCongestionEvent(const std::string&)` | `void` | 记录拥堵事件 |
| `recordQueueLength(int)` | `void` | 记录队列长度 |
| `completedPassengers()` | `int` | 获取完成乘客数 |
| `timedOutPassengers()` | `int` | 获取超时乘客数 |
| `congestionEvents()` | `int` | 获取拥堵事件数 |
| `maxQueueLength()` | `int` | 获取最大队列长度 |
| `averageTravelTime()` | `double` | 获取平均通行时间 |
| `congestionCountByNode()` | `const std::unordered_map<std::string, int>&` | 获取各节点拥堵次数 |

---

### 1.7 visualization.h

**文件职责**：2D 可视化组件，包含拓扑图、热力图、统计图表

#### 类 VisualizationWidget（继承 QWidget）

| 方法 | 返回类型 | 说明 |
|------|----------|------|
| `VisualizationWidget(QWidget*)` | - | 构造函数 |
| `setGraph(const MetroGraph&)` | `void` | 设置拓扑图 |
| `setSimulation(const Simulation&)` | `void` | 设置仿真数据 |
| `setHighlightedPath(const std::vector<std::string>&)` | `void` | 设置高亮路径 |
| `setComparedPaths(const std::vector<std::pair<std::vector<std::string>, QColor>>&)` | `void` | 设置对比路径 |
| `clearComparedPaths()` | `void` | 清除对比路径 |
| `clearHistory()` | `void` | 清除历史数据 |

#### 私有成员（关键）

| 成员 | 类型 | 说明 |
|------|------|------|
| `topologyPlot_` | `QCustomPlot*` | 拓扑图控件 |
| `heatmapPlot_` | `QCustomPlot*` | 热力图控件 |
| `statisticsPlot_` | `QCustomPlot*` | 统计曲线图 |
| `eventLog_` | `QPlainTextEdit*` | 事件日志 |
| `graph_` | `MetroGraph` | 拓扑图数据 |
| `timeHistory_` | `QVector<double>` | 时间历史 |
| `activeHistory_` | `QVector<double>` | 活跃乘客历史 |

---

### 1.8 mainwindow.h

**文件职责**：主窗口框架，协调各功能模块

#### 类 MainWindow（继承 QMainWindow）

| 方法 | 返回类型 | 说明 |
|------|----------|------|
| `MainWindow(QWidget*)` | - | 构造函数 |
| `buildUi()` | `void` | 构建 UI |
| `setupToolbar()` | `void` | 设置工具栏 |
| `setupConnections()` | `void` | 设置信号槽连接 |
| `loadInitialScenario()` | `void` | 加载初始场景 |
| `stepSimulation()` | `void` | 执行仿真步进 |
| `refreshDashboard()` | `void` | 刷新仪表盘 |
| `toggleSimulation()` | `void` | 切换仿真状态 |
| `resetSimulation()` | `void` | 重置仿真 |
| `exportResults()` | `void` | 导出结果 |
| `openTopologyEditor()` | `void` | 打开拓扑编辑器 |
| `open3DView()` | `void` | 打开 3D 视图 |

---

### 1.9 station_3d_view.h

**文件职责**：3D 站厅可视化，基于 OpenGL 渲染

#### 结构体定义

| 结构体 | 说明 | 关键字段 |
|--------|------|----------|
| `Vertex3D` | 3D 顶点 | `position`, `normal`, `color` |
| `Passenger3DInfo` | 乘客 3D 信息 | `id`, `x`, `y`, `z`, `progress`, `state` |

#### 类 Station3DView（继承 QOpenGLWidget）

| 方法 | 返回类型 | 说明 |
|------|----------|------|
| `Station3DView(QWidget*)` | - | 构造函数 |
| `~Station3DView()` | - | 析构函数 |
| `setGraph(const MetroGraph&)` | `void` | 设置拓扑图 |
| `setPassengers(const std::vector<Passenger3DInfo>&)` | `void` | 设置乘客数据 |
| `setVisibleFloor(int)` | `void` | 设置可见楼层 |
| `setShowAllFloors(bool)` | `void` | 设置是否显示所有楼层 |

#### OpenGL 生命周期方法

| 方法 | 说明 |
|------|------|
| `initializeGL()` | 初始化 OpenGL |
| `resizeGL(int, int)` | 窗口大小改变 |
| `paintGL()` | 绘制帧 |

---

### 1.10 station_editor.h

**文件职责**：拓扑编辑器，基于 QtNodes 库实现可视化编辑

#### 类定义

| 类 | 基类 | 说明 |
|----|------|------|
| `StationNodePainter` | `QtNodes::AbstractNodePainter` | 自定义节点绘制器 |
| `MetroGraphModel` | `QtNodes::AbstractGraphModel` | 图数据模型 |
| `StationEditorWidget` | `QDialog` | 编辑器主窗口 |

#### MetroGraphModel 关键方法

| 方法 | 返回类型 | 说明 |
|------|----------|------|
| `loadFromMetroGraph()` | `void` | 从 MetroGraph 加载 |
| `saveToMetroGraph()` | `void` | 保存到 MetroGraph |
| `addNode(QString)` | `NodeId` | 添加节点 |
| `addConnection(ConnectionId)` | `void` | 添加连接 |
| `deleteNode(NodeId)` | `bool` | 删除节点 |
| `deleteConnection(ConnectionId)` | `bool` | 删除连接 |

---

### 1.11 result_export.h

**文件职责**：结果导出模块，支持 JSON/CSV 格式

#### 命名空间 results

| 方法 | 返回类型 | 说明 |
|------|----------|------|
| `exportStep3Results(const Simulation&, const ExportPaths&, std::string*)` | `bool` | 导出 Step3 结果 |

#### 结构体 ExportPaths

| 成员 | 类型 | 说明 |
|------|------|------|
| `summaryJsonPath` | `std::string` | 汇总 JSON 路径 |
| `eventsCsvPath` | `std::string` | 事件 CSV 路径 |

---

### 1.12 report_writer.h

**文件职责**：HTML 报告生成模块

#### 命名空间 reports

| 方法 | 返回类型 | 说明 |
|------|----------|------|
| `writeStep3HtmlReport(const Simulation&, const std::string&, std::string*)` | `bool` | 生成 Step3 HTML 报告 |

---

### 1.13 asset_catalog.h

**文件职责**：资源管理，提供图标路径和类型映射

#### 命名空间 assets

| 方法 | 返回类型 | 说明 |
|------|----------|------|
| `iconRoot()` | `std::string` | 获取图标根目录 |
| `iconPath(const std::string&)` | `std::string` | 获取图标完整路径 |
| `nodeIconForType(const std::string&)` | `std::string` | 获取节点类型对应图标 |
| `passengerIconForState(PassengerState)` | `std::string` | 获取乘客状态对应图标 |
| `eventIconForType(EventType)` | `std::string` | 获取事件类型对应图标 |
| `lineIconForIndex(int)` | `std::string` | 获取线路索引对应图标 |

---

### 1.14 utils.h

**文件职责**：通用工具函数

#### 命名空间 utils

| 方法 | 返回类型 | 说明 |
|------|----------|------|
| `loadTextFile(const std::string&, std::string*)` | `bool` | 读取文本文件 |
| `writeTextFile(const std::string&, const std::string&)` | `bool` | 写入文本文件 |

---

## 二、核心源文件（src/core/）

### 2.1 main.cpp

**职责**：程序入口，初始化应用和主窗口

**核心流程**：
1. 创建 QApplication 实例
2. 创建 MainWindow 实例
3. 显示主窗口
4. 执行应用事件循环

---

### 2.2 mainwindow.cpp

**职责**：主窗口实现，协调仿真控制与 UI 更新

**关键实现**：
- `buildUi()`: 构建工具栏、仪表盘、状态栏
- `setupConnections()`: 连接按钮点击与仿真控制
- `stepSimulation()`: 定时器触发的仿真步进
- `refreshDashboard()`: 更新可视化组件

---

### 2.3 simulation.cpp

**职责**：仿真引擎核心实现

**核心函数**：

| 函数 | 说明 | 关键逻辑 |
|------|------|----------|
| `step()` | 单步仿真 | 统计占用→生成乘客→更新状态→记录事件 |
| `reset()` | 重置仿真 | 清空乘客、事件、统计数据 |
| `loadScenario()` | 加载场景 | 加载站点拓扑+仿真参数 |

**关键辅助函数**（匿名命名空间）：
- `samplePassengerCount()`: 根据泊松分布采样乘客数
- `assignNodeProcessing()`: 根据节点类型设置处理时间
- `isPeakHour()`: 判断是否高峰时段

---

### 2.4 path_planner.cpp

**职责**：路径规划算法实现

**核心算法**：

| 算法 | 实现位置 | 说明 |
|------|----------|------|
| A* 寻路 | `findPath()` | 使用优先队列，支持多种启发式 |
| Pareto 前沿 | `findParetoFrontier()` | 多目标优化路径集 |
| 路径指标计算 | `computePathMetrics()` | 时间、距离、拥堵、换乘 |

**代价计算**（computeEdgeCost）：
```cpp
// 根据目标策略计算边代价
switch (objective) {
    case MinTime: return time_cost;
    case MinDistance: return distance;
    case MinCongestion: return congestion_factor * 10;
    case MinZoneSwitches: return zone_switch_penalty + time * 0.1;
    case WeightedSum: return wTime*t + wDistance*d + wCongestion*c + wZoneSwitch*z;
}
```

---

### 2.5 metro_graph.cpp

**职责**：图数据结构实现

**核心功能**：
- `loadFromJsonFile()`: 解析 JSON 配置文件
- `addNode()`/`addEdge()`: 维护节点和边
- 邻接表构建与查询

---

### 2.6 passenger.cpp

**职责**：乘客模型实现

**核心逻辑**：
- 乘客状态转换
- 路径追踪
- 耐心值衰减

---

### 2.7 visualization.cpp

**职责**：可视化渲染实现

**核心组件**：
1. **拓扑图**：节点位置渲染、拥堵颜色映射
2. **热力图**：密度可视化
3. **统计曲线**：QCustomPlot 绘制历史趋势
4. **事件日志**：实时事件展示

---

### 2.8 station_3d_view.cpp

**职责**：OpenGL 3D 渲染实现

**核心功能**：
- 节点几何体构建（球体、圆柱体）
- 楼层平面渲染
- 乘客位置更新
- 相机控制（旋转、缩放、平移）

---

### 2.9 station_editor.cpp

**职责**：拓扑编辑器实现

**核心功能**：
- QtNodes 集成
- 节点属性编辑面板
- 预设布局加载
- 自动布局算法

---

### 2.10 result_export.cpp

**职责**：结果导出实现

**导出格式**：
- **JSON**：汇总统计数据
- **CSV**：事件日志明细

---

### 2.11 report_writer.cpp

**职责**：HTML 报告生成

**报告内容**：
- 仿真参数摘要
- 统计图表嵌入
- 事件时间线
- 数据分析总结

---

## 三、第三方库 API 调用说明

### 3.1 Qt6 框架

| 模块 | 用途 | 主要 API |
|------|------|----------|
| **QtCore** | 核心功能 | `QTimer`, `QVariant`, `QJsonObject` |
| **QtWidgets** | UI 组件 | `QMainWindow`, `QWidget`, `QToolBar`, `QTableWidget` |
| **QtOpenGL** | 3D 渲染 | `QOpenGLWidget`, `QOpenGLShaderProgram`, `QMatrix4x4` |
| **QtGui** | 图形 | `QPainter`, `QColor`, `QIcon`, `QMouseEvent` |

### 3.2 QCustomPlot

**用途**：图表绘制

**主要 API**：
| 类 | 说明 |
|----|------|
| `QCustomPlot` | 图表控件 |
| `QCPGraph` | 曲线图 |
| `QCPColorMap` | 热力图 |
| `QCPColorScale` | 颜色刻度条 |

### 3.3 nlohmann/json

**用途**：JSON 序列化/反序列化

**主要 API**：
| 操作 | 示例 |
|------|------|
| 解析 | `nlohmann::json::parse(content)` |
| 序列化 | `json.dump(4)` |
| 访问 | `json["key"]`, `json.at("key")` |

### 3.4 QtNodes

**用途**：节点编辑器框架

**主要 API**：
| 类 | 说明 |
|----|------|
| `QtNodes::AbstractGraphModel` | 图数据模型接口 |
| `QtNodes::BasicGraphicsScene` | 图形场景 |
| `QtNodes::GraphicsView` | 视图控件 |
| `QtNodes::ConnectionId` | 连接标识 |
| `QtNodes::NodeId` | 节点标识 |

### 3.5 WaitingSpinnerWidget

**用途**：加载动画

**主要 API**：
| 方法 | 说明 |
|------|------|
| `start()` | 开始旋转 |
| `stop()` | 停止旋转 |
| `setRoundness(double)` | 设置圆角 |
| `setColor(QColor)` | 设置颜色 |

---

## 四、文件依赖关系图

```
main.cpp
    └── mainwindow.h
            ├── simulation.h
            │       ├── metro_graph.h
            │       ├── passenger.h
            │       ├── path_planner.h
            │       ├── event.h
            │       └── statistics.h
            ├── visualization.h
            │       ├── simulation.h
            │       └── metro_graph.h
            ├── station_3d_view.h
            │       └── metro_graph.h
            └── station_editor.h
                    └── metro_graph.h

result_export.cpp
    └── simulation.h

report_writer.cpp
    └── simulation.h

asset_catalog.cpp
    ├── event.h
    └── passenger.h
```

---

**文档版本**: v1.0  
**生成日期**: 2026年6月  
**项目地址**: https://github.com/SUNMOONHD/metro-simulation