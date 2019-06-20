---
title: 設定、 最佳化和疑難排解 Azure 儲存體的 AzCopy |Microsoft Docs
description: 設定、 最佳化和疑難排解 AzCopy。
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 1a67846889b43d582a7a7d477a33f0e2168fd760
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147858"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>設定、 最佳化和疑難排解 AzCopy

AzCopy 是命令列公用程式可供您儲存體帳戶來回複製 blob 或檔案。 本文可協助您執行進階的組態工作，並可協助您使用 AzCopy，可能會發生的問題進行疑難排解。

> [!NOTE]
> 如果您要尋找的內容，可協助您開始使用 AzCopy，請參閱下列文章：
> - [開始使用 AzCopy](storage-use-azcopy-v10.md)
> - [使用 AzCopy 和 Blob 儲存體轉送資料](storage-use-azcopy-blobs.md)
> - [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)
> - [使用 AzCopy 和 Amazon S3 貯體轉送資料](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>進行 Proxy 設定

若要設定 AzCopy 的 proxy 設定，設定`https_proxy`環境變數。

| 作業系統 | 命令  |
|--------|-----------|
| **Windows** | 在命令提示字元中使用： `set https_proxy=<proxy IP>:<proxy port>`<br> 在 PowerShell，請使用： `$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

目前，AzCopy 不支援需要使用 NTLM 或 Kerberos 驗證的 proxy。

## <a name="optimize-throughput"></a>輸送量最佳化

設定`AZCOPY_CONCURRENCY_VALUE`環境變數來設定並行要求數量，以及控制的輸送量效能與資源耗用量。 如果您的電腦有 5 個 Cpu，則此變數的值會設定為`32`。 否則，預設值等於 16 乘以 Cpu 數目。 此變數的最大的預設值是`300`，但您可以手動設定此高或較低的值。

| 作業系統 | 命令  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

使用`azcopy env`檢查此變數的目前值。  如果值為空白，則`AZCOPY_CONCURRENCY_VALUE`變數設定的預設值為`300`。

## <a name="change-the-location-of-the-log-files"></a>變更記錄檔的位置

根據預設，記錄檔位於`%USERPROFILE\\.azcopy`目錄，在 Windows，或是在`$HOME\\.azcopy`在 Mac 和 Linux 上的目錄。 如果您需要使用下列命令，您可以變更此位置。

| 作業系統 | 命令  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

使用`azcopy env`檢查此變數的目前值。 如果此值為空白，記錄會寫入至預設位置。

## <a name="change-the-default-log-level"></a>變更預設記錄層級

根據預設，AzCopy 記錄層級設定為`INFO`。 如果您想要降低記錄詳細資訊，以節省磁碟空間，會覆寫這項設定使用``--log-level``選項。 

可用記錄層級為： `DEBUG`， `INFO`， `WARNING`， `ERROR`， `PANIC`，和`FATAL`。

## <a name="troubleshoot-issues"></a>針對問題進行疑難排解

AzCopy 會建立每個作業的記錄檔和方案檔案。 您可以使用記錄來調查任何可能的問題並進行疑難排解。 

記錄檔會包含失敗的狀態 (`UPLOADFAILED`， `COPYFAILED`，和`DOWNLOADFAILED`)，完整路徑，以及失敗的原因。

根據預設，記錄檔和計劃檔案都位於`%USERPROFILE\\.azcopy`在 Windows 上的目錄或`$HOME\\.azcopy`在 Mac 和 Linux 上的目錄。

> [!IMPORTANT]
> 當將要求提交給 Microsoft 支援服務 （或疑難排解問題涉及任何第三方），共用您想要執行此命令的修訂的版本。 這可確保與任何人，不會被不小心共用 SAS。 您可以在記錄檔開頭找到編校的版本。

### <a name="review-the-logs-for-errors"></a>檢閱記錄以了解錯誤

下列命令會取得所有的錯誤`UPLOADFAILED`狀態`04dc9ca9-158f-7945-5933-564021086c79`記錄：

**Windows**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>檢視和繼續作業

每個傳輸作業都會建立 AzCopy 作業。 若要檢視之作業的記錄，使用下列命令：

```
azcopy jobs list
```

若要檢視作業統計資料，請使用下列命令：

```
azcopy jobs show <job-id>
```

若要依狀態篩選傳輸，請使用下列命令：

```
azcopy jobs show <job-id> --with-status=Failed
```

若要繼續失敗/已取消的工作中使用下列命令。 此命令會使用 SAS 權杖以及其識別項，並不持續基於安全性理由：

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

當您繼續作業時，則 AzCopy 會探討作業計劃檔案。 計劃檔案列出的所有檔案，找出處理第一次建立工作。 當您繼續工作時，AzCopy 會嘗試傳送的所有檔案中沒有已傳送的計劃檔案所列。