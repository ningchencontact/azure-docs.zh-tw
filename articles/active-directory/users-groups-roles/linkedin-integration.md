---
title: LinkedIn 帳戶連線-Azure Active Directory 的系統管理員同意 |Microsoft Docs
description: 說明如何啟用或停用 LinkedIn 整合帳戶連線，在 Microsoft Azure Active Directory 中的應用程式
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/21/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e07c53192ea2c8b792256af944c81c9c909dc55
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60469661"
---
# <a name="consent-to-linkedin-account-connections-for-an-azure-active-directory-organization"></a>同意 Azure Active Directory 組織的 LinkedIn 帳戶連線

您可以允許使用者存取其某些 Microsoft 應用程式內的 LinkedIn 連線組織中。 直到連接他們的帳戶的使用者同意不共用任何資料。 您可以整合 Azure Active Directory (Azure AD) 中的組織[管理中心](https://aad.portal.azure.com)。

> [!IMPORTANT]
> LinkedIn 帳戶連線設定目前正推出 Azure AD 的組織。 當它推出以供您的組織時，預設會啟用它。
> 
> 例外狀況：
> * 此設定不適用於使用 Microsoft Cloud for US Government、Microsoft Cloud Germany 或中國 21Vianet 管理之 Azure 和 Office 365 的客戶。
> * 對於佈建於德國的租用戶，此設定預設為關閉。 請注意，此設定不適用於使用 Microsoft Cloud Germany 的客戶。
> * 對於佈建於法國的租用戶，此設定預設為關閉。
>
> 一旦您的組織啟用 LinkedIn 帳戶連線之後使用者同意應用程式存取他們的名義上的公司資料,，也會運作帳戶連線。 使用者同意設定的相關資訊，請參閱[如何移除使用者的存取權給應用程式](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment)。

## <a name="use-the-azure-portal-to-enable-linkedin-account-connections"></a>使用 Azure 入口網站中啟用 LinkedIn 帳戶連線

您可以啟用只有您想要從您組織中只選取使用者您整個組織的存取權，使用者的 LinkedIn 帳戶連線。

1. 登入[Azure AD 系統管理中心](https://aad.portal.azure.com/)的 Azure AD 組織的全域管理員身分的帳戶。
2. 選取 [使用者]。
3. 在 [使用者] 刀鋒視窗上，選取 [使用者設定]。
4. 底下**LinkedIn 帳戶連線**，允許使用者連線其帳戶來存取其某些 Microsoft 應用程式內的 LinkedIn 連線。 直到連接他們的帳戶的使用者同意不共用任何資料。

  * 選取 **是**同意組織中的所有使用者的服務
  * 選取  **Selected**來組織中僅選取的使用者同意
  * 選取  **No**来撤銷您的組織中使用者的同意

    ![整合組織中的 LinkedIn 帳戶連線](./media/linkedin-integration/linkedin-integration.png)

5. 當您完成時，選取**儲存**以儲存設定。
     
## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>使用群組原則來啟用 LinkedIn 帳戶連線

1. 下載 [Office 2016 系統管理範本檔案 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. 解壓縮 **ADMX** 檔案，並將其複製到中央存放區。
3. 開啟群組原則管理。
4. 使用下列設定建立群組原則物件：[使用者設定] > [系統管理範本] > [Microsoft Office 2016] > [其他] > [在 Office 應用程式中顯示 LinkedIn 功能]。
5. 選取 [啟用] 或 [停用]。
  
   State | 效果
   ------ | ------
   **已啟用** | Office 2016 選項中的 [在 Office 應用程式中顯示 LinkedIn 功能] 設定已啟用。 在您的組織中的使用者可以使用 LinkedIn 功能，在其 Office 2016 應用程式。
   **Disabled** | Office 2016 選項中的 [在 Office 應用程式中顯示 LinkedIn 功能] 設定已停用，而且終端使用者無法變更此設定。 組織中的使用者無法在其 Office 2016 應用程式中使用 LinkedIn 功能。

這個群組原則只會影響本機電腦的 Office 2016 應用程式。 如果使用者在其 Office 2016 應用程式會停用 LinkedIn，他們仍然可以看到在 Office 365 中的 LinkedIn 功能。

## <a name="next-steps"></a>後續步驟

* [使用者同意和 LinkedIn 共用的資料](linkedin-user-consent.md)

* [Microsoft 應用程式中的 LinkedIn 資訊和功能](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn 說明中心](https://www.linkedin.com/help/linkedin)

* [在 Azure 入口網站中檢視您目前的 LinkedIn 整合設定](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
