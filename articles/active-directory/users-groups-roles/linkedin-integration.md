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
ms.date: 04/29/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1130885cc3168582935264ffaad9fd7a8ba3c60b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2019
ms.locfileid: "64920246"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>整合 Azure Active Directory 中的 LinkedIn 帳戶連線

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

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>啟用在 Azure 入口網站中的 LinkedIn 帳戶連線

您可以啟用只有您想要從您組織中只選取使用者您整個組織的存取權，使用者的 LinkedIn 帳戶連線。

1. 登入[Azure AD 系統管理中心](https://aad.portal.azure.com/)的 Azure AD 組織的全域管理員身分的帳戶。
1. 選取 [使用者]。
1. 在 [使用者] 刀鋒視窗上，選取 [使用者設定]。
1. 底下**LinkedIn 帳戶連線**，允許使用者連線其帳戶來存取其某些 Microsoft 應用程式內的 LinkedIn 連線。 直到連接他們的帳戶的使用者同意不共用任何資料。

    * 選取 **是**來啟用您的組織中的所有使用者的服務
    * 選取  **Selected**可讓您組織中的所選使用者的群組
    * 選取  **No**来撤銷您的組織中的所有使用者的同意

    ![整合組織中的 LinkedIn 帳戶連線](./media/linkedin-integration/linkedin-integration.png)

1. 當您完成時，選取**儲存**以儲存設定。

> [!Important]
> LinkedIn 整合不是完全啟用為您的使用者上，直到他們同意將他們的帳戶。 當您啟用您的使用者帳戶連線時，會不共用任何資料。

### <a name="assign-selected-users-with-a-group"></a>指派選取的使用者與群組
我們已取代指定的使用者清單選取使用者群組，以便您可以讓您能夠連接單一的群組，而不是許多個別使用者的 LinkedIn 與 Microsoft 帳戶的選項 '選取' 選項。 如果您沒有針對選取的個別使用者啟用 LinkedIn 帳戶連線，您不需要採取任何動作。 如果您先前已啟用選取的個別使用者的 LinkedIn 帳戶連線，您應該：

1. 取得目前的個別使用者清單
1. 將目前已啟用的個別使用者移至群組
1. 為選取的群組，在 Azure AD 系統管理中心中設定的 LinkedIn 帳戶連線中使用從先前的群組。

> [!NOTE]
> 即使您未將您目前選取的個別使用者移至群組，他們仍然可以看到 Microsoft 應用程式中的 LinkedIn 資訊。

### <a name="get-the-current-list-of-selected-users"></a>取得目前的選取的使用者清單

1. 以系統管理員帳戶登入 Microsoft 365。
1. 移至 https://linkedinselectedusermigration.azurewebsites.net/。 您會看到 LinkedIn 帳戶連線為選取的使用者的清單。
1. 將清單匯出至 CSV 檔案。

### <a name="move-the-currently-selected-individual-users-to-a-group"></a>將目前選取的個別使用者移至群組

1. 啟動 PowerShell
1. 執行安裝 Azure AD 模組 `Install-Module AzureAD`
1. 執行下列指令碼：

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

若要使用的群組從第二個步驟在 Azure AD 系統管理中心中設定的 LinkedIn 帳戶連線中選取的群組，請參閱[在 Azure 入口網站中的啟用 LinkedIn 帳戶連線](#enable-linkedin-account-connections-in-the-azure-portal)。

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>使用群組原則來啟用 LinkedIn 帳戶連線

1. 下載 [Office 2016 系統管理範本檔案 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
1. 解壓縮 **ADMX** 檔案，並將其複製到中央存放區。
1. 開啟群組原則管理。
1. 使用下列設定建立群組原則物件：[使用者設定] > [系統管理範本] > [Microsoft Office 2016] > [其他] > [在 Office 應用程式中顯示 LinkedIn 功能]。
1. 選取 [啟用] 或 [停用]。
  
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
