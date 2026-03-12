# W01｜虛擬化概論、環境建置與 Snapshot 機制

## 環境資訊
- Host OS：macOS 14
- VM 名稱：ubuntu-arm
- Ubuntu 版本：
  ```
  Distributor ID: Ubuntu
  Description:    Ubuntu 24.04.4 LTS
  Release:        24.04
  Codename:       noble
  ```
- Docker 版本：`Docker version 29.3.0, build 5927d80`
- Docker Compose 版本：`Docker Compose version v5.1.0`

## VM 資源配置驗證

| 項目 | VMware 設定值 | VM 內命令 | VM 內輸出 |
|---|---|---|---|
| CPU | 2 vCPU | `lscpu \| grep "^CPU(s)"` | `CPU(s):4` |
| 記憶體 | 4 GB | `free -h \| grep Mem` | `Mem:   3.8Gi   1.1Gi   636Mi    29Mi   2.3Gi   2.7Gi` |
| 磁碟 | 40 GB | `df -h /` | `/dev/mapper/ubuntu--vg-ubuntu--lv   30G   12G   17G  43% /` |
| Hypervisor | VMware | `lscpu \| grep Hypervisor` | qemu |

## 四層驗收證據
- [X] ① Repository：`cat /etc/apt/sources.list.d/docker.list` 輸出
  ```
  deb [arch=arm64 signed-by=/etc/apt/keyrings/docker.gpg]   https://download.docker.com/linux/ubuntu   noble stable
  ```
- [X] ② Engine：`dpkg -l | grep docker-ce` 輸出
  ```
  ii  docker-ce                    5:29.3.0-1~ubuntu.24.04~noble   arm64
  ii  docker-ce-cli                5:29.3.0-1~ubuntu.24.04~noble   arm64
  ii  docker-ce-rootless-extras    5:29.3.0-1~ubuntu.24.04~noble   arm64
  ```
- [X] ③ Daemon：`sudo systemctl status docker` 顯示 active
  ```
  ● docker.service - Docker Application Container Engine
       Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; preset: e>
       Active: active (running) since Thu 2026-03-12 11:14:03 CST; 30min ago
  TriggeredBy: ● docker.socket
         Docs: https://docs.docker.com
     Main PID: 4421 (dockerd)
        Tasks: 12
       Memory: 34.2M (peak: 66.9M)
          CPU: 3.110s
       CGroup: /system.slice/docker.service
             └─4421 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/cont
  ```
- [X] ④ 端到端：`sudo docker run hello-world` 成功輸出
  ```
  Hello from Docker!
  This message shows that your installation appears to be working correctly.
  ```
- [X] Compose：`docker compose version` 可執行
  ```
  Docker Compose version v5.1.0
  ```

## 容器操作紀錄
- [ ] nginx：`sudo docker run -d -p 8080:80 nginx` + `curl localhost:8080` 輸出
- [ ] alpine：`sudo docker run -it --rm alpine /bin/sh` 內部命令與輸出
- [ ] 映像列表：`sudo docker images` 輸出

## Snapshot 清單

| 名稱 | 建立時機 | 用途說明 | 建立前驗證 |
|---|---|---|---|
| clean-baseline | （時間點） | （此節點代表的狀態） | （列出建點前做了哪些驗證） |
| docker-ready | （時間點） | （此節點代表的狀態） | （列出建點前做了哪些驗證） |

## 故障演練三階段對照

| 項目 | 故障前（基線） | 故障中（注入後） | 回復後 |
|---|---|---|---|
| docker.list 存在 | 是 | 否 | （填入） |
| apt-cache policy 有候選版本 | 是 | 否 | （填入） |
| docker 重裝可行 | 是 | 否 | （填入） |
| hello-world 成功 | 是 | N/A | （填入） |
| nginx curl 成功 | 是 | N/A | （填入） |

## 手動修復 vs Snapshot 回復

| 面向 | 手動修復 | Snapshot 回復 |
|---|---|---|
| 所需時間 | （你的實測） | （你的實測） |
| 適用情境 | （你的判斷） | （你的判斷） |
| 風險 | （你的判斷） | （你的判斷） |

## Snapshot 保留策略
- 新增條件：
- 保留上限：
- 刪除條件：

## 最小可重現命令鏈
（列出讓他人能重現故障注入與回復驗證的命令序列）

## 排錯紀錄
- 症狀：
- 診斷：（你首先查了什麼？）
- 修正：（做了什麼改動？）
- 驗證：（如何確認修正有效？）

## 設計決策
（說明本週至少 1 個技術選擇與取捨）
