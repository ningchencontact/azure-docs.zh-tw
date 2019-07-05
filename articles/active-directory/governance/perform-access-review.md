---
title: 檢閱存取權的群組或存取權檢閱-Azure Active Directory 中的應用程式 |Microsoft Docs
description: 了解如何檢閱群組成員的存取或在 Azure Active Directory 存取權檢閱的應用程式存取。
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/21/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6f73d3bf5e502a758dd46561059c15a2970d9b6
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2019
ms.locfileid: "67471834"
---
# <a name="review-access-to-groups-or-applications-in-azure-ad-access-reviews"></a>檢閱群組的存取權或在 Azure AD 中的應用程式存取權檢閱

Azure Active Directory (Azure AD)，簡化企業在 Azure AD 中管理群組和應用程式的存取以及其他 Microsoft Online Services 的 「 使用稱為 Azure AD 存取權檢閱。

這篇文章會說明如何指定檢閱者執行之成員的群組或使用者的存取權的應用程式的存取權檢閱。

## <a name="prerequisites"></a>必要條件

- Azure AD Premium P2

如需詳細資訊，請參閱 <<c0> [ 哪些使用者必須有授權？](access-reviews-overview.md#which-users-must-have-licenses)。

## <a name="open-the-access-review"></a>開啟存取權檢閱

若要執行的存取權檢閱的第一個步驟是尋找及開啟存取權檢閱。

1. 尋找來自會要求您檢閱存取權的 Microsoft 電子郵件。 以下是範例電子郵件給檢閱群組存取權。

    ![若要檢閱群組的存取權的 microsoft 的範例電子郵件](./media/perform-access-review/access-review-email.png)

1. 按一下 **開始檢閱**開啟存取權檢閱的連結。

如果您沒有電子郵件，您可以找到您暫止的存取權檢閱，依照下列步驟。

1. 登入 MyApps 入口網站，網址[ https://myapps.microsoft.com ](https://myapps.microsoft.com)。

    ![列出您有權限的應用程式的 MyApps 入口網站](./media/perform-access-review/myapps-access-panel.png)

1. 在頁面右上角按一下使用者符號，其中顯示您的名稱和預設組織。 如果列出多個組織，請選取已要求存取權檢閱的組織。

1. 按一下 **存取權檢閱**圖格以查看暫止的存取權檢閱的清單。

    如果看不到磚，則沒有針對該組織執行的存取權檢閱，此時也不需要採取任何動作。

    ![應用程式和群組的擱置中的存取權檢閱清單](./media/perform-access-review/access-reviews-list.png)

1. 按一下 **開始檢閱**您想要執行的存取權檢閱的連結。

## <a name="perform-the-access-review"></a>執行存取權檢閱

一旦您已開啟存取權檢閱，您會看到需要檢閱的使用者名稱。

如果要求檢閱您自己的存取權，網頁會看起來不同。 如需詳細資訊，請參閱 <<c0> [ 自行檢閱存取權，來群組或應用程式](review-your-access.md)。

![列出需要檢閱的使用者開啟的存取權檢閱](./media/perform-access-review/perform-access-review.png)

有兩種方式，您可以核准或拒絕存取：

- 您可以核准或拒絕存取的一或多個使用者，或
- 您可以接受系統建議，這是最簡單且最快速方式。

### <a name="approve-or-deny-access-for-one-or-more-users"></a>核准或拒絕一或多個使用者的存取權

1. 檢閱要決定是否要核准或拒絕其持續存取權的使用者清單。

1. 若要核准或拒絕存取的單一使用者，請按一下要開啟視窗，以指定要採取的動作的資料列。 若要核准或拒絕存取的多個使用者，請在新增之使用者旁邊的核取記號，，然後按一下**檢閱 X 個使用者**按鈕以開啟視窗，以指定要採取的動作。

1. 按一下 **核准**或是**拒絕**。 如果您不確定，您可以按一下**不知道**。 這樣會導致使用者維護其存取權，但選取項目將會反映在稽核記錄檔。

    ![動作的視窗，其中包含核准、 拒絕，而且不知道選項](./media/perform-access-review/approve-deny.png)

1. 如有必要，請輸入中的原因**原因** 方塊中。

    存取權檢閱的系統管理員可能會要求您提供理由來核准持續存取權或群組成員資格。

1. 一旦您指定要採取的動作，按一下**儲存**。

    如果您想要變更您的回應，請選取資料列，並更新回應。 例如，您可以核准先前拒絕的使用者，或拒絕先前核准的使用者。 您可以存取權檢閱結束之前隨時變更您的回應。

    如果有多個檢閱者，則會記錄上次提交的回應。 請考慮以系統管理員指定兩個檢閱者-Alice 和 Bob 的其中一個範例。 Alice 先開啟存取權檢閱並核准存取權。 在檢閱結束之前，Bob 就會開啟存取權檢閱，並拒絕存取。 最後一個會拒絕回應是什麼，就會記錄。

    > [!NOTE]
    > 如果使用者被拒絕存取，他們不會立即移除。 檢閱已結束或系統管理員停止檢閱時，則會移除。

### <a name="approve-or-deny-access-based-on-recommendations"></a>核准或拒絕存取的建議權限

若要為您快且更容易讓存取權檢閱，我們也提供，則您可以接受建議，只要按一下。 根據使用者的登入活動，所產生的建議。

1. 在頁面底部的藍色列，按一下**接受建議**。

    ![開啟的存取權檢閱清單顯示 [接受建議] 按鈕](./media/perform-access-review/accept-recommendations.png)

    您會看到建議的動作的摘要。

    ![顯示建議的動作的摘要視窗](./media/perform-access-review/accept-recommendations-summary.png)

1. 按一下  **Ok**來接受建議。

## <a name="next-steps"></a>後續步驟

- [完成群組或應用程式的存取權檢閱](complete-access-review.md)
