Functions 主機在本機執行時，會將記錄寫入至下列路徑：

```
<DefaultTempDirectory>\LogFiles\Application\Functions
```

在 Windows 上，第一個找到的 TMP、TEMP、USERPROFILE 環境變數值會是 `<DefaultTempDirectory>`，或是 Windows 目錄。
在 MacOS 或 Linux 上，`<DefaultTempDirectory>` 則是 TMPDIR 環境變數。

[!Note]
Functions 主機啟動時，將會覆寫該目錄中現有的檔案結構。