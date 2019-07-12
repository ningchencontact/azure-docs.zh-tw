---
title: 管理對 Azure Active Directory B2C 中的資源與資料的威脅
description: 了解 Azure Active Directory B2C 中針對拒絕服務攻擊和密碼攻擊的偵測和風險降低技術。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7232917df6018c9c8afc7e7edd3730a277b193f4
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798240"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>管理對 Azure Active Directory B2C 中的資源與資料的威脅

Azure Active Directory (Azure AD) B2C 具備內建功能，可協助您保護資源與資料，避免遭受威脅。 這些威脅包括拒絕服務的攻擊和密碼攻擊。 拒絕服務的攻擊可能會造成預定使用者無法使用資源。 密碼攻擊可導致未經授權的資源存取。

## <a name="denial-of-service-attacks"></a>拒絕服務的攻擊

Azure AD B2C 可對抗使用 SYN cookie 進行的 SYN 洪水攻擊。 Azure AD B2C 也可以透過限制速率和連線數來防止拒絕服務的攻擊。

## <a name="password-attacks"></a>密碼攻擊

使用者所設定的密碼必須達到適當的複雜性。 Azure AD B2C 備有降低風險的技術，可防範密碼攻擊。 風險降低包括暴力密碼破解攻擊和字典密碼破解攻擊的偵測。 Azure AD B2C 會利用不同的訊號來分析要求的完整性。 Azure AD B2C 的設計會很聰明地區分預定使用者與駭客和殭屍網路。

Azure AD B2C 使用複雜的策略來鎖定帳戶。 帳戶會依據要求的 IP 和所輸入的密碼進行鎖定。 鎖定持續期間也會根據它是攻擊的可能性而加長。 密碼會嘗試 10 次失敗之後 （預設嘗試臨界值），在一分鐘鎖定就會發生。 下一次登入失敗的帳戶解除鎖定 （也就是說，在帳戶具有已自動解除鎖定服務鎖定期限到期後），另一個的一分鐘鎖定，就會發生並繼續進行的每個登入失敗。 重複輸入相同密碼不會計算為多次登入失敗。

前 10 次鎖定期間長度為一分鐘。 接下來的 10 次鎖定期間會略微加長，而且每達到 10 次鎖定期間之後，鎖定持續時間都會繼續增加。 在帳戶未遭到鎖定之前登入成功，鎖定計數器會重設為零。 鎖定期間最長會持續五個小時。

## <a name="manage-password-protection-settings"></a>管理密碼的保護設定

若要管理的密碼保護設定，包括鎖定閾值：

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。
1. 選取 **目錄 + 訂用帳戶**右上方功能表中的入口網站中，篩選，然後選取 Azure AD B2C 租用戶。
1. 選取  **Azure Active Directory**左側功能表中 (或選取**所有服務**在入口網站左上角區段中，然後搜尋並選取*Azure Active Directory*).
1. 底下**安全性**，選取**驗證方法**，然後選取**密碼保護**。
1. 輸入您想要的密碼保護的設定，然後選取**儲存**。

    ![Azure 入口網站的密碼保護頁面上，在 Azure AD 設定](media/active-directory-b2c-reference-threat-management/portal-02-password-protection.png)
    <br />*設定為 5 中的鎖定閾值**密碼保護**設定*。

## <a name="view-locked-out-accounts"></a>檢視遭鎖定帳戶

若要取得鎖定帳戶的相關資訊，您可以檢查 Active Directory[登入活動報告](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)。 底下**狀態**，選取**失敗**。 無法使用的登入嘗試**登入錯誤碼**的`50053`表示鎖定的帳戶：

![Azure AD 登入報表，其中顯示鎖定帳戶區段](media/active-directory-b2c-reference-threat-management/portal-01-locked-account.png)

若要深入了解 Azure Active Directory 中檢視登入活動報告，請參閱[登入活動報告錯誤碼](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)。
