---
title: Azure AD Connect 雲端布建新的代理程式設定
description: 本主題說明如何安裝雲端布建。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 956962503dcb3a6355d455ff96744143c5965d1e
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794298"
---
# <a name="azure-ad-connect-cloud-provisioning-new-configuration"></a>Azure AD Connect 雲端布建新設定

安裝代理程式之後，您必須登入 Azure 入口網站並設定布建。  使用下列步驟來啟用代理程式。

## <a name="configure-provisioning"></a>設定布建
若要設定布建，請使用下列步驟：

1.  在 Azure AD 入口網站中，按一下  **Azure Active Directory**
2.  按一下  **Azure AD Connect**
3.  選取 **[管理布建（預覽）** ]
![](media/how-to-configure/manage1.png)

4.  按一下 [**新增**設定]。
5.  在 [設定] 畫面上，已預先填入內部部署網域
6. 輸入**通知電子郵件**。 這封電子郵件將會收到通知 
7. 當布建狀況不良時。  
8. 將選取器移至 [**啟用**]，然後按一下 [**儲存**]。
![](media/tutorial-single-forest/configure2.png)

## <a name="scoping-provisioning-to-specific-users-and-groups"></a>將布建限定于特定使用者和群組
如果您想要將代理程式限定為僅同步處理特定的使用者和群組，您可以執行此動作。 您可以使用內部部署 AD 群組或組織單位來進行範圍。 您無法在設定內設定群組和組織單位。 

1.  在 Azure AD 入口網站中，按一下  **Azure Active Directory**
2.  按一下  **Azure AD Connect**
3.  選取 **[管理布建（預覽）** ]
4.  在 [設定 **] 下，** 按一下您的設定。  
![](media/how-to-configure/scope1.png)

5.  在 [**設定**] 底下，選取 [**所有使用者**] 以變更設定規則的範圍。
![](media/how-to-configure/scope2.png)

6. 在右側，您可以藉由輸入群組的辨別名稱，然後按一下 [**新增**]，將範圍變更為僅包含安全性群組。
![](media/how-to-configure/scope3.png)

7. 或者，將它變更為只包含特定的 Ou。 按一下 [**完成**] 並**儲存**。
![](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>重新開機布建 
如果您不想等候下一個排程的執行，您可以使用 [重新開機布建] 按鈕來觸發布建執行。 
1.  在 Azure AD 入口網站中，按一下  **Azure Active Directory**
2.  按一下  **Azure AD Connect**
3.  選取 **[管理布建（預覽）** ]
4.  在 [設定 **] 下，** 按一下您的設定。  
![](media/how-to-configure/scope1.png)

5.  按一下頂端的 [**重新開機**布建]。

## <a name="removing-a-configuration"></a>移除設定
如果您想要刪除設定，可以使用下列步驟來執行此動作。

1.  在 Azure AD 入口網站中，按一下  **Azure Active Directory**
2.  按一下  **Azure AD Connect**
3.  選取 **[管理布建（預覽）** ]
4.  在 [設定 **] 下，** 按一下您的設定。  
![](media/how-to-configure/scope1.png)

5.  按一下頂端的 [**刪除**]。
![](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>在刪除設定之前不會確認，因此請確定這是您要在按一下 [**刪除**] 之前採取的動作。


## <a name="next-steps"></a>後續步驟 

- [什麼是布建？](what-is-provisioning.md)
- [什麼是 Azure AD Connect 雲端布建？](what-is-cloud-provisioning.md)
