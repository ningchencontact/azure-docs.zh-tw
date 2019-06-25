---
title: 針對 Azure 資料箱磁碟進行疑難排解 | Microsoft Docs
description: 說明如何針對在 Azure 資料箱磁碟中察覺的問題進行疑難排解。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: f725f38a335972ae8e0a8b8402a99202caa54a70
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147074"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>使用 Azure 資料箱磁碟中的驗證問題進行疑難排解的記錄檔

這篇文章適用於 Microsoft Azure 資料箱磁碟。 本文說明如何使用記錄來疑難排解您可能看到當您部署此解決方案的驗證問題。

## <a name="validation-tool-log-files"></a>驗證工具記錄檔

當您驗證使用的磁碟上的資料[驗證工具](data-box-disk-deploy-copy-data.md#validate-data)，則*error.xml*產生記錄任何錯誤。 記錄檔位於`Drive:\DataBoxDiskImport\logs`磁碟機的資料夾。 當您執行驗證時，會提供錯誤記錄檔的連結。

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

如果您執行多個工作階段進行驗證，則會產生一個錯誤記錄檔，每個工作階段。

- 以下是範例的錯誤記錄檔時將資料載入`PageBlob`資料夾不是 512-位元組對齊。 傳送到 PageBlob 上傳任何資料必須是 512-位元組對齊，比方說，VHD 或 VHDX。 此檔案中的錯誤位於`<Errors>`中的警告和`<Warnings>`。

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
            <SessionId>session#1</SessionId>
            <ItemType>PageBlob</ItemType>
            <SourceDirectory>D:\Dataset\TestDirectory</SourceDirectory>
            <Errors>
                <Error Code="Not512Aligned">
                    <Description>The file is not 512 bytes aligned.</Description>
                    <List>
                        <File Path="\Practice\myScript.ps1" />
                    </List>
                    <Count>1</Count>
                </Error>
            </Errors>
            <Warnings />
        </ErrorLog>
    ```

- 容器名稱無效時，以下是錯誤記錄檔的範例。 您在下建立資料夾`BlockBlob`， `PageBlob`，或`AzureFile`磁碟上的資料夾會變成您的 Azure 儲存體帳戶中的容器。 容器的名稱必須遵照[Azure 命名慣例](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
          <SessionId>bbsession</SessionId>
          <ItemType>BlockBlob</ItemType>
          <SourceDirectory>E:\BlockBlob</SourceDirectory>
          <Errors>
            <Error Code="InvalidShareContainerFormat">
              <List>
                <Container Name="Azu-reFile" />
                <Container Name="bbcont ainer1" />
              </List>
              <Count>2</Count>
            </Error>
          </Errors>
          <Warnings />
    </ErrorLog>
    ```

## <a name="validation-tool-errors"></a>驗證工具錯誤

中所包含的錯誤*error.xml*與對應的建議的動作會摘要在下列資料表。

| 錯誤碼| 描述                       | 建議動作               |
|------------|--------------------------|-----------------------------------|
| `None` | 已成功驗證資料。 | 不需採取任何動作。 |
| `InvalidXmlCharsInPath` |無法建立資訊清單檔案，因為檔案路徑包含無效的字元。 | 移除這些字元，以繼續。  |
| `OpenFileForReadFailed`| 無法處理檔案。 這可能是因為存取問題或檔案系統損毀。|無法讀取檔案，因為發生錯誤。 錯誤詳細資料會在例外狀況。 |
| `Not512Aligned` | 這個檔案不是有效的格式，PageBlob 資料夾。| 只有上傳資料是 512 位元組對齊`PageBlob`資料夾。 移除 PageBlob 資料夾中的檔案，或將它移到 BlockBlob 資料夾。 重試驗證。|
| `InvalidBlobPath` | 檔案路徑不會對應至在雲端根據命名慣例的 Azure Blob 中的有效 blob 路徑。|請遵循 Azure 命名指導方針，若要重新命名的檔案路徑。 |
| `EnumerationError` | 無法列舉驗證的檔案。 |可能有多個原因導致此錯誤。 最可能的原因是檔案的存取權。 |
| `ShareSizeExceeded` | 此檔案會導致 Azure 檔案共用大小超過 Azure 的 5 TB 的限制。|減少的共用中的資料大小，使其符合[Azure 物件大小限制](data-box-disk-limits.md#azure-object-size-limits)。 重試驗證。 |
| `AzureFileSizeExceeded` | 檔案大小超過 Azure 檔案大小限制。| 減少檔案或資料的大小，使其符合[Azure 物件大小限制](data-box-disk-limits.md#azure-object-size-limits)。 重試驗證。|
| `BlockBlobSizeExceeded` | 檔案大小超過 Azure 區塊 Blob 大小限制。 | 減少檔案或資料的大小，使其符合[Azure 物件大小限制](data-box-disk-limits.md#azure-object-size-limits)。 重試驗證。 |
| `ManagedDiskSizeExceeded` | 檔案大小超過 Azure 受控磁碟大小限制。 | 減少檔案或資料的大小，使其符合[Azure 物件大小限制](data-box-disk-limits.md#azure-object-size-limits)。 重試驗證。 |
| `PageBlobSizeExceeded` | 檔案大小超過 Azure 受控磁碟大小限制。 | 減少檔案或資料的大小，使其符合[Azure 物件大小限制](data-box-disk-limits.md#azure-object-size-limits)。 重試驗證。 |
| `InvalidShareContainerFormat`  |目錄名稱不符合容器或共用的 Azure 命名慣例。         |建立在磁碟上預先存在的資料夾底下的第一個資料夾會變成您的儲存體帳戶中的容器。 此共用或容器名稱不符合 Azure 命名慣例。 重新命名檔案，使其符合[Azure 命名慣例](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。 重試驗證。   |
| `InvalidBlobNameFormat` | 檔案路徑不會對應至在雲端根據命名慣例的 Azure Blob 中的有效 blob 路徑。|重新命名檔案，使其符合[Azure 命名慣例](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。 重試驗證。 |
| `InvalidFileNameFormat` | 檔案路徑不會對應至有效的檔案路徑，在雲端根據命名慣例的 Azure 檔案中。 |重新命名檔案，使其符合[Azure 命名慣例](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。 重試驗證。 |
| `InvalidDiskNameFormat` | 檔案路徑不會對應至 Azure 受控磁碟的命名慣例根據雲端中有效的磁碟名稱。 |重新命名檔案，使其符合[Azure 命名慣例](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。 重試驗證。       |
| `NotPartOfFileShare` | 無法上傳的上傳路徑不是有效的檔案。 檔案上傳至 Azure 檔案服務中的資料夾。   | 移除錯誤中的檔案，並將這些檔案上傳至預先建立的資料夾。 重試驗證。 |
| `NonVhdFileNotSupportedForManagedDisk` | 無法為受控磁碟上傳非 VHD 檔案。 |請移除-VHD 檔案，因為不支援這些。 重試驗證。 |


## <a name="next-steps"></a>後續步驟

- 針對進行疑難排解[資料上傳錯誤](data-box-disk-troubleshoot-upload.md)。
