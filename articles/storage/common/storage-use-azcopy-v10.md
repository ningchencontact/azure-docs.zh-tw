---
title: 複製或移動資料至 Azure 儲存體，使用 AzCopy v10 （預覽） |Microsoft Docs
description: 使用 AzCopy v10 （預覽） 來移動或來回複製資料從 blob、 data lake 和檔案內容的命令列公用程式。 從本機檔案複製資料到 Azure 儲存體，或在儲存體帳戶內或之間複製資料。 輕鬆地將資料移轉至 Azure 儲存體。
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 02/24/2019
ms.author: artemuwka
ms.subservice: common
ms.openlocfilehash: ca7081bdfedae3abb5ec426a9d3ec0a7867a2ef9
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337012"
---
# <a name="transfer-data-with-azcopy-v10-preview"></a>使用 AzCopy v10 傳輸資料 （預覽）

AzCopy v10 （預覽） 是 Microsoft Azure Blob 和檔案儲存體來回複製資料的命令列公用程式。 AzCopy v10 提供重新設計的命令列介面，而新的架構，可靠的資料傳輸。 使用 AzCopy，您可以複製之間的檔案系統和儲存體帳戶，或儲存體帳戶之間的資料。

## <a name="whats-new-in-azcopy-v10"></a>AzCopy v10 的新增功能

- 同步處理檔案系統，Azure Blob 儲存體，反之亦然。 使用 `azcopy sync <source> <destination>`。 適用於增量複製案例。
- 支援 Azure Data Lake Storage Gen2 API。 使用`myaccount.dfs.core.windows.net`為呼叫 Data Lake 儲存體 Gen2 Api 的 URI。
- 支援將整個帳戶 (僅限 Blob 服務) 複製到另一個帳戶。
- 使用新[放置的區塊，從 URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url)支援帳戶-複製的 Api。 資料傳輸速度快，因為傳送到用戶端不需要。
- 列出或移除指定的路徑中的檔案和 blob。
- 支援萬用字元模式比對的路徑和--排除旗標。
- 與每個 AzCopy 執行個體，會建立工作順序和相關的記錄檔。 您可以檢視和重新啟動之前的工作，並繼續執行失敗的作業。 AzCopy 也會在失敗之後自動重試傳輸作業。
- 功能的一般效能改善。

## <a name="download-and-install-azcopy"></a>下載並安裝 AzCopy

### <a name="latest-preview-version-v10"></a>最新預覽版本 (v10)

下載 AzCopy 的最新預覽版本：
- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

### <a name="latest-production-version-v81"></a>最新生產版本 (v8.1)

