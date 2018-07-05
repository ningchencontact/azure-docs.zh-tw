---
title: 在 Azure Active Directory 中啟用 Microsoft 應用程式和服務的 LinkedIn 連線 | Microsoft Docs
description: 說明如何在 Azure Active Directory 中為 Microsoft 應用程式啟用或停用 LinkedIn 帳戶連線
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 06/28/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 4b3ff0b2481b42f516d28ac17f2616685730b7d5
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2018
ms.locfileid: "37109581"
---
# <a name="linkedin-account-connections-for-microsoft-apps-and-services"></a>Microsoft 應用程式和服務的 LinkedIn 帳戶連線
在本文中，您可以了解如何在 Azure Active Directory (Azure AD) 系統管理中心管理您租用戶的 LinkedIn 帳戶連線。 

> [!IMPORTANT]
> LinkedIn 帳戶連線功能即將推出以供 Azure AD 租用戶使用。 當它推出以供您的租用戶使用時，預設會加以啟用。 它不適用於美國政府客戶，以及將 Exchange Online 信箱裝載於澳洲、加拿大、中國、法國、德國、印度、南韓、英國、日本和南非的組織。 即將推出這些信箱位置的支援。  如需首度發行資訊的最新檢視，請參閱 [Office 365 藍圖](https://products.office.com/business/office-365-roadmap?filters=%26freeformsearch=linkedin#abc)頁面。

## <a name="benefit-to-users"></a>使用者的權益
當使用者連線他們的 LinkedIn 帳戶之後，就能使用 LinkedIn 資訊，在各種 Microsoft 應用程式或服務中顯示個人化的資訊與功能。 使用者可以在 Microsoft 個人檔案卡片中查看與其合作人員的見解，即使那些人員是組織外部人員也一樣。 經過一段時間之後，他們的 LinkedIn 體驗也將變得更加相關且更適合他們的工作。 例如，LinkedIn 可以根據使用者要合作的對象建議新的連線，或者在其當天的行事曆上呈現有關人員的見解。

## <a name="how-linkedin-account-connections-appear-to-the-user"></a>對使用者顯示 LinkedIn 帳戶連線的方式
LinkedIn 帳戶連線可讓使用者看到其某些 Microsoft 應用程式內的公用 LinkedIn 設定檔資訊。 您租用戶中的使用者可以選擇連線其 LinkedIn 和 Microsoft 公司或學校帳戶，以查看其他 LinkedIn 設定檔資訊。 如需詳細資訊，請參閱 [Microsoft 應用程式和服務中的 LinkedIn 資訊與功能](https://go.microsoft.com/fwlink/?linkid=850740)。

當貴組織中的使用者連線其 LinkedIn 和 Microsoft 公司或學校帳戶時，有兩個選項可用： 
* 賦予在兩個帳戶之間共用資料的權限。 這表示它們為其 LinkedIn 帳戶賦予與 Microsoft 公司或學校帳戶共用資料的權限，並為 Microsoft 公司或學校帳戶賦予與其 LinkedIn 帳戶共用資料的權限。 與 LinkedIn 共用的資料會離開線上服務。 
* 賦予只能將其 LinkedIn 帳戶的資料與 Microsoft 公司和學校帳戶共用的權限

如需有關在使用者的 LinkedIn 與 Microsoft 公司或學校帳戶之間共用之資料的詳細資訊，請參閱[公司或學校 Microsoft 應用程式中的 LinkedIn](https://www.linkedin.com/help/linkedin/answer/84077)。 
* [使用者可以將帳戶中斷連線](https://www.linkedin.com/help/linkedin/answer/85097)並隨時移除資料共用權限。 
* [使用者可以控制自有 LinkedIn 設定檔的檢視方式](https://www.linkedin.com/help/linkedin/answer/83)，包括是否可以在 Microsoft 應用程式中檢視其設定檔。

## <a name="privacy-considerations"></a>隱私權考量
啟用 LinkedIn 帳戶連線可讓 Microsoft 應用程式和服務存取使用者的某些 LinkedIn 資訊。 若要深入了解在 Azure AD 中啟用 LinkedIn 帳戶連線時的隱私權考量，請閱讀 [Microsoft 隱私權聲明](https://privacy.microsoft.com/privacystatement/)。 

## <a name="manage-linkedin-account-connections"></a>管理 LinkedIn 帳戶連線
預設會針對整個租用戶開啟 LinkedIn 帳戶連線功能。 您可以選擇停用整個租用戶的 LinkedIn 帳戶連線，或是針對您租用戶中所選的使用者啟用 LinkedIn 帳戶連線。 

### <a name="enable-or-disable-linkedin-account-connection-for-your-tenant-in-the-azure-portal"></a>在 Azure 入口網站中為租用戶啟用或停用 LinkedIn 帳戶連線

1. 使用具有 Azure AD 租用戶全域管理員身分的帳戶來登入 [Azure Active Directory 系統管理中心](https://aad.portal.azure.com/)。
2. 選取 [使用者]。
3. 在 [使用者] 刀鋒視窗上，選取 [使用者設定]。
4. 在 [LinkedIn 帳戶連線] 之下：
  * 選取 [是] 可啟用您租用戶中所有使用者的 LinkedIn 帳戶連線
  * 選取 [已選取] 只啟用所選租用戶使用者的 LinkedIn 帳戶連線
  * 選取 [否] 可停用所有使用者的 LinkedIn 帳戶連線 ![啟用 LinkedIn 帳戶連線](./media/linkedin-integration/linkedin-integration.png)
5. 當您完成時，選取 [儲存] 會儲存您的設定。

### <a name="enable-or-disable-linkedin-account-connections-for-your-organizations-office-2016-apps-using-group-policy"></a>使用群組原則為組織的 Office 2016 應用程式啟用或停用 LinkedIn 帳戶連線

1. 下載 [Office 2016 系統管理範本檔案 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. 解壓縮 **ADMX** 檔案，並將其複製到中央存放區。
3. 開啟群組原則管理。
4. 使用下列設定建立群組原則物件：[使用者設定] > [系統管理範本] > [Microsoft Office 2016] > [其他] > [在 Office 應用程式中顯示 LinkedIn 功能]。
5. 選取 [啟用] 或 [停用]。
  * 當原則 [啟用] 時，在 Office 2016 [選項] 對話方塊中找到的 [在 Office 應用程式顯示 LinkedIn 功能] 設定也會啟用。 這也表示組織中的使用者可以在其 Office 應用程式中使用 LinkedIn 功能。
  * 當原則 [停用] 時，在 Office 2016 [選項] 對話方塊中找到的 [在 Office 應用程式顯示 LinkedIn 功能] 設定會設為停用狀態，且使用者無法變更此設定。 組織中的使用者無法在其 Office 2016 應用程式中使用 LinkedIn 功能。

這個群組原則只會影響本機電腦的 Office 2016 應用程式。 即使使用者在其 Office 2016 應用程式中停用 LinkedIn，他們仍會在 Office 365 的設定檔卡中看到 LinkedIn 功能。 

### <a name="learn-more"></a>深入了解 
* [Microsoft 應用程式中的 LinkedIn 資訊和功能](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn 說明中心](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>後續步驟
使用下列連結，在 Azure 入口網站中查看您目前的 LinkedIn 帳戶連線設定：

[設定 LinkedIn 帳戶連線](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 