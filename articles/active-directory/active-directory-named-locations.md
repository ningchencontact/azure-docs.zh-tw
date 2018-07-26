---
title: 在 Azure Active Directory 中設定具名位置 | Microsoft Docs
description: 了解如何設定具名位置。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.component: protection
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: f739917b201d5255716d22930d7c4bd9e6602f37
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224512"
---
# <a name="configure-named-locations-in-azure-active-directory"></a>在 Azure Active Directory 中設定具名位置

您可以使用具名位置，標記組織中受信任的 IP 位址範圍。 Azure Active Directory 會在下列動作的內容中使用具名位置：

- 偵測[風險事件](active-directory-reporting-risk-events.md)以降低報告的誤判數量。  

- [位置型條件式存取](active-directory-conditional-access-locations.md)。


本文說明如何在您的環境中設定具名位置。


## <a name="entry-points"></a>進入點

您可以在 [Azure Active Directory] 頁面的 [安全性] 區段中存取具名位置設定頁面，方法是按一下：

![進入點](./media/active-directory-named-locations/34.png)

- **條件式存取：**

    - 在 [管理] 區段中，按一下 [具名位置]。
    
        ![具名位置命令](./media/active-directory-named-locations/06.png)

- **具風險的登入：**

    - 在頂端工具列上，按一下 [加入已知的 IP 位址範圍]。

       ![具名位置命令](./media/active-directory-named-locations/35.png)



## <a name="configuration-example"></a>設定範例

**設定具名位置：**

1. 以全域管理員身分登入 [Azure 入口網站](https://portal.azure.com)。

2. 在左窗格中，按一下 [Azure Active Directory]。

    ![左窗格中的 Azure Active Directory 連結](./media/active-directory-named-locations/01.png)

3. 在 [Azure Active Directory] 頁面的 [安全性] 區段中，按一下 [條件式存取]。

    ![條件式存取命令](./media/active-directory-named-locations/05.png)


4. 在 [條件式存取] 頁面的 [管理] 區段中，按一下 [具名位置]。

    ![具名位置命令](./media/active-directory-named-locations/06.png)


5. 在 [具名位置] 頁面上，按一下 [新增位置]。

    ![新增位置命令](./media/active-directory-named-locations/07.png)


6. 在 [新增] 頁面上，執行下列動作：

    ![[新增] 刀鋒視窗](./media/active-directory-named-locations/61.png)

    a. 在 [名稱] 方塊中，輸入具名位置的名稱。

    b. 在 [IP 範圍] 方塊中，輸入 IP 範圍。 IP 範圍的格式必須為「無類別網域間路由選擇」(CIDR)。  

    c. 按一下頁面底部的 [新增] 。



## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱

- [Azure Active Directory 中的條件式存取](active-directory-conditional-access-azure-portal.md)。

- [Azure Active Directory 條件式存取中的位置條件](active-directory-conditional-access-locations.md)

- [Azure Active Directory 風險事件](active-directory-reporting-risk-events.md)。

- [Azure Active Directory 入口網站中有風險的登入報告](active-directory-reporting-security-risky-sign-ins.md)。  
