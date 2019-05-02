---
title: 針對您的 Azure 資料箱上的問題進行疑難排解 |Microsoft Docs
description: 描述如何將資料上傳至 Azure 時看到 Azure 資料箱的問題進行疑難排解。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 04/26/2019
ms.author: alkohli
ms.openlocfilehash: 329ea6ac52e972c42e0e2aab247a7ad8e04c76e7
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927353"
---
# <a name="troubleshoot-issues-related-to-azure-data-box"></a>針對 Azure 資料箱的相關問題進行疑難排解

本文詳細說明使用 Azure Data Box 時，可能會看到有關如何針對問題進行疑難排解資訊。

## <a name="errors-during-data-copy"></a>資料複製期間發生的錯誤

下列各節摘要說明資料在複製期間發現的所有錯誤。

### <a name="errorcontainerorsharenamelength"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH 

**錯誤說明：** 容器或共用名稱必須介於 3 到 63 個字元之間。

**建議的解決方式：** 您要複製資料的資料方塊 (SMB/NFS) 共用下的資料夾會變成您的儲存體帳戶中的 Azure 容器。 

- 在 **連線 和 複製**頁面資料箱本機 web UI、 下載及檢閱錯誤檔來找出資料夾名稱的問題。
- 變更資料 方塊中共用，並確定下的資料夾名稱：

    - 名稱必須介於 3 到 63 個字元之間。
    - 名稱只能包含字母、 數字和連字號。
    - 名稱不能啟動，或連字號結尾。
    - 名稱不能有連續的連字號。
    - 有效名稱的範例： `my-folder-1`， `my-really-extra-long-folder-111`</li><li>
    - 不是有效的名稱的範例： `my-folder_1`， `my`， `--myfolder`， `myfolder--`， `myfolder!`

    如需詳細資訊，請參閱 Azure 命名慣例[容器名稱](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names)並[共用名稱](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)。

### <a name="errorcontainerorsharenamelength"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**錯誤說明：** 容器或共用名稱必須介於 3 到 63 個字元之間。 

**建議的解決方式：** 您要複製資料的資料方塊 (SMB/NFS) 共用下的資料夾會變成您的儲存體帳戶中的 Azure 容器。 

- 在 **連線 和 複製**頁面資料箱本機 web UI、 下載及檢閱錯誤檔來找出資料夾名稱的問題。
- 變更資料 方塊中共用，並確定下的資料夾名稱：

    - 名稱必須介於 3 到 63 個字元之間。
    - 名稱只能包含字母、 數字和連字號。
    - 名稱不能啟動，或連字號結尾。
    - 名稱不能有連續的連字號。
    - 有效名稱的範例： `my-folder-1`， `my-really-extra-long-folder-111`
    - 不是有效的名稱的範例： `my-folder_1`， `my`， `--myfolder`， `myfolder--`， `myfolder!`

    如需詳細資訊，請參閱 Azure 命名慣例[容器名稱](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names)並[共用名稱](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)。


### <a name="errorcontainerorsharenamealphanumericdash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**錯誤說明：** 容器或共用名稱只能包含字母、數字或連字號。

**建議的解決方式：** 您要複製資料的資料方塊 (SMB/NFS) 共用下的資料夾會變成您的儲存體帳戶中的 Azure 容器。 

- 在 **連線 和 複製**頁面資料箱本機 web UI、 下載及檢閱錯誤檔來找出資料夾名稱的問題。
- 變更資料 方塊中共用，並確定下的資料夾名稱：

    - 名稱必須介於 3 到 63 個字元之間。
    - 名稱只能包含字母、 數字和連字號。
    - 名稱不能啟動，或連字號結尾。
    - 名稱不能有連續的連字號。
    - 有效名稱的範例： `my-folder-1`， `my-really-extra-long-folder-111`
    - 不是有效的名稱的範例： `my-folder_1`， `my`， `--myfolder`， `myfolder--`， `myfolder!`

    如需詳細資訊，請參閱 Azure 命名慣例[容器名稱](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names)並[共用名稱](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)。

