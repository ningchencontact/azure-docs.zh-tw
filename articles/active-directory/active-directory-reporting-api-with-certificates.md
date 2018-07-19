---
title: 使用 Azure AD 報告 API 搭配憑證來取得資料 | Microsoft Docs
description: 說明如何使用 Azure AD 報告 API 搭配憑證認證來取得目錄中的資料，而不需使用者介入。
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance-reports
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 0da0e5d4b7dd8ff000d6c56716bea1b36935af01
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928901"
---
# <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>使用 Azure Active Directory 報告 API 搭配憑證來取得資料

[Azure Active Directory (Azure AD) 報告 API](active-directory-reporting-api-getting-started-azure-portal.md) 透過一組以 REST 為基礎的 API 為您提供資料的程式設計方式存取。 您可以從各種程式設計語言和工具呼叫這些 API。 如果您想要在沒有使用者介入的情況下存取 Azure AD 報告 API，則可以設定您的存取權以使用憑證。

請執行下列步驟：

1. [安裝必要條件](#install-prerequisites)
2. [在您的應用程式中註冊憑證](#register-the-certificate-in-your-app)
3. [取得 MS 圖形 API 的存取權杖](#get-an-access-token-for-ms-graph-api)
4. [查詢 MS 圖形 API 端點](#query-the-ms-graph-api-endpoints)


## <a name="install-prerequisites"></a>安裝必要條件

1. 首先，請確定您已經完成[存取 Azure Active Directory 報告 API 的必要條件](active-directory-reporting-api-prerequisites-azure-portal.md)。 

2. 遵循 [Azure Active Directory PowerShell](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md) 的指示，下載並安裝 Azure AD Powershell V2

3. 從 [PowerShellGallery - MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/) 安裝 MSCloudIDUtils。 此模組會提供數個公用程式 Cmdlet，包括︰
    - 驗證所需的 ADAL 程式庫
    - 從使用者、應用程式金鑰和憑證存取權杖 (使用 ADAL)
    - 處理分頁結果的圖形 API

4. 如果您第一次使用模組執行 **Install-MSCloudIdUtilsModule** 來完成設定，則只能使用 **Import-Module** Powershell 命令來匯入它。

您的工作階段看起來應該類似此畫面：

  ![Windows Powershell](./media/active-directory-reporting-api-with-certificates/module-install.png)

## <a name="register-the-certificate-in-your-app"></a>在您的應用程式中註冊憑證

1. 首先，請移至您的應用程式註冊頁面。 做法是巡覽至 [Azure 入口網站](https://portal.azure.com)，並按一下 [Azure Active Directory]，然後按一下 [應用程式註冊]，再從清單中選擇應用程式。 

2. 然後，遵循步驟以針對應用程式[向 Azure AD 註冊憑證](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-certificate-credentials#register-your-certificate-with-azure-ad)。 

3. 請記下應用程式識別碼，以及您剛剛向應用程式註冊的憑證指紋。 若要尋找指紋，請從入口網站的應用程式頁面中前往 [設定]，然後按一下 [金鑰]。 指紋將會在 [公開金鑰] 清單下方。

  
## <a name="get-an-access-token-for-ms-graph-api"></a>取得 MS 圖形 API 的存取權杖

若要取得 MS 圖形 API 的存取權杖，請使用 MSCloudIdUtils PowerShell 模組中的 **Get-MSCloudIdMSGraphAccessTokenFromCert** Cmdlet。 

>[!NOTE]
>您需要使用應用程式識別碼 (也稱為 ClientID) 以及具有電腦憑證存放區 (CurrentUser 或 LocalMachine 憑證存放區) 中所安裝私密金鑰之憑證的憑證指紋。
>

 ![Azure 入口網站](./media/active-directory-reporting-api-with-certificates/getaccesstoken.png)

## <a name="use-the-access-token-to-call-the-graph-api"></a>使用存取權杖來呼叫圖形 API

現在，您可以使用 Powershell 指令碼中的存取權杖來查詢圖形 API。 下列範例使用 MSCloudIDUtils 中的 **Invoke-MSCloudIdMSGraphQuery** Cmdlet，以列舉 signins 或 diectoryAudits 端點。 這個 Cmdlet 可處理多個分頁結果，然後再將這些結果傳送至 PowerShell 管線。

### <a name="query-the-directoryaudits-endpoint"></a>查詢 DirectoryAudits 端點
 ![Azure 入口網站](./media/active-directory-reporting-api-with-certificates/query-directoryAudits.png)

 ### <a name="query-the-signins-endpoint"></a>查詢 SignIns 端點
 ![Azure 入口網站](./media/active-directory-reporting-api-with-certificates/query-signins.png)

您現在可以選擇將此資料匯出為 CSV，並儲存至 SIEM 系統。 您也可以在排定的工作中包裝您的指令碼，以便定期從租用戶取得 Azure AD 資料，而不必將應用程式金鑰儲存在原始程式碼中。 


## <a name="next-steps"></a>後續步驟

- [取得報告 API 的第一印象](active-directory-reporting-api-getting-started-azure-portal.md#explore)

- [建立您自己的解決方案](active-directory-reporting-api-getting-started-azure-portal.md#customize)




