---
title: 適用於 PIM 的 Microsoft Graph API (預覽) - Azure Active Directory | Microsoft Docs
description: 針對如何使用適用於 Azure AD Privileged Identity Management (PIM) 的 Microsoft Graph API (預覽) 提供相關資訊。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 11/13/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49d49f42e0d705981a5b4e41630b425fcb02e940
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756250"
---
# <a name="microsoft-graph-apis-for-privileged-identity-management-preview"></a>適用於 Privileged Identity Management 的 Microsoft Graph API (預覽)

您可以使用適用於 Azure Active Directory 的 [Microsoft Graph API](https://developer.microsoft.com/graph/docs/concepts/overview) 來執行所有 Privileged Identity Management 工作。 本文將說明使用適用於 Privileged Identity Management 的 Microsoft Graph AP 時，應了解的重要概念。

如需有關 Microsoft Graph API 的詳細資訊，請參閱 [Azure AD Privileged Identity Management API 參考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)。

> [!IMPORTANT]
> Microsoft Graph 中 /beta 版本底下的 API 皆處於預覽階段，可能會有所變更。 不支援在生產應用程式中使用這些 API。

## <a name="required-permissions"></a>所需的權限

若要呼叫適用於 Privileged Identity Management 的 Microsoft Graph API，您必須擁有下列**一個或多個**權限：

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>設定權限

應用程式若要呼叫適用於 Privileged Identity Management 的 Microsoft Graph API，則一定要有必要權限。 指定必要權限的最簡單方式是使用 [Azure AD 同意架構](../develop/consent-framework.md)。

### <a name="set-permissions-in-graph-explorer"></a>在 Graph 總管中的設定權限

如果您使用 Graph 總管來測試呼叫，您可以在工具中指定權限。

1. 以全域管理員的身分登入 [Graph 總管](https://developer.microsoft.com/graph/graph-explorer)。

1. 按一下 [修改權限]  。

    ![Graph 總管 - 修改權限](./media/pim-apis/graph-explorer.png)

1. 在您想要包含的權限旁選取核取方塊。 Graph 總管中尚無法使用 `PrivilegedAccess.ReadWrite.AzureAD`。

    ![Graph 總管 - 修改權限](./media/pim-apis/graph-explorer-modify-permissions.png)

1. 按一下 [修改權限]  以套用權限變更。

## <a name="next-steps"></a>後續步驟

- [Azure AD Privileged Identity Management API 參考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)
