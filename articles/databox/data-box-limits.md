---
title: Azure 資料箱限制 | Microsoft Docs
description: 描述 Microsoft Azure 資料箱元件和連線的系統限制和建議大小。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 02/22/2019
ms.author: alkohli
ms.openlocfilehash: 147cf61dcd36edc75a936cf9b467fd89c8d8a965
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082818"
---
# <a name="azure-data-box-limits"></a>Azure 資料箱限制

當您部署和操作 Microsoft Azure 資料箱時，請考慮這些限制。 下表描述資料箱的這些限制。


## <a name="data-box-service-limits"></a>資料箱服務限制

 - 如果通过 Data Box 服务使用多个存储帐户，则所有存储帐户应属于同一 Azure 区域。
 - 我們建議您使用三個以下的儲存體帳戶。 使用更多存储帐户可能影响性能。

## <a name="data-box-limits"></a>資料箱限制

- 資料箱最多可以儲存 5 億個檔案。

## <a name="azure-storage-limits"></a>Azure 儲存體限制

本節說明 Azure 儲存體服務的限制，以及適用於資料箱服務的 Azure 檔案、Azure 區塊 Blob 和 Azure 分頁 Blob 所需的命名慣例。 請仔細檢閱儲存體限制，並遵循所有的建議。

如需與 Azure 儲存體服務限制以及命名共用、容器和檔案的最佳作法有關的最新資訊，請移至：

- [命名和參考容器](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [命名和參考共用](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [區塊 Blob 和分頁 Blob 慣例](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> 如果有任何文件或目录超出了 Azure 存储服务限制，或者不符合 Azure 文件/Blob 命名约定，则这些文件或目录不会通过 Data Box 服务引入到 Azure 存储中。

## <a name="data-upload-caveats"></a>資料上傳注意事項

- 不要直接将文件复制到任何预创建的共享。 需要在共享下创建文件夹，然后将文件复制到该文件夹。
- StorageAccount_BlockBlob 和 StorageAccount_PageBlob 底下的資料夾是容器。 例如，容器會建立為 StorageAccount_BlockBlob/container 和 StorageAccount_PageBlob/container。
- 直接在 StorageAccount_AzureFiles 底下建立的每個資料夾會轉譯成 Azure 檔案共用。
- 如果雲端中現有的 Azure 物件 (例如 Blob 或檔案) 與要複製的物件同名，資料箱將會覆寫雲端中的檔案。
- 寫入 StorageAccount_BlockBlob 和 StorageAccount_PageBlob 共用中的每個檔案，分別會以區塊 Blob 和分頁 Blob 的形式上傳。
- Azure Blob 存储不支持目录。 如果您在 StorageAccount_BlockBlob 資料夾底下建立資料夾，則會以 Blob 名稱建立虛擬資料夾。 對於 Azure 檔案服務，則會維持實際的目錄結構。
- 在 StorageAccount_BlockBlob 和 StorageAccount_PageBlob 文件夹下创建的任何空目录层次结构（没有任何文件）都不会上传。
- 如果將資料上傳至 Azure 時發生任何錯誤，則會在目標儲存體帳戶中建立錯誤記錄。 在上傳完成後，系統會提供此錯誤記錄的路徑，而您可以檢閱記錄以執行更正動作。 在验证上传的数据之前，不要删除源中的数据。

## <a name="azure-storage-account-size-limits"></a>Azure 儲存體帳戶大小限制

以下是複製到儲存體帳戶中的資料在大小方面的限制。 請確定您上傳的資料符合這些限制。 如需關於這些限制的最新資訊，請移至 [Azure Blob 儲存體調整目標](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets)和 [Azure 檔案調整目標](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets)。

| 複製到 Azure 儲存體帳戶中的資料大小                      | 預設限制          |
|---------------------------------------------------------------------|------------------------|
| 區塊 Blob 和分頁 Blob                                            | 每一儲存體帳戶 500 TiB。 <br> 其中包含來自所有來源 (包括資料箱) 的資料。|
| Azure 檔案                                                          | 每個共用 5 TiB。<br> StorageAccount_AzureFiles 底下的所有資料夾必須遵循此限制。       |

## <a name="azure-object-size-limits"></a>Azure 物件大小限制

以下是可寫入的 Azure 物件大小。 請確定所有上傳的檔案均符合這些限制。

| Azure 物件類型 | 預設限制                                             |
|-------------------|-----------------------------------------------------------|
| 區塊 Blob        | ~ 4.75 TiB                                                 |
| 分頁 Blob         | 8 TiB <br> 以分頁 Blob 格式上傳的每個檔案都必須是 512 位元組規格 (整數倍數)，否則上傳會失敗。 <br> VHD 和 VHDX 為 512 位元組規格。 |
| Azure 檔案        | 1 TiB                                                      |
| 受控磁碟     | 4 TiB <br> 有关大小和限制的详细信息，请参阅： <li>[标准 SSD 的可伸缩性目标](../virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[高级 SSD 的可伸缩性目标](../virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[标准 HDD 的可伸缩性目标](../virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[托管磁盘的定价和计费](../virtual-machines/windows/disks-types.md#billing)</li>                                                     |

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Azure 區塊 Blob、分頁 Blob 和檔案命名慣例

| 實體                                       | 慣例                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 區塊 Blob 和分頁 Blob 的容器名稱 | 必須是長度介於 3 到 63 個字元的有效 DNS 名稱。 <br>  必須以字母或數字開頭。 <br> 只能包含小寫字母、數字和連字號 (-)。 <br> 每個連字號 (-) 的前後都必須緊鄰字母或數字。 <br> 名称中不允许连续的连字符。 |
| Azure 檔案的共用名稱                  | 同上                                                                                                                                                                                                                                                                                                             |
| Azure 檔案的目錄和檔案名稱     |<li> 保留大小寫、不區分大小寫而且長度不得超過 255 個字元。 </li><li> 不能以正斜線 (/) 結尾。 </li><li>如果有的話，則會自動移除。 </li><li> 不允许使用以下字符：<code>" \\ / : \| < > * ?</code></li><li> 保留的 URL 字元必須正確逸出。 </li><li> 不允许使用非法的 URL 路径字符。 之類的字碼指標\\uE000 不是有效的 Unicode 字元。 某些 ASCII 或 Unicode 字元，像是控制字元 (0x00 到 0x1F \\u0081，等等)，也不允許。 如需在 HTTP/1.1 中控管 Unicode 字串的規則，請參閱 RFC 2616 第 2.2 節：基本規則和 RFC 3987。 </li><li> 不允许使用以下文件名：LPT1、LPT2、LPT3、LPT4、LPT5、LPT6、LPT7、LPT8、LPT9、COM1、COM2、COM3、COM4、COM5、COM6、COM7、COM8、COM9、PRN、AUX、NUL、CON、CLOCK$、點字元 (.) 和雙點字元 (..)。</li>|
| 區塊 Blob 和分頁 Blob 的 Blob 名稱      | </li><li>Blob 名稱會區分大小寫，而且可以包含字元的任意組合。 </li><li>Blob 名稱長度必須介於 1 到 1,024 個字元之間。 </li><li>保留的 URL 字元必須正確逸出。 </li><li>構成 Blob 名稱的路徑區段數目不可超過 254 個。 路徑線段是連續分隔符號字元 (例如，正斜線 '/') 之間的字串，會對應到虛擬目錄的名稱。</li> |
