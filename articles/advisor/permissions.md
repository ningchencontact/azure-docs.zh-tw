---
title: Azure Advisor 中的權限
description: Advisor 權限，他們可能會如何封鎖您能夠設定訂用帳戶或延期或關閉建議。
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 04/03/2019
ms.author: kasparks
ms.openlocfilehash: cbd2e456c96dbf8ca01387f0c7c17a1541dbfe55
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60467605"
---
# <a name="permissions-in-azure-advisor"></a>Azure Advisor 中的權限

Azure Advisor 提供的使用方式和設定您的 Azure 資源和訂用帳戶為基礎的建議。 Advisor 會利用[內建角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)所提供[角色型存取控制](https://docs.microsoft.com/azure/role-based-access-control/overview)(RBAC) 來管理您的存取權的建議和 Advisor 功能。 

## <a name="roles-and-their-access"></a>角色和其存取權

下表定義的角色，以及它們對 Advisor 中的存取權：

| **角色** | **檢視建議** | **編輯規則** | **編輯訂用帳戶設定** | **編輯資源群組設定**| **關閉，並延後建議**|
|---|:---:|:---:|:---:|:---:|:---:|
|訂用帳戶擁有者|**X**|**X**|**X**|**X**|**X**|
|訂用帳戶參與者|**X**|**X**|**X**|**X**|**X**|
|訂用帳戶讀者|**X**|--|--|--|--|
|資源群組擁有者|**X**|--|--|**X**|**X**|
|資源群組參與者|**X**|--|--|**X**|**X**|
|讀取器的資源群組|**X**|--|--|--|--|
|資源擁有者|**X**|--|--|--|**X**|
|資源的參與者|**X**|--|--|--|**X**|
|資源讀取器|**X**|--|--|--|--|

> [!NOTE]
> 若要檢視建議的存取是取決於您的建議受影響資源的存取。

## <a name="permissions-and-unavailable-actions"></a>權限和無法使用的動作

缺乏適當的權限可以封鎖您能夠在 Advisor 中執行動作。 以下是一些常見的問題。

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>無法設定訂用帳戶或資源群組

當您嘗試設定 Advisor 中的 訂用帳戶或資源群組時，您可能會看到包含或排除的選項會停用。 這個狀態表示您沒有足夠的層級，該資源群組或訂用帳戶的權限。 若要解決此問題，了解如何[授與使用者存取](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)。

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>無法延後或解除建議

如果嘗試要延期或關閉建議時，您會收到錯誤，您可能沒有足夠的權限。 請確定您至少擁有參與者存取權受影響的資源，建議您要延後或關閉。 若要解決此問題，了解如何[授與使用者存取](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)。

## <a name="next-steps"></a>後續步驟

這篇文章概要介紹如何 Advisor 會使用 RBAC 來控制使用者權限，以及如何解決常見的問題。 若要深入了解 Advisor，請參閱︰

- [何謂 Azure 建議程式？](https://docs.microsoft.com/azure/advisor/advisor-overview)
- [開始使用 Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-get-started)
