# W05｜把容器拆開來看：Namespace / Cgroups / Union FS / OCI

## Docker 環境

- Storage Driver：（貼上）
- Cgroup Version：（貼上）
- Cgroup Driver：（貼上）
- Default Runtime：（貼上）

## Namespace 觀察

### 六種 namespace 用途（用自己的話）
- PID：
- NET：
- MNT：
- UTS：
- IPC：
- USER：

### Host vs 容器 inode 對照
（貼上或連結 `namespace-table.md`）

### 容器內 `ps aux` 輸出
（只看到幾支 process？為什麼？）

## Cgroups 實驗

### 容器內讀到的限制
- memory.max：
- cpu.max：

### Host 端對照（用 `docker inspect -f '{{.HostConfig.CgroupParent}}'` 動態取得路徑）
- memory.max：
- cpu.max：
- memory.current（執行時某一刻）：

### OOM 故障三階段
| 項目 | 故障前 | 故障中（memory=32m + dd 200m）| 回復後（memory=256m）|
|---|---|---|---|
| 容器 exit code | - | （填入）| （填入）|
| OOMKilled | - | （填入）| （填入）|
| dmesg 關鍵字 | 無 OOM | （填入）| 無 OOM |

## Image 分層

### `docker image inspect nginx:1.27-alpine` layer 數量
（填入）

### 兩個同源 image 共享 layer 的證據
（前幾個 sha256 是否相同？）

### `docker diff` 輸出範例與解讀
（貼上 A/C/D 實例並說明）

## OCI 呼叫鏈

（用自己的話說明 dockerd → containerd → containerd-shim → runc 各自負責什麼，以及 OCI Runtime Spec `config.json` 裡哪些欄位對應到 namespace / cgroup 設定）

## 排錯紀錄
- 症狀：
- 診斷：
- 修正：
- 驗證：

## 想一想（回答 3 題）
1. 容器裡的 PID 1 跟 host PID 1 是同一支 process 嗎？`kill -9 1`（在容器內）會發生什麼？
2. 兩個容器都基於 `ubuntu:24.04`，磁碟空間是吃兩份還是共用？怎麼驗證？
3. 如果 host 的 kernel 爆漏洞，容器還能稱為「隔離」嗎？這個限制跟 VM 差在哪？
