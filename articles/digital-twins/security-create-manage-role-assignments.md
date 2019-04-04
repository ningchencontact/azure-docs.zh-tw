---
title: 在 Azure Digital Twins 中建立及管理角色指派 | Microsoft Docs
description: 在 Azure Digital Twins 中建立及管理角色指派。
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: lyrana
ms.custom: seodec18
ms.openlocfilehash: 72155799971760e9ddc93746dceafb1ea554d88b
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905302"
---
# <a name="create-and-manage-role-assignments-in-azure-digital-twins"></a>在 Azure Digital Twins 中建立及管理角色指派

Azure Digital Twins 會使用角色型存取控制 ([RBAC](./security-role-based-access-control.md)) 來管理對資源的存取。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="role-assignments-overview"></a>角色指派概觀

每個角色指派符合下列定義：

```JSON
{
  "roleId": "00e00ad7-00d4-4007-853b-b9968ad000d1",
  "objectId": "be2c6daa-a3a0-0c0a-b0da-c000000fbc5f",
  "objectIdType": "ServicePrincipalId",
  "path": "/",
  "tenantId": "00f000bf-86f1-00aa-91ab-2d7cd000db47"
}
```

下表描述每個屬性：

| 屬性 | 名稱 | 必要項 | 類型 | 描述 |
| --- | --- | --- | --- | --- |
| roleId | 角色定義識別碼 | 是 | 字串 | 所需角色指派的唯一識別碼。 藉由查詢系統 API 或檢閱下表，來尋找角色定義及其識別碼。 |
| objectId | 物件識別碼 | 是 | 字串 | Azure Active Directory 識別碼、服務主體物件識別碼或網域名稱。 角色指派的指派內容以及指派給誰。 角色指派必須根據其相關聯的類型進行格式化。 對於 `DomainName` objectIdType，objectId 必須以 `“@”` 字元開頭。 |
| objectIdType | 物件識別碼類型 | 是 | 字串 | 使用的物件識別項類型。 請參閱以下**支援的 ObjectIdTypes**。 |
| path | 空間路徑 | 是 | 字串 | `Space` 物件的完整存取路徑。 例如 `/{Guid}/{Guid}`。 如果某個識別碼需要整個圖形的角色指派，請指定 `"/"`。 這個字元會指定根目錄，但不鼓勵使用。 一律遵循最低權限原則。 |
| tenantId | 租用戶識別碼 | 視情況而異 | 字串 | 在大部分的情況下為Azure Active Directory 租用戶識別碼。 不允許用於 `DeviceId` 和 `TenantId` ObjectIdTypes。 必須用於 `UserId` 和 `ServicePrincipalId` ObjectIdTypes。 DomainName ObjectIdType 可選用。 |

### <a name="supported-role-definition-identifiers"></a>支援的角色定義識別碼

每個角色指派會將角色定義與 Azure Digital Twins 環境中的實體建立關聯。

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

### <a name="supported-object-identifier-types"></a>支援的物件識別碼類型

先前已介紹過 **objectIdType** 屬性。

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

## <a name="role-assignment-operations"></a>角色指派作業

Azure Digital Twins 對於角色指派支援完整的*建立*、*讀取*和*刪除*作業。 *更新*作業的處理方法是透過新增角色指派、移除角色指派，或修改角色指派允許存取的[空間智慧圖形](./concepts-objectmodel-spatialgraph.md)節點。

![角色指派端點][1]

提供的 Swagger 參考文件包含所有可用 API 端點、要求作業和定義的更多相關資訊。

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

<div id="grant"></div>

### <a name="grant-permissions-to-your-service-principal"></a>將權限授與服務主體

將權限授與服務主體通常是使用 Azure Digital Twins 時要採取的第一步。 它需要：

1. 透過 PowerShell 登入您的 Azure 執行個體。
1. 取得您的服務主體資訊。
1. 將所需的角色指派給服務主體。

您的應用程式識別碼是在 Azure Active Directory 中提供的。 若要深入了解在 Active Directory 中設定及佈建 Azure Digital Twins，請仔細閱讀[快速入門](./quickstart-view-occupancy-dotnet.md)。

獲得應用程式識別碼之後，請執行以下 PowerShell 命令：

```shell
Login-AzAccount
Get-AzADServicePrincipal -ApplicationId  <ApplicationId>
```

然後，具有**管理員**角色的使用者可以透過對 URL 提出驗證的 HTTP POST 要求，將「空間管理員」角色指派給使用者：

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

包含下列 JSON 主體：

