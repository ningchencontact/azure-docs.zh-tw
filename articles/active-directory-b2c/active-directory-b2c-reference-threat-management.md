---
title: Azure Active Directory B2C 中的威脅管理 | Microsoft Docs
description: 了解 Azure Active Directory B2C 中針對拒絕服務攻擊和密碼攻擊的偵測和風險降低技術。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 03/27/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: b2686b4bb2b98d3f79d8087f6857c149cfdeb553
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711258"
---
# <a name="azure-active-directory-b2c-threat-management"></a>Azure Active Directory B2C：威脅管理

威脅管理包括規劃保護系統和網路，避免遭受攻擊。 拒絕服務的攻擊可能會造成預定使用者無法使用資源。 密碼攻擊可導致未經授權的資源存取。 Azure Active Directory B2C (Azure AD B2C) 具備內建功能，可協助您透過多種方式保護您的資料，避免遭受威脅。

## <a name="denial-of-service-attacks"></a>拒絕服務的攻擊

Azure AD B2C 使用偵測和降低風險的技術 (例如 SYN Cookie) 及速率和連線限制來保護基礎資源，避免遭受拒絕服務的攻擊。

## <a name="password-attacks"></a>密碼攻擊

Azure AD B2C 也備有降低風險的技術，可防範密碼攻擊。 風險降低包括暴力密碼破解攻擊和字典密碼破解攻擊。 使用者所設定的密碼必須達到適當的複雜性。 Azure AD B2C 會利用不同的訊號來分析要求的完整性。 Azure AD B2C 的設計會很聰明地區分預定使用者與駭客和殭屍網路。 Azure AD B2C 提供很精密的策略，在可能遭受攻擊時，可根據輸入的密碼來鎖定帳戶。

如需詳細資訊，請瀏覽 [Microsoft 信任中心](https://www.microsoft.com/trustcenter/security/threatmanagement)。
