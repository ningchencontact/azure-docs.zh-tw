---
title: 部署管理工具 - Azure
description: 如何安裝使用者介面工具以管理 Windows 虛擬桌面預覽版資源。
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: v-chjenk
ms.openlocfilehash: 275fec5fb696a7e1352bbddccd288863e984b796
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304501"
---
# <a name="tutorial-deploy-a-management-tool"></a>教學課程：部署管理工具

管理工具提供使用者介面 (UI) 讓您管理 Microsoft 虛擬桌面預覽版資源。 在此教學課程中，您將學到如何部署及連線到管理工具。

>[!NOTE]
>這些指示適用於可搭配您組織現有程序使用的 Windows 虛擬桌面預覽版特定設定。

## <a name="important-considerations"></a>重要考量︰

因為應用程式需要獲得同意才能與 Windows 虛擬桌面互動，此工具不支援企業對企業 (B2B) 案例。 每個 Azure Active Directory (AAD) 租用戶的訂閱帳戶都需要它自己的獨立管理工具部署。

這個管理工具很簡單。 Microsoft 將提供重要安全性與品質更新。 [原始程式碼位於 GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy)\(英文\)。 我們鼓勵客戶與合作夥伴根據其業務需求來自訂此工具。

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>執行 Azure Resource Manager 範本需要什麼

部署 Azure Resource Manager 範本之前，您將需要 Azure Active Directory 使用者以部署管理 UI。 此使用者必須：

- 停用 Azure Multi-Factor Authentication (MFA)
- 擁有在您的 Azure 訂用帳戶中建立資源的權限
- 擁有建立 Azure AD 應用程式的權限。 依照這些步驟來檢查使用者是否有[必要權限](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions) \(部分機器翻譯\)。

部署 Azure Resource Manager 範本之後，您將必須啟動管理 UI 以進行驗證。 此使用者必須：
- 獲指派角色可檢視或編輯您的 Windows 虛擬桌面租用戶

## <a name="run-the-azure-resource-manager-template-to-provision-the-management-ui"></a>執行 Azure Resource Manager 範本以佈建管理 UI

在您開始之前，請確定您已透過瀏覽 Azure Active Directory (AAD) 呈現之 [Windows 虛擬桌面同意頁面](https://rdweb.wvd.microsoft.com) 來同意伺服器與用戶端應用程式。

依照下列指示部署 Azure 資源管理範本：

1. 移至 [GitHub Azure RDS 範本頁面](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy) \(英文\)。
2. 將範本部署到 Azure。
    - 若您是在「企業」訂用帳戶中部署，請向下捲動並選取 [部署至 Azure]  。 請參閱[範本參數的指導方針](#guidance-for-template-parameters)。
    - 若您是在「雲端解決方案提供者」訂用帳戶中部署，請依照下列指示部署到 Azure：
        1. 向下捲動並以滑鼠右鍵按一下 [部署至 Azure]  ，然後選取 [複製連結位置]  。
        2. 開啟文字編輯器 (例如 [記事本])，並在該處貼上連結。
        3. 在 <https://portal.azure.com/> 後方、主題標籤 (#) 前面，輸入 at 符號 (@)，後面接著租用戶網域名稱。 格式範例如下：<https://portal.azure.com/@Contoso.onmicrosoft.com#create/>。
        4. 以具有「雲端解決方案提供者」訂用帳戶系統管理員/參與者權限的使用者身分登入 Azure 入口網站。
        5. 將您之前複製到文字編輯器的連結貼到網址列。

### <a name="guidance-for-template-parameters"></a>範本參數的指導方針
以下說明如何輸入參數來設定工具：

- 這是 RD 訊息代理程式 URL： <https://rdbroker.wvd.microsoft.com/>
- 這是資源 URL： <https://mrs-prod.ame.gbl/mrs-RDInfra-prod>
- 在已停用 MFA 的情況下使用您的 AAD 認證來登入 Azure。 請參閱[執行 Azure Resource Manager 範本需要什麼](#what-you-need-to-run-the-azure-resource-manager-template)。
- 為應用程式使用將在您的 Azure Active Directory 中為管理工具註冊的為依名稱，例如 Apr3UX。

## <a name="provide-consent-for-the-management-tool"></a>為管理工具提供同意

在 GitHub Azure Resource Manager 範本完成之後，您將會發現資源群組包含兩個應用程式服務與一個應用程式服務方案 (在 Azure 入口網站中)。

在您登入並使用管理工具之前，您將必須為與管理工具關聯的新 Azure Active Directory 應用程式提供同意。 一旦提供同意，即表示您允許管理工具代表已登入入口網站的使用者發出 Windows 虛擬桌面管理呼叫。

![顯示當您同意 UI 管理工具時提供之權限的螢幕擷取畫面。](media/management-ui-delegated-permissions.png)

若要判斷您可以使用哪個使用者登入工具，請移至您的 [Azure Active Directory 使用者設定頁面](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)並記下 [使用者可同意應用程式代表自己存取公司資料]  的值。

![顯示使用者是否可以授與同意給應用程式 (僅針對其使用者) 的螢幕擷取畫面。](media/management-ui-user-consent-allowed.png)

- 若該值設定為 [是]  ，您可以使用 Azure Active Directory 中的任何使用者登入並僅針對該使用者提供同意。 不過，若您稍後使用不同的使用者登入管理工具，您必須重新執行相同的同意程序。
- 若該值設定為 [否]  ，您必須使用 Azure Active Directory 中的「全域管理員」登入並針對該目錄中的所有使用者提供管理員同意。 不會有任何其他使用者看到同意提示。


一旦您決定要用來提供同意的使用者請依照下列指示提供同意給工具：

1. 移至您的 Azure 資源、選取具有您在範本中提供之名稱 (例如 Apr3UX) 的 Azure App Services 資源並瀏覽到與它關聯的 URL，例如 <https://rdmimgmtweb-210520190304.azurewebsites.net>。
2. 使用適當的 Azure Active Directory 使用者帳戶登入。
3. 若使用「全域管理員」來驗證，您現在可以選取 [代表您的組織同意]  核取方塊。 選取 [接受]  以提供同意。
   
   ![顯示使用者或系統管理員將看到之完整同意頁面的螢幕擷取畫面。](media/management-ui-consent-page.png)

這現在會將您帶到管理工具。

## <a name="use-the-management-tool"></a>使用管理工具

為組織或指定使用者提供同意之後，您可以隨時存取管理工具。

依照下列指示啟動工具：

1. 選取具有您在範本中提供之名稱 (例如 Apr3UX) 的 Azure App Services 資源並瀏覽到與它關聯的 URL，例如 <https://rdmimgmtweb-210520190304.azurewebsites.net>。
2. 使用您的 Windows 虛擬桌面認證登入。
3. 當系統提示您選擇租用戶群組時，請從下拉式清單選取 [預設租用戶群組]  。

> [!NOTE]
> 若您有自訂租用戶群組，請手動輸入名稱，而不要從下拉式清單選擇。

## <a name="next-steps"></a>後續步驟

既然您已學到如何部署並連線到管理工具，您可以學習如何使用 Azure 服務健康狀態來監視服務問題與健康情況諮詢。

> [!div class="nextstepaction"]
> [設定服務警示教學課程](./set-up-service-alerts.md)
