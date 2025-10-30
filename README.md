# 輪式機器人ROS2專案 - 完整系統分析

## 📖 專案概述

這是一個基於ROS2 Humble的**分散式輪式機器人系統**，採用主控台-機器人分離架構設計。系統集成了完整的機器人功能模組，包括底盤控制、感測器驅動、SLAM建圖、自主導航、智能跟隨等功能。

### 🏗️ 系統架構

```
┌─────────────────┐    DDS/UDP通訊    ┌─────────────────┐
│   主控台端      │ ←──────────────→ │    機器人端     │
│ - RViz2監控     │                   │ - 底盤控制      │
│ - 任務規劃      │                   │ - 感測器驅動    │
│ - 手動控制      │                   │ - 自主運作      │
│ - 參數調整      │                   │ - 數據回傳      │
└─────────────────┘                   └─────────────────┘
```

## 🎯 核心特性

- ✅ **完全自主運作** - 機器人可脫離主控台獨立工作
- ✅ **分散式架構** - DDS自動發現與通訊
- ✅ **模組化設計** - 功能解耦，易於維護擴展
- ✅ **多重感知** - 雷達、相機、IMU融合感知
- ✅ **豐富算法** - 4種SLAM + 3種路徑規劃
- ✅ **智能跟隨** - 視覺/雷射多模式跟隨
- ✅ **安全保障** - 故障自動恢復機制

## 📋 模組架構分析

### 🔧 核心系統模組

| 模組名稱 | 功能描述 | 啟動指令 |
|---------|---------|---------|
| **turn_on_wheeltec_robot** | 底盤控制核心、串口通訊 | `ros2 launch turn_on_wheeltec_robot turn_on_wheeltec_robot.launch.py` |
| **wheeltec_robot_msg** | 自定義消息格式 | - |
| **wheeltec_robot_urdf** | 機器人3D模型 | - |

### 🎮 人機控制模組

| 模組名稱 | 功能描述 | 啟動指令 | 重疊性 |
|---------|---------|---------|-------|
| **wheeltec_joy** | 手柄遙控 | `ros2 launch wheeltec_joy wheeltec_joy.launch.py` | ⚠️ 與鍵盤控制重疊 |
| **wheeltec_robot_keyboard** | 鍵盤控制 | `ros2 run wheeltec_robot_keyboard wheeltec_keyboard` | ⚠️ 與手柄控制重疊 |

### 👁️ 感測器驅動模組

| 模組名稱 | 支援設備 | 啟動指令 |
|---------|---------|---------|
| **wheeltec_lidar_ros2** | LD06/14/19, LSM10/LSN10系列 | `ros2 launch turn_on_wheeltec_robot wheeltec_lidar.launch.py` |
| **ros2_astra_camera** | Astra Mini/Pro, Dabai, Gemini | `ros2 launch turn_on_wheeltec_robot wheeltec_camera.launch.py` |
| **web_video_server-ros2** | HTTP影像串流 | `ros2 run web_video_server web_video_server` |

### 🤖 智能跟隨模組

| 模式 | 功能描述 | 啟動指令 |
|-----|---------|---------|
| **視覺巡線** | 識別並跟隨彩色線條 | `ros2 launch simple_follower_ros2 line_follower.launch.py` |
| **雷射跟隨** | 基於雷達的目標跟隨 | `ros2 launch simple_follower_ros2 laser_follower.launch.py` |
| **視覺跟隨** | 基於相機的目標追蹤 | `ros2 launch simple_follower_ros2 visual_follower.launch.py` |
| **HSV調試** | 顏色閾值調整工具 | `ros2 launch simple_follower_ros2 adjust_hsv.launch.py` |

### 🗺️ SLAM建圖模組

| SLAM算法 | 特點 | 啟動指令 | 重疊性 |
|---------|-----|---------|-------|
| **GMapping** | 經典2D雷射SLAM | `ros2 launch slam_gmapping slam_gmapping.launch.py` | 🔄 選一使用 |
| **Cartographer** | Google開源SLAM | `ros2 launch wheeltec_cartographer cartographer.launch.py` | 🔄 選一使用 |
| **SLAM Toolbox** | 現代SLAM解決方案 | `ros2 launch wheeltec_slam_toolbox online_async_launch.py` | 🔄 選一使用 |
| **RTAB-Map** | 3D視覺SLAM | `ros2 launch wheeltec_robot_rtab wheeltec_slam_rtab.launch.py` | 🔄 選一使用 |

### 🧭 導航系統模組

| 模組名稱 | 功能描述 | 啟動指令 |
|---------|---------|---------|
| **wheeltec_robot_nav2** | Navigation2配置封裝 | `ros2 launch wheeltec_robot_nav2 wheeltec_nav2.launch.py` |
| **navigation2-humble** | 完整Nav2框架 | 包含路徑規劃、控制器、行為樹等 |

### 📺 可視化模組

| 模組名稱 | 功能描述 | 啟動指令 |
|---------|---------|---------|
| **wheeltec_rviz2** | RViz2可視化界面 | `ros2 launch wheeltec_rviz2 wheeltec_rviz.launch.py` |

