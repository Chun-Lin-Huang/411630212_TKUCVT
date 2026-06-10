# W08｜容器生產實踐

## Healthcheck 故障測試
- 停 db 後幾秒被標 unhealthy：約 30 秒內
  - 測試流程為執行 `docker compose stop db` 後等待 `sleep 30`，再用 `docker inspect --format='{{.State.Health.Status}}' $(docker compose ps -q app)` 查詢，結果顯示為 `unhealthy`。
- 對應的 log 訊息：
  ```log
  127.0.0.1 - - [10/Jun/2026 07:19:55] "GET /healthz HTTP/1.1" 503 -
  127.0.0.1 - - [10/Jun/2026 07:20:03] "GET /healthz HTTP/1.1" 503 -
  127.0.0.1 - - [10/Jun/2026 07:20:11] "GET /healthz HTTP/1.1" 503 -
  127.0.0.1 - - [10/Jun/2026 07:20:20] "GET /healthz HTTP/1.1" 503 -
  127.0.0.1 - - [10/Jun/2026 07:20:28] "GET /healthz HTTP/1.1" 503 -
  ```

## Log 失控估算
- noisy 容器 30s log 大小：324,787,620 bytes（約 310 MB）
- 預估 24h 大小：871.148 GB
- 套 rotation 後穩定上限：約 6 MB
- 實測 rotation 後 log 目錄大小：5.4 MB

## 資源限制實驗
| 實驗 | 命令 | 觀察結果 | 對應 cgroup 檔 | 值 |
|---|---|---|---|---|
| OOM | `docker run --rm --name oomtest --memory 128m python:3.12-slim python -c "x = bytearray(256 * 1024 * 1024)"` | 嘗試配置 256MB，超過 128MB 限制後被 OOM killer 殺掉，exit code = 137 | `memory.max` | `134217728` |
| CPU throttle | `docker compose exec -d stress stress-ng --cpu 4 --timeout 30s` | `docker stats` 顯示 CPU% 約 49.11%，接近 50%，代表被 `cpus: "0.5"` 限速 | `cpu.max` | `50000 100000` |

## 權限四階對照
| 階梯 | id | CapEff | NoNewPrivs | curl /healthz |
|---|---|---|---|---|
| 0（baseline） |  uid=0(root) | `00000000a80425fb` | 0 | 200 |
| 1（非 root） | uid=1000(appuser) | `0000000000000000` | 0 | 200 |
| 2（唯讀 rootfs） | uid=1000(appuser) | `0000000000000000` | 0 | 200 |
| 3（cap_drop ALL） | uid=1000(appuser) | `0000000000000000` | 0 | 200 |
| 4（no-new-privileges） | uid=1000(appuser) | `0000000000000000` | 1 | 200 |

## 排錯紀錄
- 症狀：一開始執行 `docker compose up -d --build` 的時候，`app` 容器沒有成功啟動，畫面出現 `Bind for 0.0.0.0:8080 failed: port is already allocated`。
- 診斷：這代表主機的 8080 port 已經被其他服務佔用了，後來用 `sudo ss -ltnp | grep ':8080'` 和 `docker ps` 檢查，發現是之前 W07 的 `w07-app-1` 容器還在跑，而且也有使用 `8080:80`。
- 修正：先進到 W07 的資料夾，執行 `docker compose down` 把舊的 W07 容器關掉，釋放 8080 port，接著再回到 W08 資料夾重新執行 `docker compose up -d --build`。
- 驗證：重新啟動後，`docker compose ps` 顯示 `w08-app-1` 和 `w08-db-1` 都有正常啟動，而且狀態是 healthy，再用 `curl http://localhost:8080/healthz` 測試，也可以正常回傳 `ok`，代表問題已解決。

## 設計決策
這次我在 `app` service 設定 `mem_limit: 256m` 和 `cpus: "0.5"`，主要是因為這個 app 只是簡單的 Flask 網頁服務，功能不複雜，正常情況下不需要吃到很多記憶體或 CPU，設定 256MB 記憶體可以避免程式如果發生記憶體洩漏時，一直把主機資源吃光，CPU 限制在 0.5 顆，也可以避免程式發生無限迴圈時，把整台 VM 的 CPU 打滿，影響其他服務。

`db` service 則設定 `mem_limit: 512m` 和 `cpus: "1.0"`，因為資料庫通常比 app 更需要資源，而且 PostgreSQL 需要處理資料存取，所以給它比 app 多一點的記憶體和 CPU，不過這裡只是課堂實驗用的小型資料庫，所以也不需要給太高。

在權限設定的部分，我把 app 設成 `read_only: true`，讓容器的 root filesystem 變成唯讀，這樣如果攻擊者進到容器裡，也比較不容易亂寫檔案或塞惡意程式，但因為很多程式執行時還是會需要暫存空間，所以我另外補了 `tmpfs`，例如 `/tmp:size=32M`，讓程式需要暫存檔時還是有地方可以寫，這樣可以兼顧安全性和程式正常執行。

整體來說，這些設定的目的不是讓容器跑得更快，而是避免單一容器出問題時影響整台主機，讓 app 在資源使用和權限上都比較可控。

## 可重跑最小命令鏈
```bash
cd ~/virt-container-labs/w08
cp .env.example .env
docker compose up -d --build
sleep 15
curl http://localhost:8080/healthz
```