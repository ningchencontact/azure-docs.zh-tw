---
title: 驗證和授權 Azure 空間的錨點 |Microsoft Docs
description: 了解 Azure 空間的錨點，和您有 Azure 空間的錨點來限制存取權限的控制層級的各種方式可驗證的應用程式或服務。
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: e641025d49dd42125aa692925c0697235489b1db
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/29/2019
ms.locfileid: "66307148"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>驗證和授權 Azure 空間的錨點

在本節中，我們將討論您可以向 Azure 空間的起點，從您的應用程式或 web 服務的各種方式以及您可以在此使用 Azure Directory (Azure AD) 中的角色型存取控制的方式來控制對儲存空間的錨點帳戶存取。  

## <a name="overview"></a>概觀

![驗證 Azure 空間的錨點的概觀](./media/spatial-anchors-authentication-overview.png)

若要存取指定的 Azure 空間的錨點帳戶，用戶端需要先從 Azure 混合實境安全性權杖服務 (STS) 取得存取權杖。 從 STS 取得權杖即時 24 小時，，和包含空間的錨點服務帳戶進行授權決策，並確保只有已獲授權的主體可以存取該帳戶的資訊。 

從任一帳戶金鑰，或從 Azure AD 發出的權杖，您可以在 exchange 中取得存取權杖。 

帳戶金鑰可讓您快速開始使用 Azure 空間的錨點服務;不過，您應用程式部署至生產環境之前，建議您更新您的應用程式使用 Azure AD 為基礎的驗證。 

兩種方式可取得 azure AD 驗證權杖：

- 如果您要建置企業應用程式，且貴公司使用 Azure AD 作為其身分識別系統，您可以使用以使用者為基礎的 Azure AD 驗證，在您的應用程式和授與存取儲存空間的錨點帳戶，使用現有的 Azure AD 安全性群組，或直接指派給您組織中的使用者。 
- 否則，建議您從 web 服務，支援您的應用程式取得 Azure AD 權杖。 使用支援的 web 服務是生產應用程式，建議的驗證方法，因為它避免內嵌的認證在用戶端應用程式中存取 Azure 空間的錨點。 

## <a name="account-keys"></a>帳戶金鑰

使用 Azure 空間的錨點帳戶的存取權的帳戶金鑰是最簡單的方式，若要開始使用。 在 Azure 入口網站，您會發現您的帳戶金鑰。 瀏覽至您的帳戶，並選取 [金鑰] 索引標籤。

