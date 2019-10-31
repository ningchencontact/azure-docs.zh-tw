---
title: Azure 檔案共用–無法刪除 Azure 檔案共用中的檔案
description: 找出錯誤，並對其進行疑難排解，以從 Azure 檔案共用中刪除檔案。
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/25/2019
ms.service: storage
ms.subservice: common
ms.openlocfilehash: b535578328e7ca77f1071187b6ac761bc7076ac1
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2019
ms.locfileid: "73065973"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Azure 檔案共用–無法刪除 Azure 檔案共用中的檔案

無法從 Azure 檔案共用中刪除檔案可能會有數個徵兆：

**徵兆1：**

因為下列兩個問題的其中一個，所以無法刪除 azure 檔案共用中的檔案：

* 標記為要刪除的檔案
* SMB 用戶端可能正在使用指定的資源

**徵兆2：**

沒有足夠的配額可用來處理此命令

## <a name="cause"></a>原因

當您在裝載檔案共用的電腦上達到檔案所允許的並行開啟控制碼上限時，就會發生錯誤1816。 如需詳細資訊，請參閱[Azure 儲存體效能和擴充性檢查清單](https://docs.microsoft.com/azure/storage/blobs/storage-performance-checklist)。

## <a name="resolution"></a>解析度

關閉一些控點以減少並行開啟的控制碼數目。

## <a name="prerequisite"></a>先決條件

### <a name="install-the-latest-azure-powershell-module"></a>安裝最新的 Azure PowerShell 模組

* [安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>連接到 Azure：

```
# Connect-AzAccount
```

### <a name="select-the-subscription-of-the-target-storage-account"></a>選取目標儲存體帳戶的訂用帳戶：

```
# Select-AzSubscription -subscriptionid "SubscriptionID"
```

### <a name="create-context-for-the-target-storage-account"></a>建立目標儲存體帳戶的內容：

```
$Context = New-AzStorageContext -StorageAccountName "StorageAccountName" -StorageAccountKey "StorageAccessKey"
```

### <a name="get-the-current-open-handles-of-the-file-share"></a>取得檔案共用目前的開啟控制碼：

```
# Get-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Recursive
```

## <a name="example-result"></a>範例結果：

|HandleId|路徑|clientIp|clientPort|OpenTime|LastReconnectTime|FileId|ParentId|SessionId|
|---|---|---|---|---|---|---|---|---|
|259101229083|---|10.222.10.123|62758|2019-10-05|12：16：50Z|0|0|9507758546259807489|
|259101229131|---|10.222.10.123|62758|2019-10-05|12：36：20Z|0|0|9507758546259807489|
|259101229137|---|10.222.10.123|62758|2019-10-05|12：36：53Z|0|0|9507758546259807489|
|259101229136|新增資料夾/測試 .zip|10.222.10.123|62758|2019-10-05|12：36：29Z|13835132822072852480|9223446803645464576|9507758546259807489|
|259101229135|測試 .zip|37.222.22.143|62758|2019-10-05|12：36：24Z|11529250230440558592|0|9507758546259807489|

### <a name="close-an-open-handle"></a>關閉開啟的控制碼：

若要關閉開啟的控制碼，請使用下列命令：

```
# Close-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Path 'New folder/test.zip' -CloseAll
```

## <a name="next-steps"></a>後續步驟

* [針對 Windows 中的 Azure 檔案服務進行疑難排解](storage-troubleshoot-windows-file-connection-problems.md)
* [針對 Linux 中的 Azure 檔案服務進行疑難排解](storage-troubleshoot-linux-file-connection-problems.md)
* [針對 Azure 檔案同步進行移難排解](storage-sync-files-troubleshoot.md)