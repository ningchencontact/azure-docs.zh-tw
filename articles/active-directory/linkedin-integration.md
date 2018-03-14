---
title: "在 Azure Active Directory 中為 Office 應用程式啟用或停用 LinkedIn 整合| Microsoft Docs"
description: "說明如何在 Azure Active Directory 中為 Microsoft 應用程式啟用或停用 LinkedIn 整合"
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 02/28/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: cdfb5458b020e9d3a3f33cecbeb0ee7b9a48909d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/02/2018
---
# <a name="linkedin-integration-for-office-applications"></a>Office 應用程式的 LinkedIn 整合
本文會說明如何限制在 Azure Active Directory (Azure AD) 中要為其提供 LinkedIn 整合的使用者。 LinkedIn 整合在新增至租用戶時會依預設啟用，讓使用者能在其某些 Microsoft 應用程式內存取公用 LinkedIn 資料。 每個使用者都可以獨立地選擇將其公司或學校帳戶連結到其 LinkedIn 帳戶。

> [!IMPORTANT]
> LinkedIn 整合不會同時部署至所有 Azure AD 租用戶。 LinkedIn 整合在部署到您的 Azure 租用戶之後，便會依預設啟用。 LinkedIn 整合無法供 go-local、sovereign 和 government 租用戶使用。 如需首度發行資訊的最新檢視，請參閱 [Office 365 藍圖](https://products.office.com/business/office-365-roadmap?filters=%26freeformsearch=linkedin#abc)頁面。

## <a name="linkedin-integration-from-the-user-perspective"></a>使用者觀點的 LinkedIn 整合
當組織中的使用者將其 LinkedIn 帳戶連結至其公司或學校帳戶時，即表示[他們允許 LinkedIn 提供資料](https://www.linkedin.com/help/linkedin/answer/84077)給組織所提供的 Microsoft 應用程式和服務使用。 [使用者可以中斷與帳戶的連結](https://www.linkedin.com/help/linkedin/answer/85097)，從而移除 LinkedIn 與 Microsoft 共用資料的權限。 LinkedIn 整合會使用公開可用的 LinkedIn 設定檔資訊。 [使用者可以控制自有 LinkedIn 設定檔的檢視方式](https://www.linkedin.com/help/linkedin/answer/83)，方法是使用 LinkedIn 隱私權設定 (包括是否可以在 Microsoft 應用程式中檢視其設定檔)。

## <a name="privacy-considerations"></a>隱私權考量
在 Azure AD 中啟用 LinkedIn 整合可讓 Microsoft 應用程式和服務存取使用者的某些 LinkedIn 資訊。 若要深入了解在 Azure AD 中啟用 LinkedIn 整合時的隱私權考量，請閱讀 [Microsoft 隱私權聲明](https://privacy.microsoft.com/privacystatement/)。 

## <a name="manage-linkedin-integration"></a>管理 LinkedIn 整合
Azure AD 中預設會啟用企業版的 LinkedIn 整合。 啟用 LinkedIn 整合可讓組織中的所有使用者在 Microsoft 服務內使用 LinkedIn 功能，例如在 Outlook 中檢視 LinkedIn 設定檔。 停用 LinkedIn 整合則會從 Microsoft 應用程式和服務移除 LinkedIn 功能，並停止透過 Microsoft 服務在 LinkedIn 與組織之間共用資料。

### <a name="enable-or-disable-linkedin-integration-for-your-organization-in-the-azure-portal"></a>在 Azure 入口網站中為組織啟用或停用 LinkedIn 整合

1. 使用具有 Azure AD 租用戶全域管理員身分的帳戶來登入 [Azure Active Directory 系統管理中心](https://aad.portal.azure.com/)。
2. 選取 [使用者]。
3. 在 [使用者] 刀鋒視窗上，選取 [使用者設定]。
4. 在 [LinkedIn 整合] 底下，選取 [是] 來啟用 LinkedIn 整合，或選取 [否] 來予以停用。
   ![啟用 LinkedIn 整合](./media/linkedin-integration/LinkedIn-integration.PNG)

### <a name="enable-or-disable-linkedin-integration-for-your-organizations-office-2016-apps-using-group-policy"></a>使用群組原則為組織的 Office 2016 應用程式啟用或停用 LinkedIn 整合

1. 下載 [Office 2016 系統管理範本檔案 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. 解壓縮 **ADMX** 檔案，並將其複製到**中央存放庫**。
3. 開啟群組原則管理。
4. 使用下列設定建立群組原則物件：[使用者設定] > [系統管理範本] > [Microsoft Office 2016] > [其他] > [允許 LinkedIn 整合]。
5. 選取 [啟用] 或 [停用]。
  * 當原則 [啟用] 時，在 Office 2016 [選項] 對話方塊中找到的 [在 Office 應用程式顯示 LinkedIn 功能] 設定也會啟用。 這也表示組織中的使用者可以在其 Office 應用程式中使用 LinkedIn 功能。
  * 當原則 [停用] 時，在 Office 2016 [選項] 對話方塊中找到的 [在 Office 應用程式顯示 LinkedIn 功能] 設定會設為停用狀態，且使用者無法變更此設定。 組織中的使用者無法在其 Office 2016 應用程式中使用 LinkedIn 功能。 

這個群組原則只會影響本機電腦的 Office 2016 應用程式。 即使使用者在其 Office 2016 應用程式中停用 LinkedIn，他們仍會在 Office 365 的設定檔卡中看到 LinkedIn 功能。 

### <a name="learn-more"></a>深入了解 
* [Microsoft 應用程式中的 LinkedIn 資訊和功能](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn 說明中心](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>後續步驟
使用下列連結在 Azure 入口網站中查看您目前的 LinkedIn 整合設定：

[設定 LinkedIn 整合](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 