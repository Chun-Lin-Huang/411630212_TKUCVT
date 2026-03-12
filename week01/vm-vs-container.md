# vm-vs-container.md
必須包含：
- 至少四個維度的 VM vs Container 對照
- 本課選擇「VM 裡跑 Docker」的理由（用自己的話寫）
- Hypervisor Type 1 vs Type 2 的差異與本課的選擇

## VM vs Container
VM 和 Container 都提供隔離的執行環境，但隔離層級不同：

| 維度 | Virtual Machine (VM) | Container |
|---|---|---|
| 隔離層 | 完整 Guest OS + Hypervisor | 共用 Host OS kernel |
| 啟動速度 | 較慢，需要啟動整個 OS | 快速，只需啟動程序 |
| 資源使用 | 較多，每台 VM 需要 OS | 較少，可同時運行多個容器 |
| 封裝內容 | OS + 應用程式 + 設定 | 應用程式與相依套件 |
| 映像大小 | 數 GB | 數十 MB ~ 數百 MB |
| 核心技術 | Hypervisor | Docker / container engine |
| 回復方式 | Snapshot 還原 | 重新拉取映像 |

## 為什麼本課選擇「VM 裡跑 Docker」
在這門課中，我們是在 VM 裡面安裝 Docker，而不是直接在自己的電腦上安裝。主要原因是每個同學的電腦環境都不太一樣，有的人用 Windows、有的人用 macOS，如果直接在主機上安裝 Docker，可能會因為系統或設定不同而出現問題，透過先建立一個 Ubuntu 的虛擬機，就可以讓大家在同一個 Linux 環境下操作 Docker。這樣在做實驗或排錯時比較容易重現，也比較不會出現「只有某些人跑不過」的情況，還有 VM 也可以建立 snapshot，如果實驗過程中出現問題，可以直接回復到之前的狀態，不需要重新安裝整個系統 ; Docker 則可以快速啟動不同的服務，例如 nginx 或 alpine，這樣的架構在實驗上會比較方便，也比較好管理環境。

## Hypervisor Type 1 vs Type 2 的差異與本課的選擇
在課程中，我們使用的是 Type 2 Hypervisor。這是因為課程通常是在個人電腦上進行實作，例如使用 VMware 或 UTM 來建立 Ubuntu 虛擬機。Type 2 Hypervisor 安裝與使用比較簡單，適合教學與開發環境。

| 類型 | Type 1 Hypervisor | Type 2 Hypervisor |
|---|---|---|
| 架構 | 直接安裝在實體硬體上，不需要先有作業系統 | 安裝在現有作業系統之上，像一般應用程式一樣執行 |
| 效能 | 效能高、延遲低，VM 透過 Hypervisor 直接與硬體溝通 | 方便安裝在個人電腦，但多一層 Host OS，效能略低 |
| 常見用途 | 企業資料中心、雲端基礎設施 | 個人開發、教學、本機測試 |
| 範例 | VMware ESXi、Microsoft Hyper-V、KVM | VMware Workstation、VirtualBox、UTM |
