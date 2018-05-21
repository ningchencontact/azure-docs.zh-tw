---
title: 使用 Azure AD 報告 API 搭配憑證來取得資料 | Microsoft Docs
description: 說明如何使用 Azure AD 報告 API 搭配憑證認證來取得目錄中的資料，而不需使用者介入。
services: active-directory
documentationcenter: ''
author: ramical
writer: v-lorisc
manager: kannar
ms.assetid: ''
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/07/2018
ms.author: ramical
ms.openlocfilehash: 54e661284c539b835089e858ba7b5e0016e89a83
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
---
# <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>使用 Azure Active Directory 報告 API 搭配憑證來取得資料

[Azure Active Directory (Azure AD) 報告 API](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) 透過一組以 REST 為基礎的 API 為您提供資料的程式設計方式存取。 您可以從各種程式設計語言和工具呼叫這些 API。

如果您想要在沒有使用者介入的情況下存取 Azure AD 報告 API，您可以設定您的存取權以使用憑證

本文：

- 提供您使用憑證存取 Azure AD 報告 API 所需的步驟。
- 我們假設您已經完成[存取 Azure Active Directory 報告 API 的必要條件](active-directory-reporting-api-prerequisites-azure-portal.md)。 


若要使用憑證來存取報告 API，您需要：

1. 安裝必要條件
2. 在您的應用程式中設定憑證 
3. 授與權限
4. 取得存取權杖




如需原始程式碼的相關資訊，請參閱[運用報告 API 模組](https://github.com/AzureAD/azure-activedirectory-powershell/tree/gh-pages/Modules/AzureADUtils)。 

## <a name="install-prerequisites"></a>安裝必要條件

您必須已安裝 Azure AD PowerShell V2 和 AzureADUtils 模組。

1. 遵循 [Azure Active Directory PowerShell](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md) 的指示，下載並安裝 Azure AD Powershell V2。

2. 從 [AzureAD/azure-activedirectory-powershell](https://github.com/AzureAD/azure-activedirectory-powershell/blob/gh-pages/Modules/AzureADUtils/AzureADUtils.psm1) 下載 Azure AD Utils 模組。 
  此模組會提供數個公用程式 Cmdlet，包括︰
    - 最新版的 ADAL (使用 Nuget)
    - 從使用者、應用程式金鑰和憑證存取權杖 (使用 ADAL)
    - 處理分頁結果的圖形 API

**若要安裝 Azure AD Utils 模組：**

1. 建立目錄來儲存公用程式模組 (例如 c:\azureAD) 並從 GitHub 下載此模組。
2. 開啟 PowerShell 工作階段，然後移至您剛建立的目錄。 
3. 匯入模組，並使用 Install-AzureADUtilsModule Cmdlet 將它安裝於 PowerShell 模組路徑中。 

此工作階段看起來應類似這個畫面：

  ![Windows Powershell](./media/active-directory-report-api-with-certificates/windows-powershell.png)

## <a name="set-the-certificate-in-your-app"></a>在您的應用程式中設定憑證

**若要在您的應用程式中設定憑證：**

1. 從 Azure 入口網站針對應用程式[取得物件識別碼](active-directory-reporting-api-prerequisites-azure-portal.md#get-your-applications-client-id)。 

  ![Azure 入口網站](./media/active-directory-report-api-with-certificates/azure-portal.png)

2. 開啟 PowerShell 工作階段並使用 Connect-AzureAD Cmdlet 連線至 Azure AD。

  ![Azure 入口網站](./media/active-directory-report-api-with-certificates/connect-azuaread-cmdlet.png)

3. 使用 AzureADUtils 中的 New-AzureADApplicationCertificateCredential Cmdlet，將憑證認證新增至該模組。 

>[!Note]
>您必須提供先前擷取的應用程式物件識別碼，以及憑證物件 (使用 Cert: 磁碟機取得此物件)。
>


  ![Azure 入口網站](./media/active-directory-report-api-with-certificates/add-certificate-credential.png)
  
## <a name="get-an-access-token"></a>取得存取權杖

若要取得存取權杖，請使用 AzureADUtils 中的 **Get-AzureADGraphAPIAccessTokenFromCert** Cmdlet。 

>[!NOTE]
>您必須使用應用程式識別碼，而不是您在上一節中使用的物件識別碼。
>

 ![Azure 入口網站](./media/active-directory-report-api-with-certificates/application-id.png)

## <a name="use-the-access-token-to-call-the-graph-api"></a>使用存取權杖來呼叫圖形 API

您現在可以建立指令碼。 以下是使用 AzureADUtils 中 **Invoke-AzureADGraphAPIQuery** Cmdlet 的範例。 這個 Cmdlet 可處理多個分頁結果，然後再將這些結果傳送至 PowerShell 管線。 

 ![Azure 入口網站](./media/active-directory-report-api-with-certificates/script-completed.png)

您現在已準備好匯出至 CSV 並儲存至 SIEM 系統。 您也可以在排定的工作中包裝您的指令碼，以便定期從租用戶取得 Azure AD 資料，而不必將應用程式金鑰儲存在原始程式碼中。 

## <a name="next-steps"></a>後續步驟

- [取得報告 API 的第一印象](active-directory-reporting-api-getting-started-azure-portal.md#explore)

- [建立您自己的解決方案](active-directory-reporting-api-getting-started-azure-portal.md#customize)




