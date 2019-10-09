---
title: 將存取管理委派給 Azure AD 權利管理（預覽）中的目錄建立者-Azure Active Directory
description: 瞭解如何將 IT 系統管理員的存取管理委派給目錄建立者和專案管理人員，讓他們可以自行管理存取權。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: da6183599f2dacd1a98d8fe359edda741b903b50
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170759"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management-preview"></a>在 Azure AD 權利管理（預覽）中將存取管理委派給目錄建立者

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 權利管理目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

若要委派給不是系統管理員的使用者，讓他們可以建立自己的目錄，您可以將這些使用者新增至 Azure AD 權利管理定義的目錄建立者角色。 您可以新增個別使用者，也可以新增群組，其成員接著可以建立目錄。

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>身為 IT 系統管理員，委派給目錄建立者

請遵循下列步驟，將使用者指派給目錄建立者角色。

**必要角色：** 全域管理員或使用者管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表的 [**權利管理**] 區段中，按一下 [**設定**]。

1. 按一下 [編輯]。

    ![新增目錄建立者的設定](./media/entitlement-management-delegate/settings-delegate.png)

1. 在 [**委派權利管理**] 區段中，按一下 [**新增類別目錄建立者**]，選取您要委派此權利管理角色的使用者或群組。

1. 按一下 [選取]。

1. 按一下 [儲存]。

## <a name="next-steps"></a>後續步驟

- [建立和管理資原始目錄](entitlement-management-catalog-create.md)
- [委派存取管理以存取封裝管理員](entitlement-management-delegate-managers.md)