## 🚀 快速開始

### 基礎遙控模式
```bash
# 啟動機器人底盤
ros2 launch turn_on_wheeltec_robot turn_on_wheeltec_robot.launch.py

# 鍵盤控制
ros2 run wheeltec_robot_keyboard wheeltec_keyboard
```

### 自主導航模式
```bash
# 完整導航系統
ros2 launch wheeltec_robot_nav2 wheeltec_nav2.launch.py
```

### SLAM建圖模式
```bash
# SLAM建圖
ros2 launch wheeltec_slam_toolbox online_async_launch.py

# 可視化監控
ros2 launch wheeltec_rviz2 wheeltec_rviz.launch.py

# 保存地圖
ros2 launch wheeltec_robot_nav2 save_map.launch.py
```

### 智能跟隨模式
```bash
# 視覺巡線跟隨
ros2 launch simple_follower_ros2 line_follower.launch.py

# 雷射目標跟隨
ros2 launch simple_follower_ros2 laser_follower.launch.py
```

## 🔄 ROS2分散式通訊機制

### 底層技術棧
```
應用層:    Publisher/Subscriber
    ↓
ROS2層:    rclcpp/rclpy APIs
    ↓  
DDS層:     FastDDS/CycloneDDS
    ↓
網路層:    UDP Multicast + TCP
    ↓
物理層:    Ethernet/WiFi
```

### 自動發現流程
1. **DDS Domain設定** - 相同Domain ID的設備自動互連
2. **Multicast Discovery** - UDP組播自動發現網路節點
3. **Topic Matching** - 自動配對Publisher/Subscriber
4. **數據傳輸** - 透明的跨網路通訊

### 網路配置
```bash
# 環境變量設定
export ROS_DOMAIN_ID=0  # 同一系統使用相同ID
export ROS_LOCALHOST_ONLY=0  # 允許跨網路通訊

# 典型網路拓撲
路由器 (192.168.0.1)
├── 主控台PC (192.168.0.101)
└── 機器人 (192.168.0.100)
```

## 🔒 安全性考量

### 多層防護策略
```
物理層:    機器人本體安全、端口封閉
    ↓
網路層:    VPN、防火牆、網路隔離
    ↓
DDS層:     SROS2安全、憑證認證
    ↓
應用層:    業務邏輯驗證、速率限制
    ↓
監控層:    安全事件監控、異常檢測
```

### SROS2安全框架
```bash
# 啟用安全功能
export ROS_SECURITY_KEYSTORE=/path/to/keystore
export ROS_SECURITY_ENABLE=true
export ROS_SECURITY_STRATEGY=Enforce

# 創建安全憑證
ros2 security create_keystore ~/robot_keystore
ros2 security create_enclave ~/robot_keystore /robot_001
```

## 🤖 自主運作能力

### 完全自主運作
機器人具備脫離主控台的完整自主能力：

✅ **感知系統** - 雷達、相機、IMU自主感知環境  
✅ **決策系統** - 行為樹自主決策和故障恢復  
✅ **控制系統** - PID控制器自主運動控制  
✅ **導航系統** - Navigation2自主路徑規劃執行  
✅ **任務系統** - 多種預設任務模式  

### 故障自動恢復
```xml
<!-- 行為樹自動重試機制 -->
<RecoveryNode number_of_retries="6">
    <FollowPath path="{path}"/>
    <!-- 失敗時自動恢復 -->
    <ClearEntireCostmap/>
    <Spin spin_dist="1.0"/>
    <Wait wait_duration="1"/>
</RecoveryNode>
```

## ⚠️ 模組重疊性分析

### 高度重疊 (選一使用)
- **手動控制：** wheeltec_joy ↔ wheeltec_robot_keyboard
- **SLAM算法：** 4種SLAM方案功能重疊
- **路徑規劃：** Navigation2內多種規劃器

### 部分重疊 (配置不同)
- **導航封裝：** wheeltec_robot_nav2 封裝 navigation2-humble
- **跟隨模式：** simple_follower_ros2內多種跟隨模式

### 互補關係
- **感測器驅動：** 雷達+相機提供不同感知能力
- **控制層次：** 手動控制 ↔ 自主導航 ↔ 智能跟隨

## 🛠️ 系統要求

- **作業系統：** Ubuntu 22.04 LTS
- **ROS版本：** ROS2 Humble
- **硬體支援：** 輪式機器人底盤、雷達、深度相機
- **網路：** Ethernet/WiFi連接

## 📚 相關資源

- [ROS2官方文檔](https://docs.ros.org/en/humble/)
- [Navigation2文檔](https://navigation.ros.org/)
- [DDS安全規範](https://www.omg.org/spec/DDS-SECURITY/)

## 🤝 貢獻指南

1. Fork本專案
2. 創建功能分支 (`git checkout -b feature/AmazingFeature`)
3. 提交變更 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 開啟Pull Request

## 📄 許可證

本專案採用多種許可證，詳見各模組的package.xml文件。

---

**注意：** 此專案支援完全自主運作，主控台主要用於監控、調試和複雜任務配置，並非運行必需。