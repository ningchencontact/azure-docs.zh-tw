---
title: 擷取群組或應用程式存取權檢閱-Azure Active Directory 中的存取權檢閱結果 |Microsoft Docs
description: 了解如何擷取群組成員或在 Azure Active Directory 存取權檢閱的應用程式存取權的存取權檢閱結果。
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
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: eae4bafb3eefcee2785c784030d7be8dde66988e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60245831"
---
# <a name="retrieve-access-review-results-for-groups-or-applications-in-azure-ad-access-reviews"></a>擷取存取權檢閱結果的群組或 Azure AD 中的應用程式存取權檢閱

系統管理員可以使用 Azure Active Directory (Azure AD) 為群組成員或指派給應用程式的或使用者[建立存取權檢閱](create-access-review.md)。  中的使用者**全域管理員**，**使用者系統管理員**，**安全性系統管理員**或是**安全性讀取者**角色也可以讀取存取權檢閱的結果。  若要將使用者指派給其中一個角色，特殊權限的角色系統管理員可以使用 Azure AD PIM 讓使用者能夠啟用角色，或全域管理員可以永久[將使用者指派給角色](../fundamentals/active-directory-users-assign-role-azure-portal.md)。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="locating-an-access-review"></a>找出存取權檢閱

如果您知道哪個程式包含存取權檢閱，請移至存取權檢閱頁面，選取 [程式集]，並選取包含存取權檢閱控制項的程式。  然後，選取 [控制項]，再選取存取權檢閱控制項。 如果程式中有許多控制項，您可以篩選特定類型的控制項，並依其狀態排序。 您也可以依據存取權檢閱控制項的名稱或原先建立它之擁有者的顯示名稱來搜尋。 

如果您不知道哪個程式包含存取權檢閱，請移至存取權檢閱頁面，然後選取 [控制項]。  您可以篩選出特定類型的控制項，並按其狀態排序，您也可以按存取權檢閱控制項的名稱或建立它之擁有者的顯示名稱進行搜尋。 

## <a name="retrieving-the-results-for-a-one-time-access-review"></a>擷取一次性存取權檢閱的結果

如果檢閱週期類型是一次性的，則可以從 [結果] 區段取得作用中存取權檢閱的進度，和已完成的存取權檢閱的結果。  您可以輸入存取權要受到檢閱之使用者的顯示名稱或使用者主體名稱，而僅檢視該使用者的存取權。  若要擷取已完成的存取權檢閱的所有結果，請按一下 [下載] 按鈕。

## <a name="retrieving-the-results-for-multiple-instances-of-a-recurring-access-review"></a>擷取週期性存取權檢閱的多個執行個體的結果

若要檢視具有週期性的作用中存取權檢閱的進度，請按一下 [結果] 區段。  您可以輸入存取權要受到檢閱之使用者的顯示名稱或使用者主體名稱。

若要檢視具有週期性且已完成的存取權檢閱執行個體的結果，請選取 [檢閱歷程記錄]，然後根據執行個體的開始和結束日期，從已完成的存取權檢閱執行個體清單中選取特定的執行個體。   此執行個體的結果可從 [結果] 區段中取得。  您可以輸入存取權要受到檢閱之使用者的顯示名稱或使用者主體名稱，而僅檢視該使用者的存取權。  若要擷取已完成的週期性存取權檢閱執行個體的所有結果，請按一下 [下載] 按鈕。


## <a name="removing-users-from-an-access-review"></a>從存取權檢閱中移除使用者

根據預設，已刪除的使用者會在 Azure AD 中以已刪除的狀態保留 30 天，在這段期間內，系統管理員可以視需要加以還原。  經過 30 天後，該使用者將永久刪除。  此外，全域管理員可以使用 Azure Active Directory 入口網站，在該時段結束之前明確地[永久刪除最近刪除的使用者](../fundamentals/active-directory-users-restore.md)。  使用者永久刪除後，關於該使用者的資料將會從作用中的存取權檢閱中移除。  與已刪除的使用者有關的稽核資訊仍會保留在稽核記錄中。

## <a name="next-steps"></a>後續步驟

- [使用 Azure AD 存取權檢閱來管理使用者存取權](manage-user-access-with-access-reviews.md)
- [使用 Azure AD 存取權檢閱來管理來賓存取權](manage-guest-access-with-access-reviews.md)
- [管理 Azure AD 存取權檢閱的程式和控制項](manage-programs-controls.md)
- [建立群組或應用程式的存取權檢閱](create-access-review.md)
- [在 Azure AD 管理角色中建立使用者的存取權檢閱](../privileged-identity-management/pim-how-to-start-security-review.md)


