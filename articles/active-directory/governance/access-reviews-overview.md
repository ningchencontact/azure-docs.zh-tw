---
title: 什麼是 Azure AD 存取權檢閱？ | Microsoft Docs
description: 您可以使用 Azure Active Directory 存取權檢閱，來控制群組成員資格和應用程式存取權，以符合貴組織的控管、風險管理和合規性計畫。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance
ms.date: 11/19/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: ecaceea42d64ab15676a9cb5a42ee8659e40d517
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2018
ms.locfileid: "52847281"
---
# <a name="what-are-azure-ad-access-reviews"></a>什麼是 Azure AD 存取權檢閱？

Azure Active Directory (Azure AD) 存取權檢閱可讓組織有效地管理群組成員資格、對企業應用程式的存取，以及角色指派。 您可以定期檢閱使用者的存取權，以確定只有適當的人員會具有持續存取權。

以下影片會快速地概述存取權檢閱：

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>存取權檢閱為何重要？

Azure AD 可讓您在組織內部以及與外部組織 (例如，合作夥伴) 的使用者共同作業。 使用者可以加入群組、邀請來賓、連線至雲端應用程式，以及從他們的工作或個人裝置遠端工作。 由於能夠方便地運用自助式服務的能力，因此需要更好的存取管理功能。

- 當有新員工加入時，如何確保他們會有適當的存取權，以便發揮生產力？
- 當有員工調動到其他小組或離職時，如何確保他們的舊有存取權會移除，特別是當此存取權涉及來賓時？
- 多餘的存取權限可能會導致稽核後果與洩露，因為這表示公司對於存取權的控制不夠嚴謹。
- 您必須主動與資源擁有者合作，以確保他們會定期檢閱可存取其資源的使用者。

## <a name="when-to-use-access-reviews"></a>何時要使用存取權檢閱？

- **太多使用者具有特殊權限角色：** 您最好檢查有多少使用者擁有系統管理存取權、其中有多少人是全域管理員，以及是否有任何受邀來賓或合作夥伴未在受指派執行系統管理工作之後移除。 您可以針對 [Azure AD 目錄角色](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) (例如，全域管理員) 或 [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) 體驗中的 [Azure 資源角色](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) (例如，使用者存取管理員)，重新認證其中的角色指派使用者。
- **無法自動化時：** 您可以在安全性群組或 Office 365 群組上建立動態成員資格的規則，但如果 HR 資料未放在 Azure AD 中，或如果使用者在離開群組之後依然需要存取權以便訓練其接替者呢？ 這時，您就可以在該群組上建立檢閱，以確保仍需要存取權的人員應該會具有持續存取權。
- **當群組用於新的用途時：** 如果您有要同步處理至 Azure AD 的群組，或如果您計劃讓銷售小組群組中的每個人都能使用 Salesforce 應用程式，則要求群組擁有者先檢閱群組成員資格，再將群組用於不同風險內容中的做法會很有用。
- **資料存取權：** 對於某些資源來說，您可能必須要求 IT 以外的人定期登出，並請他們提出需要存取權的理由，以便進行稽核。
- **為了維護原則的例外狀況清單：** 在理想的世界中，所有使用者都會遵循存取原則來安全地存取您組織的資源。 不過，有時候會有需要您視為例外狀況的商務案例。 身為 IT 系統管理員，您可以管理這項工作、免於監督原則例外狀況，並向稽核人員證明您有定期檢閱這些例外狀況。
- **要求群組擁有者確認他們的群組中仍然需要來賓：** 員工的存取權可透過某些內部部署 IAM 來自動指派，但受邀的來賓則不行。 如果有群組賦予來賓存取商務機密內容的權限，該群組的擁有者就有責任確認其來賓仍有合理獲得存取權的商務需求。
- **反覆定期檢閱：** 您可以設定以一定的頻率 (例如，每週、每月、每季或每年) 週期性地檢閱使用者的存取權，每次檢閱開始時，檢閱者都會收到通知。 檢閱者可以透過容易使用的介面與智慧建議的協助，來核准或拒絕存取權。

## <a name="where-do-you-create-reviews"></a>在哪裡建立檢閱？

根據所要檢閱的內容，可將存取權檢閱建立在 Azure AD 存取權檢閱、Azure AD 企業應用程式 (預覽版) 或 Azure AD PIM 中。

| 使用者的存取權限 | 檢閱者可以是 | 檢閱建立於 | 檢閱者體驗 |
| --- | --- | --- | --- |
| 安全性群組成員</br>Office 群組成員 | 指定的檢閱者</br>群組擁有者</br>自我檢閱 | Azure AD 存取權檢閱</br>Azure AD 群組 | 存取面板 |
| 指派給已連線的應用程式 | 指定的檢閱者</br>自我檢閱 | Azure AD 存取權檢閱</br>Azure AD 企業應用程式 (預覽版) | 存取面板 |
| Azure AD 目錄角色 | 指定的檢閱者</br>自我檢閱 | Azure AD PIM | Azure 入口網站 |
| Azure 資源角色 | 指定的檢閱者</br>自我檢閱 | Azure AD PIM | Azure 入口網站 |

## <a name="prerequisites"></a>必要條件

若要使用存取權檢閱，您必須具有下列其中一個授權：

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5 授權

如需詳細資訊，請參閱[操作說明：註冊 Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) 或 [Enterprise Mobility + Security E5 試用版](https://aka.ms/emse5trial)。

## <a name="get-started-with-access-reviews"></a>開始使用存取權檢閱

若要深入了解如何建立和執行存取權檢閱，請觀看這個簡短示範：

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

如果您已準備好在組織中部署存取權檢閱，請遵循影片中的這些步驟，以便上架、訓練系統管理員，以及建立您的第一個存取權檢閱！

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="enable-access-reviews"></a>啟用存取權檢閱

若要啟用存取權檢閱，請遵循下列步驟。

1. 請以全域管理員或使用者帳戶管理員的身分，登入要在其中使用存取權檢閱的 [Azure 入口網站](https://portal.azure.com)。

1. 按一下 [所有服務] 並尋找存取權檢閱服務。

    ![所有服務 - 存取權檢閱](./media/access-reviews-overview/all-services-access-reviews.png)

1. 按一下 [存取權檢閱]。

    ![存取權檢閱上架](./media/access-reviews-overview/onboard-button.png)

1. 在導覽清單中，按一下 [上架] 來開啟 [上架存取權檢閱] 頁面。

    ![上架存取權檢閱](./media/access-reviews-overview/onboard-access-reviews.png)

1. 按一下 [建立] 以在目前的目錄中啟用存取權檢閱。 下次您啟動存取權檢閱時，選項便會啟用。

    ![存取權檢閱已啟用](./media/access-reviews-overview/access-reviews-enabled.png)

## <a name="next-steps"></a>後續步驟

- [建立群組成員或應用程式存取的存取權檢閱](create-access-review.md)
- [在 Azure AD 管理角色中建立使用者的存取權檢閱](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [使用 Azure AD 存取權檢閱執行存取權檢閱](perform-access-review.md)
- [在 Azure AD 中，為群組成員或可存取應用程式的使用者完成存取權檢閱](complete-access-review.md)
