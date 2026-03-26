# W02｜VMware 網路模式與雙 VM 排錯

## 網路配置

| VM | 網卡 | 模式 | IP | 用途 |
|---|---|---|---|---|
| dev-a | NIC 1 | NAT | 192.168.71.130 | 上網 |
| dev-a | NIC 2 | Host-only | 192.168.16.128 | 內網互連 |
| server-b | NIC 1 | Host-only | 192.168.16.129 | 內網互連 |

## 連線驗證紀錄

- [X] dev-a NAT 可上網：`ping google.com` 輸出

  ![圖片說明](images/picture1.png)
- [X] 雙向互 ping 成功：貼上雙方 `ping` 輸出

  ![圖片說明](images/雙向pinga.png)
  ![圖片說明](images/雙向pingb.png)
- [X] SSH 連線成功：`ssh <user>@<ip> "hostname"` 輸出

  ![圖片說明](images/sshsuccess.png)
- [X] SCP 傳檔成功：`cat /tmp/test-from-dev.txt` 在 server-b 上的輸出

  ![圖片說明](images/SCPSuccess1.png)
  ![圖片說明](images/SCPSuccess2.png)
- [X] server-b 不能上網：`ping 8.8.8.8` 失敗輸出

  ![圖片說明](images/pingfail.png)

## 故障演練一：介面停用

| 項目 | 故障前 | 故障中 | 回復後 |
|---|---|---|---|
| server-b 介面狀態 | UP | DOWN | UP |
| dev-a ping server-b | 成功 | 失敗 | 成功 |
| dev-a SSH server-b | 成功 | 失敗 | 成功 |

## 故障演練二：SSH 服務停止

| 項目 | 故障前 | 故障中 | 回復後 |
|---|---|---|---|
| ss -tlnp grep :22 | 有監聽 | 無監聽 | 有監聽 |
| dev-a ping server-b | 成功 | 成功 | 成功 |
| dev-a SSH server-b | 成功 | Connection refused | 成功 |

## 排錯順序
（寫出你的 L2 → L3 → L4 排錯步驟與每層使用的命令）

## 網路拓樸圖
（嵌入或連結 network-diagram.png）

## 排錯紀錄
- 症狀：
- 診斷：（你首先查了什麼？用了哪個命令？）
- 修正：（做了什麼改動？）
- 驗證：（如何確認修正有效？）

## 設計決策
（說明本週至少 1 個技術選擇與取捨，例如：為什麼 server-b 只設 Host-only 不給 NAT？）
