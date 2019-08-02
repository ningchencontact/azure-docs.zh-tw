---
title: 使用 Azure 儲存體設定、優化和疑難排解 AzCopy |Microsoft Docs
description: 設定、優化和疑難排解 AzCopy。
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 07/25/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 3773f9a8464dc94436d6d2503b173d4674033ab1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565048"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>設定、優化和疑難排解 AzCopy

AzCopy 是命令列公用程式, 可讓您在儲存體帳戶之間複製 blob 或檔案。 本文可協助您執行先進的設定工作, 並協助您針對使用 AzCopy 時可能發生的問題進行疑難排解。

> [!NOTE]
> 如果您要尋找可協助您開始使用 AzCopy 的內容, 請參閱下列文章:
> - [開始使用 AzCopy](storage-use-azcopy-v10.md)
> - [使用 AzCopy 和 Blob 儲存體轉送資料](storage-use-azcopy-blobs.md)
> - [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)
> - [使用 AzCopy 和 Amazon S3 貯體轉送資料](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>進行 Proxy 設定

若要設定 AzCopy 的 proxy 設定, 請設定`https_proxy`環境變數。 如果您在 Windows 上執行 AzCopy, AzCopy 會自動偵測 proxy 設定, 因此您不需要在 Windows 中使用此設定。 如果您選擇在 Windows 中使用此設定, 將會覆寫自動偵測。

| 作業系統 | 命令  |
|--------|-----------|
| **Windows** | 在命令提示字元中，請使用：`set https_proxy=<proxy IP>:<proxy port>`<br> 在 PowerShell 中，請使用：`$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

目前, AzCopy 不支援需要使用 NTLM 或 Kerberos 進行驗證的 proxy。

## <a name="optimize-throughput"></a>輸送量最佳化

您可以使用`cap-mbps`旗標, 將輸送量資料速率設為上限。 例如, 下列命令的上限為`10`每秒百萬位元 (MB)。

```azcopy
azcopy cap-mbps 10
```

傳輸小型檔案時, 輸送量可能會降低。 您可以藉由設定`AZCOPY_CONCURRENCY_VALUE`環境變數來增加輸送量。 此變數會指定可能發生的並行要求數目。  如果您的電腦有少於5個 Cpu, 則此變數的值會設定為`32`。 否則, 預設值等於16乘以 Cpu 的數目。 此變數的最大預設值為`300`, 但您可以手動將此值設定為較高或較低。

| 作業系統 | 命令  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

`azcopy env`使用來檢查這個變數目前的值。  如果此值為空白, `AZCOPY_CONCURRENCY_VALUE`則變數會設定為的預設`300`值。

## <a name="change-the-location-of-the-log-files"></a>變更記錄檔的位置

根據預設, 記錄檔位於 Windows 上的`%USERPROFILE\\.azcopy`目錄中, 或位於 Mac 和`$HOME\\.azcopy` Linux 上的目錄中。 如有需要, 您可以使用這些命令來變更此位置。

| 作業系統 | 命令  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

`azcopy env`使用來檢查這個變數目前的值。 如果此值為空白, 則會將記錄寫入至預設位置。

## <a name="change-the-default-log-level"></a>變更預設記錄層級

根據預設, AzCopy 記錄層級會設定`INFO`為。 如果您想要減少記錄的詳細資訊以節省磁碟空間, 請使用``--log-level``選項來覆寫此設定。 

可用的記錄層級`DEBUG`為`INFO`: `WARNING`、 `ERROR`、 `PANIC`、、 `FATAL`和。

## <a name="troubleshoot-issues"></a>針對問題進行疑難排解

AzCopy 會為每個作業建立記錄檔和計畫檔案。 您可以使用記錄來調查任何可能的問題並進行疑難排解。 

記錄檔將包含失敗的狀態 (`UPLOADFAILED`、 `COPYFAILED`和`DOWNLOADFAILED`)、完整路徑, 以及失敗的原因。

根據預設, 記錄檔和方案檔位於 Windows 上的`%USERPROFILE\\.azcopy`目錄或`$HOME\\.azcopy` Mac 和 Linux 上的目錄。

> [!IMPORTANT]
> 提交要求以 Microsoft 支援服務 (或針對任何協力廠商的問題進行疑難排解) 時, 請共用您要執行之命令的編校版本。 這可確保 SAS 不會意外與任何人共用。 您可以在記錄檔開頭找到編校的版本。

### <a name="review-the-logs-for-errors"></a>檢閱記錄以了解錯誤

下列命令會`04dc9ca9-158f-7945-5933-564021086c79`從記錄檔中取得`UPLOADFAILED`狀態為的所有錯誤:

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>檢視和繼續作業

每個傳輸作業都會建立 AzCopy 作業。 使用下列命令來查看工作的歷程記錄:

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

使用下列命令來繼續失敗/已取消的工作。 此命令會使用其識別碼和 SAS 權杖, 因為它在安全性方面並不會持續:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

當您繼續工作時, AzCopy 會查看作業計畫檔案。 計畫檔案會列出第一次建立作業時識別要處理的所有檔案。 當您繼續工作時, AzCopy 會嘗試傳送尚未傳送的計畫檔案中列出的所有檔案。