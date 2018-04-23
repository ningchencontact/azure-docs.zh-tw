---
title: 讓雲端服務提供者能夠管理您的 Azure Stack 訂用帳戶 | Microsoft Docs
description: 讓服務提供者能夠存取 Azure Stack 中的訂用帳戶。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: 26ba68be6d4932da77befaf7c968525393c0a033
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>讓雲端服務提供者能夠管理您的 Azure Stack 訂用帳戶

「適用於：Azure Stack 整合系統」

如果您是透過雲端服務提供者 (CSP) 使用 Azure Stack，您在 Azure 訂用帳戶和 Azure Stack 中的資源存取權可能會由此提供者管理。 您也可以管理您自己的訂用帳戶。 本文探討如何讓服務提供者能夠代表您存取您的訂用帳戶，或是確認服務提供者可以管理您的服務。

> [!Note]  
>  如果略過下列步驟且 CSP 尚未管理您的帳戶，則 CSP 將無法代表您管理您的 Azure Stack 訂用帳戶。

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>透過雲端服務提供者管理您的訂用帳戶

1. 在您的租用戶目錄中將 CSP 新增為具有使用者角色的來賓使用者。  如需新增使用者的步驟，請參閱[將新的使用者新增至 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. 接著，CSP 會為您建立本機 Azure Stack 訂用帳戶。
3. 您已準備好開始使用 Azure Stack。
3. 接著，您的 CSP 應該會在您的訂用帳戶中建立資源，以確認它們也可以管理您的資源。 例如，他們可以[使用 Azure Stack 入口網站建立 Windows 虛擬機器](azure-stack-quick-windows-portal.md)。

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>讓雲端服務提供者能夠使用 RBAC 權限來管理您的訂用帳戶

將 CSP 新增為您訂用帳戶的擁有者。 

1. 在您的租用戶目錄中將 CSP 新增為 具有擁有者角色的來賓使用者。  如需新增使用者的步驟，請參閱[將新的使用者新增至 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. 將擁有者角色新增至 CSP 來賓使用者。 如需將 CSP 使用者新增至您訂用帳戶的步驟，請參閱[使用角色型存取控制來管理 Azure 訂用帳戶資源的存取](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
3. 接著，CSP 會為您建立本機 Azure Stack 訂用帳戶。
4. 您已準備好開始使用 Azure Stack。
5. 接著，您的 CSP 應該會在您的訂用帳戶中建立資源，以確認它們可以管理您的資源。 

## <a name="next-steps"></a>後續步驟

  - 若要深入了解如何取出 Azure Stack 的資源使用量資訊，請參閱 [Azure Stack 中的使用量與帳單](../azure-stack-billing-and-chargeback.md)。
