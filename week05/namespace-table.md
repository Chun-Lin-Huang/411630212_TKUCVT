# Host vs 容器 inode 對照

| Namespace | Host PID 1 inode | 容器 sleep inode | 一樣嗎？ |
|---|---|---|---|
| pid | pid:[4026531836] | pid:[4026532768] | 否 |
| net | net:[4026531833] | net:[4026532770] | 否 |
| mnt | mnt:[4026531832] | mnt:[4026532640] | 否 |
| uts | uts:[4026531838] | uts:[4026532766] | 否 |
| ipc | ipc:[4026531839] | ipc:[4026532767] | 否 |
| user | user:[4026531837] | user:[4026531837] | 是 |
