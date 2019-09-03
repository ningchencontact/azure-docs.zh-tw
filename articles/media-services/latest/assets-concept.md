---
title: Azure 媒體服務中的資產 |Microsoft Docs
description: 本文解釋資產是什麼，以及 Azure 媒體服務用它們來做什麼。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/29/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2f2dea922b7a3ba45ad6493ce94f0c52649dfa68
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2019
ms.locfileid: "70230988"
---
# <a name="assets"></a>資產

在 Azure 媒體服務中,[資產](https://docs.microsoft.com/rest/api/media/assets)會包含儲存在 Azure 儲存體中之數位檔案 (包括影片、音訊、影像、縮圖集合、文字播放軌和隱藏式輔助字幕檔案) 的相關資訊。 

資產會對應到 [Azure 儲存體帳戶](storage-account-concept.md)中的 Blob 容器，且資產中的檔案會儲存為該容器中的區塊 Blob。 當帳戶使用一般用途 v2 (GPv2) 儲存體時，媒體服務支援 Blob 層。 您可以利用 GPv2，將檔案移至[非經常性存取儲存體或封存儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)。 來源檔案不再需要時，很適合放到**封存**儲存體中保管 (例如，在檔案已編碼之後)。

**封存**儲存層只建議用於經過編碼，且編碼作業輸出已放在輸出 Blob 容器的極大型來源檔案。 如果您要讓輸出容器中的 Blob 與資產建立關聯，並且用來串流或分析您的內容，則該 Blob 必須位在**經常性存取**或**非經常性存取**儲存層。

### <a name="naming-blobs"></a>命名 blob

資產中的檔案/blob 名稱必須遵循[blob 名稱需求](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)和[NTFS 名稱需求](https://docs.microsoft.com/windows/win32/fileio/naming-a-file)。 這些需求的原因是檔案可以從 blob 儲存體複製到本機 NTFS 磁片進行處理。

## <a name="upload-digital-files-into-assets"></a>將數位檔案上傳到資產

將數位檔案上傳到儲存體並與資產相關聯之後, 就可以在媒體服務編碼、串流處理、分析內容工作流程中使用這些檔案。 其中一個常見的媒體服務工作流程是上傳、編碼和串流檔案。 本節將概述一般步驟。

> [!TIP]
> 在您開始開發之前, 請先參閱[使用媒體服務 V3 api 進行開發](media-services-apis-overview.md)(包含存取 api、命名慣例等的資訊)。

1. 使用媒體服務 v3 API 建立新的「輸入」資產。 這項作業會在與您媒體服務帳戶相關聯的儲存體帳戶中建立容器。 此 API 會傳回容器名稱 (例如 `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`)。
   
    如果您已經有要與資產建立關聯的 Blob 容器，您可以在建立資產時指定容器名稱。 媒體服務目前僅支援根目錄中的 Blob 容器，且檔案名稱中不能有路徑。 如此一來，檔案名稱為 "input.mp4" 的容器可運作。 而檔案名稱為 "videos/inputs/input.mp4" 的容器將無法運作。

    您可以使用 Azure CLI，直接上傳至訂用帳戶中任何您有權存取的儲存體帳戶和容器。 <br/>容器名稱必須是唯一的，並且必須遵循儲存體命名指引。 名稱不需要遵循媒體服務資產容器名稱 (資產-GUID) 的格式。 
    
    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. 以讀寫權限取得 SAS URL，這將用來將數位檔案上傳到資產容器。 您可以使用媒體服務 API 來[列出資產容器 URL](https://docs.microsoft.com/rest/api/media/assets/listcontainersas)。
3. 使用 Azure 儲存體 API 或 SDK (例如 [儲存體 REST API](../../storage/common/storage-rest-api-auth.md)、[JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md) 或 [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) 將檔案上傳到資產容器。 
4. 使用媒體服務 v3 API 建立可處理「輸入」資產的轉換和作業。 如需詳細資訊，請參閱[轉換和作業](transform-concept.md)。
5. 串流來自「輸出」資產的內容。

如需完整的 .NET 範例 (其中會示範如何建立資產、取得儲存體中資產容器的可寫入 SAS URL、使用 SAS URL 將檔案上傳到儲存體的容器)，請參閱[從本機檔案建立作業輸入](job-input-from-local-file-how-to.md)。

### <a name="create-a-new-asset"></a>建立新的資產

> [!NOTE]
> 資產的日期時間類型屬性一律為 UTC 格式。

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

如需 REST 範例，請參閱[使用 REST 建立資產](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples)。

此範例會示範如何建立**要求本文**，您可以在其中指定有用的資訊，例如描述、容器名稱、儲存體帳戶和其他資訊。

#### <a name="curl"></a>cURL

```cURL
curl -X PUT \
  'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "properties": {
    "description": "",
  }
}'
```

#### <a name="net"></a>.NET

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```

如需完整範例，請參閱[從本機檔案建立作業輸入](job-input-from-local-file-how-to.md)。 在媒體服務 v3 中，您也可以從 HTTPS URL 中建立作業的輸入 (請參閱[從 HTTPS URL 中建立作業輸入](job-input-from-http-how-to.md))。

## <a name="map-v3-asset-properties-to-v2"></a>將 v3 資產屬性對應至 v2

下表顯示[資產](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)在 v3 中的屬性如何對應至第2版中的資產屬性。

|v3 屬性|v2 屬性|
|---|---|
|識別碼-(唯一) 完整 Azure Resource Manager 路徑, 請參閱[資產](https://docs.microsoft.com/rest/api/media/assets/createorupdate)中的範例||
|名稱-(唯一) 請參閱[命名慣例](media-services-apis-overview.md#naming-conventions) ||
|alternateId|AlternateId|
|assetId|識別碼-(唯一) 值的`nb:cid:UUID:`開頭為前置詞。|
|已建立|建立時間|
|description|Name|
|lastModified|LastModified|
|storageAccountName|StorageAccountName|
|storageEncryptionFormat| 選項 (建立選項)|
|Type||

## <a name="storage-side-encryption"></a>儲存端加密

若要保護待用資產，資產應該透過儲存端加密來進行加密。 下表顯示儲存端加密在媒體服務中的運作方式：

|加密選項|描述|媒體服務 v2|媒體服務 v3|
|---|---|---|---|
|媒體服務的儲存體加密|AES-256 加密，由媒體服務管理金鑰|支援<sup>(1)</sup>|不支援<sup>(2)</sup>|
|[待用資料的儲存體服務加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Azure 儲存體提供的伺服器端加密，由 Azure 或客戶管理金鑰|支援|支援|
|[儲存體用戶端加密](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Azure 儲存體提供的用戶端加密，由客戶管理 Key Vault 中的金鑰|不支援|不支援|

<sup>1</sup> 雖然媒體服務支援處理乾淨/不含任何加密形式的內容，但不建議您這麼做。

<sup>2</sup> 在媒體服務 v3 中，如果您的資產是以媒體服務 v2 建立，則儲存體加密 (AES-256 加密) 只對回溯相容性有所支援。 這表示 v3 可用於現有的儲存體加密資產，但不允許建立新的。

## <a name="filtering-ordering-paging"></a>篩選、排序、分頁

請參閱[媒體服務實體的篩選、排序、分頁](entities-overview.md)。

## <a name="next-steps"></a>後續步驟

* [串流處理檔案](stream-files-dotnet-quickstart.md)
* [使用雲端 DVR](live-event-cloud-dvr.md)
* [媒體服務 v2 及 v3 之間的差異](migrate-from-v2-to-v3.md)
