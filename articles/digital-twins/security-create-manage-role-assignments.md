---
title: 了解 Azure Digital Twins 裝置連線和驗證 | Microsoft Docs
description: 使用 Azure Digital Twins 連線和驗證裝置
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lyrana
ms.openlocfilehash: adfb4c369ea1b324da8562a5b0b245ebdecff602
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323724"
---
# <a name="create-and-manage-role-assignments"></a>建立和管理角色指派

Azure Digital Twins 會使用角色型存取控制 ([RBAC](./security-role-based-access-control.md)) 來管理對資源的存取。

每個角色指派都包含：

* **物件識別碼** (Azure Active Directory 識別碼、服務主體物件識別碼或網域名稱)。
* **物件識別碼類型**。
* **角色定義識別碼**。
* **空間路徑**。
* (在大部分的情況下) Azure Active Directory **租用戶識別碼**。

## <a name="role-definition-identifiers"></a>角色定義識別碼

下表顯示可藉由查詢系統/角色 API 取得的項目：

| **角色** | **識別碼** |
| --- | --- |
| 空間管理員 | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| 使用者管理員| dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| 裝置管理員 | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| 金鑰管理員 | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| 權杖管理員 | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| 使用者 | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| 支援專家 | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| 裝置安裝人員 | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| GatewayDevice | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |

## <a name="supported-objectidtypes"></a>支援的 ObjectIdTypes

支援的 `ObjectIdTypes` 包括：

* `UserId`
* `DeviceId`
* `DomainName`
* `TenantId`
* `ServicePrincipalId`
* `UserDefinedFunctionId`

## <a name="create-a-role-assignment"></a>建立角色指派

```plaintext
HTTP POST /api/v1.0/roleassignments
```

| **名稱** | **必要** | **類型** | **說明** |
| --- | --- | --- | --- |
| roleId| 是 |字串 | 角色定義識別碼。 角色定義及其識別碼可藉由查詢系統 API 找到。 |
| objectId | 是 |字串 | 角色指派的物件識別碼，必須根據其相關聯的類型進行格式化。 `DomainName` ObjectIdType 的 ObjectId 必須以 `“@”` 字元開頭。 |
| objectIdType | 是 |字串 | 角色指派的類型。 必須是此表格中的下列其中一列。 |
| tenantId | 視情況而異 | 字串 |租用戶識別碼。 不允許用於 `DeviceId` 和 `TenantId` ObjectIdTypes。 必須用於 `UserId` 和 `ServicePrincipalId` ObjectIdTypes。 DomainName ObjectIdType 可選用。 |
| path* | 是 | 字串 |`Space` 物件的完整存取路徑。 例如：`/{Guid}/{Guid}`。如果某個識別碼需要整個圖形的角色指派，請指定 `"/"` (這會指定根目錄)。 但不建議加以使用，且**您應嚴格遵循最低權限原則**。 |

## <a name="sample-configuration"></a>範例組態

使用者必須具有最低限度租用戶空間的系統管理權限：

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : " 0fc863bb-eb51-4704-a312-7d635d70e599",
      "ObjectIdType" : "UserId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/ 091e349c-c0ea-43d4-93cf-6b57abd23a44/ d84e82e6-84d5-45a4-bd9d-006a118e3bab"
    }
  ```

模擬裝置和感應器以執行測試案例的應用程式：

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : "cabf7acd-af0b-41c5-959a-ce2f4c26565b",
      "ObjectIdType" : "ServicePrincipalId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/"
    }
  ```

網域中的所有使用者都會獲得空間、感應器和使用者 (及其對應的相關物件) 的讀取權限：

  ```JSON
    {
      "RoleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
      "ObjectId" : "@microsoft.com",
      "ObjectIdType" : "DomainName",
      "Path": "/091e349c-c0ea-43d4-93cf-6b57abd23a44"
    }
  ```

若要取得角色指派：

```plaintext
HTTP GET /api/v1/roleassignments?path={path}
```

| **名稱** | **位置** | **必要** |    **類型** |  **說明** |
| --- | --- | --- | --- | --- |
| Path | Path | True | 字串 | 空間的完整路徑 |

若要刪除角色指派：

```plaintext
HTTP DELETE /api/v1/roleassignments/{id}
```

| **名稱** | **位置** | **必要** | **類型** | **說明** |
| --- | --- | --- | --- | --- |
| ID | Path | True | 字串 |   角色指派識別碼 |

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure Digital Twins 安全性，請參閱 [API 驗證](./security-authenticating-apis.md)。
