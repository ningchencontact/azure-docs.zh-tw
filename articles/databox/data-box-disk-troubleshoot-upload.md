---
title: Azure 資料箱磁碟疑難排解資料上傳的使用記錄檔的問題 |Microsoft Docs
description: 描述如何使用記錄和疑難排解資料上傳至 Azure 資料箱磁碟時看到的問題。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: 6f3ac38c3eac968bd2f7ec2aada435466d3ff279
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148305"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>了解在 Azure 資料箱磁碟中的資料上傳問題進行疑難排解的記錄檔

本文章適用於 Microsoft Azure 資料箱磁碟，並描述當您將資料上傳至 Azure 時，您會看到的問題。

## <a name="data-upload-logs"></a>資料上傳記錄檔

當資料上傳至 Azure 資料中心內，`_error.xml`和`_verbose.xml`產生檔案。 這些記錄檔會上傳至相同的儲存體帳戶用來將資料上傳。 範例`_error.xml`如下所示。
    
```xml
 <?xml version="1.0" encoding="utf-8"?>
    <DriveLog Version="2018-10-01">
      <DriveId>18041C582D7E</DriveId>
      <Summary>
     <!--Summary for validation and data copy to Azure -->
        <ValidationErrors>
          <None Count="8" />
        </ValidationErrors>
        <CopyErrors>
          <Completed Count="5" Description="No errors encountered" />
          <ContainerRenamed Count="3" Description="Renamed the container as the original container name does not follow Azure conventions." />
        </CopyErrors>
      </Summary>
    <!--List of renamed containers with the new names, new file path in Azure, original invalid file path, and size -->
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/private.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\private.vhd</OriginalFilePath>
        <SizeInBytes>10490880</SizeInBytes>
      </Blob>
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/resource.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\resource.vhd</OriginalFilePath>
        <SizeInBytes>71528448</SizeInBytes>
      </Blob>
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/role.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\role.vhd</OriginalFilePath>
        <SizeInBytes>10490880</SizeInBytes>
      </Blob>
      <Status>CompletedWithErrors</Status>
    </DriveLog>
```

## <a name="download-logs"></a>下載記錄

有兩種方式可尋找及下載診斷記錄檔。

- 如果有任何錯誤，將資料上傳至 Azure 時，入口網站會在診斷記錄檔所在的資料夾中顯示的路徑。

    ![連結至入口網站中的記錄檔](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

- 請移至您的資料箱訂單相關聯的儲存體帳戶。 移至 [Blob 服務 > 瀏覽 Blob]  並尋找對應到儲存體帳戶的 Blob。 移至**waies**。

    ![複製記錄 2](./media/data-box-disk-troubleshoot/data-box-disk-copy-logs2.png)

在每個案例中，您會看到錯誤記錄檔和詳細資訊記錄檔。 選取每個記錄檔，然後下載的本機複本。


## <a name="data-upload-errors"></a>資料上傳錯誤

下表摘要說明將資料上傳至 Azure 時，產生的錯誤。

| 錯誤碼 | 描述                        |
|-------------|------------------------------|
|`None` |  已順利完成。           |
|`Renamed` | 已成功重新命名的 blob。  |                                                            |
|`CompletedWithErrors` | 上傳已完成但發生錯誤。 記錄檔中包含之錯誤的詳細資料。  |
|`Corrupted`|資料擷取期間所計算的 CRC 不符合在上傳期間所計算的 CRC。  |  
|`StorageRequestFailed` | Azure 儲存體要求失敗。   |     |
|`LeasePresent` | 此項目租用，且正由另一位使用者。 |
|`StorageRequestForbidden` |無法上傳因驗證問題。 |
|`ManagedDiskCreationTerminalFailure` | 無法上傳為受控磁碟。 檔案可用於暫存儲存體帳戶做為分頁 blob。 您可以手動轉換為受控磁碟的分頁 blob。  |
|`DiskConversionNotStartedTierInfoMissing` | 由於將 VHD 檔案複製預先建立的層資料夾外，不被建立受控的磁碟。 在訂單建立時，檔案會上傳至暫存儲存體帳戶所指定的分頁 blob 的形式。 您可以將它轉換以手動方式為受控磁碟。|
|`InvalidWorkitem` | 無法上傳資料，因為它不符合 Azure 命名，並限制慣例。|
|`InvalidPageBlobUploadAsBlockBlob` | 為具有前置詞的容器中的區塊 blob 上傳`databoxdisk-invalid-pb-`。|
|`InvalidAzureFileUploadAsBlockBlob` | 為具有前置詞的容器中的區塊 blob 上傳`databoxdisk-invalid-af`-。|
|`InvalidManagedDiskUploadAsBlockBlob` | 為具有前置詞的容器中的區塊 blob 上傳`databoxdisk-invalid-md`-。|
|`InvalidManagedDiskUploadAsPageBlob` |為具有前置詞的容器中的分頁 blob 上傳`databoxdisk-invalid-md-`。 |
|`MovedToOverflowShare` |上傳的檔案到新的共用，做為原始的共用大小超過 Azure 大小上限。 新的檔案共用名稱已加上的原始名稱`-2`。   |
|`MovedToDefaultAzureShare` |上傳的檔案，沒有預設的共用資料夾的一部分。 共用名稱開頭為`databox-`。 |
|`ContainerRenamed` |這些檔案的容器不符合 Azure 命名慣例，而且已重新命名。 新的名稱開頭為`databox-`和後置字元的原始名稱的 SHA1 雜湊 |
|`ShareRenamed` |這些檔案共用不符合 Azure 命名慣例，並重新命名。 新的名稱開頭為`databox-`和後置字元的原始名稱的 SHA1 雜湊。 |
|`BlobRenamed` |這些檔案不符合 Azure 命名慣例，並已重新命名。 檢查`BlobPath`欄位的新名稱。 |
|`FileRenamed` |這些檔案不符合 Azure 命名慣例，並已重新命名。 檢查`FileStoragePath`欄位的新名稱。 |
|`DiskRenamed` |這些檔案不符合 Azure 命名慣例，並已重新命名。 檢查`BlobPath`欄位的新名稱。 |


## <a name="next-steps"></a>後續步驟

- [資料箱磁碟相關問題，請開啟支援票證](data-box-disk-contact-microsoft-support.md)。
