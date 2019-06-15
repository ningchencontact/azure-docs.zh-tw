---
title: 使用將資料傳送至 Azure 儲存體從 Amazon S3 貯體 AzCopy v10 |Microsoft Docs
description: 使用 AzCopy 和 Amazon S3 貯體傳輸資料
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 04/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 414573a90e5a7b7de845b65e6e88715a26024f10
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67125778"
---
# <a name="copy-data-from-amazon-s3-buckets-by-using-azcopy"></a>將資料從 Amazon S3 貯體，使用 AzCopy 複製

AzCopy 是命令列公用程式可供您儲存體帳戶來回複製 blob 或檔案。 這篇文章可協助您的物件、 目錄和貯體將從 Amazon Web Services (AWS) S3 複製到 Azure blob 儲存體使用 AzCopy。

## <a name="choose-how-youll-provide-authorization-credentials"></a>選擇您要如何提供授權認證

* 若要授權使用 Azure 儲存體，請使用 Azure Active Directory (AD) 或共用存取簽章 (SAS) 權杖。

* 若要授權與 AWS S3，請使用 AWS 存取金鑰和祕密存取金鑰。

### <a name="authorize-with-azure-storage"></a>授權與 Azure 儲存體

請參閱[開始使用 AzCopy](storage-use-azcopy-v10.md)下載 AzCopy 時，發行項，然後選擇 您將會提供儲存體服務的授權認證。

> [!NOTE]
> 這篇文章中的範例假設您已經使用驗證您的身分識別`AzCopy login`命令。 然後，AzCopy 會使用您的 Azure AD 帳戶來授權存取 Blob 儲存體中的資料。
>
> 如果您想使用 SAS 權杖來授權存取 blob 資料，您可以將該權杖附加至每個 AzCopy 命令中的資源 URL。
>
> 例如： `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>` 。

### <a name="authorize-with-aws-s3"></a>授權與 AWS S3

收集您的 AWS 存取金鑰和祕密存取金鑰，然後再設定這些環境變數：

| 作業系統 | 命令  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **MacOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>複製物件、 目錄和貯體

AzCopy 會使用[從 URL 將區塊](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url)API，以便直接在 AWS S3 與存放區伺服器之間複製資料。 這些複製作業不會使用您電腦的網路頻寬。

> [!IMPORTANT]
> 此功能目前為預覽狀態。 如果您決定要移除您的 S3 貯體中的資料，複製作業之後，請務必確認資料已正確複製到您的儲存體帳戶之前先移除資料。

### <a name="copy-an-object"></a>複製物件

|    |     |
|--------|-----------|
| **語法** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>/<object-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>"` |
| **範例** | `azcopy cp "https://s3.amazonaws.com/mybucket/myobject" "https://mystorageaccount.blob.core.windows.net/mycontainer/myblob"` |

> [!NOTE]
> 針對 AWS S3 貯體，這篇文章中的範例會使用路徑式 Url (例如： `http://s3.amazonaws.com/<bucket-name>`)。 
>
> 您也可以使用虛擬裝載式 Url (例如： `http://bucket.s3.amazonaws.com`)。 
>
> 若要深入了解虛擬裝載的貯體，請參閱 [虛擬裝載的貯體]] (https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html) 。

### <a name="copy-a-directory"></a>複製目錄

|    |     |
|--------|-----------|
| **語法** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>/<directory-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>" --recursive=true` |
| **範例** | `azcopy cp "https://s3.amazonaws.com/mybucket/mydirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory" --recursive=true` |

### <a name="copy-a-bucket"></a>複製的貯體

|    |     |
|--------|-----------|
| **語法** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true` |
| **範例** | `azcopy cp "https://s3.amazonaws.com/mybucket" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>在所有區域中複製所有的貯體

|    |     |
|--------|-----------|
| **語法** | `azcopy cp "https://s3.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **範例** | `azcopy cp "https://s3.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>複製所有的值區中特定的 S3 區域

|    |     |
|--------|-----------|
| **語法** | `azcopy cp "https://s3-<region-name>.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **範例** | `azcopy cp "https://s3-rds.eu-north-1.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>處理物件命名規則的差異

AWS S3 有一組不同的命名慣例，相較於 Azure blob 容器的貯體名稱。 您可以閱讀它們[此處](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules)。 如果您選擇的值區的群組複製到 Azure 儲存體帳戶，複製作業可能會失敗，因為命名差異。

AzCopy 處理兩個常見的問題所引起;包含句點及包含連續的連字號的值區的貯體。 AWS S3 貯體名稱可以包含句號和連續的連字號，但無法在 Azure 中的容器。 AzCopy 會以連字號與連續的連字號，以代表連續的連字號的數字的數字取代期間 (例如： 名為的貯體`my----bucket`會變成`my-4-bucket`。 

此外，AzCopy 會複製檔案，它會檢查有命名衝突並嘗試解決這些問題。 比方說，如果有同名的貯體`bucket-name`並`bucket.name`，AzCopy 會解析具名的貯體`bucket.name`至第一個`bucket-name`然後`bucket-name-2`。

## <a name="handle-differences-in-object-metadata"></a>處理物件中繼資料中的差異

AWS S3 和 Azure 可讓不同字元的名稱中的物件索引鍵。 您可以閱讀的是字元，會使用 AWS S3[此處](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys)。 在 Azure 端，blob 物件索引鍵符合的命名規則[C#識別碼](https://docs.microsoft.com/dotnet/csharp/language-reference/)。

做為一部分的 AzCopy`copy`命令時，您可以提供值給選擇性`s2s-invalid-metadata-handle`旗標，指定您希望如何處理其中的檔案中繼資料包含不相容的索引鍵名稱的檔案。 下表描述每個旗標值。

| 旗標值 | 描述  |
|--------|-----------|
| **ExcludeIfInvalid** | （預設選項）中繼資料不包含在傳送的物件。 AzCopy 會記錄警告。 |
| **FailIfInvalid** | 不會複製物件。 AzCopy 會記錄錯誤，並在傳輸在摘要中顯示的失敗計數中包含該錯誤。  |
| **RenameIfInvalid**  | AzCopy 解析無效的中繼資料機碼，並將物件複製到 Azure 中使用解析中繼資料機碼值組。 若要深入了解重新命名物件索引鍵的 AzCopy 所需的步驟完全，請參閱[如何 AzCopy 會將重新命名物件索引鍵](#rename-logic)下一節。 如果無法重新命名的機碼 AzCopy，將不會複製的物件。 |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>AzCopy 如何重新命名物件索引鍵

AzCopy 會執行下列步驟：

1. 取代無效的字元 '_'。

2. 將字串加入`rename_`至新的有效索引鍵的開頭。

   此金鑰會用來儲存原始的中繼資料**值**。

3. 將字串加入`rename_key_`至新的有效索引鍵的開頭。
   此金鑰會用來儲存原始中繼資料無效**金鑰**。
   您可以使用這個索引鍵，嘗試並修復 Azure 端的中繼資料，因為中繼資料機碼已保留為 Blob 儲存體服務中的值。

## <a name="next-steps"></a>後續步驟

在這些文件中找到更多範例：

- [開始使用 AzCopy](storage-use-azcopy-v10.md)

- [使用 AzCopy 和 Blob 儲存體轉送資料](storage-use-azcopy-blobs.md)

- [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)

- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)