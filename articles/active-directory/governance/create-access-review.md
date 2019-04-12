---
title: 建立群組或應用程式-Azure Active Directory 的存取權檢閱 |Microsoft Docs
description: 了解如何在 Azure Active Directory 存取權檢閱中建立群組成員或應用程式存取的存取權檢閱。
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
ms.date: 04/01/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 804efa6e0a39e009e18bbb9dec5ad1638a163597
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/11/2019
ms.locfileid: "59495049"
---
# <a name="create-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>建立群組的存取權檢閱，或在 Azure AD 中的應用程式存取權檢閱

員工和來賓對於群組和應用程式的存取權會隨著時間而變更。 為了降低過時存取權指派的相關風險，系統管理員可以使用 Azure Active Directory (Azure AD)，建立群組成員或應用程式存取的存取權檢閱。 如果您需要定期檢閱存取權，您也可以建立週期性存取權檢閱。 如需關於這些案例的詳細資訊，請參閱[管理使用者存取權](manage-user-access-with-access-reviews.md)和[管理來賓存取權](manage-guest-access-with-access-reviews.md)。

本文說明如何建立一或多個存取權檢閱群組成員或應用程式存取。

## <a name="prerequisites"></a>必要條件

- [已啟用的存取權檢閱](access-reviews-overview.md)
- 全域管理員或使用者管理員

## <a name="create-one-or-more-access-reviews"></a>建立一或多個存取權檢閱

1. 登入 Azure 入口網站，然後開啟[存取權檢閱頁面](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)。

1. 在左側功能表中，按一下**存取權檢閱**。

1. 按一下 [新增存取權檢閱]，以建立新的存取權檢閱。

    ![存取權檢閱 - 控制項](./media/create-access-review/access-reviews.png)

1. 替存取權檢閱命名。 選擇性地提供檢閱的描述。 檢閱者就會看到名稱和描述。

    ![建立存取權檢閱 - 檢閱名稱和描述](./media/create-access-review/name-description.png)

1. 設定 [開始日期]。 根據預設，存取權檢閱會在其建立的相同時間開始，並在一個月後結束。 您可以將存取權檢閱的開始和結束日期變更為未來，並持續任何天數。

    ![建立存取權檢閱 - 開始和結束日期](./media/create-access-review/start-end-dates.png)

1. 若要讓存取權檢閱重複發生，請變更**頻率**從設定**一次**來**每週**，**每月**， **每季**或是**每年**。 使用**持續時間**滑桿或文字的方塊，以定義多少天後，每個檢閱的週期性的序列將會開啟從檢閱者的輸入。 例如，您可以為每月檢閱設定的最長持續期間為 27 天，以避免重疊的檢閱。

1. 使用 [結束] 設定來指定如何結束週期性存取權檢閱系列。 此系列的結束方式有三種：它會持續執行而無限期地啟動檢閱、直到特定日期為止，或是在完成所定義的發生次數之後。 您、 其他使用者的系統管理員或其他全域系統管理員可以停止數列建立之後變更的日期**設定**，如此一來，在該日期結束。

1. 在 **使用者**區段中，指定要套用的存取權檢閱的使用者。 存取權檢閱可針對群組成員或指派給應用程式的使用者執行。 您可以進一步將存取權檢閱限縮成僅檢閱身為成員 (或指派給應用程式) 的來賓使用者，而非檢閱身為成員或可存取應用程式的所有使用者。

    ![建立存取權檢閱 - 使用者](./media/create-access-review/users.png)

1. 在 **群組**區段中，選取您想要檢閱的成員資格的一或多個群組。

    > [!NOTE]
    > 選取一個以上的群組，將會建立多個存取權檢閱。 例如，選取五個群組會建立五個不同的存取權檢閱。
    
    ![建立存取權檢閱-選取的群組](./media/create-access-review/select-group.png)

1. 在 **應用程式**一節 (如果您選取**指派給應用程式**在步驟 8)，選取您想要檢閱存取權的應用程式。

    > [!NOTE]
    > 選取多個應用程式會建立多個存取權檢閱。 例如，選取五個應用程式會建立五個不同的存取權檢閱。
    
    ![建立存取權檢閱-選取應用程式](./media/create-access-review/select-application.png)

1. 在 [檢閱者] 區段中，選取一或多個人員來檢閱範圍內的所有使用者。 或者，您可以選擇讓成員檢閱自己的存取權。 如果資源是群組，您可以要求群組擁有者檢閱。 您也可以要求檢閱者在核准存取權時提供原因。

    ![建立存取權檢閱 - 檢閱者](./media/create-access-review/reviewers.png)

1. 在 [程式] 區段中，選取您要使用的程式。 您可以將追蹤和收集不同用途的存取權檢閱簡化，方法是將它們組織到程式中。 一律會出現一個「預設程式」程式，您也可以建立不同的程式。 比方說，您可以針對每個合規性方案或業務目標建立一個程式。

    ![建立存取權檢閱 - 程式](./media/create-access-review/programs.png)