![驗證 Azure 空間的錨點的概觀](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


兩個金鑰都可以，這兩者都同時適用於存取空間的錨點帳戶。 建議您定期更新您用來存取帳戶的金鑰有兩個不同的有效金鑰啟用這類更新而不需要停機;您只需要或者更新主索引鍵和次要金鑰。 

SDK 有內建支援驗證與帳戶金鑰;您只需要 cloudSession 物件上設定 [AccountKey] 屬性。 

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

完成後，SDK 會處理交換存取權杖，帳戶金鑰，以及必要的應用程式的權杖快取。 

> [!WARNING] 
> 快速上架，但在開發/原型設計只建議使用帳戶金鑰。 強烈建議不寄送您的應用程式至生產環境中，使用內嵌的帳戶金鑰，並改為使用使用者或服務為基礎的 Azure AD 驗證方法所列的下一步。

## <a name="azure-ad-user-authentication"></a>Azure AD 使用者驗證

針對以 Azure Active Directory 使用者為目標的應用程式，建議的方法是使用 Azure AD 權杖的使用者，您可以使用下列文件中所述的 ADAL 程式庫取得： https://docs.microsoft.com/azure/active-directory/develop/v1-overview; 您應該遵循所列的步驟在 「 快速啟動 」，其中包括：

1. 在 Azure 入口網站中的組態
    1.  Azure AD 中註冊您的應用程式**原生應用程式**。 在註冊時，您必須判斷您的應用程式應該是多租用戶，並提供您的應用程式允許的 Url 重新導向。  
    2.  授與您的應用程式或使用者資源的存取權： 
        1.  瀏覽至您在 Azure 入口網站中的空間的錨點資源
        2.  若要切換**存取控制 (IAM)**  索引標籤
        3.  叫用**新增角色指派**
            1.  [選取角色](#role-based-access-control)
            2.  在 **選取**欄位中輸入的使用者、 群組和/或您要指派存取權的應用程式名稱。 
            3.  叫用**儲存**。
2. 在您的程式碼：
    1.  請務必使用**應用程式識別碼**並**重新導向 Uri**自己的 Azure AD 應用程式，做為**用戶端識別碼**並**RedirectUri**在 ADAL 參數
    2.  設定租用戶的資訊：
        1.  如果您的應用程式支援**我的組織僅**，將此值取代您**租用戶識別碼**或**租用戶名稱**(比方說，contoso.microsoft.com)
        2.  如果您的應用程式支援**任何組織的目錄中的帳戶**，將此值取代**組織**
        3.  如果您的應用程式支援**所有的 Microsoft 帳戶使用者**，將此值取代**常見**
    3.  在您的權杖要求，將**資源**到 「 https://sts.mixedreality.azure.com"。 此 「 資源 」 會指示 Azure AD 應用程式會要求 Azure 空間的錨點服務的權杖。  

一來，您的應用程式應該能夠從 ADAL 取得的 Azure AD 權杖;您可以設定為該 Azure AD 權杖**authenticationToken**您雲端的工作階段設定物件上。 

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

## <a name="azure-ad-service-authentication"></a>Azure AD 服務驗證

若要部署到生產環境中利用 Azure 空間的錨點的應用程式建議的選項是利用將 broker 驗證要求的後端服務。 在此圖中所述，應該是一般的配置：

![驗證 Azure 空間的錨點的概觀](./media/spatial-anchors-aad-authentication.png)

在這裡，它會假設您的應用程式會使用其本身的機制 (例如：Microsoft 帳戶、 PlayFab、 Facebook、 Google ID，自訂的使用者名稱/密碼等。）若要向其後端服務。 一旦您的使用者會向您的後端服務，服務可以擷取驗證 Azure AD 權杖，Azure 空間的錨點，將它交換存取權杖，並將它傳回用戶端應用程式。

下列文件中所述，使用 ADAL 程式庫來擷取 Azure AD 存取權杖： https://docs.microsoft.com/azure/active-directory/develop/v1-overview; 您應該遵循的步驟列在 「 快速啟動 」，其中包括：

1.  在 Azure 入口網站中的組態：
    1.  在 Azure AD 中註冊您的應用程式：
        1.  在 Azure 入口網站中，瀏覽至**Azure Active Directory**，然後選取**應用程式註冊**
        2.  選取**新增應用程式註冊**
        3.  輸入應用程式的名稱，選取**Web 應用程式 / API**做為應用程式類型，並輸入您的服務驗證 URL。 然後按下**建立**。
        4.  在該應用程式，叫用**設定**，然後選取**金鑰** 索引標籤。輸入您的金鑰的名稱、 選取持續時間，並點擊**儲存**。 請確定儲存在該時間中，會顯示金鑰值，因為您必須將它包含在您的 web 服務程式碼。
    2.  授與您的應用程式和/或使用者資源的存取權：
        1.  瀏覽至您在 Azure 入口網站中的空間的錨點資源
        2.  若要切換**存取控制 (IAM)**  索引標籤
        3.  叫用**新增角色指派**
        1.  [選取角色](#role-based-access-control)
        2.  在 **選取**欄位中，輸入您所建立的應用程式的名稱並以您想要指派存取。 如果您希望您的應用程式使用者有不同的角色，對空間的錨點帳戶，您應該在 Azure AD 中註冊多個應用程式，並指派給每個個別的角色。 然後實作您的授權邏輯，並使用正確的角色，為您的使用者。  
    3.  叫用**儲存**。
2.  在您的程式碼中 (請注意： 您可以使用包含在 GitHub 上的 「 服務 」 範例):
    1.  請務必使用的應用程式識別碼、 應用程式祕密，並重新導向 Uri，您自己的 Azure AD 應用程式，為用戶端識別碼、 祕密，並在 ADAL 的 RedirectUri 參數
    2.  將租用戶識別碼設為您自己 AAAzure 新增租用戶識別碼，在 adal 的授權參數
    3.  在您的權杖要求，將**資源**到 「 https://sts.mixedreality.azure.com" 

一來，您的後端服務可以擷取 Azure AD 權杖。 它可以再交換 MR 權杖，它會傳回至用戶端。 使用 Azure AD 權杖擷取 MR 語彙基元是透過 REST 呼叫。 以下是範例呼叫：

```
GET https://mrc-auth-prod.trafficmanager.net/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: mrc-auth-prod.trafficmanager.net
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

其中的授權標頭的格式如下： `Bearer <accoundId>:<accountKey>`

而回應包含 MR 語彙基元，以純文字。
 
然後會將該 MR 語彙基元傳回至用戶端。 用戶端應用程式可以接著將它設為其在雲端的工作階段組態中的存取權杖。

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

## <a name="role-based-access-control"></a>角色型存取控制

若要協助控制的存取層級授與應用程式、 服務或 Azure AD 使用者，您的服務，讓您視需要針對您的 Azure 空間的錨點帳戶指派，建立下列角色：

- **空間的錨點帳戶擁有者**： 應用程式或具有此角色的使用者就可以建立空間的錨點、 查詢，並加以刪除。 當您驗證您的帳戶使用帳戶金鑰**空間的錨點帳戶擁有者**角色會指派給已驗證的主體。 
- **空間的錨點帳戶參與者**： 應用程式或具有此角色的使用者能夠建立空間的錨點，為它們的查詢，但無法予以刪除。 
- **空間的錨點帳戶讀取器**： 應用程式或具有此角色的使用者才能夠查詢空間的錨點，但是您無法建立新的、 刪除現有的或更新空間的錨點上的中繼資料。 這通常用於應用程式，某些使用者籌備環境，而其他人可以只重新呼叫之前放置在該環境中的錨點。

## <a name="next-steps"></a>後續步驟

建立第一個應用程式與 Azure 空間的錨點。

> [!div class="nextstepaction"]
> [Unity](../unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)
