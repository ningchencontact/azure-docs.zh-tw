---
title: Azure 資料箱磁碟疑難排解資料上傳的使用記錄檔的問題 |Microsoft Docs
description: 描述如何使用記錄和疑難排解資料上傳至 Azure 資料箱磁碟時看到的問題。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: deaa9a220ee4d765650779b40742225e300ffdb7
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807514"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>了解在 Azure 資料箱磁碟中的資料上傳問題進行疑難排解的記錄檔

本文章適用於 Microsoft Azure 資料箱磁碟，並描述當您將資料上傳至 Azure 時，您會看到的問題。

## <a name="about-upload-logs"></a>有關上傳記錄檔

當資料上傳至 Azure 資料中心內，`_error.xml`和`_verbose.xml`檔案會產生每個儲存體帳戶。 這些記錄檔會上傳至相同的儲存體帳戶用來將資料上傳。 

這兩個記錄檔的格式相同，而且包含從磁碟中複製資料到 Azure 儲存體帳戶時所發生的事件的 XML 描述。

詳細資訊記錄檔包含每個 blob 或檔案，複製作業的狀態的完整資訊，而錯誤記錄檔包含 blob 或檔案上傳期間發生錯誤的資訊。

錯誤記錄檔具有相同的結構的詳細資訊記錄，但會篩選出成功的作業。

## <a name="download-logs"></a>下載記錄

採取下列步驟來找出上傳記錄檔。

1. 如果有任何錯誤，將資料上傳至 Azure 時，入口網站會在診斷記錄檔所在的資料夾中顯示的路徑。

    ![連結至入口網站中的記錄檔](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. 移至**waies**。

    ![錯誤和詳細資訊記錄檔](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

在每個案例中，您會看到錯誤記錄檔和詳細資訊記錄檔。 選取每個記錄檔，然後下載的本機複本。

## <a name="sample-upload-logs"></a>範例上傳記錄檔

範例`_verbose.xml`如下所示。 在此情況下，順序已順利完成，未發生任何錯誤。

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Blob Status="Completed">
    <BlobPath>$root/botetapageblob.vhd</BlobPath>
    <FilePath>\PageBlob\botetapageblob.vhd</FilePath>
    <Length>1073742336</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <PageRangeList>
      <PageRange Offset="0" Length="4194304" Status="Completed" />
      <PageRange Offset="4194304" Length="4194304" Status="Completed" />
      <PageRange Offset="8388608" Length="4194304" Status="Completed" />
      --------CUT-------------------------------------------------------
      <PageRange Offset="1061158912" Length="4194304" Status="Completed" />
      <PageRange Offset="1065353216" Length="4194304" Status="Completed" />
      <PageRange Offset="1069547520" Length="4194304" Status="Completed" />
      <PageRange Offset="1073741824" Length="512" Status="Completed" />
    </PageRangeList>
  </Blob>
  <Blob Status="Completed">
    <BlobPath>$root/botetablockblob.txt</BlobPath>
    <FilePath>\BlockBlob\botetablockblob.txt</FilePath>
    <Length>19</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <BlockList>
      <Block Offset="0" Length="19" Status="Completed" />
    </BlockList>
  </Blob>
  <File Status="Completed">
    <FileStoragePath>botetaazurefilesfolder/botetaazurefiles.txt</FileStoragePath>
    <FilePath>\AzureFile\botetaazurefilesfolder\botetaazurefiles.txt</FilePath>
    <Length>20</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <FileRangeList>
      <FileRange Offset="0" Length="20" Status="Completed" />
    </FileRangeList>
  </File>
  <Status>Completed</Status>
</DriveLog>
```

針對相同的順序的範例`_error.xml`如下所示。

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Summary>
    <ValidationErrors>
      <None Count="3" />
    </ValidationErrors>
    <CopyErrors>
      <None Count="3" Description="No errors encountered" />
    </CopyErrors>
  </Summary>
  <Status>Completed</Status>
</DriveLog>
```

範例`_error.xml`where 如下所示完成但發生錯誤的順序。 

在此情況下有檔案時發生錯誤`Summary`區段，並包含所有檔案的另一個區段層級錯誤。 

`Summary`包含`ValidationErrors`而`CopyErrors`。 在此情況下，8 檔案或資料夾上傳至 Azure，並沒有任何驗證錯誤。 當資料已複製到 Azure 儲存體帳戶時，5 個檔案或資料夾上傳成功。 剩餘的 3 個檔案或資料夾已重新命名為根據的 Azure 容器的命名慣例，然後成功上傳至 Azure。

檔案層級狀態位於`BlobStatus`，描述任何上傳的 blob 所採取的動作。 在此情況下，三個容器會重新命名，因為資料已複製的資料夾不符合適用於容器的 Azure 命名慣例。 這些容器中上傳的 blob，新的容器名稱、 在 Azure 中的 blob 的路徑、 原始的無效的檔案路徑和 blob 大小的。
    
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

## <a name="data-upload-errors"></a>資料上傳錯誤

下表摘要說明將資料上傳至 Azure 時，產生的錯誤。

| 錯誤碼 | 描述                   |
|-------------|------------------------------|
|`None` |  已順利完成。           |
|`Renamed` | 已成功重新命名的 blob。   |
|`CompletedWithErrors` | 上傳已完成但發生錯誤。 記錄檔中包含之錯誤的詳細資料。  |
|`Corrupted`|資料擷取期間所計算的 CRC 不符合在上傳期間所計算的 CRC。  |  
|`StorageRequestFailed` | Azure 儲存體要求失敗。   |     
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
