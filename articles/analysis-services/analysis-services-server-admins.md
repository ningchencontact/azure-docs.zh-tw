---
title: 管理 Azure Analysis Services 中的伺服器管理員 | Microsoft Docs
description: 了解如何在 Azure 中管理 Analysis Services 伺服器的伺服器管理員。
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: def09f2853f761f3fefca80f341e6cc0557bac86
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="manage-server-administrators"></a>管理伺服器管理員
對於伺服器所在的租用戶，伺服器管理員必須是 Azure Active Directory (Azure AD) 中有效的使用者或群組。 在 Azure 入口網站，或 SSMS 的 [伺服器屬性] 中，您可以使用伺服器的 **Analysis Services 管理員**來管理伺服器管理員。 

## <a name="to-add-server-administrators-by-using-azure-portal"></a>使用 Azure 入口網站來新增伺服器管理員
1. 在入口網站中，針對您的伺服器按一下 [Analysis Services 管理員]。
2. 在 [\<servername> - Analysis Services 管理員] 中，按一下 [新增]。
3. 在 [新增伺服器管理員] 中，選取 Azure AD 中的使用者帳戶，或透過電子郵件地址邀請外部使用者。

    ![Azure 入口網站的伺服器管理員](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>使用 SSMS 來新增伺服器管理員
1. 以滑鼠右鍵按一下伺服器 > [屬性]。
2. 在 [Analysis Server 屬性] 中，按一下 [安全性]。
3. 按一下 [新增]，然後在 Azure AD 中輸入使用者或群組的電子郵件地址。
   
    ![在 SSMS 中新增伺服器管理員](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="next-steps"></a>後續步驟 
[驗證和使用者權限](analysis-services-manage-users.md)  
[管理資料庫角色和使用者](analysis-services-database-users.md)  
[角色型存取控制](../role-based-access-control/overview.md)  

