---
title: 完成群組或應用程式-Azure Active Directory 的存取權檢閱 |Microsoft Docs
description: 了解如何完成存取權檢閱的群組成員或在 Azure Active Directory 存取權檢閱的應用程式存取。
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
ms.subservice: compliance
ms.date: 05/22/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: bae204ec1789f227150adc560d4a292404d23b7e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66113330"
---
# <a name="complete-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>完成群組的存取權檢閱，或在 Azure AD 中的應用程式存取權檢閱

身為管理員，您[建立群組或應用程式的存取權檢閱](create-access-review.md)和 檢閱者[執行存取權檢閱](perform-access-review.md)。 本文說明如何檢視的存取權檢閱結果並套用結果。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>必要條件

- Azure AD Premium P2
- 全域管理員、 使用者管理員、 安全性系統管理員或安全性讀取者

如需詳細資訊，請參閱 <<c0> [ 哪些使用者必須有授權？](access-reviews-overview.md#which-users-must-have-licenses)。

## <a name="view-an-access-review"></a>檢視存取權檢閱

當檢閱者完成其檢閱時，您可以追蹤進度。

1. 登入 Azure 入口網站，然後開啟[存取權檢閱頁面](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)。

1. 在左側功能表中，按一下**存取權檢閱**。

1. 在清單中，按一下 存取權檢閱。

    若要檢視的存取權的一系列評論，瀏覽至 存取權檢閱，，您會發現即將推出的項目中已排程的檢閱。

    在 [**概觀**] 頁面上，您可以看到進度。 任何存取權限會不變更目錄中，直到完成檢閱。

    ![存取權檢閱進度](./media/complete-access-review/overview-progress.png)

1. 如果您想要停止存取權檢閱之前它已達到排程的結束日期，請按一下**停止** 按鈕。

    當停止檢閱、 檢閱者將不再提供回應。 檢閱停止後即無法重新開始。

1. 如果您不再想存取權檢閱，您可以加以刪除，請按一下**刪除** 按鈕。

## <a name="apply-the-changes"></a>套用變更

如果**自動將結果套用至資源**已啟用，而且根據您的選取項目，在**當設定完成時**，自動-適用於將檢閱的結束日期，或當您以手動方式停止檢閱之後執行。

如果**自動將結果套用至資源**未啟用此選項，供檢閱，按一下**套用**手動套用變更。 如果使用者的存取遭拒中檢閱，當您按一下 **套用**，Azure AD 會移除其成員資格或應用程式指派。

![套用存取權檢閱變更](./media/complete-access-review/apply-changes.png)

檢閱的狀態將會變更**已完成**歷經中繼狀態，例如**套用**最後狀態**結果套用**。 您應該會看到遭到拒絕的使用者 (若有的話)，在幾分鐘內從群組成員資格或應用程式指派中移除。

已設定的自動套用檢閱，或選取 [套用] 並不會影響源自內部部署目錄中的群組，或動態群組。 如果您想要變更源自於內部部署的群組，請下載結果，並將那些變更套用至該目錄中的群組圖像。

## <a name="retrieve-the-results"></a>擷取結果

若要檢視的單次存取權檢閱的結果，請按一下**結果**頁面。 若要檢視只是使用者的存取權，在搜尋方塊中，輸入顯示名稱或使用者的存取權檢閱的使用者主體名稱。

![擷取存取權檢閱的結果](./media/complete-access-review/retrieve-results.png)

若要檢視的週期性作用中的存取權檢閱進度，請按一下**結果**頁面。

若要檢視已完成的執行個體週期性的存取權檢閱的結果，請按一下**檢閱歷程記錄**，然後選取已完成的存取權檢閱執行個體，從清單中的特定執行個體根據執行個體的開始和結束日期。 這個執行個體的結果可以取自**結果**頁面。

若要擷取的存取權檢閱的所有結果，請按一下**下載** 按鈕。 可在 Excel 中，或開啟 UTF-8 編碼 CSV 檔案的其他程式中檢視產生的 CSV 檔案。

## <a name="remove-users-from-an-access-review"></a>移除存取權檢閱的使用者

 根據預設，已刪除的使用者會在 Azure AD 中以已刪除的狀態保留 30 天，在這段期間內，系統管理員可以視需要加以還原。  經過 30 天後，該使用者將永久刪除。  此外，全域管理員可以使用 Azure Active Directory 入口網站，在該時段結束之前明確地[永久刪除最近刪除的使用者](../fundamentals/active-directory-users-restore.md)。  使用者永久刪除後，關於該使用者的資料將會從作用中的存取權檢閱中移除。  與已刪除的使用者有關的稽核資訊仍會保留在稽核記錄中。

## <a name="next-steps"></a>後續步驟

- [使用 Azure AD 存取權檢閱來管理使用者存取權](manage-user-access-with-access-reviews.md)
- [使用 Azure AD 存取權檢閱來管理來賓存取權](manage-guest-access-with-access-reviews.md)
- [建立群組或應用程式的存取權檢閱](create-access-review.md)
- [在 Azure AD 管理角色中建立使用者的存取權檢閱](../privileged-identity-management/pim-how-to-start-security-review.md)
