---
title: 媒體服務帳戶-Azure 角色型存取控制 |Microsoft Docs
description: 這篇文章討論 Azure 媒體服務帳戶的角色型存取控制 (RBAC)。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/23/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 93b2cd3a2565b14ea07d6db6b14dd146e4223528
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66236908"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>媒體服務帳戶的角色型存取控制 (RBAC)

目前，Azure 媒體服務不會定義特定的任何自訂角色服務。 若要取得完整存取權的媒體服務帳戶，客戶可以使用的內建角色**擁有者**或是**參與者**。 這些角色的主要差異是：**擁有者**可以控制誰可以存取資源並**參與者**無法。 內建**讀取器**角色也可用，但使用者或應用程式只會有媒體服務 Api 的讀取權限。 

## <a name="design-principles"></a>設計原則

v3 API 的金鑰設計原則之一，是讓 API 更為安全。 v3 Api 不會傳回密碼或認證上**取得**或是**清單**作業。 回應中的金鑰一律為 Null、空白或處理過的。 使用者需要呼叫個別的動作方法，以取得祕密或認證。 **讀取器**角色不能呼叫 Asset.ListContainerSas，StreamingLocator.ListContentKeys，ContentKeyPolicies.GetPolicyPropertiesWithSecrets 等作業。 擁有個別的動作，可讓您設定更細微的 RBAC 安全性權限，如有需要的自訂角色。

若要列出作業的媒體服務支援，請執行：

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

[內建角色定義](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)文章會告訴您完全什麼角色授與。 

如需詳細資訊，請參閱下列文章：

- [傳統訂用帳戶管理員角色、Azure RBAC 角色和 Azure AD 管理員角色](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [什麼是適用於 Azure 資源的 RBAC？](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [使用 RBAC 管理存取](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [媒體服務資源提供者作業](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftmedia)

## <a name="next-steps"></a>後續步驟

- [使用媒體服務 v3 Api 進行開發](media-services-apis-overview.md)
- [取得內容的索引鍵原則，使用媒體服務.NET](get-content-key-policy-dotnet-howto.md)
