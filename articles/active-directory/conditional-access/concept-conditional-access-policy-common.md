---
title: 一般條件式存取原則-Azure Active Directory
description: 組織常用的條件式存取原則
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: abcca0d2712a462e4d2ecf9c8023d0cb0e68ad6c
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576676"
---
# <a name="common-conditional-access-policies"></a>一般條件式存取原則

基準保護原則很棒, 但許多組織所需的彈性比所提供的還要多。 例如, 許多組織都需要從需要多重要素驗證的條件式存取原則中排除特定帳戶, 例如其緊急存取或中斷玻璃系統管理帳戶。 針對這些組織, 本文中所參考的一般原則可以使用。

![Azure 入口網站中的條件式存取原則](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>緊急存取帳戶

如需緊急存取帳戶和其重要性的詳細資訊, 請參閱下列文章: 

* [在 Azure AD 中管理緊急存取帳戶](../users-groups-roles/directory-emergency-access.md)
* [使用 Azure Active Directory 建立彈性的存取控制管理原則](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>組織部署的一般原則

* [系統管理員需要 MFA](howto-conditional-access-policy-admin-mfa.md)
* [Azure 管理需要 MFA](howto-conditional-access-policy-azure-management.md)
* [封鎖舊版驗證](howto-conditional-access-policy-block-legacy.md)
* [以風險為基礎的條件式存取 (需要 Azure AD Premium P2)](howto-conditional-access-policy-risk.md)
* [MFA 註冊需要信任的位置](howto-conditional-access-policy-registration.md)
* [依位置封鎖存取](howto-conditional-access-policy-location.md)
* [需要符合規範的裝置](howto-conditional-access-policy-compliant-device.md)

## <a name="next-steps"></a>後續步驟

[使用條件式存取 What If 工具模擬登入行為](troubleshoot-conditional-access-what-if.md)
