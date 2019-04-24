---
title: 在 Azure AD 密碼保護-Azure Active Directory 疑難排解
description: 了解 Azure AD 密碼保護常見的疑難排解
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 108ead982529d2ac6549cceffd9d2177ab6456bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60414761"
---
# <a name="azure-ad-password-protection-troubleshooting"></a>Azure AD 密碼保護的疑難排解

部署 Azure AD 密碼保護之後，可能需要進行疑難排解。 本文將深入說明以協助您了解一些常見的疑難排解步驟。

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>DC 代理程式在目錄中找不到 proxy

這個問題的主要的徵兆是 30017 DC 代理程式管理的事件記錄檔中的事件。

此問題的常見的原因是尚未註冊 proxy。 如果尚未註冊 proxy，可能有一些延遲，因為 AD 複寫延遲直到特定的 DC 代理程式可以看到該 proxy。

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>DC 代理程式不能與 proxy 通訊

這個問題的主要的徵兆是 30018 DC 代理程式管理的事件記錄檔中的事件。 這可能有數個可能的原因：

1. DC 代理程式位於隔離網路的一部分，不允許已註冊的 proxy(s) 的網路連線。 這個問題可能因此是 expected\benign，只要與 proxy(s) 通訊其他 DC 代理程式，可以將它以便密碼原則從 Azure 下載，然後將會取得由透過複寫的 sysvol 共用中的原則檔案隔離網域控制站。

1. Proxy 主機電腦封鎖對 RPC 端點對應程式端點 （連接埠 135） 的存取

   Azure AD 密碼保護 Proxy 安裝程式會自動建立的 Windows 防火牆輸入的規則允許存取連接埠 135。 如果這項規則之後遭到刪除或停用，DC 代理程式將無法與 Proxy 服務通訊。 如果已停用 Windows 防火牆為內建代替其他防火牆產品，您必須設定防火牆以允許存取連接埠 135。

1. Proxy 主機電腦正在封鎖 RPC 端點 （動態或靜態） 的存取權的 Proxy 服務所接聽

   Azure AD 密碼保護 Proxy 安裝程式會自動建立的 Windows 防火牆輸入的規則，允許任何輸入連接埠存取的 Azure AD 密碼保護 Proxy 服務所接聽。 如果這項規則之後遭到刪除或停用，DC 代理程式將無法與 Proxy 服務通訊。 如果已停用 Windows 防火牆為內建代替其他防火牆產品，您必須設定防火牆以允許任何輸入連接埠來存取 Azure AD 密碼保護 Proxy 服務所接聽。 這項設定可能會進行更特定，如果已設定為接聽特定的靜態 RPC 連接埠的 Proxy 服務 (使用`Set-AzureADPasswordProtectionProxyConfiguration`cmdlet)。

## <a name="the-proxy-service-can-receive-calls-from-dc-agents-in-the-domain-but-is-unable-to-communicate-with-azure"></a>Proxy 服務可以接收來自網域中的 DC 代理程式的呼叫，但無法與 Azure 通訊

1. 請確定 proxy 機器能夠連線到中列出的端點[部署需求](howto-password-ban-bad-on-premises-deploy.md)。

1. 確定樹系和所有針對相同的 Azure 租用戶中註冊伺服器的 proxy。

   您可以執行來檢查這`Get-AzureADPasswordProtectionProxy`並`Get-AzureADPasswordProtectionDCAgent`PowerShell cmdlet，然後比較`AzureTenant`屬性的每個傳回的項目。 正確操作它們必須是相同的樹系內跨所有 DC 的代理程式和 proxy 伺服器。

   如果不存在的 Azure 租用戶註冊不符合條件，這可以修復執行`Register-AzureADPasswordProtectionProxy`及/或`Register-AzureADPasswordProtectionForest`PowerShell cmdlet，如有需要請務必使用相同的 Azure 租用戶中的認證來進行所有註冊。

## <a name="the-dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files-and-other-state"></a>DC 代理程式 」 無法加密或解密的密碼原則檔和其他狀態

這個問題可以使用各種不同的徵狀資訊清單，但通常有常見的根本原因。

Azure AD 密碼保護對加密和解密所提供的功能 Microsoft 金鑰發佈服務，這是可在網域控制站執行 Windows Server 2012 及更新版本的重要相依性。 KDS 服務必須啟用並在所有 Windows Server 2012 和更新版本的網域控制站在網域上運作。

根據預設 KDS 服務的服務啟動模式設定為手動 （觸發程序啟動）。 此設定表示，第一次用戶端嘗試使用服務，它會依需求啟動。 這個預設服務啟動模式為可接受的運作的 Azure AD 密碼保護。

如果 KDS 服務啟動模式已設定為 停用，此設定必須修正後將會正常運作的 Azure AD 密碼保護。

此問題的簡單測試是以手動方式啟動 KDS 服務，透過服務管理 MMC 主控台中，或使用其他服務管理工具 （例如，執行"net start kdssvc"從命令提示字元主控台）。 KDS 服務必須已成功啟動，並維持運作狀態。

KDS 服務無法啟動的最常見根本原因是 Active Directory 網域控制站物件位於預設網域控制站 OU 之外。 此設定不支援 KDS 服務，並不是 Azure AD 密碼保護所加諸的限制。 此條件的修正方法是將網域控制站物件移至 預設網域控制站 OU 下的位置。

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>弱式密碼會被接受，但不是應該

這個問題可能會有幾個原因。

