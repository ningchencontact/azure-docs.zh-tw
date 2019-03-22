---
title: 從 My Apps 入口網站-Azure Active Directory 執行存取權檢閱 |Microsoft Docs
description: 了解如何檢視及管理您組織的應用程式和群組的安全性存取權。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 551bbe4e994e15688c59fd395ab762e14927dfda
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340195"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>從 My Apps 入口網站中執行存取權檢閱
您可以使用工作或學校帳戶，透過以 web 為基礎**我的應用程式**入口網站中，檢視和啟動許多組織的雲端式應用程式，以更新您的設定檔和帳戶資訊，以查看您**群組**的詳細資訊，並執行**存取權檢閱**為您的應用程式及群組。 如果您沒有存取權**My Apps**入口網站中，您必須連絡技術服務人員以權限。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>本內容適用於使用者。 如果您是系統管理員，您可以找到有關如何設定和管理雲端中的應用程式的詳細資訊[應用程式管理文件](https://docs.microsoft.com/azure/active-directory/manage-apps)。

## <a name="manage-access-reviews"></a>管理存取權檢閱
如果您的系統管理員提供已執行您自己的存取權檢閱的權限，您可以管理您的群組或應用程式存取，從**存取權檢閱**圖格**My Apps**入口網站頁面。

>[!Note]
>如果您沒有看到**存取權檢閱**磚它可能是表示您沒有權限來執行存取權檢閱，或您沒有任何暫止的等待您核准的檢閱。 如果您認為您應該可以存取 [] 圖格，請連絡服務台尋求協助。

### <a name="to-perform-your-access-reviews"></a>若要執行您的存取權檢閱

1.  登入您的公司或學校帳戶。

2.  開啟網頁瀏覽器並移至 https://myapps.microsoft.com，或使用貴組織所提供的連結。 比方說，您可能會被導向至自訂頁面為您的組織，例如 https://myapps.microsoft.com/contoso.com。

    **應用程式**頁面隨即出現，顯示的所有雲端應用程式，您的組織所擁有且可供您使用。

    ![在 My Apps 入口網站中的 [應用程式] 頁面](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. 選取 [**存取權檢閱**] 圖格以查看清單的存取權檢閱等待您核准。

    ![與暫止的組織的存取權檢閱的存取權檢閱頁面](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. 選取 **開始檢閱**啟動您的存取權檢閱。

5. 檢閱您的存取權，並判斷它是否仍需要。

    ![顯示 檢閱詳細資料的存取權檢閱頁面](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >如果您是系統管理員，並可檢閱您的組織群組和應用程式的存取權，您會看到不同的頁面。 如需檢閱群組或組織的應用程式的詳細資訊，請參閱[檢閱存取權的群組或應用程式在 Azure AD 存取權檢閱](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review)。

6. 選取 **[是]** 保留您的存取權或**No**移除您的存取權。

    如果您選取**是**，您可能需要指定在理由**原因** 方塊中。

    ![存取權檢閱頁面上，顯示 [原因] 方塊的範例文字](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. 選取 [提交]。

    您的存取權檢閱完畢，並且您返回**My Apps**入口網站。

    >[!Note]
    >您可以在您的存取權檢閱期間結束之前隨時變更您的存取權。 如果您移除您的應用程式或群組的存取，就是無法立即移除。 當存取權檢閱期間結束時，或系統管理員關閉檢閱時，就會發生移除。 

## <a name="next-steps"></a>後續步驟

- [存取和使用 My Apps 入口網站上的應用程式](my-apps-portal-end-user-access.md)。

- [變更您的設定檔資訊](my-apps-portal-end-user-update-profile.md)。

- [檢視和更新您群組相關的資訊](my-apps-portal-end-user-groups.md)。