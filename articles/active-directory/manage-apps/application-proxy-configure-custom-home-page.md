---
title: 使用 Azure AD 應用程式 Proxy 為發佈的應用程式設定自訂首頁 | Microsoft Docs
description: 涵蓋 Azure AD 應用程式 Proxy 連接器的基本概念
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
ms.date: 05/23/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f4e71bd7fd7e0ed9a220619995ba108fdccabe4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66233756"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>使用 Azure AD 應用程式 Proxy 為發佈的應用程式設定自訂首頁

這篇文章討論如何設定應用程式以將使用者引導至自訂的首頁。 當您發行應用程式 Proxy 應用程式時，您設定內部 URL，但有時，不是使用者應該先看到的頁面。 設定自訂首頁，讓使用者存取應用程式時，會得到正確的頁面。 使用者會看到自訂的首頁上，您將設定，不論它們是否從 Azure Active Directory 存取面板或 Office 365 應用程式啟動器存取應用程式。

當使用者啟動應用程式時，他們導向至已發佈的應用程式的根網域 URL 的預設值。 登陸頁面通常設定為首頁 URL。 使用 Azure AD PowerShell 模組，當您想讓應用程式使用者抵達應用程式內的特定頁面時，定義自訂首頁 URL。

以下是說明為何您的公司會設定自訂的首頁上的其中一個案例：

- 使用者移至您的公司網路內部`https://ExpenseApp/login/login.aspx`登入並存取您的應用程式。
- 因為您有其他應用程式 Proxy 需要存取的資料夾結構的高層級的資產 （例如影像），您發行應用程式與`https://ExpenseApp`作為內部 URL。
- 預設外部 URL 是`https://ExpenseApp-contoso.msappproxy.net`，這並不需要外部使用者登入頁面。
- 您想要設定`https://ExpenseApp-contoso.msappproxy.net/login/login.aspx`為首頁 URL 相反的因此外部使用者會看到登入頁面第一次。

>[!NOTE]
>當您將已發佈應用程式的存取權提供給使用者時，應用程式會顯示在 [Azure AD 存取面板](../user-help/my-apps-portal-end-user-access.md)和 [Office 365 應用程式啟動器](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/)。

## <a name="before-you-start"></a>開始之前

設定首頁 URL 之前，請記住下列需求︰

- 您指定的路徑必須是根網域 URL 的子網域路徑。

  例如，如果根網域 URL `https://apps.contoso.com/app1/`，您設定首頁 URL 開頭必須`https://apps.contoso.com/app1/`。

- 若變更已發佈的應用程式，則變更可能會重設首頁 URL 的值。 當您未來更新應用程式時，您應該重新檢查並視需要更新首頁 URL。

您可以設定首頁 URL，透過 Azure 入口網站或使用 PowerShell。

## <a name="change-the-home-page-in-the-azure-portal"></a>在 Azure 入口網站中變更首頁

若要變更您的應用程式，透過 Azure AD 入口網站的首頁 URL，請遵循下列步驟：

1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
2. 選取  **Azure Active Directory**，然後**應用程式註冊**。 已註冊的應用程式清單隨即出現。
3. 從清單中選擇您的應用程式。 顯示已註冊的應用程式的詳細資料頁面隨即出現。
4. 底下**管理**，選取**商標**。
5. 更新**首頁 URL**使用新路徑。

   ![顯示 [首頁 URL] 欄位為已註冊的應用程式的商標設定頁面](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)
 
6. 選取 [ **儲存**]。

## <a name="change-the-home-page-with-powershell"></a>使用 PowerShell 變更首頁

若要設定使用 PowerShell 的應用程式的首頁上，您需要：

1. 安裝 Azure AD PowerShell 模組。
2. 尋找應用程式的 ObjectId 值。
3. 更新應用程式的首頁 URL，使用 PowerShell 命令。

### <a name="install-the-azure-ad-powershell-module"></a>安裝 Azure AD PowerShell 模組

使用 PowerShell 定義自訂首頁 URL 之前，請先安裝 Azure AD PowerShell 模組。 您可以從 [PowerShell 資源庫](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16)下載此套件，其使用圖形 API 端點。

若要安裝套件，請遵循下列步驟：

1. 開啟標準 PowerShell 視窗，然後執行下列命令：

   ```powershell
   Install-Module -Name AzureAD
   ```

    若您以非系統管理員身分執行此命令，請使用 `-scope currentuser` 選項。

2. 在安裝期間，選取 [Y]  以從 Nuget.org 安裝兩個套件。兩個套件都是必要套件。

### <a name="find-the-objectid-of-the-app"></a>尋找應用程式的 ObjectId

您可以依其顯示名稱或首頁搜尋應用程式，以取得應用程式的 ObjectId。

1. 在相同的 PowerShell 視窗中，匯入 Azure AD 模組。

   ```powershell
   Import-Module AzureAD
   ```

2. 以租用戶系統管理員身分登入 Azure AD 模組。

   ```powershell
   Connect-AzureAD
   ```

3. 尋找應用程式。 此範例使用 PowerShell 透過應用程式的顯示名稱搜尋來尋找 ObjectId `SharePoint`。

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   您應會取得如下所示的結果。 複製的 ObjectId GUID，在下一節中使用。

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   或者，您無法只提取所有的應用程式清單、 特定的顯示名稱或首頁上，應用程式清單中搜尋和複製應用程式的 ObjectId，一旦找到應用程式。

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>更新首頁 URL

建立首頁 URL，並使用該值來更新您的應用程式。 繼續使用相同的 PowerShell 視窗中，或如果您使用新的 PowerShell 視窗，登入一次使用 Azure AD 模組`Connect-AzureAD`。 接著，遵循下列步驟：

1. 建立變數來保存您在上一節中複製的 ObjectId 值。 （取代為此範例中使用 SharePoint 與您的應用程式的 ObjectId 值的 ObjectId 值）。

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

2. 請確認您有正確的應用程式，方法是執行下列命令。 輸出應該是您在上一節中看到的輸出完全相同 ([尋找應用程式的 ObjectId](#find-the-objectid-of-the-app))。

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

3. 建立空白應用程式物件以存放您要進行的變更。

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

4. 將首頁 URL 設定為您想要的值。 此值必須是已發佈應用程式的子網域路徑。 例如，如果您將首頁 URL 從 `https://sharepoint-iddemo.msappproxy.net/` 變更為 `https://sharepoint-iddemo.msappproxy.net/hybrid/`，則應用程式使用者會直接前往自訂首頁。

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

5. 請首頁上的更新。

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

6. 若要確認變更成功，請從步驟 2 再次執行下列命令。

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   我們的範例中，輸出現在應該會出現，如下所示：

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

7. 重新啟動應用程式，以確認首頁上顯示為第一個畫面中，如預期般運作。

>[!NOTE]
>您對應用程式所做的任何變更都可能會重設首頁 URL。 如果您的首頁 URL 重設，請重複本節中的步驟重新設定。

## <a name="next-steps"></a>後續步驟

- [使用 Azure AD 應用程式 Proxy 啟用 SharePoint 的遠端存取](application-proxy-integrate-with-sharepoint-server.md)
- [教學課程：Azure Active Directory 中新增透過應用程式 Proxy 遠端存取內部部署應用程式](application-proxy-add-on-premises-application.md)