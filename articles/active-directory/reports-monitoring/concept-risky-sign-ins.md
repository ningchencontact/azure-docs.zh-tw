---
title: Azure Active Directory 入口網站中有風險的登入報告 | Microsoft Docs
description: 了解 Azure Active Directory 入口網站中有風險的登入報告
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40e125f8e1e7909c5866a03c0571f49ec42d690a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60287397"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Azure Active Directory 入口網站中有風險的登入報告

Azure Active Directory (Azure AD) 會偵測使用者帳戶相關的可疑動作。 針對每個偵測到的動作，將會建立一筆稱為**風險事件**的記錄。 如需詳細資訊，請參閱 [Azure AD 風險事件](concept-risk-events.md)。 

您可以從 [Azure 入口網站](https://portal.azure.com)中，藉由選取 [Azure Active Directory]  刀鋒視窗，然後瀏覽至 [安全性]  區段，來存取安全性報告。 

有兩個會根據風險事件來進行計算的不同安全性報告：

- **有風險的登入** - 有風險的登入表示非使用者帳戶合法擁有者的某人嘗試登入。

- **標幟為有風險的使用者** - 有風險的使用者表示可能被盜用的使用者帳戶。 

![有風險的登入](./media/concept-risky-sign-ins/10.png)

若要了解如何設定原則來觸發這些風險事件，請參閱[如何設定使用者風險原則](../identity-protection/howto-user-risk-policy.md)。  

## <a name="who-can-access-the-risky-sign-ins-report"></a>誰可以存取風險登入報表？

以下角色的使用者可以檢視風險登入報表：

- 安全性系統管理員
- 全域管理員
- 安全性讀取者

若要了解如何將系統管理角色指派給 Azure Active Directory 中的使用者，請參閱[在 Azure Active Directory 中檢視和指派系統管理員角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal)。

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>您需要哪項 Azure AD 授權才能存取安全性報告？  

所有 Azure AD 版本都會為您提供有風險的登入報告。 不過，報告細微性層級因版本而異： 

- 在 [Azure Active Directory Free 和 Basic 版本]  中，您會取得有風險的登入清單。 

- 此外，**Azure Active Directory Premium 1** 版本也可讓您檢查每份報告中所偵測到的某些基礎風險事件。 

- **Azure Active Directory Premium 2** 版本可提供有關所有基礎風險事件的最詳細資訊，也可讓您設定安全性原則，自動回應已設定的風險層級。

## <a name="risky-sign-ins-report-for-azure-ad-free-and-basic-edition"></a>Azure AD 免費和基本版本的有風險的登入報告

Azure AD 免費和基本版本會為您的使用者提供已偵測到的有風險登入清單。 每個記錄都包含下列屬性：

- **使用者** - 在登入作業期間所使用的使用者名稱。
- **IP** - 用來連線至 Azure Active Directory 的裝置 IP 位址。
- **位置** - 用來連線至 Azure Active Directory 的位置。 這是根據追蹤、登錄資料、反向查詢及其他資訊，儘可能取得的近似值。
- **登入時間** - 執行登入的時間
- **狀態** - 登入狀態

![有風險的登入](./media/concept-risky-sign-ins/01.png)

在調查有風險的登入後，您可以採取下列動作，以向 Azure AD 提供意見反應：

- 解決
- 標記為誤判
- 略過
- 重新啟動

![有風險的登入](./media/concept-risky-sign-ins/21.png)

此報告也會提供選項以便：

- 搜尋資源
- 下載報告資料

![有風險的登入](./media/concept-risky-sign-ins/93.png)

## <a name="risky-sign-ins-report-for-azure-ad-premium-editions"></a>Azure AD 進階版本的有風險的登入報告

Azure AD 進階版本中有風險的登入報告可為您提供：

- 關於已偵測到之[風險事件類型](concept-risk-events.md)的彙總資訊。 使用 **Azure AD Premium P1 版本**時，您授權未涵蓋的偵測會顯示為偵測到**有額外風險的登入**風險事件。 使用 **Azure AD Premium P2 版本**時，您會獲得有關所有基礎偵測的最詳細資訊。

- 下載報告的選項

![有風險的登入](./media/concept-risky-sign-ins/456.png)

當您選取風險事件時，即會取得這個風險事件的詳細報告檢視，讓您能夠：

- 選擇設定[使用者風險補救原則](../identity-protection/howto-user-risk-policy.md)  

- 檢閱風險事件的偵測時間軸  

- 檢閱已偵測到此風險事件的使用者清單

- 手動關閉風險事件。 

![有風險的登入](./media/concept-risky-sign-ins/457.png)

> [!IMPORTANT]
> 有時，您可能會發現在[登入報告](concept-sign-ins.md)中沒有相對應登入項目的風險事件。 這是因為 Identity Protection 會同時評估**互動式**和**非互動式**登入的風險，而登入報告則只會顯示互動式登入的部分。

當您選取使用者時，即會取得這位使用者的詳細報告檢視，讓您能夠：

- 開啟 [所有登入] 檢視

- 重設使用者的密碼

- 關閉所有事件

- 調查針對該使用者報告的風險事件。 

![有風險的登入](./media/concept-risky-sign-ins/324.png)

若要調查風險事件，請從清單中選取一項。  
這會開啟這個風險事件的 [詳細資料]  刀鋒視窗。 在 [詳細資料]  刀鋒視窗上，您可以選擇手動關閉風險事件或重新啟動已手動關閉的風險事件。 

![有風險的登入](./media/concept-risky-sign-ins/325.png)

## <a name="next-steps"></a>後續步驟

- [如何設定使用者風險原則](../identity-protection/howto-user-risk-policy.md)
- [如何設定風險補救原則](../identity-protection/howto-user-risk-policy.md)
- [風險事件類型](concept-risk-events.md)
