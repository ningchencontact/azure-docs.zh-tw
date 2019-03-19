---
title: 自行檢閱存取權，來群組或應用程式在 Azure AD 存取權檢閱 |Microsoft Docs
description: 了解如何檢閱您自己的存取權的群組或應用程式在 Azure Active Directory 存取權檢閱。
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
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76f90a5aa3f201fa5d1578ac63526be26377aedf
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731203"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>自行檢閱存取權，來群組或應用程式在 Azure AD 存取權檢閱

Azure Active Directory (Azure AD) 可讓您簡化了企業在 Azure AD 中管理群組或應用程式的存取權，以及其他 Microsoft Online Services 的功能稱為 Azure AD 存取權檢閱。

本文說明如何檢閱您自己的存取權的群組或應用程式。

## <a name="open-the-access-review"></a>開啟存取權檢閱

若要執行的存取權檢閱的第一個步驟是尋找及開啟存取權檢閱。

1. 尋找來自會要求您檢閱存取權的 Microsoft 電子郵件。 以下是範例電子郵件給檢閱您的存取權的群組。

    ![檢閱存取權電子郵件](./media/review-your-access/access-review-email.png)

1. 按一下 **檢閱存取權**開啟存取權檢閱的連結。

如果您沒有電子郵件，您可以找到您暫止的存取權檢閱，依照下列步驟。

1. 登入 MyApps 入口網站，網址[ https://myapps.microsoft.com ](https://myapps.microsoft.com)。

    ![MyApps 入口網站](./media/review-your-access/myapps-access-panel.png)

1. 在頁面的右上角，按一下 使用者符號，其顯示您的名稱和預設組織。 如果列出多個組織，請選取已要求存取權檢閱的組織。

1. 在頁面的右側，按一下**存取權檢閱**圖格以查看暫止的存取權檢閱的清單。

    如果看不到磚，則沒有針對該組織執行的存取權檢閱，此時也不需要採取任何動作。

    ![存取權檢閱清單](./media/review-your-access/access-reviews-list.png)

1. 按一下 **開始檢閱**您想要執行的存取權檢閱的連結。

## <a name="perform-the-access-review"></a>執行存取權檢閱

一旦您已開啟存取權檢閱，您可以看到您的存取權。

1. 檢閱您的存取權，並決定您是否仍需要存取。

    如果要求是要供其他人檢閱存取權，網頁會看起來不同。 如需詳細資訊，請參閱 <<c0> [ 檢閱存取權的群組或應用程式](perform-access-review.md)。

    ![執行存取權檢閱](./media/review-your-access/perform-access-review.png)

1. 按一下  **是**保護的存取，或按一下  **No**移除您的存取權。

1. 如果您按一下**是**，您可能需要指定在理由**原因** 方塊中。

    ![執行存取權檢閱](./media/review-your-access/perform-access-review-submit.png)

1. 按一下 [提交] 。

    提交您的選擇和您回到 MyApps 入口網站。

    如果您想要變更您的回應，重新開啟 存取權檢閱頁面，並更新您的回應。 您可以存取權檢閱結束之前隨時變更您的回應。

    > [!NOTE]
    > 如果您特別指出您不再需要存取，您不會立即移除。 您會移除或系統管理員停止檢閱時檢閱已結束。

## <a name="next-steps"></a>後續步驟

- [完成群組或應用程式的存取權檢閱](complete-access-review.md)
