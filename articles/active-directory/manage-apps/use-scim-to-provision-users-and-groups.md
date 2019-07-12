---
title: 在 Azure Active Directory 中使用 SCIM 自動佈建應用程式 | Microsoft Docs
description: Azure Active Directory 會利用 SCIM 通訊協定規格中定義的介面，自動佈建使用者和群組到 Web 服務前端的任何應用程式或身分識別存放區
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7e819551e7d85ccd039e23298b852302bba2d92
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807586"
---
# <a name="using-system-for-cross-domain-identity-management-scim-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>使用 System for Cross-Domain Identity Management (SCIM) 自動將使用者和群組從 Azure Active Directory 佈建到應用程式

標準化的通訊協定和結構描述的目標磁碟機中的身分識別管理跨系統的方式更好的一致性是 SCIM。 當應用程式支援 SCIM 端點的使用者管理時，Azure AD 使用者佈建服務可以傳送要求以建立、 修改或刪除指派的使用者與群組對此端點。

Azure AD 支援的應用程式的許多[預先整合的自動使用者佈建](../saas-apps/tutorial-list.md)實作變更通知的方法，以接收使用者的 SCIM。  除了這些之外，客戶可以連接支援特定的設定檔的應用程式[SCIM 2.0 通訊協定規格](https://tools.ietf.org/html/rfc7644)使用 Azure 入口網站中的泛用 「 非資源庫 」 整合選項。

這篇文章的重點所在的 Azure AD 會實作其一般的 SCIM 連接器為非資源庫應用程式一部分的 SCIM 2.0 設定檔。 不過，成功測試應用程式支援 SCIM 與一般的 Azure AD 連接器是透過讓應用程式以支援使用者佈建 Azure AD 資源庫中列出的步驟。 如需有關如何在 Azure AD 應用程式資源庫中列出的應用程式的詳細資訊，請參閱[How to:列出您的應用程式，Azure AD 應用程式庫中](../develop/howto-app-gallery-listing.md)。

> [!IMPORTANT]
> Azure AD SCIM 實作行為的上次更新日期為 2018 年 12 月 18 日。 如需已變更內容的資訊，請參閱 [Azure AD 使用者佈建服務的 SCIM 2.0 通訊協定合規性](application-provisioning-config-problem-scim-compatibility.md)。

![顯示從 Azure AD 佈建到應用程式或身分識別存放區][0]<br/>
*圖 1：從 Azure Active Directory 佈建至實作 SCIM 的應用程式或身分識別存放區*

這篇文章會分割成四個區段：

* **[佈建使用者和群組至支援 SCIM 2.0 的第三方應用程式](#provisioning-users-and-groups-to-applications-that-support-scim)** -如果您的組織使用的 Azure AD 的 SCIM 2.0 設定檔支援的實作，您可以開始自動化兩者的第三方應用程式佈建和取消佈建使用者和群組現在。
* **[了解 Azure AD SCIM 實作](#understanding-the-azure-ad-scim-implementation)** -如果您正在建置支援 SCIM 2.0 使用者管理 API 的應用程式，這一節詳細說明如何實作 Azure AD SCIM 用戶端，以及如何，您應該建立模型您的 SCIM 通訊協定要求處理和回應。
* **[建置使用 Microsoft CLI 程式庫的 SCIM 端點](#building-a-scim-endpoint-using-microsoft-cli-libraries)** -Common Language Infrastructure (CLI) 程式庫，以及程式碼範例會示範如何開發 SCIM 端點及轉譯 SCIM 訊息。  
* **[使用者和群組的結構描述參考](#user-and-group-schema-reference)** -描述支援的非資源庫應用程式的 Azure AD SCIM 實作的使用者和群組結構描述。

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>將使用者與群組佈建至支援 SCIM 的應用程式

Azure AD 可以設定為自動指派的佈建使用者和群組，以實作特定的設定檔的應用程式[SCIM 2.0 通訊協定](https://tools.ietf.org/html/rfc7644)。 設定檔的詳細資料記載於[了解 Azure AD SCIM 實作](#understanding-the-azure-ad-scim-implementation)。

洽詢應用程式提供者，或參閱應用程式提供者文件中的相關陳述，以了解是否符合這些需求。

> [!IMPORTANT]
> Azure AD SCIM 實作為建置基礎的佈建服務，其設計目的是要不斷保持使用者 Azure AD 之間同步的 Azure AD 使用者與目標應用程式，並實作一組非常特定的標準作業。 請務必了解這些行為，以了解 Azure AD SCIM 用戶端的行為。 如需詳細資訊，請參閱 <<c0> [ 使用者佈建期間發生什麼事？](user-provisioning.md#what-happens-during-provisioning)。

### <a name="getting-started"></a>使用者入門

支援本文所述 SCIM 設定檔的應用程式，可使用 Azure AD 應用程式庫中的「不在資源庫內的應用程式」功能連線到 Azure Active Directory。 連線之後，Azure AD 會每隔 40 分鐘執行一次同步處理程序，此程序會為指派的使用者和群組查詢應用程式的 SCIM 端點，並根據指派詳細資料加以建立或修改。

**若要連接支援 SCIM 的應用程式：**

1. 登入[Azure Active Directory 入口網站](https://aad.portal.azure.com)。 
1. 選取 **企業應用程式**從左窗格中。 會顯示所有設定的應用程式的清單，包括從資源庫新增的應用程式。
1. 選取  **+ 新的應用程式** > **所有** > **非資源庫應用程式**。
1. 輸入您的應用程式的名稱，然後選取**新增**來建立應用程式物件。 新的應用程式新增至 [企業應用程式清單，並以其應用程式管理] 畫面隨即開啟。

   ![螢幕擷取畫面顯示 Azure AD 應用程式庫][1]<br/>
   *圖 2：Azure AD 應用程式庫*

1. 在 [應用程式管理] 畫面中，選取**佈建**左方面板中。
1. 在 [佈建模式]  功能表上，選取 [自動]  。

   ![範例：在 Azure 入口網站中的應用程式的佈建頁面][2]<br/>
   *圖 3：在 Azure 入口網站中設定佈建*

1. 在 [租用戶 URL]  欄位中，輸入應用程式 SCIM 端點的 URL。 範例： https://api.contoso.com/scim/v2/
1. 如果 SCIM 端點需要來自非 Azure AD 簽發者的 OAuth 持有人權杖，那麼便將所需的 OAuth 持有人權杖複製到選擇性 [祕密權杖]  欄位。
1. 選取 **測試連接**，讓 Azure Active Directory 嘗試連線到 SCIM 端點。 如果嘗試失敗，則會顯示錯誤的資訊。  

    > [!NOTE]
    > [測試連線]  會查詢 SCIM 端點中是否有不存在的使用者，並使用隨機 GUID 作為 Azure AD 設定中選取的比對屬性。 預期的正確回應是 HTTP 200 OK 和空白的 SCIM ListResponse 訊息。

1. 如果嘗試連線到應用程式成功，然後選取**儲存**儲存管理員認證。
1. 在 [對應]  區段中，有兩組可選取的屬性對應：一個用於使用者物件，一個用於群組物件。 選取其中一個以檢閱從 Azure Active Directory 同步處理至應用程式的屬性。 選取為 [比對]  屬性的屬性會用來比對應用程式中的使用者和群組以進行更新作業。 選取 [儲存]  認可任何變更。

    > [!NOTE]
    > 您可以選擇性地藉由停用「群組」對應以停用同步處理群組物件。

1. 在 [設定]  底下，[範圍]  欄位會定義要同步的使用者和群組。 選取 **同步處理只會指派使用者和群組**（建議） 在 指派使用者和群組只同步處理**使用者和群組** 索引標籤。
1. 一旦完成您的設定，設定**佈建狀態**要**上**。
1. 選取 **儲存**啟動 Azure AD 佈建服務。
1. 如果僅同步處理指派使用者和群組 （建議選項），請務必選取**使用者和群組**索引標籤，並將指派的使用者或您想要同步的群組。

一旦啟動初始同步處理之後，您可以選取**稽核記錄檔**在左窗格中來監視進度，其中顯示由您的應用程式上佈建服務的所有動作。 如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](check-status-user-account-provisioning.md)。

> [!NOTE]
> 初始同步處理所花費的時間比更新的同步處理，會發生大約每 40 分鐘，只要服務正在執行一次執行。

## <a name="understanding-the-azure-ad-scim-implementation"></a>了解 Azure AD SCIM 實作

如果您正在建置支援 SCIM 2.0 使用者管理 API 的應用程式，這一節詳細說明如何實作 Azure AD SCIM 用戶端，以及如何應該您 SCIM 通訊協定模型要求處理和回應。 一旦您已實作 SCIM 端點，您可以遵循上一節中所述的程序來進行測試。

內[SCIM 2.0 通訊協定規格](http://www.simplecloud.info/#Specification)，您的應用程式必須符合下列需求：

* 支援建立使用者，以及選擇性地群組，根據一節[SCIM 通訊協定 3.3](https://tools.ietf.org/html/rfc7644#section-3.3)。  
* 支援修改具有修補要求使用者或群組依據[一節的 SCIM 通訊協定 3.5.2](https://tools.ietf.org/html/rfc7644#section-3.5.2)。  
* 支援擷取已知的資源的使用者或稍早建立的群組依據[一節的 SCIM 通訊協定 3.4.1](https://tools.ietf.org/html/rfc7644#section-3.4.1)。  
* 支援查詢的使用者或群組，根據一節[SCIM 通訊協定 3.4.2](https://tools.ietf.org/html/rfc7644#section-3.4.2)。  根據預設，使用者藉由擷取其`id`，而且查詢其`username`並`externalid`，並藉由查詢群組`displayName`。  
* 支援查詢使用者依 ID 和管理員 中，根據 SCIM 通訊協定 3.4.2 區段。  
* 支援查詢群組依 ID 和成員，根據 SCIM 通訊協定 3.4.2 區段。  
* 接受單一的持有人權杖進行驗證和授權您的應用程式的 Azure ad。

實作以確保相容性與 Azure AD 的 SCIM 端點時，請遵循下列一般指導方針：

* `id` 是必要的屬性建立的所有資源。 資源應該確定每個資源會傳回每個回應會有這個屬性中，除了`ListResponse`具有零的成員。
* 回應的查詢/篩選要求應該永遠是`ListResponse`。
* 群組是選擇性的但只支援 SCIM 實作支援 PATCH 要求。
* 它不需要修補程式回應中包含整個資源。
* Microsoft Azure AD 只會使用下列運算子：  
     - `eq`
     - `and`
* 不需要區分大小寫的相符項目結構中的項目在特定的修補程式的 SCIM`op`作業的值，如同 https://tools.ietf.org/html/rfc7644#section-3.5.2 。 Azure AD 發出 'o' 的值作為`Add`， `Replace`，和`Remove`。
* Microsoft Azure AD 會要求擷取隨機使用者和群組，以確保端點和認證有效。 它也會完成的一部分**測試連接**朝[Azure 入口網站](https://portal.azure.com)。 
* 資源可以查詢的屬性應該設定為 比對的屬性中的應用程式[Azure 入口網站](https://portal.azure.com)。 如需詳細資訊，請參閱[自訂使用者佈建屬性對應](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

### <a name="user-provisioning-and-de-provisioning"></a>使用者佈建和取消佈建

下圖顯示 Azure Active Directory 傳送至 SCIM 服務來管理您的應用程式身分識別存放區中的使用者生命週期的訊息。  

![顯示使用者佈建和取消佈建順序][4]<br/>
*圖 4：使用者佈建和取消佈建順序*

### <a name="group-provisioning-and-de-provisioning"></a>群組佈建和取消佈建

群組佈建和取消佈建是選擇性的。 實作，且已啟用，如下圖所顯示的訊息，Azure AD 會傳送至 SCIM 服務來管理您的應用程式身分識別存放區中的群組的生命週期。  這些訊息不同，有兩種方式的使用者相關：

* 擷取群組的要求指定成員屬性從回應要求中提供的任何資源中排除。  
* 要求判斷參考屬性是否具有特定值，會是有關成員屬性的要求。  

![顯示的群組佈建和取消佈建順序][5]<br/>
*圖 5︰群組佈建和取消佈建順序*

### <a name="scim-protocol-requests-and-responses"></a>SCIM 通訊協定要求和回應
本節提供範例 SCIM 要求發出 Azure AD SCIM 用戶端和範例預期的回應。 為了獲得最佳結果，您應該撰寫您的應用程式，來處理這些要求，以下列格式，並發出的預期的回應。

> [!IMPORTANT]
> 若要了解 Azure AD 使用者佈建服務如何及何時會發出如下所述的作業，請參閱[使用者佈建期間發生什麼事？](user-provisioning.md#what-happens-during-provisioning)。

- [使用者作業](#user-operations)
  - [建立使用者](#create-user)
    - [要求](#request)
    - [回應](#response)
  - [取得使用者](#get-user)
    - [要求](#request-1)
    - [回應](#response-1)
  - [取得查詢的使用者](#get-user-by-query)
    - [要求](#request-2)
    - [回應](#response-2)
  - [查詢-零筆結果，以取得使用者](#get-user-by-query---zero-results)
    - [要求](#request-3)
    - [回應](#response-3)
  - [更新使用者 [多重值屬性]](#update-user-multi-valued-properties)
    - [要求](#request-4)
    - [回應](#response-4)
  - [更新使用者的 [單一值的內容]](#update-user-single-valued-properties)
    - [要求](#request-5)
    - [回應](#response-5)
  - [刪除使用者](#delete-user)
    - [要求](#request-6)
    - [回應](#response-6)
- [群組作業](#group-operations)
  - [建立群組](#create-group)
    - [要求](#request-7)
    - [回應](#response-7)
  - [取得群組](#get-group)
    - [要求](#request-8)
    - [回應](#response-8)
  - [取得群組的顯示名稱](#get-group-by-displayname)
    - [要求](#request-9)
    - [回應](#response-9)
  - [更新群組 [非成員屬性]](#update-group-non-member-attributes)
    - [要求](#request-10)
    - [回應](#response-10)
  - [更新群組 [新增成員]](#update-group-add-members)
    - [要求](#request-11)
    - [回應](#response-11)
  - [更新群組 [移除成員]](#update-group-remove-members)
    - [要求](#request-12)
    - [回應](#response-12)
  - [刪除群組](#delete-group)
    - [要求](#request-13)
    - [回應](#response-13)

### <a name="user-operations"></a>使用者作業

* 使用者可以查詢`userName`或`email[type eq "work"]`屬性。  

#### <a name="create-user"></a>建立使用者

###### <a name="request"></a>要求

*POST/使用者*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>Response

*HTTP/1.1 201 已建立*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="get-user"></a>取得使用者

###### <a name="request-1"></a>要求
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response-1"></a>回應
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="get-user-by-query"></a>取得查詢的使用者

##### <a name="request-2"></a>要求

*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response-2"></a>回應

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="get-user-by-query---zero-results"></a>查詢-零筆結果，以取得使用者

##### <a name="request-3"></a>要求

*GET/使用者？ filter = 使用者名稱 eq"不存在的使用者 」*

##### <a name="response-3"></a>回應

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>更新使用者 [多重值屬性]

##### <a name="request-4"></a>要求

*修補程式/使用者/6764549bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response-4"></a>回應

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>更新使用者的 [單一值的內容]

##### <a name="request-5"></a>要求

*修補程式/使用者/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response-5"></a>回應

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="delete-user"></a>刪除使用者

##### <a name="request-6"></a>要求

*DELETE /Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response-6"></a>回應

*HTTP/1.1 204 沒有內容*

### <a name="group-operations"></a>群組作業

* 使用空的成員清單時，應該會一律建立群組。
* 群組可以查詢`displayName`屬性。
* 更新群組 PATCH 要求應該產生*HTTP 204 「 無內容*在回應中。 傳回清單的所有成員的主體，不建議這麼做。
* 它不需要支援傳回群組的所有成員。

#### <a name="create-group"></a>建立群組

##### <a name="request-7"></a>要求

*POST /Groups HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "members": [],
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response-7"></a>回應

*HTTP/1.1 201 已建立*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>取得群組

##### <a name="request-8"></a>要求

*GET/群組/40734ae655284ad3abcc？ excludedAttributes = HTTP/1.1*

##### <a name="response-8"></a>回應
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>取得群組的顯示名稱

##### <a name="request-9"></a>要求
*取得 /Groups？ excludedAttributes = 成員 filter = displayName eq"displayName"HTTP/1.1*

##### <a name="response-9"></a>回應

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-group-non-member-attributes"></a>更新群組 [非成員屬性]

##### <a name="request-10"></a>要求

*PATCH /Groups/fa2ce26709934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response-10"></a>回應

*HTTP/1.1 204 沒有內容*

### <a name="update-group-add-members"></a>更新群組 [新增成員]

##### <a name="request-11"></a>要求

*修補程式/群組/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response-11"></a>回應

*HTTP/1.1 204 沒有內容*

#### <a name="update-group-remove-members"></a>更新群組 [移除成員]

##### <a name="request-12"></a>要求

*修補程式/群組/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response-12"></a>回應

*HTTP/1.1 204 沒有內容*

#### <a name="delete-group"></a>刪除群組

##### <a name="request-13"></a>要求

*DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response-13"></a>回應

*HTTP/1.1 204 沒有內容*

## <a name="building-a-scim-endpoint-using-microsoft-cli-libraries"></a>建置使用 Microsoft CLI 程式庫的 SCIM 端點

藉由建立介面的 SCIM web 服務與 Azure Active Directory，您可以讓幾乎任何應用程式或身分識別存放區的自動使用者佈建。

其運作方式如下：

1. Azure AD 提供名為 Microsoft.SystemForCrossDomainIdentityManagement，如下所述的範例程式碼隨附的通用語言基礎結構 (CLI) 程式庫。 系統整合業者和開發人員可以使用此程式庫來建立及部署可以連線到任何應用程式的身分識別存放區的 Azure AD 的 SCIM 式 web 服務端點。
2. 對應會在 Web 服務中實作，以將標準的使用者結構描述對應到使用者結構描述和應用程式所需的通訊協定。 
3. 端點 URL 會在 Azure AD 中註冊，作為應用程式資源庫中自訂應用程式的一部分。
4. 使用者和群組會在 Azure AD 中指派給此應用程式。 指派時，他們要放入佇列，以同步處理至目標應用程式。 同步處理程序會每隔 40 分鐘處理佇列的執行。

### <a name="code-samples"></a>程式碼範例

為了簡化此程序，[程式碼範例](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)所提供，這會建立 SCIM web 服務端點，並示範自動佈建。 此範例是維護檔案與資料列的以逗號分隔的值，代表使用者和群組的提供者。

**必要條件**

* Visual Studio 2013 或更新版本
* [Azure SDK for .NET](https://azure.microsoft.com/downloads/)
* 支援將 ASP.NET Framework 4.5 用作 SCIM 端點的 Windows 電腦。 這部電腦必須能夠從雲端中使用。
* [具有 Azure AD Premium 試用版或授權版的 Azure 訂用帳戶](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>使用者入門

實作可以接受來自 Azure AD 的佈建要求的 SCIM 端點的最簡單的方式是建置和部署會將佈建的使用者輸出至以逗號分隔值 (CSV) 檔案的程式碼範例。

#### <a name="to-create-a-sample-scim-endpoint"></a>建立範例 SCIM 端點

1. 請至 [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) 下載程式碼範例套件。
1. 將套件解壓縮並將放在 Windows 電腦上的位置，例如 C:\AzureAD-BYOA-Provisioning-Samples\。
1. 在此資料夾中，啟動 Visual Studio 中的 FileProvisioning\Host\FileProvisioningService.csproj 專案。
1. 選取 **工具** > **NuGet 套件管理員** > **Package Manager Console**，並執行下列命令FileProvisioningService 專案解析方案參考：

   ```powershell
    Update-Package -Reinstall
   ```

1. 建置 FileProvisioningService 專案。
1. 在 Windows 中啟動「命令提示字元」應用程式 (以系統管理員身分)，然後使用 **cd** 命令將目錄變更為您的 **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug** 資料夾。
1. 執行下列命令，取代`<ip-address>`Windows 電腦的 IP 位址或網域名稱：

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

1. 在 Windows 底下**Windows 設定** > **網路和網際網路設定**，選取**Windows 防火牆** >  **進階設定**，並建立**輸入規則**，可讓連接埠 9000 進行輸入的存取。
1. 如果 Windows 電腦位於路由器背後，必須將路由器設定為在 Windows 電腦上，執行其連接埠公開至網際網路，9000 和連接埠 9000 之間的網路存取轉譯。 若要存取此端點在雲端中的 Azure AD 需要這項設定。

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>在 Azure AD 中註冊範例 SCIM 端點

1. 登入[Azure Active Directory 入口網站](https://aad.portal.azure.com)。 
1. 選取 **企業應用程式**從左窗格中。 會顯示所有設定的應用程式的清單，包括從資源庫新增的應用程式。
1. 選取  **+ 新的應用程式** > **所有** > **非資源庫應用程式**。
1. 輸入您的應用程式的名稱，然後選取**新增**來建立應用程式物件。 建立的應用程式物件要代表您要佈建和實作登一登入的目標應用程式，而不只是 SCIM 端點。
1. 在 [應用程式管理] 畫面中，選取**佈建**左方面板中。
1. 在 [佈建模式]  功能表上，選取 [自動]  。    
1. 在 [租用戶 URL]  欄位中，輸入網際網路公開的 URL 和 SCIM 端點的連接埠。 此項目就像是 http://testmachine.contoso.com:9000 或 http://\< ip-address>:9000/，其中 \< ip-address> 是網際網路公開 IP 位址。 
1. 如果 SCIM 端點需要來自非 Azure AD 簽發者的 OAuth 持有人權杖，那麼便將所需的 OAuth 持有人權杖複製到選擇性 [祕密權杖]  欄位。 
1. 選取 **測試連接**，讓 Azure Active Directory 嘗試連線到 SCIM 端點。 如果嘗試失敗，則會顯示錯誤的資訊。  

    > [!NOTE]
    > [測試連線]  會查詢 SCIM 端點中是否有不存在的使用者，並使用隨機 GUID 作為 Azure AD 設定中選取的比對屬性。 預期的正確回應是 HTTP 200 OK 和空白的 SCIM ListResponse 訊息

1. 如果嘗試連線到應用程式成功，然後選取**儲存**儲存管理員認證。
1. 在 [對應]  區段中，有兩組可選取的屬性對應：一個用於使用者物件，一個用於群組物件。 選取其中一個以檢閱從 Azure Active Directory 同步處理至應用程式的屬性。 選取為 [比對]  屬性的屬性會用來比對應用程式中的使用者和群組以進行更新作業。 選取 [儲存]  認可任何變更。
1. 在 [設定]  底下的 [範圍]  欄位定義哪些使用者或群組會進行同步處理。 選取  **「 只同步已指派使用者和群組**（建議） 在 指派使用者和群組只同步處理**使用者和群組** 索引標籤。
1. 一旦完成您的設定，設定**佈建狀態**要**上**。
1. 選取 **儲存**啟動 Azure AD 佈建服務。
1. 如果僅同步處理指派使用者和群組 （建議選項），請務必選取**使用者和群組**索引標籤，並將指派的使用者或您想要同步的群組。

一旦啟動初始同步處理之後，您可以選取**稽核記錄檔**在左窗格中來監視進度，其中顯示由您的應用程式上佈建服務的所有動作。 如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](check-status-user-account-provisioning.md)。

確認此範例的最後一個步驟是開啟 Windows 電腦上 \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug 資料夾中的 TargetFile.csv 檔案。 一旦執行佈建程序，此檔案會顯示所有指派和佈建的使用者和群組的詳細資料。

### <a name="development-libraries"></a>開發程式庫

若要開發自己符合 SCIM 規格的 Web 服務，請先熟悉下列 Microsoft 所提供、有助於加速開發程序的程式庫：

* 提供通用語言基礎結構 (CLI) 程式庫，可與以該基礎結構為基礎的語言 (例如 C#) 搭配使用。 其中一個程式庫，Microsoft.SystemForCrossDomainIdentityManagement.Service，宣告介面 Microsoft.SystemForCrossDomainIdentityManagement.IProvider，如下圖所示。 使用程式庫的開發人員會對某個類別實作該介面，一般稱為提供者。 程式庫可讓開發人員部署 web 服務符合 SCIM 規格。 Web 服務可以裝載在 Internet Information Services 或任何可執行的 CLI 組件。 要求會轉譯成對提供者的方法呼叫，該呼叫會由開發人員以程式方式設計，以對某些身分識別存放區進行操作。
  
   ![分解：將要求轉譯成提供者的方法呼叫][3]
  
* [ExpressRoute 處理常式](https://expressjs.com/guide/routing.html)可剖析代表對 node.js Web 服務發出之呼叫 (如 SCIM 規格所定義) 的 node.js 要求物件。

### <a name="building-a-custom-scim-endpoint"></a>建置自訂 SCIM 端點

使用 CLI 程式庫的開發人員可以其服務託管在任何可執行的 CLI 組件，或在網際網路資訊服務內。 以下是範例程式碼，此程式碼可將服務裝載於位於 http://localhost:9000: 位址的可執行組件內： 

   ```csharp
    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }
   ```

這項服務必須具有 HTTP 位址，而其伺服器驗證憑證的根憑證授權單位是下列其中一個名稱： 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

伺服器驗證憑證可以使用網路殼層公用程式繫結到 Windows 主機上的連接埠： 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

此處，對 certhash 引數提供的值為憑證指紋，而對 appid 引數提供的值為任意的全域唯一識別碼。  

若要裝載在網際網路資訊服務內的服務，開發人員會建置組件的預設命名空間中名為 Startup 的類別使用的 CLI 程式碼程式庫組件。  以下是這種類別的範例： 

   ```csharp
    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }
   ```

### <a name="handling-endpoint-authentication"></a>處理端點驗證

來自 Azure Active Directory 的要求包括 OAuth 2.0 持有人權杖。   接收要求的任何服務應該驗證簽發者為 Azure Active Directory 預期的 Azure Active Directory 租用戶，Azure Active Directory 圖形 web 服務的存取。  在權杖中，簽發者由用 iss 宣告，例如"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/ 」。  在此範例中，宣告值的基底位址 https://sts.windows.net 識別為簽發者的 Azure Active Directory，而相對位址區段 cbb1a5ac-f33b-45fa-9bf5-f37db0fed422，不會針對 Azure Active Directory 租用戶的唯一識別碼這發出的權杖。  如果發出的權杖要用於存取 Azure Active Directory 圖形 Web 服務，則該服務的識別項 00000002-0000-0000-c000-000000000000，應該位於權杖的 aud 宣告中的值。  每個在單一租用戶中註冊應用程式可能會收到相同`iss`SCIM 要求宣告。

使用 Microsoft 所提供來建置 SCIM 服務的 CLI 程式庫的開發人員可以驗證來自 Azure Active Directory 依照下列步驟使用 Microsoft.Owin.Security.ActiveDirectory 套件要求： 

1. 在提供者中，透過讓 Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior 屬性在每次服務啟動時傳回要呼叫的方法，來實作此屬性： 

   ```csharp
     public override Action<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration> StartupBehavior
     {
       get
       {
         return this.OnServiceStartup;
       }
     }

     private void OnServiceStartup(
       Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
       System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
     {
     }
   ```

1. 將下列程式碼新增至該方法，以對任何驗證為持有 Azure Active Directory 針對指定的租用戶，以存取 Azure AD 圖形 web 服務發出的權杖的服務任何的端點要求： 

   ```csharp
     private void OnServiceStartup(
       Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
       System.Web.Http.HttpConfiguration HttpConfiguration configuration)
     {
       // IFilter is defined in System.Web.Http.dll.  
       System.Web.Http.Filters.IFilter authorizationFilter = 
         new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

       // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
       // System.IdentityModel.Token.Jwt.dll.
       SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
         new TokenValidationParameters()
         {
           ValidAudience = "00000002-0000-0000-c000-000000000000"
         };

       // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
       // Microsoft.Owin.Security.ActiveDirectory.dll
       Microsoft.Owin.Security.ActiveDirectory.
       WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
         new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
         TokenValidationParameters = tokenValidationParameters,
         Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                       // identifier for this one.  
       };

       applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
     }
   ```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>處理佈建和解除佈建使用者

1. Azure Active Directory 會查詢服務是否有 externalId 屬性值與 Azure AD 中使用者的 mailNickname 屬性值相符的使用者。 查詢會以超文字傳輸通訊協定 (HTTP) 要求，例如此範例中，jyoung 是 Azure Active Directory 中使用者的 mailNickname 範例。

    >[!NOTE]
    > 這是僅為範例。 並非所有的使用者將具有 mailNickname 屬性，且使用者具有的值可能不是唯一的目錄中。 此外，用來比對 （即在此情況下是否有 externalId） 的屬性是可在中設定[Azure AD 屬性對應](customize-application-attributes.md)。

   ```
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
   ```
   如果服務建置使用 Microsoft 所提供用於實作 SCIM 服務的 CLI 程式庫，然後將要求轉譯為對服務的提供者的查詢方法的呼叫。  以下是該方法的簽章： 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);
   ```
   以下是 Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters 介面的定義： 
   ```csharp
    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }
   ```

   ```
     GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
     Authorization: Bearer ...
   ```

   如果是使用 Microsoft 所提供、用於實作 SCIM 服務的通用語言基礎結構程式庫建置服務，會將要求轉譯為對服務提供者的 Query 方法的呼叫。  以下是該方法的簽章： 

   ```csharp
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  
 
     System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]>  Query(
       Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
       string correlationIdentifier);
   ```

   以下是 Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters 介面的定義： 

   ```csharp
     public interface IQueryParameters: 
       Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
         System.Collections.Generic.IReadOnlyCollection  <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
         { get; }
     }

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
       system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
       { get; }
       System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
       { get; }
       string SchemaIdentifier 
       { get; }
     }

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
     {
         Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
           { get; set; }
         string AttributePath 
           { get; } 
         Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
           { get; }
         string ComparisonValue 
           { get; }
     }

     public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
     {
         Equals
     }
   ```

   在查詢具有給定值 externalId 屬性的使用者的下列範例中，傳遞至 Query 方法的引數值為： 
   * parameters.AlternateFilters.Count：1
   * parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
   * parameters.AlternateFilters.ElementAt(0).ComparisonOperator：ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"
   * correlationIdentifier：System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

1. 如果 web 服務有 externalId 屬性值符合使用者的 mailNickname 屬性值與使用者查詢的回應未傳回任何使用者，然後 Azure Active Directory 要求服務佈建使用者，對應至在 Azure Active Directory。  以下是這類要求的範例： 

   ```
    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/scim+json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}
   ```
   Microsoft 所提供用於實作 SCIM 服務的 CLI 程式庫會將該要求轉譯為對服務的提供者的 Create 方法的呼叫。  Create 方法具有此簽章： 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);
   ```
   在佈建使用者的要求中，資源引數的值會是 Microsoft.SystemForCrossDomainIdentityManagement 的執行個體。 Microsoft.SystemForCrossDomainIdentityManagement.Schemas 程式庫中定義的 Core2EnterpriseUser 類別。  如果佈建使用者的要求成功，則方法的實作應該會傳回 Microsoft.SystemForCrossDomainIdentityManagement 的執行個體。 Core2EnterpriseUser 類別，且其識別碼屬性值設定為新佈建使用者的唯一識別碼。  

1. 為了更新已知存在於前端為 SCIM 之身分識別存放區中的使用者，Azure Active Directory 會以類似下方的要求向服務要求該使用者的目前狀態，來繼續執行： 
   ```
    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
   ```
   在服務中使用 Microsoft 所提供用於實作 SCIM 服務的 CLI 程式庫所建置，將要求轉譯為對服務的提供者的 Retrieve 方法的呼叫。  以下是 Retrieve 方法的簽章： 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);

    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }
   ```
   在擷取使用者目前狀態之要求的範例中，提供作為參數引數值的物件具有的屬性值如下所示： 
  
   * 識別碼："54D382A4-2050-4C03-94D1-E769F1D15682"
   * SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

1. 如果要更新參考屬性，Azure Active Directory 查詢服務，以判斷服務前端的身分識別存放區中參考屬性目前值是否已經符合在 Azure Active 該屬性的值目錄。 對於使用者，以這種方式可查詢目前值的唯一屬性會是管理員屬性。 要判斷特定使用者物件的管理員屬性目前是否具有某個值之要求的範例如下： 

   如果服務建置使用 Microsoft 所提供用於實作 SCIM 服務的 CLI 程式庫，然後將要求轉譯為對服務的提供者的查詢方法的呼叫。 提供物件的屬性值作為參數引數的值，如下所示： 
  
   * parameters.AlternateFilters.Count：2
   * parameters.AlternateFilters.ElementAt(x).AttributePath：「識別碼」
   * parameters.AlternateFilters.ElementAt(x).ComparisonOperator：ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(x).ComparisonValue："54D382A4-2050-4C03-94D1-E769F1D15682"
   * parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
   * parameters.AlternateFilters.ElementAt(y).ComparisonOperator：ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(y).ComparisonValue："2819c223-7f76-453a-919d-413861904646"
   * parameters.RequestedAttributePaths.ElementAt(0)：「識別碼」
   * parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

   在這裡，索引 x 的值可以是 0 索引 y 的值可以是 1，或 x 的值可以是 1 而 y 的值可以是 0，視篩選查詢參數運算式中的順序而定。   

1. 以下是由 Azure Active Directory 對 SCIM 服務發出要求來更新使用者的範例： 
   ```
    PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/scim+json
    {
      "schemas": 
      [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations":
      [
        {
          "op":"Add",
          "path":"manager",
          "value":
            [
              {
                "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
   ```
   用於實作 SCIM 服務的 Microsoft CLI 程式庫會將要求轉譯為對服務的提供者的 Update 方法的呼叫。 以下是更新方法的簽章： 
   ```csharp
    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}
   ```

   如果是使用 Microsoft 所提供、用於實作 SCIM 服務的通用語言基礎結構程式庫建置服務，會將要求轉譯為對服務提供者的 Query 方法的呼叫。 提供物件的屬性值作為參數引數的值，如下所示： 
  
* parameters.AlternateFilters.Count：2
* parameters.AlternateFilters.ElementAt(x).AttributePath：「識別碼」
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator：ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue："54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator：ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue："2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0)：「識別碼」
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

  在這裡，索引 x 的值可以是 0 索引 y 的值可以是 1，或 x 的值可以是 1 而 y 的值可以是 0，視篩選查詢參數運算式中的順序而定。   

1. 以下是由 Azure Active Directory 對 SCIM 服務發出要求來更新使用者的範例： 

   ```
     PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
     Content-type: application/scim+json
     {
       "schemas": 
       [
         "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
       "Operations":
       [
         {
           "op":"Add",
           "path":"manager",
           "value":
             [
               {
                 "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                 "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
   ```

   用於實作 SCIM 服務的 Microsoft 通用語言基礎結構程式庫，會將要求轉譯為對服務提供者的 Update 方法的呼叫。 以下是更新方法的簽章： 

   ```csharp
     // System.Threading.Tasks.Tasks and 
     // System.Collections.Generic.IReadOnlyCollection<T>
     // are defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
     // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
     // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
     // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
     // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
     // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

     System.Threading.Tasks.Task Update(
       Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
       string correlationIdentifier);

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
     {
     Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
       PatchRequest 
         { get; set; }
     Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
       ResourceIdentifier 
         { get; set; }        
     }

     public class PatchRequest2: 
       Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
     {
     public System.Collections.Generic.IReadOnlyCollection
       <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
         Operations
         { get;}

     public void AddOperation(
       Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
     }

     public class PatchOperation
     {
     public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
       Name
       { get; set; }

     public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
       Path
       { get; set; }

     public System.Collections.Generic.IReadOnlyCollection
       <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
       { get; }

     public void AddValue(
       Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
     }

     public enum OperationName
     {
       Add,
       Remove,
       Replace
     }

     public interface IPath
     {
       string AttributePath { get; }
       System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
       Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
     }

     public class OperationValue
     {
       public string Reference
       { get; set; }

       public string Value
       { get; set; }
     }
   ```

    在更新使用者之要求的範例中，提供作為修補程式引數值的物件具有這些屬性值： 
  
   * ResourceIdentifier.Identifier："54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier："urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
   * (PatchRequest as PatchRequest2).Operations.Count：1
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName：OperationName.Add
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath: "manager"
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count：1
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value：2819c223-7f76-453a-919d-413861904646

1. 若要將使用者從前端為 SCIM 服務的身分識別存放區中取消佈建，Azure AD 會傳送像以下的要求： 

   ```
     DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
   ```

   如果是使用 Microsoft 所提供、用於實作 SCIM 服務的通用語言基礎結構程式庫建置服務，會將要求轉譯為對服務提供者的 Delete 方法的呼叫。   該方法具有此簽章： 

   ```csharp
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
     System.Threading.Tasks.Task Delete(
       Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
         resourceIdentifier, 
       string correlationIdentifier);
   ```

   提供作為 resourceIdentifier 引數值的物件，在要取消佈建使用者之要求的範例中，會具有這些屬性值： 

1. 若要將使用者從前端為 SCIM 服務的身分識別存放區中取消佈建，Azure AD 會傳送像以下的要求： 
   ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
   ````
   如果服務建置使用 Microsoft 所提供用於實作 SCIM 服務的 CLI 程式庫，然後將要求轉譯為對服務的提供者的 Delete 方法的呼叫。   該方法具有此簽章： 
   ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
   ````
   提供作為 resourceIdentifier 引數值的物件，在要取消佈建使用者之要求的範例中，會具有這些屬性值： 
  
   * ResourceIdentifier.Identifier："54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="user-and-group-schema-reference"></a>使用者和群組的結構描述參考

Azure Active Directory 可以佈建兩種類型的資源至 SCIM Web 服務。  這些類型的資源是使用者和群組。  

結構描述識別碼，識別使用者資源`urn:ietf:params:scim:schemas:extension:enterprise:2.0:User`，隨附於此通訊協定規格： https://tools.ietf.org/html/rfc7643 。  表 1 中提供的 Azure Active Directory 中使用者的使用者資源屬性的屬性的預設對應。  

用結構描述識別碼 `urn:ietf:params:scim:schemas:core:2.0:Group` 識別群組資源。 表 2 顯示的群組屬性的預設對應 Azure Active Directory 中群組資源的屬性。  

### <a name="table-1-default-user-attribute-mapping"></a>表 1：預設使用者屬性對應

| Azure Active Directory 使用者 | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |作用中 |
| displayName |displayName |
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |title |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |manager |
| mobile |phoneNumbers[type eq "mobile"].value |
| objectId |id |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |

### <a name="table-2-default-group-attribute-mapping"></a>表 2：預設預設屬性對應

| Azure Active Directory 群組 | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |externalId |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| members |members |
| objectId |id |
| proxyAddresses |emails[type eq "other"].Value |

## <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>允許 IP 位址用來提出 SCIM 佈建服務的 Azure AD

某些應用程式可讓其應用程式的流量。 為了讓 Azure AD 佈建服務如預期般運作，必須允許使用的 IP 位址。 如需每個服務標籤/地區的 IP 位址的清單，請參閱 JSON 檔案- [Azure IP 範圍和服務標籤 – 公用雲端](https://www.microsoft.com/download/details.aspx?id=56519)。 您可以下載，並視需要由程式設計納入您的防火牆這些 Ip。 Azure AD 佈建的保留的 IP 範圍可以找到 「 AzureActiveDirectoryDomainServices。 」

## <a name="related-articles"></a>相關文章

* [自動化 SaaS 應用程式使用者佈建/解除佈建](user-provisioning.md)
* [自訂使用者佈建的屬性對應](customize-application-attributes.md)
* [撰寫屬性對應的運算式](functions-for-customizing-application-data.md)
* [適用於使用者佈建的範圍篩選器](define-conditional-rules-for-provisioning-user-accounts.md)
* [帳戶佈建通知](user-provisioning.md)
* [如何整合 SaaS 應用程式的教學課程清單](../saas-apps/tutorial-list.md)

<!--Image references-->
[0]: ./media/use-scim-to-provision-users-and-groups/scim-figure-1.png
[1]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2a.png
[2]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2b.png
[3]: ./media/use-scim-to-provision-users-and-groups/scim-figure-3.png
[4]: ./media/use-scim-to-provision-users-and-groups/scim-figure-4.png
[5]: ./media/use-scim-to-provision-users-and-groups/scim-figure-5.png
