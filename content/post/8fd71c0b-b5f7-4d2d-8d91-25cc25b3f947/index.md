---
date: 2024-06-28
publish: true
slug: bb4160f4-dc8e-432c-8631-6100b47b6814
tags:
- CS/NetWork
title: Rclone
---
[rclone](https://rclone.org/)
Rclone 是一个用于管理云存储上的文件的命令行程序
Windows 上設定檔在 `C:\Users\eric2_chen\AppData\Roaming\rclone\rclone.conf`
`--interactive` 在執行操作前先詢問，通常在錯誤復原中使用

### config

```sh
rclone config
```

命令行互動式設定遠端

### ls

```sh
rclone ls note:
rclone ls --max-depth 1 note: 
```

列出遠端資源，遠端一定要加冒號 `note:`，會遞迴的列出檔案樹上的全部資源
`--max-depth 1` 用以限制只列一級資料夾

- `ls` to list size and path of objects only
- `lsl` to list modification time, size and path of objects only
- `lsd` to list directories only
- `lsf` to list objects and directories in easy to parse format
- `lsjson` to list objects and directories in JSON format

### [bisync](https://rclone.org/bisync/)

使用差分同步

> 在兩條路徑之間執行雙向同步
> Bisync 在 rclone 中提供了雙向雲端同步解決方案。它保留先前運行中的 Path1 和 Path2 檔案系統清單。在每次連續運行時，它將：

> - 列出 Path1 和 Path2 上的文件，並檢查每一側的變更。更改包括`New`、`Newer`、`Older`和`Deleted`文件
> - 將 Path1 上的變更傳播到 Path2，反之亦然



```bash
rclone bisync . note: --create-empty-src-dirs --compare size,modtime,checksum --slow-hash-sync-only --resilient -vP --drive-skip-gdocs --fix-case --exclude ".**" --resync
```

先設定好 obsibian 作為遠端
第一次上傳，如同 sync 不會刪除 path1 裡的任何文件

```bash
rclone bisync . note: --create-empty-src-dirs --compare size,modtime,checksum --slow-hash-sync-only -v --drive-skip-gdocs --fix-case --max-delete 5 --max-lock 720 --exclude ".**"
```

之後直接兩個資料夾同步

`--max-lock [second]` bisync 會產生操作中的檔案列表在操作目錄，如果異常中斷會導致下一次執行失敗，直到 lock 檔被刪除
`--check-sync=only` 只確定兩側檔案是否一致，不改變任何檔案
Windows 上 `C:\Users\eric2_chen\AppData\Local\rclone\bisync` 為儲存檔案系統清單的路徑
`--recover` 如果異常終止而使 lock 檔位被解鎖，不想`--resync` 強制覆蓋碗端檔案，可使用此指令指無視 lock 檔

### Google Drive

`root_folder_id` 可以指定以特定資料夾做為根，在 Scope 為 drive or drive.file 都可以運作
如果用 Scope 為 drive.file 設定不同的使用者上傳到同一個  root_folder_id 會無法看到對方的上傳

> [!warning]
> 當設定 Scope 為 drive.file 與 FolderSync 以同步手機筆記時即使以相同使用者授權 Rclone 也無法看到 FolderSync 新增的檔案，但 Rclone 新增的 FolderSync 可以看見
> Rclone 同步不同電腦的話則可以正常同步



`--drive-acknowledge-abuse` Google 可能白目地認為某些檔案存在風險而阻止您下載，此標誌忽略此限制
