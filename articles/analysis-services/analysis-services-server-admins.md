---
title: 管理 Azure Analysis Services 中的伺服器管理員 | Microsoft Docs
description: 了解如何在 Azure 中管理 Analysis Services 伺服器的伺服器管理員。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: fbb7d339c5c3c12990a49d6ebd53760e101f4eb7
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301088"
---
# <a name="manage-server-administrators"></a>管理伺服器管理員

對於伺服器所在的租用戶，伺服器管理員必須是 Azure Active Directory (Azure AD) 中有效的使用者或安全性群組。 在 Azure 入口網站、SSMS 的 [伺服器屬性]、PowerShell 或 REST API 中，您可以使用伺服器的 **Analysis Services 管理員**來管理伺服器管理員。 

**安全性群組**必須[啟用郵件](https://docs.microsoft.com/exchange/recipients-in-exchange-online/manage-mail-enabled-security-groups)，並將 `MailEnabled` 屬性設定為 `True`。 以電子郵件地址指定群組時，請使用 `obj:groupid@tenantid`。

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

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

建立新的伺服器時，請使用[AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/new-azanalysisservicesserver) Cmdlet 來指定 Administrator 參數。 <br>
使用[AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver)指令程式來修改現有伺服器的系統管理員參數。

## <a name="rest-api"></a>REST API

使用 [Create](https://docs.microsoft.com/rest/api/analysisservices/servers/create) 可在建立新的伺服器時，指定 asAdministrator 屬性。 <br>
使用 [Update](https://docs.microsoft.com/rest/api/analysisservices/servers/update) 可在修改現有伺服器時，指定 asAdministrator 屬性。 <br>



## <a name="next-steps"></a>後續步驟 

[驗證和使用者權限](analysis-services-manage-users.md)  
[管理資料庫角色和使用者](analysis-services-database-users.md)  
[角色型存取控制](../role-based-access-control/overview.md)  