1. 您的 DC 代理程式無法下載原則，或無法解密現有的原則。 請檢查上述主題中可能的原因。

1. 密碼原則強制模式仍會設為 [稽核]。 如果這項設定作用中時，請將加以重新設定來強制執行使用 Azure AD 密碼保護入口網站。 請參閱[啟用密碼保護](howto-password-ban-bad-on-premises-operations.md#enable-password-protection)。

1. 密碼原則已停用。 如果此設定作用中時，重新設定為使用 Azure AD 密碼保護入口網站啟用。 請參閱[啟用密碼保護](howto-password-ban-bad-on-premises-operations.md#enable-password-protection)。

1. 您尚未在網域中的所有網域控制站上安裝 DC 代理程式軟體。 在此情況下，很難確保遠端的 Windows 用戶端目標特定網域控制站，在密碼變更作業期間。 如果您認為您已成功的目標 DC 代理程式軟體安裝所在的特定 DC 時，您可以確認仔細檢查 DC 代理程式管理的事件記錄檔： 無論結果，會有至少一個事件來記錄密碼的結果驗證。 如果沒有任何使用者變更其密碼時出現的事件，然後變更密碼可能處理由不同的網域控制站。

   做為替代的測試，請嘗試 setting\changing 密碼而直接登入 DC 代理程式軟體安裝所在的 DC。 這項技術不建議用於生產環境的 Active Directory 網域。

   雖然受限於這些限制，都支援累加部署 DC 代理程式軟體，Microsoft 強烈建議 DC 代理程式軟體儘速安裝在網域中的所有網域控制站上。

1. 密碼驗證演算法可能實際上會如預期運作。 請參閱[如何評估密碼](concept-password-ban-bad.md#how-are-passwords-evaluated)。

## <a name="directory-services-repair-mode"></a>目錄服務修復模式

如果網域控制站會開機進入目錄服務修復模式，DC 代理程式服務會偵測這種情況，且會導致所有密碼驗證或強制執行的活動，以停用，不論目前作用中的原則組態。

## <a name="emergency-remediation"></a>緊急補救

如果發生 DC 代理程式服務造成問題的情況，可以立即將 DC 代理程式服務關閉。 DC 代理程式密碼篩選 dll 仍會嘗試呼叫非執行中服務，且會記錄警告事件 (10012、10013)，但在這段期間傳入的所有密碼都會被接受。 隨後如有需要，也可以透過 Windows 服務控制管理員，在啟動類型設為 [已停用] 的情況下設定 DC 代理程式服務。

另一個補救措施是在 Azure AD 密碼保護入口網站中，將啟用模式設為 [否]。 下載更新的原則之後，每個 DC 代理程式服務都將進入靜止模式，在此模式中會依原樣接受所有密碼。 如需詳細資訊，請參閱[強制模式](howto-password-ban-bad-on-premises-operations.md#enforce-mode)。

## <a name="domain-controller-demotion"></a>網域控制站降階

目前支援將仍在執行 DC 代理程式軟體的網域控制站降階。 不過，系統管理員應了解 DC 代理程式軟體會在降級程序期間繼續強制執行目前的密碼原則。 新的本機系統管理員帳戶密碼 (指定為降階作業的一部分) 會像任何其他密碼一樣受到驗證。 Microsoft 建議在 DC 降階程序中應為本機系統管理員帳戶選擇安全的密碼；但 DC 代理程式軟體對新的本機系統管理員帳戶密碼所做的驗證，可能會干擾到既有的降階作業程序。

在降階成功後，網域控制站即已重新啟動，並重新以一般成員伺服器的形式執行，且 DC 代理程式軟體會回復為以被動模式執行。 其後您可以隨時將其解除安裝。

## <a name="removal"></a>移除

如果它決定要解除安裝 Azure AD 密碼防護軟體且已清除所有相關的狀態從 網域和樹系，就可以使用下列步驟來完成這項工作：

> [!IMPORTANT]
> 這些步驟務必要依序執行。 如果有任何 Proxy 服務的執行個體仍繼續執行，它將會定期重新建立其 serviceConnectionPoint 物件。 如果有任何 DC 代理程式服務的執行個體仍繼續執行，它將會定期重新建立其 serviceConnectionPoint 物件與 sysvol 狀態。

1. 從所有機器解除安裝 Proxy 軟體。 此步驟**不需要**重新開機。
2. 從所有網域控制站解除安裝 DC 代理程式軟體。 此步驟**需要**重新開機。
3. 手動移除每個網域命名內容中的所有 Proxy 服務連接點。 您可以使用下列 Active Directory PowerShell 命令來找出這些物件的位置：

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   請勿省略 $keywords 變數值結尾處的星號 ("*")。

   透過 `Get-ADObject` 命令找到的產生物件隨後可使用管線傳送到 `Remove-ADObject`，或以手動方式刪除。

4. 手動移除在每個網域命名內容中所有的 DC 代理程式連接點。 可能有其中一個這些物件每個網域控制站的樹系，取決於軟體已廣。 您可以使用下列 Active Directory PowerShell 命令來找出該物件的位置：

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   透過 `Get-ADObject` 命令找到的產生物件隨後可使用管線傳送到 `Remove-ADObject`，或以手動方式刪除。

   請勿省略 $keywords 變數值結尾處的星號 ("*")。

5. 手動移除樹系層級的組態狀態。 樹系組態狀態會保存在 Active Directory 組態命名內容中的容器內。 您可以依照下列方式加以探索和刪除：

   ```powershell
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