下載 [AzCopy for Windows 的最新生產版本](https://aka.ms/downloadazcopy)。

### <a name="azcopy-supporting-table-storage-service-v73"></a>支援資料表儲存體服務的 AzCopy (v7.3)

下載[支援在 Microsoft Azure 資料表儲存體服務中來回複製資料的 AzCopy v7.3](https://aka.ms/downloadazcopynet)。

## <a name="post-installation-steps"></a>後續安裝步驟

AzCopy v10 不需要安裝。 開啟您慣用的命令列應用程式，並瀏覽至資料夾其中`azcopy.exe`所在。 如有需要您可以新增至您的系統路徑，以方便使用的 AzCopy 的資料夾位置。

## <a name="authentication-options"></a>驗證選項

使用 Azure 儲存體進行驗證時，AzCopy v10 支援下列選項：
- **Azure Active Directory** (支援**Blob 和 Data Lake 儲存體 Gen2 服務**)。 使用```.\azcopy login```使用 Azure Active Directory 登入。  使用者應該擁有[「 儲存體 Blob 資料參與者 」 角色指派](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)寫入 Blob 儲存體與 Azure Active Directory 驗證。 針對透過 Azure 資源管理的身分識別驗證，使用`azcopy login --identity`。
- **共用存取簽章權杖 [支援的 Blob 和檔案服務]**。 將共用的存取簽章 (SAS) 權杖附加至使用命令列上的 blob 路徑中。 您可以產生 SAS 權杖，使用 Azure 入口網站中，[儲存體總管](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/)， [PowerShell](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageblobsastoken)，或您選擇的其他工具。 如需詳細資訊，請參閱[範例](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)。

## <a name="getting-started"></a>開始使用

> [!TIP]
> **偏好圖形化使用者介面？**
>
> [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)，桌面的用戶端，可簡化管理的 Azure 儲存體資料，現在使用 AzCopy 來加速資料傳輸及移出 Azure 儲存體。
>
> 啟用下的儲存體總管中的 AzCopy**預覽**功能表。
> ![為 Azure 儲存體總管中的傳輸引擎讓 AzCopy](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

AzCopy v10 語法自行記錄。 當您已登入 Azure Active Directory 時，一般的語法看起來如下所示：

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>

# Examples if you have logged into the Azure Active Directory:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile"
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/container"

# Examples if you're using SAS tokens to authenticate:
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container?sastoken" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile?sastoken"
```

用來取得可用命令清單的方法如下：

```azcopy
.\azcopy --help
# To use the alias instead
.\azcopy -h
```

若要查看 [說明] 頁面和特定命令的範例，請執行下列命令：

```azcopy
.\azcopy <cmd> --help
# Example:
.\azcopy cp -h
```

## <a name="create-a-blob-container-or-file-share"></a>建立 blob 容器或檔案共用 

**建立 blob 容器**

```azcopy
.\azcopy make "https://account.blob.core.windows.net/container-name"
```

**建立檔案共用**

```azcopy
.\azcopy make "https://account.file.core.windows.net/share-name"
```

**使用 Azure Data Lake 儲存體 Gen2 建立 blob 容器**

如果您已在您的 Blob 儲存體帳戶上啟用階層式命名空間，您可以使用下列命令來建立新的 blob 容器上, 傳檔案。

```azcopy
.\azcopy make "https://account.dfs.core.windows.net/top-level-resource-name"
```

## <a name="copy-data-to-azure-storage"></a>將資料複製到 Azure 儲存體

使用 copy 命令將資料從來源傳輸到目的地。 來源或目的地可以是：
- 本機檔案系統
- Azure Blob/虛擬目錄/容器 URI
- Azure 檔案/目錄/檔案共用 URI
- Azure Data Lake Storage Gen2 檔案系統/目錄/檔案 URI

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using the alias instead 
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

下列命令會將資料夾下的所有檔案上都傳`C:\local\path`容器的遞迴`mycontainer1`建立`path`目錄中的容器：

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

下列命令會將資料夾 `C:\local\path` 下的所有檔案 (不會遞迴至子目錄) 上傳到容器 `mycontainer1`：

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>"
```

若要尋找更多範例，請使用下列命令：

```azcopy
.\azcopy cp -h
```

## <a name="copy-data-between-two-storage-accounts"></a>在兩個儲存體帳戶之間複製資料

兩個儲存體帳戶之間複製資料會使用[從 URL 將區塊](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url)API，但未使用用戶端電腦的網路頻寬。 AzCopy 只是用來協調的複製作業時，會直接，兩個 Azure 儲存體伺服器之間複製資料。 這個選項目前只適用於 Blob 儲存體。

若要在兩個儲存體帳戶之間複製資料，請使用下列命令：
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/<sastoken>" "https://myotheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

> [!NOTE]
> 此命令會列舉所有的 blob 容器，並將其複製到目的地帳戶。 在此階段中，AzCopy v10 支援複製只有兩個儲存體帳戶之間的區塊 blob。 它會略過所有其他儲存體帳戶物件 （例如附加 blob、 分頁 blob、 檔案、 資料表和佇列）。

## <a name="copy-a-vhd-image-to-a-storage-account"></a>將 VHD 映像複製到儲存體帳戶

AzCopy v10 依預設會將資料上傳至區塊 Blob。 不過，如果原始程式檔有`.vhd`延伸模組，AzCopy v10 會預設為上傳至分頁 blob。 在此階段中，此動作不是可設定。

## <a name="sync-incremental-copy-and-delete-blob-storage-only"></a>同步處理：累加複製和刪除 (僅限 Blob 儲存體)

同步命令會同步處理至目的地目錄比較檔案名稱的來源目錄的內容，以及上次修改時間戳記。 如果這些不存在來源中，此作業會包含選擇性的刪除作業的目的地檔案時`--delete-destination=prompt|true`會提供旗標。 根據預設，已停用 「 刪除 」 行為。 

> [!NOTE] 
> 使用`--delete-destination`謹慎的旗標。 啟用[虛刪除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)功能才能啟用以防止意外刪除您帳戶中的同步處理的刪除行為。 

> 當`--delete-destination`設為 true，則 AzCopy 會刪除不存在於目的地，而不需要任何提示的來源給使用者的檔案。 如果您想要收到確認提示，使用`--delete-destination=prompt`。

若要將本機檔案系統同步處理到儲存體帳戶，請使用下列命令：

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

您也可以同步處理到本機檔案系統的 blob 容器：

```azcopy
# The SAS token isn't required for Azure Active Directory authentication.
.\azcopy sync "https://account.blob.core.windows.net/mycontainer1" "C:\local\path" --recursive=true
```

此命令以累加方式同步處理到目的地的上次修改時間戳記為基礎的來源。 如果您在來源中新增或刪除檔案，AzCopy v10 也會在目的地進行相同操作。 刪除之前，AzCopy 會提示您確認。

## <a name="advanced-configuration"></a>進階組態

### <a name="configure-proxy-settings"></a>進行 Proxy 設定

若要設定 AzCopy v10 的 proxy 設定，請使用下列命令來設定環境變數 https_proxy:

```cmd
# For Windows:
set https_proxy=<proxy IP>:<proxy port>
# For Linux:
export https_proxy=<proxy IP>:<proxy port>
# For MacOS
export https_proxy=<proxy IP>:<proxy port>
```

### <a name="optimize-throughput"></a>輸送量最佳化

設定環境變數 AZCOPY_CONCURRENCY_VALUE 來設定並行要求數量，以及控制的輸送量效能與資源耗用量。 依預設，此值會設定為 300。 減少此值便可限制 AzCopy v10 所使用的頻寬和 CPU。

```cmd
# For Windows:
set AZCOPY_CONCURRENCY_VALUE=<value>
# For Linux:
export AZCOPY_CONCURRENCY_VALUE=<value>
# For MacOS
export AZCOPY_CONCURRENCY_VALUE=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank then the default value is currently in use
```

### <a name="change-the-location-of-the-log-files"></a>變更記錄檔的位置

您可以視需要變更記錄檔的位置，或避免填滿 OS 磁碟。

```cmd
# For Windows:
set AZCOPY_LOG_LOCATION=<value>
# For Linux:
export AZCOPY_LOG_LOCATION=<value>
# For MacOS
export AZCOPY_LOG_LOCATION=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank, then the default value is currently in use
```
### <a name="change-the-default-log-level"></a>變更預設記錄層級 

根據預設，AzCopy 記錄層級設為 INFO。 如果您想要減少記錄詳細資訊以節省磁碟空間，請使用 ``--log-level`` 選項來覆寫此設定。 可用記錄層級如下：偵錯、 資訊、 警告、 錯誤、 異常和嚴重錯誤。

### <a name="review-the-logs-for-errors"></a>檢閱記錄以了解錯誤

下列命令會從 04dc9ca9-158f-7945-5933-564021086c79 記錄取得狀態為 UPLOADFAILED 的所有錯誤：

```azcopy
cat 04dc9ca9-158f-7945-5933-564021086c79.log | grep -i UPLOADFAILED
```
## <a name="troubleshooting"></a>疑難排解

AzCopy v10 建立記錄檔和每個工作的方案檔。 您可以使用記錄來調查任何可能的問題並進行疑難排解。 記錄會包含失敗狀態 (UPLOADFAILED、COPYFAILED 和 DOWNLOADFAILED)、完整路徑和失敗原因。 作業記錄和方案檔位於 Windows 上的 %USERPROFILE\\.azcopy 資料夾中，或在 Mac 和 Linux 上的 $HOME\\.azcopy 資料夾中。

> [!IMPORTANT]
> 當將要求提交給 Microsoft 支援服務 （或疑難排解問題涉及任何第三方），共用您想要執行此命令的修訂的版本。 這可確保與任何人，不會被不小心共用 SAS。 您可以在記錄檔開頭找到編校的版本。

### <a name="view-and-resume-jobs"></a>檢視和繼續作業

每個傳輸作業都會建立 AzCopy 作業。 若要檢視之作業的記錄，使用下列命令：

```azcopy
.\azcopy jobs list
```

若要檢視作業統計資料，請使用下列命令：

```azcopy
.\azcopy jobs show <job-id>
```

若要依狀態篩選傳輸，請使用下列命令：

```azcopy
.\azcopy jobs show <job-id> --with-status=Failed
```

若要繼續失敗/已取消的工作中使用下列命令。 此命令會使用 SAS 權杖以及其識別碼。 它不是基於安全性理由，持續性：

```azcopy
.\azcopy jobs resume <jobid> --sourcesastokenhere --destinationsastokenhere
```

## <a name="next-steps"></a>後續步驟

如果您有疑問、 問題或一般的意見反應，將它們提交[GitHub 上](https://github.com/Azure/azure-storage-azcopy.)。


