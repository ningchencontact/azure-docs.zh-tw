---
title: 同意您的組織-Azure Active Directory 的 LinkedIn 服務 |Microsoft Docs
description: 說明如何在 Azure Active Directory 中為 Microsoft 應用程式啟用或停用 LinkedIn 整合
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: abcb1696efe44293d01153aa37a9835ba5f43370
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199693"
---
# <a name="consent-to-linkedin-integration-for-your-azure-active-directory-organization"></a>同意您的 Azure Active Directory 組織的 LinkedIn 整合

在本文中，您可以了解如何啟用或停用 LinkedIn 整合，為您的組織在 Azure Active Directory (Azure AD) 系統管理中心。

> [!IMPORTANT]
> LinkedIn 整合設定目前正推出 Azure AD 的組織。 當它推出以供您的組織時，預設會啟用它。
> 
> 例外狀況：
> * 此設定不適用於使用 Microsoft Cloud for US Government、Microsoft Cloud Germany 或中國 21Vianet 管理之 Azure 和 Office 365 的客戶。
> * 對於佈建於德國的租用戶，此設定預設為關閉。 請注意，此設定不適用於使用 Microsoft Cloud Germany 的客戶。
> * 對於佈建於法國的租用戶，此設定預設為關閉。
>
> 只有當您有啟用整合的運作方式*和*使用者同意應用程式存取公司資料，其代表之後。 使用者同意設定的相關資訊，請參閱[如何移除使用者的存取權給應用程式](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment)。

## <a name="enable-or-disable-linkedin-integration-for-your-users-in-the-azure-portal"></a>在 Azure 入口網站中為使用者啟用或停用 LinkedIn 整合

您可以以啟用或停用整個租用戶的 LinkedIn 整合，或是僅針對您租用戶中選取的使用者。

1. 使用具有 Azure AD 租用戶全域管理員身分的帳戶來登入 [Azure Active Directory 系統管理中心](https://aad.portal.azure.com/)。
2. 選取 [使用者]。
3. 在 [使用者] 刀鋒視窗上，選取 [使用者設定]。
4. 在 [LinkedIn 帳戶連線] 之下：

   * 選取 **是**來連線他們的帳戶，以取得其某些 Microsoft 應用程式內的 LinkedIn 連絡人的存取權的組織中的所有使用者都同意。
   * 選取  **Selected**來同意只選取組織中的使用者連線其帳戶，以取得其某些 Microsoft 應用程式內的 LinkedIn 連絡人的存取權。
   * 選取  **No**提領同意您的組織中的使用者連線其帳戶，以取得其某些 Microsoft 應用程式內的 LinkedIn 連絡人的存取權。
    ![組織中啟用 LinkedIn 整合](./media/linkedin-integration/linkedin-integration.png)
5. 當您完成時，選取 [儲存] 會儲存您的設定。

## <a name="enable-or-disable-linkedin-integration-for-your-users-in-group-policy"></a>在群組原則中為使用者啟用或停用 LinkedIn 整合

1. 下載 [Office 2016 系統管理範本檔案 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. 解壓縮 **ADMX** 檔案，並將其複製到中央存放區。
3. 開啟群組原則管理。
4. 使用下列設定建立群組原則物件：[使用者設定] > [系統管理範本] > [Microsoft Office 2016] > [其他] > [在 Office 應用程式中顯示 LinkedIn 功能]。
5. 選取 [啟用] 或 [停用]。
  
   State | 效果
   ------ | ------
   **已啟用** | Office 2016 選項中的 [在 Office 應用程式中顯示 LinkedIn 功能] 設定已啟用。 組織中的使用者可在其 Office 應用程式中使用 LinkedIn 功能。
   **已停用** | Office 2016 選項中的 [在 Office 應用程式中顯示 LinkedIn 功能] 設定已停用，而且終端使用者無法變更此設定。 組織中的使用者無法在其 Office 2016 應用程式中使用 LinkedIn 功能。

這個群組原則只會影響本機電腦的 Office 2016 應用程式。 即使使用者在其 Office 2016 應用程式中停用 LinkedIn，他們仍會在 Office 365 的設定檔卡中看到 LinkedIn 功能。

## <a name="learn-more"></a>深入了解

* [在您的組織中整合 LinkedIn](linkedin-user-consent.md)

* [Microsoft 應用程式中的 LinkedIn 資訊和功能](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn 說明中心](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>後續步驟

使用下列連結在 Azure 入口網站中查看您目前的 LinkedIn 整合設定：

[在 Azure 入口網站中檢視您目前的 LinkedIn 整合設定](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
