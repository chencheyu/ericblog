---
date: 2024-12-07
publish: true
slug: 2a5584d3-64f9-4035-8c51-9b034eb8e7aa
tags:
- CS/Windows
title: Path
uuid: 3947ec88-3571-41dd-8686-38701b8850ab
---
[Naming Files, Paths, and Namespaces](https://learn.microsoft.com/en-us/windows/win32/fileio/naming-a-file)

## DOS Path

`C:\File`
路徑不得有以下保留字元：

- < (less than)
- `&gt;` (greater than)
- `:` (colon)
- `&quot;` (double quote)
- `/` (forward slash)
- `\` (backslash)
- | (vertical bar or pipe)
- ? (question mark)
- `*` (asterisk)
- ASCII NULL

檔案名稱或目錄名稱請勿以空格或句點結尾。儘管底層檔案系統可能支援此類名稱，但 Windows shell 和使用者介面不支援。但是，可以將句點指定為名稱的第一個字元
shell 和檔案系統有不同的要求。可以使用 Windows API 建立 shell 使用者介面無法正確解釋的路徑
Do not use the following reserved names for the name of a file:
CON, PRN, AUX, NUL, COM0, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, LPT0, LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9

- `2018\January.xlsx` A relative path to a file in a subdirectory of the current directory
- `\Program Files\Custom Utilities\StringFinder.exe` A relative path from the root of the current drive
- `C:\Projects\apilibrary\apilibrary.sln` An absolute file path from the root of drive C
- `C:Projects\apilibrary\apilibrary.sln` A relative path from the current directory of the C drive

注意最後兩個路徑的區別。兩者都指定了可選的磁碟卷標 (在這兩種情況下均為 C:)，但第一個路徑從指定卷的根目錄開始，而第二個路徑則不是。結果，第一個路徑是從 C: 磁碟驅動器根目錄開始的絕對路徑，而第二個路徑是從 C: 磁碟驅動器當前目錄開始的相對路徑。當需要使用第一種形式時，卻錯誤地使用了第二種形式，是涉及 Windows 文件路徑的常見錯誤來源之一

## Universal Naming Convention(UNC)

描述網路資源（例如共用檔案、目錄或印表機）位置的通用語法
`\\HostName[@SSL][@Port]\SharedFolder\Resource`
UNC 最大長度為 260
`\\127.0.0.1\c$\MinGW` 表示 Local File System 的 `c:\MinGW` 磁碟 C 用 `$c` 表示，c 不區分大小寫
Windows API 中的檔案 I/O 函數會將 `/` 轉換為 `\`，作為將名稱轉換為 NT 樣式名稱的一部分，但使用 `\\?\` 前綴時除外

### MAX_PATH

從 Windows 10 版本 1607 開始，許多常見 Win32 檔案和目錄函數中已刪除MAX_PATH限制。但是，您的應用程式必須滿足兩個條件

- 設定註冊表

```sh
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem]
"LongPathsEnabled"=dword:00000001
```

- 應用程式清單聲明長路徑

```xml
<application xmlns="urn:schemas-microsoft-com:asm.v3">
    <windowsSettings xmlns:ws2="http://schemas.microsoft.com/SMI/2016/WindowsSettings">
        <ws2:longPathAware>true</ws2:longPathAware>
    </windowsSettings>
</application>
```

### Long UNC

開頭方式跟 UNC 不一樣 `\\?\c:\`
資料夾需要 `\` 結尾
Long UNC 最大長度為 32767
`\\?\C:\test\longUNC.exe`
如果 Long UNC 以空白結尾

- 无法双击执行
- 无法通过命令行执行(Long UNC 也不行)
- 如果同資料下有無空白版本，檔案管理器的屬性會是沒空白版本的資訊
- 如果同資料下有無空白版本，檔案管理器的刪除會刪到沒空白的版本
- 可以用 Long UNC 刪除, 創建