### <a name="upon-completion-settings"></a>完成時的設定

1. 若要指定檢閱完成後所應執行的動作，請展開 [完成時的設定] 區段。

    ![完成時的設定](./media/create-access-review/upon-completion-settings.png)

1. 如果您想要對遭拒絕的使用者自動移除存取權，請將 [自動將結果套用至資源] 設為 [啟用]。 如果您想要在檢閱完成時手動套用結果，請將開關設為 [停用]。

1. 使用 [若檢閱者未回應] 清單，指定檢閱者在檢閱期間內未檢閱的使用者將受到何種處置。 此設定並不會影響檢閱者已手動檢閱的使用者。 如果最終的檢閱者決定 [拒絕]，則會移除使用者的存取權。

    - **無變更** - 使用者的存取權保持不變
    - **移除存取權** - 移除使用者的存取權
    - **核准存取權** - 核准使用者的存取權
    - **採納建議** - 採納系統針對應拒絕或核准使用者的持續存取所提出的建議

### <a name="advanced-settings"></a>進階設定

1. 若要指定其他設定，請展開 [進階設定] 區段。

    ![進階設定](./media/create-access-review/advanced-settings.png)

1. 將 [顯示建議] 設為 [啟用]，會向檢閱者顯示系統根據使用者的存取資訊所做的建議。

1. 將 [需要核准的原因] 設為 [啟用]，會要求檢閱者提供核准原因。

1. 將 [郵件通知] 設為 [啟用]，會讓 Azure AD 在存取權檢閱開始時傳送電子郵件通知給檢閱者，並在檢閱完成時傳送電子郵件通知給管理員。

1. 將 [提醒] 設為 [啟用]，會讓 Azure AD 對尚未完成其檢閱的檢閱者傳送存取權檢閱正在進行中的提醒。

## <a name="start-the-access-review"></a>開始存取權檢閱

在指定存取權檢閱的設定後，請按一下 [啟動]。 存取權檢閱會出現在您的清單，其中包含其狀態的指標。

![存取權檢閱清單](./media/create-access-review/access-reviews-list.png)

依預設，Azure AD 會在檢閱開始不久後傳送電子郵件給檢閱者。 如果您選擇不讓 Azure AD 傳送電子郵件，請務必通知檢閱者有存取權檢閱等待他們完成。 您可以提供他們如何指示[檢閱存取權的群組或應用程式](perform-access-review.md)。 如果您允許來賓檢閱自己的存取權的可顯示它們如何指示[自行檢閱存取權，來群組或應用程式](review-your-access.md)。

若有部分檢閱者是來賓，則只會透過電子郵件通知已接受邀請的來賓。

## <a name="manage-the-access-review"></a>管理存取權檢閱

當檢閱者完成其檢閱，您可以追蹤進度**概觀**的存取權檢閱 頁面。 在[完成檢閱](complete-access-review.md)之前，不會變更目錄中的任何存取權限。

![存取權檢閱進度](./media/create-access-review/overview-progress.png)

如果這是一次性的檢閱，然後存取權檢閱期間結束後或系統管理員停止存取權檢閱之後, 請依照下列中的步驟[完成群組或應用程式的存取權檢閱](complete-access-review.md)來查看並套用結果。  

若要管理一系列的存取權檢閱，瀏覽至 [存取權檢閱，和您將即將推出的項目中尋找已排程檢閱和編輯的結束日期] 或新增/移除檢閱者據以。

根據您的選取項目，在**當設定完成時**，將會自動套用檢閱的結束日期，或當您以手動方式停止檢閱之後執行。 檢閱的狀態將會變更**已完成**歷經中繼狀態，例如**套用**最後狀態**套用**。 您應該會看到遭到拒絕的使用者 (若有的話)，在幾分鐘內從群組成員資格或應用程式指派中移除。

## <a name="create-reviews-via-apis"></a>透過 API 建立檢閱

您也可以使用 API 來建立存取權檢閱。 您在 Azure 入口網站中為群組和應用程式使用者的存取權檢閱所做的管理工作，也可以使用 Microsoft Graph API 來執行。 如需詳細資訊，請參閱 < [Azure AD 存取權檢閱 API 參考](https://docs.microsoft.com/graph/api/resources/accessreviews-root?view=graph-rest-beta)。 如需程式碼範例，請參閱[透過 Microsoft Graph 的範例中擷取 Azure AD 存取權的檢閱](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096)。

## <a name="next-steps"></a>後續步驟

- [檢閱存取權的群組或應用程式](perform-access-review.md)
- [檢閱您自己的存取權，來群組或應用程式](review-your-access.md)
- [完成群組或應用程式的存取權檢閱](complete-access-review.md)
