# W06｜Docker Image 與 Dockerfile

## 映像組成
- Layers 是什麼：（用自己的話寫）
- Config 是什麼：（用自己的話寫）
- Manifest 是什麼：（用自己的話寫）

## python:3.12-slim inspect 摘錄
- Config.Cmd：（填入）
- Config.Env：（填入）
- Config.WorkingDir：（填入）
- RootFS.Layers 數量：（填入）

## Layer 快取實驗
| 情境 | build 時間 |
|---|---|
| v1 首次 build | （填入） |
| v1 改 app.py 後 rebuild | （填入） |
| v2 首次 build | （填入） |
| v2 改 app.py 後 rebuild | （填入） |

觀察（用自己的話寫）：為什麼 v2 的 rebuild 這麼快？

## CMD vs ENTRYPOINT 實驗
| 寫法 | `docker run <img>` 輸出 | `docker run <img> extra1 extra2` 輸出 |
|---|---|---|
| CMD shell form | （填入） | （填入） |
| CMD exec form | （填入） | （填入） |
| ENTRYPOINT + CMD | （填入） | （填入） |

結論（用自己的話寫）：

## Multi-stage 大小對照
| Image | SIZE |
|---|---|
| python:3.12（builder base） | （填入） |
| python:3.12-slim（runtime base） | （填入） |
| myapp:v2（單階段） | （填入） |
| myapp:multi（多階段） | （填入） |

解釋（用自己的話寫）：builder stage 的 layer 去哪了？

## .dockerignore 故障注入
| 項目 | 故障前 | 故障中 | 回復後 |
|---|---|---|---|
| du -sh . | （填入） | （填入） | （填入） |
| build context 傳輸大小 | （填入） | （填入） | （填入） |
| build 時間 | （填入） | （填入） | （填入） |

## 排錯紀錄
- 症狀：
- 診斷：
- 修正：
- 驗證：

## 設計決策
（說明本週至少 1 個技術選擇與取捨，例如：為什麼 runtime 選 `python:3.12-slim` 而不是 `alpine`？）
