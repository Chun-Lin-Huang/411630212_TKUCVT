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
- [X] nginx：`sudo docker run -d -p 8080:80 nginx` + `curl localhost:8080` 輸出
- [X] alpine：`sudo docker run -it --rm alpine /bin/sh` 內部命令與輸出
- [X] 映像列表：`sudo docker images` 輸出

## Snapshot 清單

| 名稱 | 建立時機 | 用途說明 | 建立前驗證 |
|---|---|---|---|
| clean-baseline | Ubuntu 建置完成並通過 Docker 四層驗證後 | 建立第一個可回復基線，建 snapshot 之前必須先確認環境健康 | `hostnamectl`、`ip route`、`docker --version`、`docker compose version`、`sudo systemctl status docker --no-pager`、`sudo docker run --rm hello-world`（全部通過才建點）|
| docker-ready | 完成 nginx 與 alpine 容器操作實驗後 | 已經有完整容器環境，可以直接進行服務測試 | `sudo systemctl status docker --no-pager`、`sudo docker run --rm hello-world`、`sudo docker images（確認 nginx、alpine 映像都在）|

## 故障演練三階段對照

| 項目 | 故障前（基線） | 故障中（注入後） | 回復後 |
|---|---|---|---|
| docker.list 存在 | 是 | 否 | 是 |
| apt-cache policy 有候選版本 | 是 | 否 | 是 |
| docker 重裝可行 | 是 | 否 | 是 |
| hello-world 成功 | 是 | N/A | 是 |
| nginx curl 成功 | 是 | N/A | 是 |

## 手動修復 vs Snapshot 回復

| 面向 | 手動修復 | Snapshot 回復 |
|---|---|---|
| 所需時間 | 大約幾秒鐘到幾分鐘（要看故障複雜的程度） | 大約幾秒鐘而已（因為只要 VM 關機後 → Snapshot Manager → 選 docker-ready → Revert → 開機） |
| 適用情境 | 小問題或是設定錯誤時（可以精準修復單一元件） | 系統被破壞或是環境混亂，需要快速回到穩定狀態時 |
| 風險 | 可能會修錯設定或遺漏某些步驟，導致問題不能完全解決 | 會回到舊狀態，snapshot 之後的變更會全部消失 |

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
