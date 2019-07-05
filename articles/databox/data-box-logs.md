---
title: 追蹤及記錄 Azure 資料箱，Azure 資料方塊大量事件 |Microsoft Docs
description: 說明如何追蹤和記錄您的 Azure 資料方塊繁重而 Azure 資料箱訂單的各種階段的事件。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: ba08cd7fdecda99c04d5bb1007b3e5f61cd1bd5c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446773"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy"></a>追蹤與您 Azure 資料箱 」 和 「 Azure 資料方塊大量的事件記錄

資料箱 」 或 「 大量資料方塊的順序會經歷下列步驟： 訂購時，設定好之後，資料複製、 傳回、 上傳至 Azure 和驗證，和資料清除。 對應至每個步驟的順序，您可以採取多個動作，以控制的存取權的順序、 稽核的事件、 追蹤訂單，並解譯所產生的各種記錄檔。

下表顯示的資料箱 」 或 「 大量資料方塊的 「 順序 」 步驟和工具可用來追蹤和稽核順序在每個步驟的摘要。

| 資料方塊順序的階段       | 追蹤和稽核工具                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| 建立訂單               | [設定存取控制，透過 RBAC 的順序](#set-up-access-control-on-the-order)                                                    |
| 處理順序            | [追蹤順序](#track-the-order)透過 <ul><li> Azure 入口網站 </li><li> 出貨承運業者網站 </li><li>電子郵件通知</ul> |
| 設定裝置              | 裝置認證登入存取[活動記錄](#query-activity-logs-during-setup)                                              |
| 資料複製到裝置        | [檢視*error.xml*檔案](#view-error-log-during-data-copy)進行的資料複製                                                             |
| 準備寄送            | [檢查 BOM 的檔案](#inspect-bom-during-prepare-to-ship)或在裝置上的資訊清單檔案                                      |
| 資料上傳至 Azure       | [檢閱*copylogs* ](#review-copy-log-during-upload-to-azure)錯誤期間的資料上傳，Azure 資料中心                         |
| 從裝置的資料清除   | [檢視的監督鏈記錄鏈結](#get-chain-of-custody-logs-after-data-erasure)包括稽核記錄檔和訂購歷程記錄                |

本文會說明詳細資料，不同的機制或工具可用來追蹤和稽核資料箱 」 或 「 大量資料方塊的順序。 這篇文章中的資訊適用於，方塊中的資料和大量資料方塊中。 在後續章節中，任何參考資料 方塊也適用於大量資料方塊中。

## <a name="set-up-access-control-on-the-order"></a>設定存取控制的順序

您可以控制誰可以存取您的訂單，當第一次建立訂單。 設定角色型存取控制 (RBAC) 角色在不同範圍來控制存取資料箱訂單。 RBAC 角色會決定存取 – 讀寫、 唯讀、 讀寫子集的作業類型。

可以針對 Azure 資料箱服務定義兩個角色如下：

- **資料中讀取器**-具有唯讀存取 order(s) 範圍所定義。 他們只能檢視訂單的詳細資料。 它們無法存取儲存體帳戶相關的其他詳細資料，或編輯訂單詳細資料，例如地址，依此類推。
- **資料方塊參與者**-只能建立順序，將資料傳送至指定的儲存體帳戶*如果他們已經有儲存體帳戶的寫入權限*。 如果不是使用儲存體帳戶的存取權，即使無法建立資料箱訂單將資料複製到帳戶。 此角色沒有定義任何儲存體帳戶與儲存體帳戶的權限也授與的存取。  

若要限制存取順序，您可以：

- 在訂單 」 層級角色指派。 使用者僅擁有這些權限所定義的角色與該特定資料箱訂單只和沒有其他項目互動。
- 將資源群組層級角色指派，使用者可以存取的資源群組內的所有資料箱訂單。

如需有關建議的 RBAC 使用的詳細資訊，請參閱[RBAC 的最佳作法](../role-based-access-control/overview.md#best-practice-for-using-rbac)。

## <a name="track-the-order"></a>追蹤訂單狀態

您可以追蹤您的訂單，透過 Azure 入口網站和出貨承運業者網站。 下列機制已就緒，隨時追蹤資料箱訂單：

- 若要在 Azure 資料中心或您的內部部署裝置時，請追蹤順序，請移至您**資料箱訂單 > 概觀**在 Azure 入口網站中。

    ![檢視訂單狀態和追蹤否](media/data-box-logs/overview-view-status-1.png)

- 若要追蹤的順序，而裝置會在傳輸過程中，移至區域的貨運公司網站，例如，在我們的 UPS 網站。 提供您的訂單與相關聯的追蹤號碼。
- 資料箱也會傳送電子郵件通知，每當訂單狀態變更會根據建立順序時提供的電子郵件。 如需所有資料箱訂單狀態的清單，請參閱 <<c0> [ 檢視訂單狀態](data-box-portal-admin.md#view-order-status)。 若要變更與訂單相關聯的通知設定，請參閱[編輯通知詳細資料](data-box-portal-admin.md#edit-notification-details)。

## <a name="query-activity-logs-during-setup"></a>在安裝期間的查詢活動記錄

- 資料箱抵達您的內部部署鎖定的狀態。 您訂單，您可以使用 Azure 入口網站中可用的裝置認證。  

    資料箱已設定下，您可能需要知道所有使用者存取裝置認證。 若要找出誰存取了**裝置認證**刀鋒視窗中，您可以查詢活動記錄檔。  牽涉到存取的任何動作**裝置詳細資料 > 認證**刀鋒視窗中已登入活動記錄為`ListCredentials`動作。

    ![查詢活動記錄](media/data-box-logs/query-activity-log-1.png)

- 每個登入 Data Box 是記錄的實際時間。 不過，這項資訊僅供以[稽核記錄檔](#audit-logs)順序順利完成之後。

## <a name="view-error-log-during-data-copy"></a>檢視資料複製期間的錯誤記錄檔

在將資料複製到資料箱 」 或 「 大量資料方塊的說明，如果有任何問題所複製的資料，就會產生檔案時發生錯誤。

### <a name="errorxml-file"></a>Error.xml 檔案

請確定複製作業已完成，未發生錯誤。 如果複製程序期間發生錯誤，下載的記錄檔 **連線並複製** 頁面。

- 如果您複製的檔案，不是 512 個位元組對齊，以在資料中的受控的磁碟資料夾時，不到暫存的儲存體帳戶的分頁 blob 的形式上傳檔案。 您將在記錄中看到錯誤。 移除檔案，並複製 512 位元組規格的檔案。
- 如果您複製的 VHDX 或動態 VHD 或差異 VHD （不支援這些檔案），您會看到錯誤記錄檔中。

以下是範例*error.xml*時複製至受控磁碟的不同錯誤。

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

以下是範例*error.xml*複製到分頁 blob 時的不同錯誤。

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


以下是範例*error.xml*複製到區塊 blob 時的不同錯誤。

```xml
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ni3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMy3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL">\InvalidUnicodeFiles\Ã.txt</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMi3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ny3vv70=</file>
```

以下是範例*error.xml*複製到 Azure 檔案時的不同錯誤。

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_LIMIT">\AzFileMorethan1TB\AzFile1.2TB</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
```

在每個上述所有情況下，請解決錯誤，再繼續進行下一個步驟。 如需有關透過 SMB 或 NFS 的通訊協定的資料複製到資料箱期間收到的錯誤的詳細資訊，請移至[疑難排解 Data Box 和大量資料方塊問題](data-box-troubleshoot.md)。 如需有關在資料複製到資料箱，透過 REST 期間所收到的錯誤，請移至[疑難排解資料 Blob 儲存體問題](data-box-troubleshoot-rest.md)。

## <a name="inspect-bom-during-prepare-to-ship"></a>檢查 BOM 期間準備寄送

在準備寄送一份已知為帳單的用料表 (BOM) 檔案，或建立資訊清單檔案。

- 使用此檔案來進行驗證的實際名稱，並已複製到 [資料] 中的檔案數目。
- 使用此檔案來驗證檔案的實際大小。
- 確認*crc64*對應到非零的字串。 <!--A null value for crc64 indicates that there was a reparse point error)-->

如需有關期間收到的錯誤準備寄送，請前往[疑難排解 Data Box 和大量資料方塊問題](data-box-troubleshoot.md)。

### <a name="bom-or-manifest-file"></a>BOM 或資訊清單檔案

BOM 或資訊清單檔案包含的所有檔案複製到資料箱裝置的清單。 BOM 檔案的檔案名稱和對應的大小，以及總和檢查碼。 區塊 blob、 分頁 blob，Azure 上的檔案，複製透過 REST Api，以及為受控磁碟複製資料 方塊中會建立個別的 BOM 檔。 在出貨準備期間，您可以從本機 web UI 的裝置下載 BOM 的檔案。

這些檔案也位於資料箱裝置，並上傳至 Azure 資料中心的相關聯的儲存體帳戶。

### <a name="bom-file-format"></a>BOM 的檔案格式

BOM 或資訊清單的檔案具有下列一般格式：

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

以下是當資料已複製到 Data Box 上的區塊 blob 共用時，產生的資訊清單的範例。

```
<file size="10923" crc64="0x51c78833c90e4e3f">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer1.png</file>
<file size="15308" crc64="0x091a8b2c7a3bcf0a">\databox\media\data-box-deploy-copy-data\get-share-credentials2.png</file>
<file size="53486" crc64="0x053da912fb45675f">\databox\media\data-box-deploy-copy-data\nfs-client-access.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="7749" crc64="0xd2e346a4588e307a">\databox\data-box-deploy-ordered.md</file>
<file size="14275" crc64="0x260296e5d1b1608a">\databox\data-box-deploy-copy-data.md</file>
<file size="4077" crc64="0x2bb0a170225bceec">\databox\data-box-deploy-picked-up.md</file>
<file size="15447" crc64="0xcec0ca8527720b3c">\databox\data-box-portal-admin.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
```

BOM 或資訊清單檔案也會複製到 Azure 儲存體帳戶中。 您可以使用 BOM 或資訊清單檔案，以確認上傳至 Azure 的檔案比對已複製到 [資料] 中的資料。

## <a name="review-copy-log-during-upload-to-azure"></a>檢閱複製記錄檔期間上傳至 Azure

在資料上傳至 Azure 期間*copylog*建立。

### <a name="copylog"></a>Copylog

每個訂單處理，資料箱服務會建立*copylog*相關聯的儲存體帳戶中。 *Copylog*已上傳檔案的總數和複製資料 方塊中，您的 Azure 儲存體帳戶，發生錯誤的資料期間的檔案數目。

循環冗餘檢查 (CRC) 計算期間完成的作業上傳至 Azure。 從複製的資料，並比較資料上傳之後 CRCs。 CRC 不相符，表示對應的檔案無法上傳。

根據預設，記錄會寫入名為容器 `copylog`。 記錄會儲存具有下列命名慣例：

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml` 。

Copylog 路徑也會顯示在**概觀**入口網站刀鋒視窗。

![在 [概觀] 刀鋒視窗完成 copylog 路徑](media/data-box-logs/copy-log-path-1.png)

Data Box 上傳已順利完成，如下列範例會說明 copylog 檔案的一般格式：

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

上的傳至 Azure，也可能會完成但發生錯誤。

![在 [概觀] 刀鋒視窗完成但發生錯誤時的 copylog 路徑](media/data-box-logs/copy-log-path-2.png)

以下是範例 copylog 上載完成但發生錯誤：

```xml
<ErroredEntity Path="iso\samsungssd.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="iso\iSCSI_Software_Target_33.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>72</TotalFiles_Blobs>
  <FilesErrored>2</FilesErrored>
</CopyLog>
```
以下是範例`copylog`已在資料上傳至 Azure 期間重新命名的容器，不符合 Azure 命名慣例。

適用於容器的新唯一名稱的格式`DataBox-GUID`和容器的資料會放入新的重新命名容器。 `copylog`指定舊的和新的容器名稱的容器。

```xml
<ErroredEntity Path="New Folder">
   <Category>ContainerRenamed</Category>
   <ErrorCode>1</ErrorCode>
   <ErrorMessage>The original container/share/blob has been renamed to: DataBox-3fcd02de-bee6-471e-ac62-33d60317c576 :from: New Folder :because either the name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>Container</Type>
</ErroredEntity>
```

以下是範例`copylog`其中的 blob 或 Azure 的命名慣例，不符合的檔案已重新命名資料上傳至 Azure 期間。 新的 blob 或檔案名稱會轉換成的容器的相對路徑的 SHA256 摘要並上傳至目的型別為基礎的路徑。 目的地可以是區塊 blob、 分頁 blob 或 Azure 檔案。

`copylog`指定 Azure 中的舊和新的 blob 或檔案名稱和路徑。

```xml
<ErroredEntity Path="TesDir028b4ba9-2426-4e50-9ed1-8e89bf30d285\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: PageBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDir9856b9ab-6acb-4bc3-8717-9a898bdb1f8c\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: AzureFile/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDirf92f6ca4-3828-4338-840b-398b967d810b\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: BlockBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity>
```

## <a name="get-chain-of-custody-logs-after-data-erasure"></a>資料清除後取得監督鏈記錄的鏈結

從根據 NIST SP 800-88 修訂 1 指導方針的資料箱磁碟會清除資料之後，保管記錄鏈結可使用。 這些記錄包含稽核記錄檔和訂單歷程記錄。 BOM 或資訊清單檔案也會複製使用稽核記錄中。

### <a name="audit-logs"></a>稽核記錄

稽核記錄檔包含在電源上的資訊和外部 Azure 資料中心時，共用上的資料箱 」 或 「 大量資料方塊的存取。 這些記錄檔位於： `storage-account/azuredatabox-chainofcustodylogs`

從資料中的稽核記錄檔的範例如下：

```
9/10/2018 8:23:01 PM : The operating system started at system time ‎2018‎-‎09‎-‎10T20:23:01.497758400Z.
9/10/2018 8:23:42 PM : An account was successfully logged on.
Subject:
    Security ID:        S-1-5-18
    Account Name:       WIN-DATABOXADMIN
    Account Domain: Workgroup
    Logon ID:       0x3E7
Logon Information:
    Logon Type:     3
    Restricted Admin Mode:  -
    Virtual Account:        No
    Elevated Token:     No
Impersonation Level:        Impersonation
New Logon:
    Security ID:        S-1-5-7
    Account Name:       ANONYMOUS LOGON
    Account Domain: NT AUTHORITY
    Logon ID:       0x775D5
    Linked Logon ID:    0x0
    Network Account Name:   -
    Network Account Domain: -
    Logon GUID:     {00000000-0000-0000-0000-000000000000}
Process Information:
    Process ID:     0x4
    Process Name:       
Network Information:
    Workstation Name:   -
    Source Network Address: -
    Source Port:        -
Detailed Authentication Information:
    Logon Process:      NfsSvr
    Authentication Package:MICROSOFT_AUTHENTICATION_PACKAGE_V1_0
    Transited Services: -
    Package Name (NTLM only):   -
    Key Length:     0
This event is generated when a logon session is created. It is generated on the computer that was accessed. 
The subject fields indicate the account on the local system which requested the logon. This is most commonly a service such as the Server service, or a local process such as Winlogon.exe or Services.exe. 
The logon type field indicates the kind of logon that occurred. The most common types are 2 (interactive) and 3 (network).
The New Logon fields indicate the account for whom the new logon was created, i.e. the account that was logged on.
The network fields indicate where a remote logon request originated. Workstation name is not always available and may be left blank in some cases.
The impersonation level field indicates the extent to which a process in the logon session can impersonate.
The authentication information fields provide detailed information about this specific logon request.
    - Logon GUID is a unique identifier that can be used to correlate this event with a KDC event.
    - Transited services indicate which intermediate services have participated in this logon request.
    - Package name indicates which sub-protocol was used among the NTLM protocols.
    - Key length indicates the length of the generated session key. This will be 0 if no session key was requested.
9/10/2018 8:25:58 PM : An account was successfully logged on.
```


## <a name="download-order-history"></a>下載訂單記錄

使用 Azure 入口網站中訂單歷程記錄。 如果順序完成，且裝置清除 （資料清除的磁碟） 已完成，然後移至您的裝置順序，並瀏覽至**訂單詳細資料**。 **在下載訂單歷程記錄** 會提供選項。 如需詳細資訊，請參閱 <<c0> [ 下載訂單記錄](data-box-portal-admin.md#download-order-history)。

如果您捲動訂單歷程記錄時，您會看到：

- 追蹤您的裝置資訊的貨運公司。
- 事件*SecureErase*活動。 這些事件會對應到磁碟上的資料清除。
- 記錄資料 方塊中的連結。 路徑*稽核記錄檔*， *copylogs*，並*BOM*檔案會出現。

以下是訂單歷程記錄，從 Azure 入口網站的範例：

```
-------------------------------
Microsoft Data Box Order Report
-------------------------------
Name                                               : gus-poland                              
StartTime(UTC)                              : 9/19/2018 8:49:23 AM +00:00                       
DeviceType                                     : DataBox                                           
-------------------
Data Box Activities
-------------------
Time(UTC)                 | Activity                       | Status          | Description

9/19/2018 8:49:26 AM      | OrderCreated                   | Completed       |
10/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |
10/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 10/3/2018 1:36:43 PM at AMSTERDAM-NLD                                                                                
10/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 10/4/2018 8:23:30 PM at AMSTERDAM-NLD                                                                        
10/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time : 10/4/2018 11:43:34 PM at AMSTERDAM-NLD
10/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in BRIGHTON-GBR. Local Time : 10/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
10/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 10/5/2018 9:13:24 AM at BRIGHTON-GBR                                                                               
10/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 10/5/2018 12:03:04 PM at BRIGHTON-GBR                                                                          
1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25 PM at BRIGHTON-GBR                                                                                       
1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at BRIGHTON-GBR. Local Time : 1/25/2019 8:03:55 PM at LAMBETH-GBR                                                                            
1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in BRIGHTON-GBR. Local Time : 1/25/2019 8:04:58 PM at BRIGHTON-GBR                                                                    
1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR
1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD. Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019 9:07:57 AM at AMSTERDAM-NLD                                                                             
1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019 1:35:56 PM at AMSTERDAM-NLD                                                                            
1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019 2:57:48 PM at AMSTERDAM-NLD
1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |
1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully.
1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.
2/12/2019 10:37:03 PM     | DataCopy                       | Resumed         |
2/13/2019 12:05:15 AM     | DataCopy                       | Resumed         |
2/15/2019 7:07:34 PM      | DataCopy                       | Completed       | Copy Completed.
2/15/2019 7:47:32 PM      | SecureErase                    | Started         |
2/15/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:<Device-serial-no> has been sanitized according to NIST 800-88 Rev 1.
------------------
Data Box Log Links
------------------
Account Name         : gusacct
Copy Logs Path       : databoxcopylog/gus-poland_<Device-serial-no>_CopyLog_<GUID>.xml
Audit Logs Path      : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
BOM Files Path       : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
```

## <a name="next-steps"></a>後續步驟

- 了解如何[ 方塊中的資料和大量資料方塊上的問題的疑難排解](data-box-troubleshoot.md)。
