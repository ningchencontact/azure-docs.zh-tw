---
title: Azure 資料箱磁碟限制 | Microsoft Docs
description: 說明 Microsoft Azure 資料箱磁碟的系統限制和建議大小。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 32445e3f6859a6161eb2fae20233c598234f18a0
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2019
ms.locfileid: "58791638"
---
# <a name="azure-data-box-disk-limits"></a>Azure 資料箱磁碟限制


當您部署和操作 Microsoft Azure 資料箱磁碟解決方案時，請考慮這些限制。

## <a name="data-box-service-limits"></a>資料箱服務限制

 - 資料箱服務會在中所列 Azure 地區上市[區域可用性](data-box-disk-overview.md#region-availability)。
 - 資料箱磁碟支援單一儲存體帳戶。

## <a name="data-box-disk-performance"></a>資料箱磁碟效能

使用透過 USB 3.0 連線的磁碟進行測試時，磁碟效能可達 430 MB/s。 實際數字會隨使用的檔案大小而不同。 如果檔案較小，您可能會看到較低的效能。

## <a name="azure-storage-limits"></a>Azure 儲存體限制

本節說明 Azure 儲存體服務的限制，以及適用於資料箱服務的 Azure 檔案、Azure 區塊 Blob 和 Azure 分頁 Blob 所需的命名慣例。 請仔細檢閱儲存體限制，並遵循所有的建議。

如需與 Azure 儲存體服務限制以及命名共用、容器和檔案的最佳作法有關的最新資訊，請移至：

- [命名和參考容器](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [命名和參考共用](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [區塊 Blob 和分頁 Blob 慣例](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> 如果有任何檔案或目錄超出 Azure 儲存體服務限制，或不符合 Azure 檔案/Blob 命名慣例，則不會透過資料箱服務將這些檔案或目錄擷取到 Azure 儲存體中。

## <a name="data-upload-caveats"></a>資料上傳注意事項

- 請勿直接將資料複製到磁碟中。 请将数据复制到预先创建的 *BlockBlob*、*PageBlob* 和 *AzureFile* 文件夹中。
- *BlockBlob* 和 *PageBlob* 下的資料夾是容器。 例如，容器會建立為 *BlockBlob/container* 和 *PageBlob/container*。
- 如果云中存在与正在复制的对象同名的现有 Azure 对象（例如 Blob），Data Box Disk 会在云中将文件重命名为 file(1)。
- 寫入 *BlockBlob* 和 *PageBlob* 共用中的每個檔案，分別會以區塊 Blob 和分頁 Blob 的形式上傳。
- 在 *BlockBlob* 和 *PageBlob* 資料夾下建立的任何空目錄階層 (不含任何檔案) 則不會上傳。
- 如果將資料上傳至 Azure 時發生任何錯誤，則會在目標儲存體帳戶中建立錯誤記錄。 在上傳完成後，入口網站中會提供此錯誤記錄的路徑，而您可以檢閱記錄以執行更正動作。 若未事先確認已上傳的資料，請勿從來源刪除資料。
- 如果按顺序指定了托管磁盘，请查看以下附加注意事项：

    - 在所有預先建立的資料夾之間以及在所有資料箱磁碟之間，您在一個資源群組中只能有一個具指定名稱的受控磁碟。 這表示上傳至預先建立資料夾的 VHD 應具備唯一名稱。 請確定指定的名稱不會與資源群組中現有的受控磁碟相符。 如果 VHD 具有相同名稱，則只會將一個 VHD 轉換為具該名稱的受控磁碟。 其他 VHD 會以分頁 Blob 形式上傳至暫存的儲存體帳戶。
    - 一律將 VHD 複製到其中一個預先建立的資料夾。 如果您在這些資料夾外部或您所建立的資料夾中複製 VHD，就會將 VHD 上傳至 Azure 儲存體帳戶以作為分頁 Blob，而非受控磁碟。
    - 只能上傳固定的 VHD 來建立受控磁碟。 不支持动态 VHD、差异 VHD 或 VHDX 文件。

## <a name="azure-storage-account-size-limits"></a>Azure 儲存體帳戶大小限制

以下是複製到儲存體帳戶中的資料在大小方面的限制。 請確定您上傳的資料符合這些限制。 如需關於這些限制的最新資訊，請移至 [Azure Blob 儲存體調整目標](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets)和 [Azure 檔案調整目標](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets)。

| 複製到 Azure 儲存體帳戶中的資料大小                      | 預設限制          |
|---------------------------------------------------------------------|------------------------|
| 區塊 Blob 和分頁 Blob                                            | 每一儲存體帳戶 500 TB。 <br> 其中包含來自所有來源 (包括資料箱磁碟) 的資料。|


## <a name="azure-object-size-limits"></a>Azure 物件大小限制

以下是可寫入的 Azure 物件大小。 請確定所有上傳的檔案均符合這些限制。

| Azure 物件類型 | 預設限制                                             |
|-------------------|-----------------------------------------------------------|
| 區塊 Blob        | ~ 4.75 TiB                                                 |
| 分頁 Blob         | 8 TiB <br> （以页 Blob 格式上传的每个文件必须经过 512 字节对齐，否则上传会失败。 <br> VHD 和 VHDX 都已经过 512 字节对齐。） |
|Azure 檔案        | 1 TiB <br> 最大 共享大小为 5 TiB     |
| 受控磁碟     |4 TiB <br> 有关大小和限制的详细信息，请参阅： <li>[托管磁盘的可伸缩性目标](../virtual-machines/windows/disk-scalability-targets.md#managed-virtual-machine-disks)</li>|


## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Azure 區塊 Blob、分頁 Blob 和檔案命名慣例

| 實體                                       | 慣例                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 區塊 Blob 和分頁 Blob 的容器名稱 <br> Azure 文件的文件共享名 | 必須是長度介於 3 到 63 個字元的有效 DNS 名稱。 <br>  必須以字母或數字開頭。 <br> 只能包含小寫字母、數字和連字號 (-)。 <br> 每個連字號 (-) 的前後都必須緊鄰字母或數字。 <br> 名稱中不允許連續的連字號。 |
| Azure 檔案的目錄和檔案名稱     |<li> 保留大小寫、不區分大小寫而且長度不得超過 255 個字元。 </li><li> 不能以正斜線 (/) 結尾。 </li><li>如果有的話，則會自動移除。 </li><li> 不允许使用以下字符：<code>" \\ / : \| < > * ?</code></li><li> 保留的 URL 字元必須正確逸出。 </li><li> 不允許使用不合法的 URL 路徑字元。 之類的字碼指標\\uE000 並不是有效的 Unicode 字元。 某些 ASCII 或 Unicode 字元，像是控制字元 (0x00 到 0x1F \\u0081，等等)，也不允許。 如需在 HTTP/1.1 中控管 Unicode 字串的規則，請參閱 RFC 2616 第 2.2 節：基本規則和 RFC 3987。 </li><li> 不允許下列檔案名稱：LPT1、LPT2、LPT3、LPT4、LPT5、LPT6、LPT7、LPT8、LPT9、COM1、COM2、COM3、COM4、COM5、COM6、COM7、COM8、COM9、PRN、AUX、NUL、CON、CLOCK$、點字元 (.) 和雙點字元 (..)。</li>|
| 區塊 Blob 和分頁 Blob 的 Blob 名稱      | Blob 名稱會區分大小寫，而且可以包含字元的任意組合。 <br> Blob 名稱長度必須介於 1 到 1,024 個字元之間。 <br> 保留的 URL 字元必須正確逸出。 <br>構成 Blob 名稱的路徑區段數目不可超過 254 個。 路徑線段是連續分隔符號字元 (例如，正斜線 '/') 之間的字串，會對應到虛擬目錄的名稱。 |

## <a name="managed-disk-naming-conventions"></a>托管磁盘命名约定

| 實體 | 慣例                                             |
|-------------------|-----------------------------------------------------------|
| 托管磁盘名称       | <li> 名称的长度必须为 1 到 80 个字符。 </li><li> 名称必须以字母或数字开头，以字母、数字或下划线结尾。 </li><li> 名称只能包含字母、数字、下划线、句点和连字符。 </li><li>   名称不可包含空格或 `/`。                                              |

## <a name="next-steps"></a>後續步驟

- 檢閱[資料箱磁碟系統需求](data-box-disk-system-requirements.md)