### <a name="errorcontainerorsharenameimproperdash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**錯誤說明：** 無法啟動的容器名稱和共用名稱，或以連字號結尾，而且不能有連續的連字號。

**建議的解決方式：** 您要複製資料的資料方塊 (SMB/NFS) 共用下的資料夾會變成您的儲存體帳戶中的 Azure 容器。 

- 在 **連線 和 複製**頁面資料箱本機 web UI、 下載及檢閱錯誤檔來找出資料夾名稱的問題。
- 變更資料 方塊中共用，並確定下的資料夾名稱：

    - 名稱必須介於 3 到 63 個字元之間。
    - 名稱只能包含字母、 數字和連字號。
    - 名稱不能啟動，或連字號結尾。
    - 名稱不能有連續的連字號。
    - 有效名稱的範例： `my-folder-1`， `my-really-extra-long-folder-111`
    - 不是有效的名稱的範例： `my-folder_1`， `my`， `--myfolder`， `myfolder--`， `myfolder!`

    如需詳細資訊，請參閱 Azure 命名慣例[容器名稱](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names)並[共用名稱](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)。

### <a name="errorcontainerorsharenamedisallowedfortype"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**錯誤說明：** 受控的磁碟共用指定的不正確的容器名稱。

**建議的解決方式：** 受控磁碟，每個在共用內，會對應到您的儲存體帳戶中的容器建立下列資料夾：進階 SSD、 標準的 HDD 和 SSD 標準。 這些資料夾會對應至受控磁碟的效能層級。

- 請確定您將您的分頁 blob 資料 (Vhd) 複製到其中一個現有的資料夾。 只有這些現有的容器中的資料上傳至 Azure。
- 在進階 SSD、 標準的 HDD 和 SSD 標準相同層級建立的任何其他資料夾未對應到有效的效能層級，並無法使用。
- 移除檔案或資料夾的效能層級之外建立。

如需詳細資訊，請參閱 <<c0> [ 至受控磁碟複製](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)。

### <a name="errorcontainerorsharecapacityexceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**錯誤說明：** Azure 檔案共用限制的共用 5TB 的資料。 一些共用已超過此限制。

