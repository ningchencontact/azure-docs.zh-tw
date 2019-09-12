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
ms.openlocfilehash: 45c7f42d536880f2578c62c6c4866b21be1cc9dc
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804552"
---
# <a name="microsoft-graph-apis-for-pim-preview"></a>適用於 PIM 的 Microsoft Graph API (預覽)

對於大部分可使用 Azure 入口網站在 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 中執行的工作，您也可以使用 [Microsoft Graph API](https://developer.microsoft.com/graph/docs/concepts/overview) 來執行。 本文將說明使用適用於 PIM 的 Microsoft Graph AP 時，應了解的一些重要概念。 如需有關 Microsoft Graph API 的詳細資訊，請參閱 [Azure AD Privileged Identity Management API 參考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)。

> [!IMPORTANT]
> Microsoft Graph 中 /beta 版本底下的 API 皆處於預覽階段，可能會有所變更。 不支援在生產應用程式中使用這些 API。

## <a name="required-permissions"></a>所需的權限

若要呼叫適用於 PIM 的 Microsoft Graph API，您必須擁有下列**一個或多個**權限：

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>設定權限

應用程式若要呼叫適用於 PIM 的 Microsoft Graph API，則一定要有必要權限。 指定必要權限的最簡單方式是使用 [Azure AD 同意架構](../develop/consent-framework.md)。

### <a name="set-permissions-in-graph-explorer"></a>在 Graph 總管中的設定權限

如果您使用 Graph 總管來測試呼叫，您可以在工具中指定權限。

1. 以全域管理員的身分登入 [Graph 總管](https://developer.microsoft.com/graph/graph-explorer)。

1. 按一下 [修改權限]  。

    ![Graph 總管 - 修改權限](./media/pim-apis/graph-explorer.png)

1. 在您想要包含的權限旁新增核取記號。 Graph 總管中尚無法使用 `PrivilegedAccess.ReadWrite.AzureAD`。

    ![Graph 總管 - 修改權限](./media/pim-apis/graph-explorer-modify-permissions.png)

1. 按一下 [修改權限]  以套用權限變更。

## <a name="next-steps"></a>後續步驟

- [Azure AD Privileged Identity Management API 參考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)
