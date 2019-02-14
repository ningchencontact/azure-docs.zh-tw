---
title: Azure AD 密碼保護預覽中的疑難排解
description: 了解 Azure AD 密碼保護預覽中常見的疑難排解
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: 7474027368949d5ad2202881ac68096fac2b8bd2
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2019
ms.locfileid: "55693899"
---
# <a name="preview-azure-ad-password-protection-troubleshooting"></a>預覽：Azure AD 密碼保護的疑難排解

|     |
| --- |
| Azure AD 密碼保護是 Azure Active Directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

部署 Azure AD 密碼保護之後，可能需要進行疑難排解。 本文將深入說明以協助您了解一些常見的疑難排解步驟。

## <a name="weak-passwords-are-not-getting-rejected-as-expected"></a>弱式密碼並未如預期般遭到拒絕

這可能有數個可能的原因：

1. 您的 DC 代理程式尚未下載原則。 此問題的徵兆是 DC 代理程式系統管理事件記錄檔中的 30001 事件。

    對於此問題的可能原因包括：

    1. 尚未註冊樹系
    2. 尚未註冊 Proxy
    3. 網路連線問題會導致 Proxy 服務無法與 Azure 通訊 (請檢查 HTTP Proxy 需求)

2. 密碼原則強制模式仍會設為 [稽核]。 如果發生這種情況，請使用 Azure AD 密碼保護入口網站，將它重新設定為 [強制]。 請參閱[啟用密碼保護](howto-password-ban-bad-on-premises-operations.md#enable-password-protection)。

3. 密碼原則已停用。 如果發生這種情況，請使用 Azure AD 密碼保護入口網站，將它重新設定為 [啟用]。 請參閱[啟用密碼保護](howto-password-ban-bad-on-premises-operations.md#enable-password-protection)。

4. 密碼驗證演算法可能會如預期般運作。 請參閱[如何評估密碼](concept-password-ban-bad.md#how-are-passwords-evaluated)。

## <a name="directory-services-repair-mode"></a>目錄服務修復模式

如果網域控制站在開機後進入目錄服務修復模式，則 DC 代理程式服務會偵測到此狀況，並將導致所有密碼驗證或強制活動停用，而不論目前作用中的原則設定為何。

## <a name="emergency-remediation"></a>緊急補救

如果發生 DC 代理程式服務造成問題的情況，可以立即將 DC 代理程式服務關閉。 DC 代理程式密碼篩選 dll 仍會嘗試呼叫非執行中服務，且會記錄警告事件 (10012、10013)，但在這段期間傳入的所有密碼都會被接受。 隨後如有需要，也可以透過 Windows 服務控制管理員，在啟動類型設為 [已停用] 的情況下設定 DC 代理程式服務。

另一個補救措施是在 Azure AD 密碼保護入口網站中，將啟用模式設為 [否]。 下載更新的原則之後，每個 DC 代理程式服務都將進入靜止模式，在此模式中會依原樣接受所有密碼。 如需詳細資訊，請參閱[強制模式](howto-password-ban-bad-on-premises-operations.md#enforce-mode)。

## <a name="domain-controller-demotion"></a>網域控制站降階

目前支援將仍在執行 DC 代理程式軟體的網域控制站降階。 不過，系統管理員應了解 DC 代理程式軟體會在降級程序期間繼續強制執行目前的密碼原則。 新的本機系統管理員帳戶密碼 (指定為降階作業的一部分) 會像任何其他密碼一樣受到驗證。 Microsoft 建議在 DC 降階程序中應為本機系統管理員帳戶選擇安全的密碼；但 DC 代理程式軟體對新的本機系統管理員帳戶密碼所做的驗證，可能會干擾到既有的降階作業程序。

在降階成功後，網域控制站即已重新啟動，並重新以一般成員伺服器的形式執行，且 DC 代理程式軟體會回復為以被動模式執行。 其後您可以隨時將其解除安裝。

## <a name="removal"></a>移除

如果決定要解除安裝公用預覽版軟體，並從網域和樹系清除所有相關的狀態，您可以使用下列步驟來完成這項工作：

> [!IMPORTANT]
> 這些步驟務必要依序執行。 如果有任何 Proxy 服務的執行個體仍繼續執行，它將會定期重新建立其 serviceConnectionPoint 物件。 如果有任何 DC 代理程式服務的執行個體仍繼續執行，它將會定期重新建立其 serviceConnectionPoint 物件與 sysvol 狀態。

1. 從所有機器解除安裝 Proxy 軟體。 此步驟**不需要**重新開機。
2. 從所有網域控制站解除安裝 DC 代理程式軟體。 此步驟**需要**重新開機。
3. 手動移除每個網域命名內容中的所有 Proxy 服務連接點。 您可以使用下列 Active Directory PowerShell 命令來找出這些物件的位置：

   ```PowerShell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   請勿省略 $keywords 變數值結尾處的星號 ("*")。

   透過 `Get-ADObject` 命令找到的產生物件隨後可使用管線傳送到 `Remove-ADObject`，或以手動方式刪除。 

4. 手動移除在每個網域命名內容中所有的 DC 代理程式連接點。 樹系中的每個網域控制站可能各有一個此類物件，視先前部署公用預覽版軟體的範圍而定。 您可以使用下列 Active Directory PowerShell 命令來找出該物件的位置：

   ```PowerShell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   透過 `Get-ADObject` 命令找到的產生物件隨後可使用管線傳送到 `Remove-ADObject`，或以手動方式刪除。

   請勿省略 $keywords 變數值結尾處的星號 ("*")。

5. 手動移除樹系層級的組態狀態。 樹系組態狀態會保存在 Active Directory 組態命名內容中的容器內。 您可以依照下列方式加以探索和刪除：

   ```PowerShell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. 手動刪除下列資料夾及其所有內容，以手動移除所有 sysvol 相關狀態：

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   如有必要，也可在指定的網域控制站上從本機存取此路徑；預設位置會類似於下列路徑：

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   如果已將 sysvol 共用設定於非預設位置中，此路徑將會不同。

## <a name="next-steps"></a>後續步驟

[Azure AD 密碼保護的常見問題集](howto-password-ban-bad-on-premises-faq.md)

如需關於全域和自訂禁用密碼清單的詳細資訊，請參閱[禁用不當密碼](concept-password-ban-bad.md)一文