```JSON
{
  "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
  "objectId": "YOUR_SERVICE_PRINCIPLE_OBJECT_ID",
  "objectIdType": "ServicePrincipalId",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

<div id="all"></div>

### <a name="retrieve-all-roles"></a>擷取所有角色

![系統角色][2]

若要列出所有可用的角色 (角色定義)，請對下列事項提出驗證的 HTTP GET 要求：

```plaintext
YOUR_MANAGEMENT_API_URL/system/roles
```

成功的要求會傳回 JSON 陣列，其中包含可能已指派之每個角色的項目：

```JSON
[
    {
        "id": "3cdfde07-bc16-40d9-bed3-66d49a8f52ae",
        "name": "DeviceAdministrator",
        "permissions": [
            {
                "notActions": [],
                "actions": [
                    "Read",
                    "Create",
                    "Update",
                    "Delete"
                ],
                "condition": "@Resource.Type Any_of {'Device', 'DeviceBlobMetadata', 'DeviceExtendedProperty', 'Sensor', 'SensorBlobMetadata', 'SensorExtendedProperty'} || ( @Resource.Type == 'ExtendedType' && (!Exists @Resource.Category || @Resource.Category Any_of { 'DeviceSubtype', 'DeviceType', 'DeviceBlobType', 'DeviceBlobSubtype', 'SensorBlobSubtype', 'SensorBlobType', 'SensorDataSubtype', 'SensorDataType', 'SensorDataUnitType', 'SensorPortType', 'SensorType' } ) )"
            },
            {
                "notActions": [],
                "actions": [
                    "Read"
                ],
                "condition": "@Resource.Type == 'Space' && @Resource.Category == 'WithoutSpecifiedRbacResourceTypes' || @Resource.Type Any_of {'ExtendedPropertyKey', 'SpaceExtendedProperty', 'SpaceBlobMetadata', 'SpaceResource', 'Matcher'}"
            }
        ],
        "accessControlPath": "/system",
        "friendlyPath": "/system",
        "accessControlType": "System"
    }
]
```

<div id="check"></div>

### <a name="check-a-specific-role-assignment"></a>檢查特定角色指派

若要檢查特定的角色指派，請對下列項目提出驗證的 HTTP GET 要求：

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/check?userId=YOUR_USER_ID&path=YOUR_PATH&accessType=YOUR_ACCESS_TYPE&resourceType=YOUR_RESOURCE_TYPE
```

| **參數值** | **必要項** |  **類型** |  **描述** |
| --- | --- | --- | --- |
| YOUR_USER_ID |  True | 字串 |   UserId objectIdType 的 objectId。 |
| YOUR_PATH | True | 字串 |   用來檢查存取權的選擇路徑。 |
| YOUR_ACCESS_TYPE |  True | 字串 |   要檢查的存取類型。 |
| YOUR_RESOURCE_TYPE | True | 字串 |  要檢查的資源。 |

成功的要求會傳回布林值 `true` 或 `false`，指示是否已為指定的路徑和資源，指派存取權類型給使用者。

### <a name="get-role-assignments-by-path"></a>依路徑取得角色指派

若要取得路徑的角色指派，請對下列項目提出驗證的 HTTP GET 要求：

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| 值 | 更換為 |
| --- | --- |
| YOUR_PATH | 空間的完整路徑 |

成功的要求會傳回 JSON 陣列，包含與所選取之 **path** 參數相關聯的每個角色指派：

```JSON
[
    {
        "id": "0000c484-698e-46fd-a3fd-c12aa11e53a1",
        "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
        "objectId": "0de38846-1aa5-000c-a46d-ea3d8ca8ee5e",
        "objectIdType": "UserId",
        "path": "/"
    }
]
```

### <a name="revoke-a-permission"></a>撤銷權限

若要從收件者撤銷權限，請藉由提出驗證的 HTTP DELETE 要求，刪除角色指派：

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ASSIGNMENT_ID
```

| 參數 | 更換為 |
| --- | --- |
| *YOUR_ROLE_ASSIGNMENT_ID* | 要移除之角色指派的**識別碼** |

成功的 DELETE 要求會傳回 204 回應狀態。 藉由[檢查](#check)角色指派是否仍然保留，驗證是否已移除角色指派。

### <a name="create-a-role-assignment"></a>建立角色指派

若要建立角色指派，請對 URL 提出驗證的 HTTP POST 要求：

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

確認 JSON 主體符合下列結構描述：

```JSON
{
  "roleId": "YOUR_ROLE_ID",
  "objectId": "YOUR_OBJECT_ID",
  "objectIdType": "YOUR_OBJECT_ID_TYPE",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

成功的要求會傳回 201 回應狀態，以及新建立之角色指派的**識別碼**：

```JSON
"d92c7823-6e65-41d4-aaaa-f5b32e3f01b9"
```

## <a name="configuration-examples"></a>組態範例

以下範例示範如何在幾個常見的角色指派案例中設定 JSON 主體。

* **範例**：使用者必須具有最低限度租用戶空間的系統管理權限。

   ```JSON
   {
    "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
    "objectId" : " 0fc863aa-eb51-4704-a312-7d635d70e000",
    "objectIdType" : "UserId",
    "tenantId": " a0c20ae6-e830-4c60-993d-a00ce6032724",
    "path": "/ 000e349c-c0ea-43d4-93cf-6b00abd23a44/ d84e82e6-84d5-45a4-bd9d-006a000e3bab"
   }
   ```

* **範例**：模擬裝置和感應器以執行測試案例的應用程式。

   ```JSON
   {
    "roleId": "98e44ad7-28d4-0007-853b-b9968ad132d1",
    "objectId" : "cabf7aaa-af0b-41c5-000a-ce2f4c20000b",
    "objectIdType" : "ServicePrincipalId",
    "tenantId": " a0c20ae6-e000-4c60-993d-a91ce6000724",
    "path": "/"
   }
    ```

* **範例**：所有隸屬於網域的使用者都會接收到適用於空間、感應器和使用者的讀取權限。 此存取權包含其對應的相關物件。

   ```JSON
   {
    "roleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
    "objectId" : "@microsoft.com",
    "objectIdType" : "DomainName",
    "path": "/000e349c-c0ea-43d4-93cf-6b00abd23a00"
   }
   ```

## <a name="next-steps"></a>後續步驟

- 若要檢閱 Azure Digital Twins 角色型存取控制，請參閱[角色型存取控制](./security-authenticating-apis.md)。

- 若要深入了解 Azure Digital Twins API 驗證，請參閱 [API 驗證](./security-authenticating-apis.md)。

<!-- Images -->
[1]: media/security-roles/roleassignments.png
[2]: media/security-roles/system.png
