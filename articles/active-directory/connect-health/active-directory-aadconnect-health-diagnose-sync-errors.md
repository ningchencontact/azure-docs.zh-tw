---
title: Azure AD Connect Health - 診斷重複屬性同步處理錯誤 | Microsoft Docs
description: 本文件說明重複屬性同步處理錯誤的診斷程序，和直接從 Azure 入口網站修正孤立物件案例的可行方式。
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: billmath
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: zhiweiw
ms.openlocfilehash: 0a345fd3443fb33d6f5912c8a04677091e20ecc8
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305562"
---
# <a name="duplicate-attribute-sync-errors-diagnosis-and-remediation"></a>重複屬性同步處理錯誤的診斷和修復 

## <a name="overview"></a>概觀
Azure Active Directory Connect Health 並未以凸顯同步處理錯誤為第一要務，而是優先導入了自助式修復功能，以對重複屬性同步處理錯誤進行疑難排解，並修正 Azure AD 中的孤立物件。 診斷功能主要的功效包括：
- 提供診斷程序以縮小重複屬性同步處理錯誤案例的範圍，並指出特定解決方案
- 輕輕按一下即可對 Azure AD 中特有的案例套用修正方法，以解決錯誤
- 無須升級或設定即可啟用這項功能。
深入了解關於 Azure AD [重複屬性恢復功能](https://aka.ms/dupattributeresdocs)的詳細資訊。

## <a name="problems"></a>問題
### <a name="common-scenario"></a>常見的案例
當 **QuarantinedAttributeValueMustBeUnique** 和 **AttributeValueMustBeUnique** 同步處理錯誤發生時，常會在 Azure AD 中發現使用者主體名稱或 Proxy 位址衝突。 您可以從內部部署端更新衝突的來源物件，藉以解決同步處理錯誤。 同步處理錯誤將可在後續的同步處理完成後解決。 例如，下圖指出兩個使用者在使用 *Joe.J@contoso.com* 時的 UserPrincipalName 發生衝突。有衝突的物件會在 Azure AD 中隔離。 

![診斷同步處理錯誤的常見案例](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>孤立物件案例
有時候，您可能會發現現有的使用者失去來源錨點。 在內部部署 AD 中刪除了來源物件後，刪除信號的變更卻一直未同步處理至 Azure AD。 此狀況的成因可能是同步處理引擎有問題，或網域移轉。 當相同的物件還原或重新建立時，理論上從來源錨點進行同步處理的使用者，應該是現有的使用者。 如果現有使用者是僅限雲端的物件，您也將發現衝突的使用者會同步處理至 Azure AD，且無法與現有的物件同步比對。 來源錨點無法直接重新對應。 深入了解[現有的知識庫](https://support.microsoft.com/help/2647098)。 例如，假設 Azure AD 中的現有物件保有 Joe 的授權。 隨後，有使用不同的來源錨點進行同步處理的物件以重複的屬性狀態出現在 Azure AD 中。 Joe 在內部部署 AD 中的變更，將無法套用至 Joe 在 Azure AD 中的原始使用者 (現有的使用者)。  

![診斷同步處理錯誤的孤立物件案例](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Connect Health 中的診斷和疑難排解步驟 
診斷功能支援具有下列重複屬性的使用者物件：

| 屬性名稱 | 同步處理錯誤類型|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique 或 AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique 或 AttributeValueMustBeUnique | 
| SipProxyAddress | AttributeValueMustBeUnique | 
| OnPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> 必須是 RBAC 設定中的**全域管理員**或**參與者**，才有權存取這項功能。 
>

在 Azure 入口網站中下列步驟，將可縮減同步處理錯誤詳細資料，並提供更明確的解決方案：

![診斷同步處理錯誤的診斷步驟](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixSteps.png)

在 Azure 入口網站中，您將可在執行若干步驟後找出可修正的特定案例：  
1.  在 [診斷狀態] 資料行中，狀態會顯示是否有潛在的疑難排解流程可縮小錯誤案例的範圍，且或許可直接從 Azure Active Directory 修正錯誤。
| 狀態 | 這代表什麼意思？ |
| ------------------ | -----------------|
| 未啟動 | 您並未瀏覽此診斷程序。 根據診斷結果，或許可直接從入口網站修正同步處理錯誤。 |
| 需要手動修正 | 錯誤不符合直接從入口網站修正的條件。 此案例可能是 (1) 衝突的物件類型不是使用者 (2) 您已執行過診斷步驟，但沒有可從入口網站執行的修正解決方案。 在此情況下，從內部部署端修正，仍會是解決方案之一。 [深入了解內部部署修正](https://support.microsoft.com/help/2647098) | 
| 待同步 | 已套用修正方法。 正在等候下一個同步處理週期將錯誤清除。 |
>[!IMPORTANT]
> 在每個同步處理週期結束後，都會重設診斷狀態資料行。 
>

2.  按一下 [錯誤詳細資料] 刀鋒視窗中的 [診斷] 按鈕後，您將必須回答幾個問題，並識別同步處理錯誤詳細資料。 回答這些問題有助於識別孤立物件狀況的案例。 

3.  如果在診斷結束時未出現 [關閉] 按鈕，即表示根據給定的答案，並沒有可從入口網站執行的快速修正。 請參閱最後一個步驟中顯示的解決方案。 從內部部署端修正仍會是解決方案之一。 按一下 [關閉] 按鈕後，您會發現目前同步處理錯誤的狀態會切換為 [需要手動修正]。 在目前的同步處理週期內都會保持此狀態。

4.  發現孤立物件案例時，您將可直接從入口網站修正重複屬性同步處理錯誤。 請按一下 [套用修正] 按鈕以觸發程序。 目前同步處理錯誤的狀態將會更新為 [待同步]。

5.  錯誤應會在下一個同步處理週期之後從清單中移除。

## <a name="how-to-answer-the-diagnosis-questions"></a>如何回答診斷問題 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>使用者是否存在於您的內部部署 Active Directory 中？

此問題嘗試從內部部署 Active Directory 中找出現有使用者的來源物件。  
1.  檢查您的 Active Directory 中是否有物件具備提供的 UserPrincipalName。 如果沒有，請回答 [否]。
2.  如果有，請檢查物件是否仍在同步處理範圍內。  
  - 使用 DN 在 Azure AD 連接器空間中搜尋。
  - 如果找到狀態為 [待新增] 的物件，請回答 [否]。 Azure AD Connect 無法將物件連線至正確的 AD 物件。
  - 如果找不到物件，請回答 [是]。

> 以下圖為例，此問題嘗試確認 *Joe Jackson* 是否仍存在於內部部署 Active Directory 中。
在**常見的案例**中，*Joe Johnson* 和 *Joe Jackson* 這兩個使用者會出現在內部部署 Active Directory 中。 隔離的物件是兩個不同的使用者。

![診斷同步處理錯誤的常見案例](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

> 在**孤立物件案例**中，只有一個使用者 (*Joe Johnson*) 會出現在內部部署 Active Directory 中：

![診斷同步處理錯誤的孤立物件案例](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

### <a name="do-both-these-accounts-belong-to-the-same-user"></a>這兩個帳戶是否屬於同一個使用者？
此問題會在 Azure AD 中檢查後續發生衝突的使用者與現有的使用者物件，以確認兩者是否屬於相同的使用者。  
1.  衝突的物件是同步處理至 Azure Active Directory 的新物件。 依下列項目比較物件：  
  - 顯示名稱
  - 使用者主體名稱
  - 物件識別碼
2.  如果無法加以比較，請檢查您的 Active Directory 中是否有物件具備提供的 UserPrincipalNames。 如果兩者皆找到，請回答 [否]。  

> 在下列情況下，兩個物件會屬於相同的使用者 *Joe Johnson*。

![診斷同步處理錯誤的孤立物件案例](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)


## <a name="what-happened-after-fix-is-applied-for-orphaned-object-scenario"></a>為孤立物件案例套用修正後，情況為何
根據問題的答案，可從 Azure AD 進行修正時，您將可看到 [套用修正] 按鈕。 在此情況下，內部部署物件將會與非預期的 Azure AD 物件同步處理。 這兩個物件會使用「來源錨點」進行對應。 套用變更會執行如下的步驟：
- 為 Azure AD 中的正確物件更新來源錨點。
- 刪除 Azure AD 中有衝突的物件 (如果有的話)。

![在修正後診斷同步處理錯誤](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixAfterFix.png)

>[!IMPORTANT]
> 「套用修正」變更將只會套用至孤立物件案例。
>

完成前述步驟後，使用者將能夠存取連結至現有物件的原始資源。 清單檢視中的 [診斷狀態] 值將會更新為 [待同步]。同步處理錯誤將可在後續的同步處理完成後解決。 Connect Health 將不會再顯示清單檢視中已解決的同步處理錯誤。 


## <a name="faq"></a>常見問題集
 -  如果套用執行失敗，是發生了什麼狀況？  
如果執行失敗，可能表示 Azure AD Connect 當時也在執行匯出錯誤。 請重新整理入口網站頁面，並在後續的同步處理完成後重試。 預設的同步處理週期為 30 分鐘。 

 -  如果**現有的物件**是要刪除的物件，將會如何？  
如果在此情況下應刪除現有的物件，則不會在此程序中變更來源錨點。 您應可從內部部署 AD 加以修正。  

 -  使用者必須具備何種權限才能套用修正？  
RBAC 設定中的全域管理員或參與者將具有存取診斷和疑難排解程序的權限。

 -  我需要針對這項功能設定 AAD Connect 或更新 Azure AD Connect Health 代理程式嗎？  
不需要，診斷程序是完整的雲端功能。

 -  如果現有的物件遭到虛刪除，診斷程序是否會將物件再次還原為使用中的物件？  
否，修正並不會更新來源錨點以外的物件屬性。 
