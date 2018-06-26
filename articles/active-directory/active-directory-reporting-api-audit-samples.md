---
title: Azure Active Directory 報告稽核 API 範例 | Microsoft Docs
description: 如何開始使用 Azure Active Directory 報告 API
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: de8b8ec3-49b3-4aa8-93fb-e38f52c99743
ms.service: active-directory
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/30/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 240095b64f4b7b37a71f84ce8d7a3521e0ab7c03
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36223880"
---
# <a name="azure-active-directory-reporting-audit-api-samples"></a>Azure Active Directory 報告稽核 API 範例
本文章屬於 Azure Active Directory 報告 API 相關系列文章。  
Azure AD 報告提供的 API 可讓您使用程式碼或相關工具來存取稽核資料。
本文章的範疇是為您提供 **稽核 API** 的範例程式碼。

請參閱：

* [稽核記錄](active-directory-reporting-azure-portal.md#activity-reports) 以取得詳細概念資訊
* [開始使用 Azure Active Directory 報告 API](active-directory-reporting-api-getting-started.md) 以取得報告 API 的詳細資訊。

如有相關疑問、問題或意見，請連絡 [AAD 報告協助](mailto:aadreportinghelp@microsoft.com)。


## <a name="prerequisites"></a>先決條件
您必須先完成[存取 Azure AD 報告 API 的必要條件](active-directory-reporting-api-prerequisites.md)，才能使用本文中的範例。  

## <a name="known-issue"></a>已知問題
如果您的租用戶位於歐洲地區，「應用程式驗證」將無法運作。 請使用「使用者驗證」做為因應措施來存取稽核 API，直到我們修正此問題為止。 

## <a name="powershell-script"></a>PowerShell 指令碼


```powershell

# This script will require the Web Application and permissions setup in Azure Active Directory
$clientID       = "<appid>"             # ApplicationId
$clientSecret   = "<key>"         # Should be a ~44 character string insert your info here
$loginURL       = "https://login.windows.net/"
$tenantdomain   = "<domain>"            # For example, contoso.onmicrosoft.com
$msgraphEndpoint = "https://graph.microsoft.com"
$countOfAuditDocsToBeSavedInAFile = 2000
    
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$msgraphEndpoint;client_id=$clientID;client_secret=$clientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
    
if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    
    $url = "$msgraphEndpoint/beta/auditLogs/directoryAudits"
    Write-Output "Fetching data using Uri: $url"
    $i=0
    $docCount=0
    Do{
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        $jsonReport = ($myReport.Content | ConvertFrom-Json).value
        $fetchedRecordCount = $jsonReport.Count
        $docCount = $docCount + $fetchedRecordCount
        $totalFetchedRecordCount = $totalFetchedRecordCount + $fetchedRecordCount
        Write-Output "Fetched $fetchedRecordCount records and saved into Audits$i.json"
        if($docCount -le $countOfAuditDocsToBeSavedInAFile)
        {
            $myReport.Content | Out-File -FilePath Audits$i.json -append  -Force        
        }
        else
        {           
            $docCount=0
            $i = $i+1
        }
            
        #Get url from next link
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'         
    }while($url -ne $null)
    Write-Output "Total Fetched record count is : $totalFetchedRecordCount"
                
} else {
    Write-Host "ERROR: No Access Token"
}

```

### <a name="executing-the-powershell-script"></a>執行 PowerShell 指令碼
完成指令碼編輯後，加以執行並確認從稽核記錄報告傳回預期的資料。

指令碼會以 JSON 格式傳回稽核報告的輸出。 它也會建立具有相同輸出的 `Audits.json` 檔案。 您可透過修改指令碼以從其他報告傳回資料來進行實驗，以及取消註解您不需要的輸出格式。





## <a name="next-steps"></a>後續步驟
* 您要自訂本文中的範例嗎？ 請查看 [Azure Active Directory 稽核 API 參考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)。 
* 如果您想要查看使用 Azure Active Directory 報告 API 的完整概觀，請參閱 [開始使用 Azure Active Directory 報告 API](active-directory-reporting-api-getting-started.md)。
* 如果您想要深入了解 Azure Active Directory 報告，請參閱 [Azure Active Directory 報告指南](active-directory-reporting-guide.md)。  