**建議的解決方式：** 在上**連線] 和 [複製**頁面的資料箱本機 web UI，請下載並檢閱檔案時發生錯誤。

識別發生此問題，從錯誤記錄檔，並確定該資料夾中的檔案會低於 5 TB 的資料夾。

### <a name="errorbloborfilenamecharactercontrol"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**錯誤說明：** Blob 或檔案名稱包含不支援的控制字元。

**建議的解決方式：** Blob 或您複製的檔案包含具有不支援的字元的名稱。

在上**連線] 和 [複製**頁面上的本機 web UI 中，請下載並檢閱檔案時發生錯誤。
移除或重新命名檔案，移除不支援的字元。

如需詳細資訊，請參閱 Azure 命名慣例[blob 名稱](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)並[檔案名稱](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)。

### <a name="errorbloborfilenamecharacterillegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**錯誤說明：** Blob 或檔案名稱包含不合法的字元。

**建議的解決方式：** Blob 或您複製的檔案包含具有不支援的字元的名稱。

在上**連線] 和 [複製**頁面上的本機 web UI 中，請下載並檢閱檔案時發生錯誤。
移除或重新命名檔案，移除不支援的字元。

如需詳細資訊，請參閱 Azure 命名慣例[blob 名稱](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)並[檔案名稱](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)。


### <a name="errorbloborfilenameending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**錯誤說明：** 不正確的字元結束的 blob 或檔案的名稱。

**建議的解決方式：** Blob 或您複製的檔案包含具有不支援的字元的名稱。

在上**連線] 和 [複製**頁面上的本機 web UI 中，請下載並檢閱檔案時發生錯誤。
移除或重新命名檔案，移除不支援的字元。

如需詳細資訊，請參閱 Azure 命名慣例[blob 名稱](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)並[檔案名稱](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)。


### <a name="errorbloborfilenamesegmentcount"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**錯誤說明：** Blob 或檔案名稱包含太多的路徑區段。

**建議的解決方式：** Blob 或您複製的檔案超過路徑區段的數目上限。 路徑區段是連續分隔符號字元，例如，正斜線 /。

- 在上**連線] 和 [複製**頁面上的本機 web UI 中，請下載並檢閱檔案時發生錯誤。
- 請確定[blob 名稱](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)並[檔案名稱](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)符合 Azure 命名慣例。

### <a name="errorbloborfilenameaggregatelength"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**錯誤說明：** Blob 或檔案名稱太長。

**建議的解決方式：** Blob 或檔案名稱超過最大長度。

- 在上**連線] 和 [複製**頁面上的本機 web UI 中，請下載並檢閱檔案時發生錯誤。
- Blob 名稱不得超過 1024 個字元。
- 移除或重新命名的 blob 或檔案，使名稱不能超過 1024年個字元。

如需詳細資訊，請參閱 blob 名稱和檔案名稱的 Azure 命名慣例。

### <a name="errorbloborfilenamecomponentlength"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**錯誤說明：** Blob 或檔案名稱的其中一個區段太長。

**建議的解決方式：** 其中一個路徑區段中的 blob 或檔案名稱超過最大字元數。 路徑區段是連續分隔符號字元，例如，正斜線 /。

- 在上**連線] 和 [複製**頁面上的本機 web UI 中，請下載並檢閱檔案時發生錯誤。
- 請確定[blob 名稱](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)並[檔案名稱](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)符合 Azure 命名慣例。

### <a name="errorbloborfilesizelimit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**錯誤說明：** 檔案大小超出上傳的檔案大小上限。

**建議的解決方式：** Blob 或檔案大小超過上傳所允許的最大限制。

- 在上**連線] 和 [複製**頁面上的本機 web UI 中，請下載並檢閱檔案時發生錯誤。
- 請確定 blob 和檔案大小不會超過 Azure 的物件大小限制。

### <a name="errorbloborfilesizealignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**錯誤說明：** Blob 或檔案未正確地對齊。

**建議的解決方式：** 頁面 blob 上共用資料 方塊中唯一支援的檔案是 512 位元組對齊 （例如 VHD/VHDX）。 複製到分頁 blob 的共用的任何資料上傳至 Azure 做為分頁 blob。

從頁面 blob 共用中移除任何非 VHD/VHDX 的資料。 您可以使用共用的區塊 blob 或 Azure 的一般資料的檔案。

如需詳細資訊，請參閱 <<c0> [ 概觀的分頁 blob](../storage/blobs/storage-blob-pageblob-overview.md)。

### <a name="errorbloborfiletypeunsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**錯誤說明：** 受控的磁碟的共用中有不支援的檔案類型。 允許唯一的固定的 Vhd。

**建議的解決方式：**

- 請確定您只能上傳固定的 Vhd 建立受控的磁碟。
- VHDX 檔案或**動態**並**差異**Vhd 不支援。

### <a name="errordirectorydisallowedfortype"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**錯誤說明：** 目錄不允許任何預先存在的資料夾中的受控磁碟。 唯一的固定的 Vhd 可以在這些資料夾中。

**建議的解決方式：** 受控磁碟，每個在共用內，下列三個資料夾會建立對應到您的儲存體帳戶中的容器：進階 SSD、 標準的 HDD 和 SSD 標準。 這些資料夾會對應至受控磁碟的效能層級。

- 請確定您將您的分頁 blob 資料 (Vhd) 複製到其中一個現有的資料夾。
- 這些現有的資料夾中不允許的資料夾或目錄。 移除任何您已建立的預先存在的資料夾內的資料夾。

如需詳細資訊，請參閱 <<c0> [ 至受控磁碟複製](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)。


## <a name="next-steps"></a>後續步驟

- 深入了解[資料方塊中的 Blob 儲存體系統需求](data-box-system-requirements-rest.md)。
