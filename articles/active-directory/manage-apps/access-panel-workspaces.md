---
title: 在 Azure Active Directory 中建立我的應用程式入口網站的工作區 |Microsoft Docs
description: 使用我的應用程式工作區，為您的使用者自訂我的應用程式頁面，以提供更簡單的我的應用程式體驗。 將應用程式組織成具有不同索引標籤的群組。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a986c2032fc41bbd34230c5b0ead745f88b000f
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199821"
---
# <a name="create-workspaces-on-the-my-apps-preview-portal"></a>在我的應用程式（預覽）入口網站上建立工作區

您的使用者可以使用 [我的應用程式（預覽）] 入口網站來查看和啟動他們可以存取的雲端式應用程式。 根據預設，使用者可以存取的所有應用程式會在單一頁面上一起列出。 若要為您的使用者提供更好的組織此頁面，如果您有 Azure AD Premium P1 或 P2 授權，您可以設定工作區。 使用工作區，您可以將相關的應用程式群組在一起（例如，依作業角色、工作或專案），並將它們顯示在不同的索引標籤上。工作區基本上會將篩選套用至使用者已可存取的應用程式，因此使用者只會看到工作區中已指派給他們的應用程式。

> [!NOTE]
> 本文涵蓋系統管理員如何啟用和建立工作區。 如需有關如何使用我的應用程式入口網站和工作區的使用者資訊，請參閱[存取和使用工作區](https://docs.microsoft.com/azure/active-directory/user-help/my-applications-portal-workspaces)。

## <a name="enable-my-apps-preview-features"></a>啟用我的應用程式預覽功能

1. 開啟[**Azure 入口網站**](https://portal.azure.com/)，然後以使用者系統管理員或全域管理員身分登入。

2. 移至**Azure Active Directory** > **使用者設定**。

3. 在 [**使用者功能預覽**] 底下，選取 [**管理使用者功能預覽設定**]。

4. 在 [**使用者可以使用預覽功能進行我的應用程式**] 下，選擇下列其中一個選項：
   * 已**選取**-啟用特定群組的預覽功能。 使用 [**選取群組**] 選項，選取您要啟用預覽功能的群組。  
   * **全部**-為所有使用者啟用預覽功能。

   ![使用者預覽功能](media/access-panel-workspaces/user-preview-features.png)

> [!NOTE]
> 若要開啟我的應用程式入口網站，使用者可以使用連結 `https://myapps.microsoft.com` 或您組織的自訂連結，例如 `https://myapps.microsoft.com/contoso.com`。 如果使用者未重新導向至我的應用程式 preview 版本，使用者應該嘗試 `https://myapplications.microsoft.com` 或 `https://myapplications.microsoft.com/contoso.com`。

## <a name="create-a-workspace"></a>建立工作區

若要建立工作區，您必須擁有 Azure AD Premium P1 或 P2 授權。

1. 開啟[**Azure 入口網站**](https://portal.azure.com/)，並以具有 Azure AD Premium P1 或 P2 授權的系統管理員身分登入。

2. 移至**Azure Active Directory** > **企業應用程式**。

3. 在 [**管理**] 下，選取 **[工作區（預覽）** ]。

4. 選取 [**新增工作區**]。 在 [**新增工作區**] 頁面中，輸入工作區的**名稱**（我們建議您不要在名稱中使用「工作區」。 然後輸入**描述**。

   ![建立新的工作區](media/access-panel-workspaces/new-workspace.png)

5. 選取 [檢閱 + 建立]。 新工作區的屬性隨即出現。

6. 選取 [**應用程式**] 索引標籤。在 [**新增應用程式**] 下，選取您想要新增至工作區的所有應用程式，或使用 [**搜尋**] 方塊來尋找應用程式。 

   ![將應用程式新增至工作區](media/access-panel-workspaces/add-applications.png)

7. 選取 [新增]。 選取的應用程式清單隨即出現。 您可以使用向上和向下箭號來變更清單中的應用程式順序。

   ![工作區中的應用程式清單](media/access-panel-workspaces/add-applications-list.png)

8. 選取 [**使用者和群組**] 索引標籤。若要新增使用者或群組，請選取 [**新增使用者**]。 

9. 在 [**選取成員**] 頁面上，選取您想要指派工作區的使用者或群組。 或者，使用 [**搜尋**] 方塊來尋找使用者或群組。

   ![將使用者和群組指派給工作區](media/access-panel-workspaces/add-users-and-groups.png)

10. 當您完成選取 [使用者和群組] 時，請選擇 [**選取**]。

11. 若要將使用者的角色從 [**讀取] 存取**權變更為 [**擁有**者] 或 [相反]，請按一下 [目前] 角色，然後選取新角色。

    ![將角色指派給使用者和群組](media/access-panel-workspaces/users-groups-list-role.png)

## <a name="view-audit-logs"></a>檢視稽核記錄

Audit 記錄會記錄我的應用程式的工作區作業，包括工作區建立使用者動作。 從我的應用程式產生下列事件：

* 建立工作區
* 編輯工作區
* 刪除工作區
* 啟動應用程式（終端使用者）
* 自助應用程式新增（終端使用者）
* 自助式應用程式刪除（終端使用者）

您可以在 [活動] 區段中選取 [ **Azure Active Directory** > **企業應用程式**] > [**審核記錄**]，以存取[Azure 入口網站](https://portal.azure.com)中的 audit 記錄。 針對 [**服務**]，選取 [**我的應用程式**]。

   ![將角色指派給使用者和群組](media/access-panel-workspaces/audit-log-myapps.png)


## <a name="next-steps"></a>後續步驟
[Azure Active Directory 中應用程式的使用者體驗](end-user-experiences.md)