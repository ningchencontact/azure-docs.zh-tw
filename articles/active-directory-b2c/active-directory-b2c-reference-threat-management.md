---
title: 管理對 Azure Active Directory B2C 中的資源與資料的威脅 | Microsoft Docs
description: 了解 Azure Active Directory B2C 中針對拒絕服務攻擊和密碼攻擊的偵測和風險降低技術。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ee99e7346d438e81a0cd25f8c522838524732568
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/02/2018
ms.locfileid: "50912844"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>管理對 Azure Active Directory B2C 中的資源與資料的威脅

Azure Active Directory (Azure AD) B2C 具備內建功能，可協助您保護資源與資料，避免遭受威脅。 這些威脅包括拒絕服務的攻擊和密碼攻擊。 拒絕服務的攻擊可能會造成預定使用者無法使用資源。 密碼攻擊可導致未經授權的資源存取。 

## <a name="denial-of-service-attacks"></a>拒絕服務的攻擊

Azure AD B2C 可對抗使用 SYN cookie 進行的 SYN 洪水攻擊。 Azure AD B2C 也可以透過限制速率和連線數來防止拒絕服務的攻擊。

## <a name="password-attacks"></a>密碼攻擊

使用者所設定的密碼必須達到適當的複雜性。 Azure AD B2C 備有降低風險的技術，可防範密碼攻擊。 風險降低包括暴力密碼破解攻擊和字典密碼破解攻擊。 Azure AD B2C 會利用不同的訊號來分析要求的完整性。 Azure AD B2C 的設計會很聰明地區分預定使用者與駭客和殭屍網路。 

Azure AD B2C 使用複雜的策略來鎖定帳戶。 帳戶會依據要求的 IP 和所輸入的密碼進行鎖定。 鎖定持續期間也會根據它是攻擊的可能性而加長。 密碼嘗試 10 次失敗之後，就會發生一分鐘鎖定。 下一次在帳戶解除鎖定後登入失敗，就會發生另一次一分鐘鎖定，並且在每次登入失敗之後都會持續發生。 重複輸入相同密碼不會計算為多次登入失敗。 

前 10 次鎖定期間長度為一分鐘。 接下來的 10 次鎖定期間會略微加長，而且每達到 10 次鎖定期間之後，鎖定持續時間都會繼續增加。 在帳戶未遭到鎖定之前登入成功，鎖定計數器會重設為零。 鎖定期間最長會持續五個小時。 

目前，您無法：

- 以少於 10 次的失敗登入次數觸發鎖定
- 擷取已鎖定帳戶清單
- 設定鎖定原則

如需詳細資訊，請瀏覽 [Microsoft 信任中心](https://www.microsoft.com/trustcenter/default.aspx)。
