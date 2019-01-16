---
title: 如何新增 Blob 到 Azure Digital Twins 中的物件 | Microsoft Docs
description: 了解如何新增 Blob 到 Azure Digital Twins 中的物件。
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: adgera
ms.custom: seodec18
ms.openlocfilehash: 36f4caac38f2f4891af6f61b78b55c7eff15eae4
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2019
ms.locfileid: "54116733"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>在 Azure Digital Twins 中將 Blob 新增到物件

Blob 是常見檔案類型 (例如圖片和記錄) 的非結構化表示法。 Blob 使用 MIME 類型 (例如："image/jpeg") 和中繼資料 (名稱、描述、類型等等)，追蹤它們所代表的資料類型。

Azure Digital Twins 支援將 Blob 連結到裝置、空間和使用者。 Blob 可以代表使用者的個人檔案圖片、裝置的相片、影片、地圖、韌體、韌體 zip、JSON 資料或記錄等等。

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-an-overview"></a>上傳 Blob：概觀

您可以使用多部分要求，將 Blob 上傳到特定端點及其各自的功能。

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Blob 中繼資料

除了 **Content-Type** 和 **Content-Disposition**，Azure Digital Twins Blob 多部分要求必須指定正確的 JSON 主體。 要提交的 JSON 主體取決於所執行 HTTP 要求作業的種類。

四個主要 JSON 結構描述是：

![JSON 結構描述][1]

Swagger 文件會完整詳細說明這些模型結構描述。

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

若要了解如何使用參考文件，請參閱[如何使用 Swagger](./how-to-use-swagger.md)。

### <a name="examples"></a>範例

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

若要上傳文字檔作為 Blob，並將其與空間建立關聯，請發出已驗證的 HTTP POST 要求至：

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs
```

使用下列主體：

```plaintext
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

| 值 | 更換為 |
| --- | --- |
| USER_DEFINED_BOUNDARY | 多部分內容界限名稱 |

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

在這兩個範例中：

1. 確認標頭包含：`Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`。
1. 確認主體是由多個部分組成的：

   - 第一個部分包含必要的 Blob 中繼資料。
   - 第二個部分包含文字檔。

1. 確認已提供文字檔作為 `Content-Type: text/plain`。

## <a name="api-endpoints"></a>API 端點

下列各節說明核心 Blob 相關的 API 端點及其功能。

### <a name="devices"></a>裝置

您可以將 blob 連結到裝置。 下圖顯示管理 API 的 Swagger 參考文件。 其中指定裝置相關 API 端點以供 Blob 取用，以及任何要傳入的必要路徑參數。

![裝置 Blob][2]

例如，若要更新或建立 Blob 並將 Blob 連結到裝置，請發出已驗證的 HTTP PATCH 要求至：

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

例如，若要傳回連結到空間的 Blob，請發出已驗證的 HTTP GET 要求至：

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| 參數 | 更換為 |
| --- | --- |
| *YOUR_BLOB_ID* | 所需的 Blob 識別碼 |

相同端點的 PATCH 要求會更新中繼資料描述，並建立新版本的 Blob。 HTTP 要求是透過 PATCH 方法搭配任何必要的中繼與多部分表單資料來發出。

成功的作業會傳回符合下列結構描述的 **SpaceBlob** 物件。 您可將它用來取用所傳回的資料。

| 屬性 | 類型 | 說明 | 範例 |
| --- | --- | --- | --- |
| **SpaceBlobType** | 字串 | 可連結到空間的 Blob 類別 | `Map`和`Image` |
| **SpaceBlobSubtype** | 字串 | 比 **SpaceBlobType** 更細微的 Blob 子類別 | `GenericMap`、`ElectricalMap`、`SatelliteMap` 和 `WayfindingMap` |

### <a name="users"></a>使用者

您可以將 Blob 連結到使用者模型 (例如，與個人檔案的圖片建立關聯)。 下圖顯示相關使用者 API 端點和任何必要的路徑參數，例如 `id`：

![使用者 Blob][4]

例如，若要擷取連結至使用者的 Blob，請發出已驗證的 HTTP GET 要求，並搭配任何必要的表單資料：

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

常見錯誤是未包含正確的標頭資訊：

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

## <a name="next-steps"></a>後續步驟

- 若要深入了解 Azure Digital Twins 的 Swagger 參考文件，請參閱[使用 Azure Digital Twins Swagger](how-to-use-swagger.md)。

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
