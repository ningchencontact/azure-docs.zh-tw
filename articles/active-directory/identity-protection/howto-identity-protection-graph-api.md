---
title: Microsoft Graph API for Azure Active Directory Identity Protection
description: Learn how to query Microsoft Graph risk detections and associated information from Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: b3c7d2c6fe5a489415103a4da5daf707f9585f9d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212870"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>開始使用 Azure Active Directory Identity Protection 和 Microsoft Graph

Microsoft Graph 是 Microsoft 統一 API 端點，也是 [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) API 的寄居地。 There are four APIs that expose information about risky users and sign-ins. The first API, **riskDetection**, allows you to query Microsoft Graph for a list of both user and sign-in linked risk detections and associated information about the detection. 第二個 API **riskyUsers** 可讓您查詢 Microsoft Graph 中，Identity Protection 偵測為風險之使用者的相關資訊。 第三個 API **signIn** 可讓您查詢 Microsoft Graph 中，具有與風險狀態、詳細資料以及層級相關特定屬性之 Azure AD 登入的相關資訊。 The fourth API, **identityRiskEvents**, allows you to query Microsoft Graph for a list of [risk detections](../reports-monitoring/concept-risk-events.md) and associated information. This article gets you started with connecting to the Microsoft Graph and querying these APIs. 如需深入的簡介、完整文件以及 Graph 總管的存取權，請參閱 [Microsoft Graph 網站](https://graph.microsoft.io/)或這些 API 的特定參考文件：

* [riskDetection API](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta)
* [riskyUsers API](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)
* [signIn API](https://docs.microsoft.com/graph/api/resources/signin?view=graph-rest-beta)
* [identityRiskEvents API](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta)

## <a name="connect-to-microsoft-graph"></a>連線至 Microsoft Graph

透過 Microsoft Graph 存取 Identity Protection 資料需要四個步驟︰

1. 擷取網域名稱。
2. 建立新的應用程式註冊。 
3. 使用此密碼和其他幾項資訊向 Microsoft Graph 驗證，以從中收到驗證權杖。 
4. 使用此權杖對 API 端點發出要求，並取回 Identity Protection 資料。

開始之前，您需要下列項目：

* 系統管理員權限，以便在 Azure AD 中建立應用程式
* 租用戶網域的名稱 (例如 contoso.onmicrosoft.com)

## <a name="retrieve-your-domain-name"></a>擷取網域名稱 

1. 以系統管理員身分[登入](https://portal.azure.com) Azure 入口網站。 
1. 在左方的瀏覽窗格中，按一下 [Active Directory]。 

   ![建立應用程式](./media/howto-identity-protection-graph-api/41.png)

1. 在 [管理] 區段中，按一下 [屬性]。

   ![建立應用程式](./media/howto-identity-protection-graph-api/42.png)

1. 複製網域名稱。

## <a name="create-a-new-app-registration"></a>建立新的應用程式註冊

1. 在 [Active Directory] 頁面的 [管理] 區段中，按一下 [應用程式註冊]。

   ![建立應用程式](./media/howto-identity-protection-graph-api/42.png)

1. 在頂端功能表中，按一下 [新增應用程式註冊]。

   ![建立應用程式](./media/howto-identity-protection-graph-api/43.png)

1. 在 [建立] 頁面上，執行下列步驟：

   ![建立應用程式](./media/howto-identity-protection-graph-api/44.png)

   1. In the **Name** textbox, type a name for your application (for example: Azure AD Risk Detection API Application).

   1. 在 [類型]，選取 [Web 應用程式和/或 Web API]。

   1. 在 [登入 URL] 文字方塊中，輸入 `http://localhost`。

   1. 按一下 [建立]。
1. 若要開啟 [設定] 頁面，請在應用程式清單中，按一下新建立的應用程式註冊。 
1. 複製 [應用程式識別碼]。

## <a name="grant-your-application-permission-to-use-the-api"></a>授與您的應用程式使用 API 的權限

1. 在 [設定] 頁面中，按一下 [必要權限]。

   ![建立應用程式](./media/howto-identity-protection-graph-api/15.png)

1. 在 [必要權限] 頁面頂端的工具列中，按一下 [新增]。

   ![建立應用程式](./media/howto-identity-protection-graph-api/16.png)

1. 在 [新增 API 存取權] 頁面中，按一下 [選取 API]。

   ![建立應用程式](./media/howto-identity-protection-graph-api/17.png)

1. 在 [選取 API] 頁面中，選取 [Microsoft Graph]，然後按一下 [選取]。

   ![建立應用程式](./media/howto-identity-protection-graph-api/18.png)

1. 在 [新增 API 存取權] 頁面中，按一下 [選取權限]。

   ![建立應用程式](./media/howto-identity-protection-graph-api/19.png)

1. 在 [啟用存取權] 頁面中，按一下 [讀取所有身分識別風險資訊]，然後按一下 [選取]。

   ![建立應用程式](./media/howto-identity-protection-graph-api/20.png)

1. 在 [新增 API 存取權] 頁面中，按一下 [完成]。

   ![建立應用程式](./media/howto-identity-protection-graph-api/21.png)

1. 在 [必要權限] 頁面中，按一下 [授與權限]，然後按一下 [是]。

   ![建立應用程式](./media/howto-identity-protection-graph-api/22.png)

## <a name="get-an-access-key"></a>取得存取金鑰

1. 在 [設定] 頁面中，按一下 [金鑰]。

   ![建立應用程式](./media/howto-identity-protection-graph-api/23.png)

1. 在 [金鑰] 頁面中，執行下列步驟︰

   ![建立應用程式](./media/howto-identity-protection-graph-api/24.png)

   1. In the **Key description** textbox, type a description (for example, *Azure AD Risk Detection*).
   1. 在 [持續時間] 中選取 [1 年]。
   1. 按一下 [儲存]。
   1. 複製金鑰值，然後將它貼到安全的位置。   
   
   > [!NOTE]
   > 如果遺失此金鑰，則必須返回本區段並建立新的金鑰。 請勿將此金鑰告知他人︰任何人只要擁有此金鑰就可以存取您的資料。
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Authenticate to Microsoft Graph and query the Identity Risk Detections API

到目前為止，您應該已擁有︰

- 租用戶網域的名稱
- 用戶端識別碼 
- 金鑰 

若要驗證，請傳送 post 要求至 `https://login.microsoft.com` ，並在主體中加入下列參數︰

- grant_type: “**client_credentials**”
- resource：`https://graph.microsoft.com`
- client_id：\<您的用戶端識別碼\>
- client_secret：\<您的金鑰\>

如果成功，這會傳回驗證權杖。  
若要呼叫 API，請建立具有下列參數的標頭︰

```
`Authorization`="<token_type> <access_token>"
```

驗證時，您可以在傳回的權杖中找到權杖類型和存取權杖。

將此標頭做為要求來傳送至下列 API URL： `https://graph.microsoft.com/beta/identityRiskEvents`

The response, if successful, is a collection of identity risk detections and associated data in the OData JSON format, which can be parsed and handled as you see fit.

以下是使用 PowerShell 驗證並呼叫 API 的範例程式碼。  
您只需要新增用戶端識別碼、祕密金鑰和租用戶網域。

```PowerShell
    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 
```

## <a name="query-the-apis"></a>查詢 API

這三個 API 會提供許多機會，在您的組織中擷取有風險的使用者和登入的相關資訊。 以下是這些 API 和相關聯範例要求的一些常見使用案例。 您可以使用上述的範例程式碼，或使用 [Graph 總管](https://developer.microsoft.com/graph/graph-explorer)執行這些查詢。

### <a name="get-all-of-the-offline-risk-detections-riskdetection-api"></a>Get all of the offline risk detections (riskDetection API)

With Identity Protection sign-in risk policies, you can apply conditions when risk is detected in real time. But what about detections that are discovered offline? To understand what detections occurred offline, and thus would not have triggered the sign-in risk policy, you can query the riskDetection API.

```
GET https://graph.microsoft.com/beta/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-the-high-risk-and-medium-risk-detections-identityriskevents-api"></a>Get the high-risk and medium-risk detections (identityRiskEvents API)

Medium and high-risk detections represent those that may have the capability to trigger Identity Protection sign-in or user-risk policies. 這些事件對於嘗試登入的使用者不是合法的身分識別擁有者具有中度或高度的可能性，因此應該優先修復這些事件。 

```
GET https://graph.microsoft.com/beta/identityRiskEvents?`$filter=riskLevel eq 'high' or riskLevel eq 'medium'" 
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>取得成功通過有危險的登入原則所觸發之 MFA 挑戰的所有使用者 (riskyUsers API)

To understand the impact Identity Protection risk-based policies have on your organization, you can query all of the users who successfully passed an MFA challenge triggered by a risky sign-ins policy. 此資訊可協助您了解哪些使用者 Identity Protection 可能會被誤偵測為風險，以及哪些合法使用者可能正在執行 AI 認為有風險的動作。

```
GET https://graph.microsoft.com/beta/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

### <a name="get-all-the-risky-sign-ins-for-a-specific-user-signin-api"></a>取得特定使用者所有有風險的登入 (signIn API)

當您認為使用者可能遭到盜用時，您可以擷取所有有風險的登入，藉此更佳了解其風險的狀態。 

```
https://graph.microsoft.com/beta/identityRiskEvents?`$filter=userID eq '<userID>' and riskState eq 'atRisk'
```
## <a name="next-steps"></a>後續步驟

恭喜，您剛剛完成了對 Microsoft Graph 的第一次呼叫！  
Now you can query identity risk detections and use the data however you see fit.

若要深入了解 Microsoft Graph 以及如何使用 Graph API 來建置應用程式，請查看[說明文件](https://docs.microsoft.com/graph/overview)，而在 [Microsoft Graph 網站](https://developer.microsoft.com/graph)上還能找到更多資訊。 

如需相關資訊，請參閱：

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
- [Types of risk detections detected by Azure Active Directory Identity Protection](../reports-monitoring/concept-risk-events.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Microsoft Graph 概觀](https://developer.microsoft.com/graph/docs)
- [Azure AD Identity Protection 服務根目錄](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)
