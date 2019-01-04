---
title: 在 Azure Digital Twins 中將 Blob 新增到物件 | Microsoft Docs
description: 了解如何新增 Blob 到 Azure Digital Twins 中的物件
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adgera
ms.openlocfilehash: 8a68ba35ddf7caacbf2339d87c5aeef80f470ba4
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2018
ms.locfileid: "52725619"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>在 Azure Digital Twins 中將 Blob 新增到物件

Blob 是常見檔案類型 (例如圖片和記錄) 的非結構化表示法。 Blob 使用 MIME 類型 (例如："image/jpeg") 和中繼資料 (名稱、描述、類型等等)，持續追蹤它們所代表的資料類型。

Azure Digital Twins 支援將 Blob 連結到裝置、空間和使用者。 Blob 可以代表使用者的個人檔案圖片、裝置的相片、影片、地圖或記錄。

> [!NOTE]
> 本文假設：
> * 您的執行個體已正確設定，以接收管理 API 的要求。
> * 您已選擇使用 REST 用戶端正確地經過驗證。

## <a name="uploading-blobs-an-overview"></a>上傳 Blob：概觀

您可以使用多部分要求，將 Blob 上傳到特定端點及其各自的功能。

> [!IMPORTANT]
> 多部分要求需要三個資訊片段：
> * **Content-Type** 標頭：
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * **Content-Disposition**：`form-data; name="metadata"`
> * 要上傳的檔案內容
>
> **Content-Type** 和 **Content-Disposition** 資訊可能會依使用案例而有所不同。

針對 Azure Digital Twins 管理 API 發出的多部分要求有兩個部分：

* Blob 中繼資料 (例如相關聯的 MIME 類型)，如 **Content-Type** 和 **Content-Disposition** 資訊所示

* Blob 內容 (非結構化的檔案內容)  

兩個部分都不需要 **PATCH** 要求。 針對 **POST** 或建立作業，兩者皆為必要。

### <a name="blob-metadata"></a>Blob 中繼資料

除了 **Content-Type** 和 **Content-Disposition**，多部分要求必須指定正確的 JSON 主體。 要提交的 JSON 主體取決於所執行 HTTP 要求作業的種類。

四個主要 JSON 結構描述是：

![JSON 結構描述][1]

Swagger 文件會完整詳細說明這些模型結構描述。

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

若要了解如何使用參考文件，請參閱[如何使用 Swagger](./how-to-use-swagger.md)。

### <a name="examples"></a>範例

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

若要發出 **POST** 要求，以 Blob 形式上傳文字檔並將它與某個空間建立關聯：

```plaintext
POST YOUR_MANAGEMENT_API_URL/spaces/blobs HTTP/1.1
Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"

--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "ParentId": "54213cf5-285f-e611-80c3-000d3a320e1e",
  "Name": "My First Blob",
  "Type": "Map",
  "SubType": "GenericMap",
  "Description": "A well chosen description",
  "Sharing": "None"
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="myblob.txt"
Content-Type: text/plain

This is my blob content. In this case, some text, but I could also be uploading a picture, an JSON file, a firmware zip, etc.

--USER_DEFINED_BOUNDARY--
```

| 參數值 | 更換為 |
| --- | --- |
| *USER_DEFINED_BOUNDARY* | 多部分內容界限名稱 |

以下程式碼是使用類別 [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent) 的相同 Blob 上傳 .NET 實作：

```csharp
//Supply your metadata in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

## <a name="api-endpoints"></a>API 端點

下列各節詳細說明核心端點及其功能。

### <a name="devices"></a>裝置

您可以將 blob 連結到裝置。 下圖顯示管理 API 的 Swagger 參考文件。 其中指定裝置相關 API 端點以供 Blob 取用，以及任何要傳入的必要路徑參數。

![裝置 Blob][2]

例如，若要更新或建立 Blob 並將 Blob 連結到裝置，請發出 **PATCH** 要求給：

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| 參數 | 更換為 |
| --- | --- |
| *YOUR_BLOB_ID* | 所需的 Blob 識別碼 |

成功的要求會在回應中傳回 **DeviceBlob** JSON 物件。 **DeviceBlob** 物件符合下列 JSON 結構描述：

| 屬性 | 類型 | 說明 | 範例 |
| --- | --- | --- | --- |
| **DeviceBlobType** | 字串 | 可連結到裝置的 Blob 類別 | `Model`和`Specification` |
| **DeviceBlobSubtype** | 字串 | 比 **DeviceBlobType** 更細微的 Blob 子類別 | `PhysicalModel`、`LogicalModel`、`KitSpecification` 和 `FunctionalSpecification` |

> [!TIP]
> 您可以使用上述表格來處理成功傳回的要求資料。

### <a name="spaces"></a>空格

您也可以江 Blob 連結道空間。 下圖列出負責處理 Blob 的所有空間 API 端點。 也會列出要傳入這些端點的任何路徑參數。

![空間 Blob][3]

例如，若要傳回連結到空間的 Blob，請發出 **GET** 要求給：

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| 參數 | 更換為 |
| --- | --- |
| *YOUR_BLOB_ID* | 所需的 Blob 識別碼 |

對相同端點發出 **PATCH** 要求可讓您更新中繼資料描述，並建立新版的 Blob。 HTTP 要求是透過 **PATCH** 方法搭配任何必要的中繼與多部分表單資料來發出。

成功的作業會傳回符合下列結構描述的 **SpaceBlob** 物件。 您可將它用來取用所傳回的資料。

| 屬性 | 類型 | 說明 | 範例 |
| --- | --- | --- | --- |
| **SpaceBlobType** | 字串 | 可連結到空間的 Blob 類別 | `Map`和`Image` |
| **SpaceBlobSubtype** | 字串 | 比 **SpaceBlobType** 更細微的 Blob 子類別 | `GenericMap`、`ElectricalMap`、`SatelliteMap` 和 `WayfindingMap` |

### <a name="users"></a>使用者

您可以將 Blob 連結到使用者模型 (例如，與個人檔案的圖片建立關聯)。 下圖顯示相關使用者 API 端點和任何必要的路徑參數，例如 `id`：

![使用者 Blob][4]

例如，若要擷取連結至使用者的 Blob，請發出 **GET** 要求並搭配任何必要的表單資料：

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| 參數 | 更換為 |
| --- | --- |
| *YOUR_BLOB_ID* | 所需的 Blob 識別碼 |

傳回的 JSON (**UserBlob**) 符合下列 JSON 模型：

| 屬性 | 類型 | 說明 | 範例 |
| --- | --- | --- | --- |
| **UserBlobType** | 字串 | 可連結到使用者的 Blob 類別 | `Image`和`Video` |
| **UserBlobSubtype** |  字串 | 比 **UserBlobType** 更細微的 Blob 子類別 | `ProfessionalImage`、`VacationImage` 和 `CommercialVideo` |

## <a name="common-errors"></a>常見錯誤

常見錯誤不會包含正確的標頭資訊：

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure Digital Twins 的 Swagger 參考文件，請參閱[使用 Azure Digital Twins Swagger](how-to-use-swagger.md)。

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
