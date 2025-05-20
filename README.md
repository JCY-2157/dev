```mermaid
flowchart TB
    %% 主程序入口
    Main[主程序 main.ino]

    %% 初始化
    Init[系统初始化 initSystem()]
    Main --> Init

    %% 调度循环
    Loop[循环主函数 loop()]
    Init --> Loop

    %% 调度各模块
    Loop --> TM[任务管理 TaskManager]
    Loop --> NAV[导航 Navigation]
    Loop --> CTRL_ATT[姿态控制 AttitudeCtrl]
    Loop --> CTRL_PWR[动力控制 PowerCtrl]
    Loop --> DPC[深度控制 DepthCtrl]
    Loop --> ENV[环境感知 EnvSense]
    Loop --> PWR_MGR[电源管理 PowerMgr]
    Loop --> COMM[通信 Comm]
    Loop --> DIAG[故障诊断 Diagnosis]

    %% 任务管理
    TM -->|任务指令| NAV
    TM -->|深度指令| DPC
    TM -->|返航指令| COMM

    %% 导航
    NAV -->|目标航向/速度| CTRL_ATT
    NAV -->|目标速度| CTRL_PWR

    %% 姿态与深度控制
    CTRL_ATT -->|姿态修正| CTRL_PWR
    DPC -->|深度修正| CTRL_PWR

    %% 动力控制
    CTRL_PWR -->|PWM/舵机指令| Actuator[推进器&泵驱动]

    %% 环境感知避障
    ENV -->|避障修正| CTRL_ATT
    ENV -->|避障修正| CTRL_PWR

    %% 电源管理
    PWR_MGR -->|低电报警| TM
    PWR_MGR -->|电量状态| COMM

    %% 通信
    COMM -->|上位机数据| TM
    COMM -->|状态回传| COMMMonitor[地面站]

    %% 故障诊断
    DIAG -->|故障警报| TM
    DIAG -->|故障日志| COMM

    %% 样式定义
    classDef module fill:#f9f,stroke:#333,stroke-width:1px;
    class TM,NAV,CTRL_ATT,CTRL_PWR,DPC,ENV,PWR_MGR,COMM,DIAG module;
```