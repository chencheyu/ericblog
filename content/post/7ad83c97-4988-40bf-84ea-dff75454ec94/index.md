---
date: 2024-06-21
publish: true
slug: 4b7b09c2-a42a-44c6-b942-36f2fd3929db
tags:
- CS/Linux
title: LinuxLink
---
### 執行期尋找 shared library 的流程

1. 若 shared library 名稱內有 “/”，表示它是路徑，直接用這個路徑找
2. 若 executable 內有定義 DT_RPATH 沒定義 DT_RUNPATH，從 DT_RPATH 列的目錄裡找
3. 從 LD_LIBRARY_PATH 列的目錄裡找
4. 從 DT_RUNPATH 列的目錄裡找
5. 從 ldconfig 產生的 cache 內找 (/etc/ld.so.cache)
6. 從 OS 的預設位置找: 先找 /lib 再找 /usr/lib

### Tool

```sh
nm -Du /bin/ls  
```

列出未定義需要外部提供的 symbol

```sh
ldd /bin/ls  
objdump -p /bin/ls  
```

列出需要的 shared library

> ldd 會嘗試尋找後給出找到的 shared library 路徑



```sh
LD_DEBUG=libs out/Debug/chrome  
LD_DEBUG=symbols out/Debug/chrome  
```

看找shared library的過程

```sh
-I<search path to include files>  
-L<search path to the lib file>  
-l<libname>  
-rpath=<runtime library search path>  
```

GCC 連結查找參數

```sh
-rpath='$ORIGIN'/lib  
```

rpath 可以用相對於可執行檔的路徑

```sh
LD_LIBRARY_PATH=. ./main  
```

在執行資料夾下找shared library

### Reference

- [Linux 編譯 shared library 的方法和注意事項](<%5Bhttps://medium.com/fcamels-notes/linux-%E7%B7%A8%E8%AD%AF-shared-library-%E7%9A%84%E6%96%B9%E6%B3%95%E5%92%8C%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A0%85-cb35844ef331%5D(https://medium.com/fcamels-notes/linux-%E7%B7%A8%E8%AD%AF-shared-library-%E7%9A%84%E6%96%B9%E6%B3%95%E5%92%8C%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A0%85-cb35844ef331>))
- [(C/C++ ) 如何在 Linux 上使用自行編譯的第三方函式庫](<%5Bhttps://medium.com/@fcamel/c-c-%E5%A6%82%E4%BD%95%E5%9C%A8-linux-%E4%B8%8A%E4%BD%BF%E7%94%A8%E8%87%AA%E8%A1%8C%E7%B7%A8%E8%AD%AF%E7%9A%84%E7%AC%AC%E4%B8%89%E6%96%B9%E5%87%BD%E5%BC%8F%E5%BA%AB-1f19c3abaebe%5D(https://medium.com/@fcamel/c-c-%E5%A6%82%E4%BD%95%E5%9C%A8-linux-%E4%B8%8A%E4%BD%BF%E7%94%A8%E8%87%AA%E8%A1%8C%E7%B7%A8%E8%AD%AF%E7%9A%84%E7%AC%AC%E4%B8%89%E6%96%B9%E5%87%BD%E5%BC%8F%E5%BA%AB-1f19c3abaebe>))
- [Linux 執行時尋找 symbol 的流程以及 shared library 相關知識](<%5Bhttps://medium.com/fcamels-notes/linux-%E5%9F%B7%E8%A1%8C%E6%99%82%E5%B0%8B%E6%89%BE-symbol-%E7%9A%84%E6%B5%81%E7%A8%8B%E4%BB%A5%E5%8F%8A-shared-library-%E7%9B%B8%E9%97%9C%E7%9F%A5%E8%AD%98-b0cf1e19cbf3%5D(https://medium.com/fcamels-notes/linux-%E5%9F%B7%E8%A1%8C%E6%99%82%E5%B0%8B%E6%89%BE-symbol-%E7%9A%84%E6%B5%81%E7%A8%8B%E4%BB%A5%E5%8F%8A-shared-library-%E7%9B%B8%E9%97%9C%E7%9F%A5%E8%AD%98-b0cf1e19cbf3>))
