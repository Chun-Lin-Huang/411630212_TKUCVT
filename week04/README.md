# W04｜Linux 系統基礎：檔案系統、權限、程序與服務管理

## FHS 路徑表

| FHS 路徑 | FHS 定義 | Docker 用途 |
|---|---|---|
| /etc/docker/ | （填入） | （填入） |
| /var/lib/docker/ | （填入） | （填入） |
| /usr/bin/docker | （填入） | （填入） |
| /run/docker.sock | （填入） | （填入） |

## Docker 系統資訊

- Storage Driver：（貼上 `docker info` 中的值）
- Docker Root Dir：（貼上 `docker info` 中的值）
- 拉取映像前 /var/lib/docker/ 大小：（填入）
- 拉取映像後 /var/lib/docker/ 大小：（填入）

## 權限結構

### Docker Socket 權限解讀
（貼上 `ls -la /var/run/docker.sock` 輸出，逐欄說明 owner/group/others 的權限）

### 使用者群組
（貼上 `id` 輸出，說明是否包含 docker 群組）

### 安全意涵
（用自己的話說明為什麼 docker group ≈ root，安全示範的觀察結果）

## 程序與服務管理

### systemctl status docker
（貼上 `systemctl status docker` 輸出）

### journalctl 日誌分析
（貼上 `journalctl -u docker --since "1 hour ago"` 的重點摘錄，說明看到什麼事件）

### CLI vs Daemon 差異
（用自己的話說明兩者的差異，為什麼 `docker --version` 正常不代表 Docker 能用）

## 環境變數

- $PATH：（貼上內容）
- which docker：（填入路徑）
- 容器內外環境變數差異觀察：（簡述）

## 故障場景一：停止 Docker Daemon

| 項目 | 故障前 | 故障中 | 回復後 |
|---|---|---|---|
| systemctl status docker | active | （填入） | （填入） |
| docker --version | 正常 | （填入） | （填入） |
| docker ps | 正常 | Cannot connect | （填入） |
| ps aux grep dockerd | 有 process | （填入） | （填入） |

## 故障場景二：破壞 Socket 權限

| 項目 | 故障前 | 故障中 | 回復後 |
|---|---|---|---|
| ls -la docker.sock 權限 | srw-rw---- | （填入） | （填入） |
| docker ps（不加 sudo） | 正常 | permission denied | （填入） |
| sudo docker ps | 正常 | （填入） | （填入） |
| systemctl status docker | active | （填入） | （填入） |

## 錯誤訊息比較

| 錯誤訊息 | 根因 | 診斷方向 |
|---|---|---|
| Cannot connect to the Docker daemon | （填入） | （填入） |
| permission denied…docker.sock | （填入） | （填入） |

（用自己的話說明兩種錯誤的差異，各自指向什麼排錯方向）

## 排錯紀錄
- 症狀：
- 診斷：（你首先查了什麼？）
- 修正：（做了什麼改動？）
- 驗證：（如何確認修正有效？）

## 設計決策
（說明本週至少 1 個技術選擇與取捨，例如：為什麼教學環境用 `usermod` 加 group 而不是每次 sudo？這個選擇的風險是什麼？）
