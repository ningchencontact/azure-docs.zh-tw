---
title: 複製或移動資料至 Azure 儲存體，使用 AzCopy v10 |Microsoft Docs
description: 您可以使用 AzCopy 命令列公用程式來移動或來回複製資料從 blob、 data lake 和檔案內容。 從本機檔案複製資料到 Azure 儲存體，或在儲存體帳戶內或之間複製資料。 輕鬆地將資料移轉至 Azure 儲存體。
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 04/23/2019
ms.author: normesta
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: b5a13dfd760f0c94343b151c9b4c1148c949e854
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790001"
---
# <a name="transfer-data-with-azcopy-v10"></a>使用 AzCopy v10 轉送資料

AzCopy 是命令列公用程式，從 Microsoft Azure Blob 和檔案儲存體來回複製資料。 AzCopy 會提供重新設計的命令列介面，以及新的架構，可靠的資料傳輸。 使用 AzCopy 可在文件系统与存储帐户之间或者在存储帐户之间复制数据。

## <a name="whats-new-in-azcopy-v10"></a>AzCopy v10 的新增功能

- 将文件系统同步到 Azure Blob 存储，或反之。 使用 `azcopy sync <source> <destination>`。 適用於增量複製案例。
- 支援 Azure Data Lake Storage Gen2 API。 使用`myaccount.dfs.core.windows.net`為呼叫 Data Lake 儲存體 Gen2 Api 的 URI。
- 支援將整個帳戶 (僅限 Blob 服務) 複製到另一個帳戶。
- 複製資料從 Amazon Web Services S3 貯體的支援。
- 使用新的[从 URL 放置块](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API 来支持帐户到帐户的复制。 无需向客户端传输数据，因此数据传输速度更快。
- 列出或删除给定路径中的文件和 Blob。
- 支持在路径中使用通配符模式，并支持 --exclude 标志。
- 使用每个 AzCopy 实例创建作业顺序和相关的日志文件。 可以查看和重启以前的作业，以及恢复失败的作业。 AzCopy 也會在失敗之後自動重試傳輸作業。
- 提供常规性能改进。

## <a name="download-and-install-azcopy"></a>下載並安裝 AzCopy

### <a name="latest-production-version-v10"></a>最新生產版本 (v10)

下載最新版 AzCopy:
- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

### <a name="latest-azcopy-supporting-table-storage-service-v73"></a>最新的 AzCopy 支援資料表儲存體服務 (v7.3)

下載[支援在 Microsoft Azure 資料表儲存體服務中來回複製資料的 AzCopy v7.3](https://aka.ms/downloadazcopynet)。

## <a name="post-installation-steps"></a>後續安裝步驟

AzCopy 不需要安裝。 只需打开首选的命令行应用程序，并浏览到 `azcopy.exe` 所在的文件夹。 如果需要，可将 AzCopy 文件夹位置添加到系统路径以方便使用。

## <a name="authentication-options"></a>驗證選項

使用 Azure 儲存體進行驗證時，AzCopy 會支援下列選項：
- **Azure Active Directory** (支援**Blob 和 Data Lake 儲存體 Gen2 服務**)。 使用```.\azcopy login```使用 Azure Active Directory 登入。  使用者應該擁有[「 儲存體 Blob 資料參與者 」 角色指派](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)寫入 Blob 儲存體與 Azure Active Directory 驗證。 針對透過 Azure 資源管理的身分識別驗證，使用`azcopy login --identity`。
- **共享访问签名令牌 [支持 Blob 和文件服务]**。 在命令行中将共享访问签名 (SAS) 令牌追加到 Blob 路径即可使用该令牌。 可以使用 Azure 门户、[存储资源管理器](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/)、[PowerShell](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageblobsastoken) 或所选的其他工具生成 SAS 令牌。 如需詳細資訊，請參閱[範例](https://docs.microsoft.com/azure/storage/blobs/common/storage-dotnet-shared-access-signature-part-1)。

## <a name="getting-started"></a>開始使用

> [!TIP]
> **偏好圖形化使用者介面？**
>
> 在可以简化 Azure 存储数据管理的桌面客户端 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)中，可以使用 AzCopy 来加速与 Azure 存储之间进行的数据传输。
>
> 在存储资源管理器中的“预览”菜单下启用 AzCopy。
> ![在 Azure 存储资源管理器中启用 AzCopy 作为传输引擎](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

AzCopy v10 語法自行記錄。 當您已登入 Azure Active Directory 時，一般的語法看起來如下所示：

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>

# Examples if you have logged into the Azure Active Directory:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile"
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/container"

# Examples if you're using SAS tokens to authenticate:
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container?st=2019-04-05T04%3A10%3A00Z&se=2019-04-13T04%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=Qdihej%2Bsbg4AiuyLVyQZklm9pSuVGzX27qJ508wi6Es%3D" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile?st=2019-04-05T04%3A10%3A00Z&se=2019-04-13T04%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=Qdihej%2Bsbg4AiuyLVyQZklm9pSuVGzX27qJ508wi6Es%3D"
```

用來取得可用命令清單的方法如下：

```azcopy
.\azcopy --help
# To use the alias instead
.\azcopy -h
```

若要查看特定命令的帮助页和示例，请运行以下命令：

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

使用 copy 命令將資料從來源傳輸到目的地。 源或目标可以是：
- 本機檔案系統
- Azure Blob/虛擬目錄/容器 URI
- Azure 檔案/目錄/檔案共用 URI
- Azure Data Lake Storage Gen2 檔案系統/目錄/檔案 URI

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using the alias instead 
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

下列命令會將資料夾下的所有檔案上都傳`C:\local\path`容器的遞迴`mycontainer1`建立`path`目錄中的容器。 當`--put-md5`會提供旗標，AzCopy 會計算並儲存在每個檔案的 md5 雜湊`Content-md5`屬性對應的 blob，以供稍後使用。

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true --put-md5
```

下列命令會將資料夾 `C:\local\path` 下的所有檔案 (不會遞迴至子目錄) 上傳到容器 `mycontainer1`：

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --put-md5
```

若要查找更多示例，请使用以下命令：

```azcopy
.\azcopy cp -h
```

## <a name="copy-blob-data-between-two-storage-accounts"></a>複製兩個儲存體帳戶之間的 Blob 資料

在两个存储帐户之间复制数据使用的是[从 URL 放置块](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API，而不使用客户端计算机的网络带宽。 直接在两个 Azure 存储服务器之间复制数据，而 AzCopy 只是协调复制操作。 此选项目前仅适用于 Blob 存储。

若要複製所有的 Blob 資料，兩個儲存體帳戶之間，使用下列命令：
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/<sastoken>" "https://myotheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

若要將 Blob 容器複製到另一個 Blob 容器中，使用下列命令：
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/mycontainer/<sastoken>" "https://myotheraccount.blob.core.windows.net/mycontainer/<sastoken>" --recursive=true
```

## <a name="copy-a-vhd-image-to-a-storage-account"></a>將 VHD 映像複製到儲存體帳戶

AzCopy 依預設會將資料上傳至區塊 blob。 若要上傳檔案，因為附加 Blob 或分頁 Blob 使用旗標`--blob-type=[BlockBlob|PageBlob|AppendBlob]`。

```azcopy
.\azcopy cp "C:\local\path\mydisk.vhd" "https://myotheraccount.blob.core.windows.net/mycontainer/mydisk.vhd<sastoken>" --blob-type=PageBlob
```

## <a name="sync-incremental-copy-and-delete-blob-storage-only"></a>同步處理：累加複製和刪除 (僅限 Blob 儲存體)

sync 命令通过比较文件名和上次修改时间戳将源目录的内容同步到目标中的目录。 在提供 `--delete-destination=prompt|true` 标志的情况下，可选择性地通过此操作删除目标文件（如果这些文件在源中不存在）。 默认会禁用删除行为。 

> [!NOTE] 
> 请谨慎使用 `--delete-destination` 标志。 在同步操作中启用删除行为之前，请启用[软删除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)功能，防止帐户中发生意外删除。 
>
> 将 `--delete-destination` 设置为 true 时，AzCopy 会在不提示用户的情况下，从目标中删除源中不存在的文件。 如果希望系统提示确认，请使用 `--delete-destination=prompt`。

若要將本機檔案系統同步處理到儲存體帳戶，請使用下列命令：

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

还可将 Blob 容器同步到本地文件系统：

```azcopy
# The SAS token isn't required for Azure Active Directory authentication.
.\azcopy sync "https://account.blob.core.windows.net/mycontainer1" "C:\local\path" --recursive=true
```

此命令根据上次修改时间戳以增量方式将源同步到目标。 如果您新增或刪除來源中的檔案，AzCopy 會採用相同的目的地。 在删除之前，AzCopy 会提示确认。

## <a name="copy-data-from-amazon-web-services-aws-s3"></a>從 Amazon Web Services (AWS) S3 複製資料

若要使用 AWS S3 貯體進行驗證，設定下列環境變數：

```
# For Windows:
set AWS_ACCESS_KEY_ID=<your AWS access key>
set AWS_SECRET_ACCESS_KEY=<AWS secret access key>
# For Linux:
export AWS_ACCESS_KEY_ID=<your AWS access key>
export AWS_SECRET_ACCESS_KEY=<AWS secret access key>
# For MacOS
export AWS_ACCESS_KEY_ID=<your AWS access key>
export AWS_SECRET_ACCESS_KEY=<AWS secret access key>
```

若要複製到 Blob 容器的貯體，請發出下列命令：

```
.\azcopy cp "https://s3.amazonaws.com/mybucket" "https://myaccount.blob.core.windows.net/mycontainer?<sastoken>" --recursive
```

如需複製使用 AzCopy，將資料從 AWS S3 的詳細資訊，請參閱頁面[此處](https://github.com/Azure/azure-storage-azcopy/wiki/Copy-from-AWS-S3)。

## <a name="advanced-configuration"></a>進階設定

### <a name="configure-proxy-settings"></a>進行 Proxy 設定

若要配置 AzCopy v10 的代理设置，请使用以下命令设置环境变量 https_proxy：

```cmd
# For Windows:
set https_proxy=<proxy IP>:<proxy port>
# For Linux:
export https_proxy=<proxy IP>:<proxy port>
# For MacOS
export https_proxy=<proxy IP>:<proxy port>
```

### <a name="optimize-throughput"></a>輸送量最佳化

设置环境变量 AZCOPY_CONCURRENCY_VALUE，以配置并发请求数并控制吞吐量性能和资源消耗。 依預設，此值會設定為 300。 減少此值便可限制 AzCopy v10 所使用的頻寬和 CPU。

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

AzCopy 日志级别默认设置为 INFO。 如果您想要減少記錄詳細資訊以節省磁碟空間，請使用 ``--log-level`` 選項來覆寫此設定。 可用記錄層級如下：DEBUG、INFO、WARNING、ERROR、PANIC 和 FATAL。

### <a name="review-the-logs-for-errors"></a>檢閱記錄以了解錯誤

下列命令會從 04dc9ca9-158f-7945-5933-564021086c79 記錄取得狀態為 UPLOADFAILED 的所有錯誤：

```azcopy
cat 04dc9ca9-158f-7945-5933-564021086c79.log | grep -i UPLOADFAILED
```
## <a name="troubleshooting"></a>疑難排解

AzCopy 會建立記錄檔和每個工作的方案檔。 您可以使用記錄來調查任何可能的問題並進行疑難排解。 記錄會包含失敗狀態 (UPLOADFAILED、COPYFAILED 和 DOWNLOADFAILED)、完整路徑和失敗原因。 作業記錄和方案檔位於 Windows 上的 %USERPROFILE\\.azcopy 資料夾中，或在 Mac 和 Linux 上的 $HOME\\.azcopy 資料夾中。

> [!IMPORTANT]
> 當將要求提交給 Microsoft 支援服務 （或疑難排解問題涉及任何第三方），共用您想要執行此命令的修訂的版本。 这可以确保不会意外地与任何人共享 SAS。 您可以在記錄檔開頭找到編校的版本。

### <a name="view-and-resume-jobs"></a>檢視和繼續作業

每個傳輸作業都會建立 AzCopy 作業。 使用以下命令查看作业的历史记录：

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

使用以下命令恢复失败/取消的作业。 此命令會使用 SAS 權杖以及其識別項，並不持續基於安全性理由：

```azcopy
.\azcopy jobs resume <jobid> --source-sas="<sastokenhere>"
.\azcopy jobs resume <jobid> --destination-sas="<sastokenhere>"
```

## <a name="next-steps"></a>後續步驟

如有任何疑问、问题或一般反馈，请通过 [GitHub](https://github.com/Azure/azure-storage-azcopy) 提交。


