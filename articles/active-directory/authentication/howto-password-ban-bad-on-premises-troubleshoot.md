---
title: Azure AD 密碼保護的疑難排解-Azure Active Directory
description: 瞭解 Azure AD 的密碼保護一般疑難排解
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
ms.openlocfilehash: 1d96f5bb189dfd20c65fc6fc6ddcb8fff66d52ff
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68666228"
---
# <a name="azure-ad-password-protection-troubleshooting"></a>Azure AD 密碼保護的疑難排解

部署 Azure AD 密碼保護之後，可能需要進行疑難排解。 本文將深入說明以協助您了解一些常見的疑難排解步驟。

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>DC 代理程式找不到目錄中的 proxy

此問題的主要徵兆是 DC 代理程式管理事件記錄檔中的30017事件。

此問題的常見原因是尚未註冊 proxy。 如果 proxy 已註冊, 則可能會因為 AD 複寫延遲而有一些延遲, 直到特定的 DC 代理程式能夠看到該 proxy 為止。

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>DC 代理程式無法與 proxy 通訊

此問題的主要徵兆是 DC 代理程式管理事件記錄檔中的30018事件。 這個問題可能有數個可能的原因:

1. DC 代理程式位於網路的隔離部分, 不允許網路連線到已註冊的 proxy。 因此, 只要其他 DC 代理程式可以與 proxy 進行通訊, 以便從 Azure 下載密碼原則, 此問題就可能會是良性的, 而隔離的 DC 接著會透過 sysvol 共用中的原則檔案複寫取得該原則。

1. Proxy 主機電腦封鎖對 RPC 端點對應程式端點的存取 (埠 135)

   Azure AD 密碼保護 Proxy 安裝程式會自動建立 Windows 防火牆輸入規則, 以允許存取埠135。 如果稍後刪除或停用此規則, DC 代理程式將無法與 Proxy 服務進行通訊。 如果已停用內建 Windows 防火牆代替另一個防火牆產品, 您必須將該防火牆設定為允許存取埠135。

1. Proxy 主機電腦封鎖了對 Proxy 服務接聽的 RPC 端點 (動態或靜態) 的存取

   Azure AD 密碼保護 Proxy 安裝程式會自動建立 Windows 防火牆輸入規則, 以允許存取由 Azure AD 的密碼保護 Proxy 服務所接聽的任何輸入埠。 如果稍後刪除或停用此規則, DC 代理程式將無法與 Proxy 服務進行通訊。 如果已停用內建 Windows 防火牆代替另一個防火牆產品, 您必須將該防火牆設定為允許存取由 Azure AD 密碼保護 Proxy 服務所接聽的任何輸入埠。 如果 Proxy 服務已設定為在特定靜態 RPC 埠 (使用`Set-AzureADPasswordProtectionProxyConfiguration` Cmdlet) 接聽, 則此設定可能會更具體。

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>Proxy 服務無法與 Azure 通訊

1. 請確定 proxy 電腦可以連線到[部署需求](howto-password-ban-bad-on-premises-deploy.md)中列出的端點。

1. 請確定樹系和所有 proxy 伺服器都已針對相同的 Azure 租使用者註冊。

   您可以藉由`Get-AzureADPasswordProtectionProxy`執行和`Get-AzureADPasswordProtectionDCAgent` PowerShell Cmdlet 來檢查此需求, 然後比較`AzureTenant`每個傳回專案的屬性。 若要正確操作, 報告的租使用者名稱在所有 DC 代理程式和 proxy 伺服器上都必須相同。

   如果 Azure 租使用者註冊不符條件存在, 您可以視需要執行`Register-AzureADPasswordProtectionProxy`和/或`Register-AzureADPasswordProtectionForest` PowerShell Cmdlet 來修正此問題, 並務必使用來自相同 Azure 租使用者的認證來進行所有註冊。

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>DC 代理程式無法加密或解密密碼原則檔案

此問題可能會以各種不同的徵兆來進行資訊清單, 但通常會有常見的根本原因。

Azure AD 密碼保護對於 Microsoft 金鑰發佈服務所提供的加密和解密功能有重要的相依性, 這可在執行 Windows Server 2012 和更新版本的網域控制站上取得。 KDS 根金鑰服務必須在網域中的所有 Windows Server 2012 和更新版本的網域控制站上啟用和運作。

根據預設, KDS 根金鑰服務的服務啟動模式會設定為手動 (觸發程式啟動)。 此設定表示當用戶端第一次嘗試使用此服務時, 它會隨選啟動。 此預設服務啟動模式可讓 Azure AD 密碼保護正常執行。

如果 KDS 根金鑰服務啟動模式已設定為停用, 則必須先修正此設定, Azure AD 密碼保護才會正常運作。

這個問題的簡單測試是透過服務管理 MMC 主控台手動啟動 KDS 根金鑰服務, 或使用其他管理工具 (例如, 從命令提示字元主控台執行 "net start kdssvc")。 KDS 根金鑰服務預期會順利啟動並繼續執行。

KDS 根金鑰服務無法啟動的最常見根本原因是 Active Directory 網域控制站物件位於預設的網域控制站 OU 之外。 KDS 根金鑰服務不支援此設定, 而且不是 Azure AD 密碼保護所加諸的限制。 這種情況的修正方法是將網域控制站物件移至預設網域控制站 OU 底下的位置。

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>已接受弱式密碼, 但不應

這個問題可能有幾個原因。

1. 您的 DC 代理程式無法下載原則, 或無法解密現有的原則。 請檢查上述主題中的可能原因。

1. 密碼原則強制模式仍會設為 [稽核]。 如果此設定作用中, 請將它重新設定為使用 Azure AD 密碼保護入口網站來強制執行。 請參閱[啟用密碼保護](howto-password-ban-bad-on-premises-operations.md#enable-password-protection)。

1. 密碼原則已停用。 如果此設定有效, 請使用 Azure AD 密碼保護入口網站, 將它重新設定為 [啟用]。 請參閱[啟用密碼保護](howto-password-ban-bad-on-premises-operations.md#enable-password-protection)。

1. 您尚未在網域中的所有網域控制站上安裝 DC 代理程式軟體。 在此情況下, 很容易確保遠端 Windows 用戶端在密碼變更作業期間以特定網域控制站為目標。 如果您認為已成功將 dc 代理程式軟體安裝在特定 DC 上, 您可以藉由仔細檢查 DC 代理程式管理事件記錄檔來確認: 不論結果為何, 至少會有一個事件記錄密碼的結果驗證. 如果變更密碼的使用者沒有任何事件, 則密碼變更可能是由不同的網域控制站處理。

   作為替代測試, 請嘗試 setting\changing 密碼, 同時登入 DC 代理程式軟體安裝所在的 DC。 不建議將這項技術用於生產 Active Directory 網域。

   雖然支援增量部署 DC 代理程式軟體受限於這些限制, 但 Microsoft 強烈建議您儘快將 DC 代理程式軟體安裝在網域中的所有網域控制站上。

1. 密碼驗證演算法實際上可能會如預期般運作。 請參閱[如何評估密碼](concept-password-ban-bad.md#how-are-passwords-evaluated)。

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Ntdsutil.exe 無法設定弱式 DSRM 密碼

Active Directory 一律會驗證新的目錄服務修復模式密碼, 以確定它符合網域的密碼複雜性需求;這種驗證也會呼叫密碼篩選 dll (例如 Azure AD 密碼保護)。 如果新的 DSRM 密碼遭到拒絕, 則會產生下列錯誤訊息:

```text
C:\>ntdsutil.exe
ntdsutil: set dsrm password
Reset DSRM Administrator Password: reset password on server null
Please type password for DS Restore Mode Administrator Account: ********
Please confirm new password: ********
Setting password failed.
        WIN32 Error Code: 0xa91
        Error Message: Password doesn't meet the requirements of the filter dll's
```

當 Azure AD 密碼保護記錄 Active Directory DSRM 密碼的密碼驗證事件記錄檔事件時, 事件記錄檔訊息預期不會包含使用者名稱。 之所以會發生這種情況, 是因為 DSRM 帳戶是不屬於實際 Active Directory 網域的本機帳戶。  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>網域控制站複本升級因弱式 DSRM 密碼而失敗

在 DC 升級程式期間, 會將新的目錄服務修復模式密碼提交到網域中的現有 DC 進行驗證。 如果新的 DSRM 密碼遭到拒絕, 則會產生下列錯誤訊息:

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

就像上述問題一樣, 任何 Azure AD 密碼保護密碼驗證結果事件在此案例中將會有空白的使用者名稱。

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>由於本機系統管理員密碼弱, 導致網域控制站降級失敗

目前支援將仍在執行 DC 代理程式軟體的網域控制站降階。 不過，系統管理員應了解 DC 代理程式軟體會在降級程序期間繼續強制執行目前的密碼原則。 新的本機系統管理員帳戶密碼 (指定為降階作業的一部分) 會像任何其他密碼一樣受到驗證。 Microsoft 建議您為本機系統管理員帳戶選擇安全的密碼, 做為 DC 降級程式的一部分。

在降階成功後，網域控制站即已重新啟動，並重新以一般成員伺服器的形式執行，且 DC 代理程式軟體會回復為以被動模式執行。 其後您可以隨時將其解除安裝。

## <a name="booting-into-directory-services-repair-mode"></a>開機進入目錄服務修復模式

如果網域控制站開機進入目錄服務修復模式, 則 DC 代理程式服務會偵測這種狀況, 而不論目前作用中的原則設定為何, 都將導致所有密碼驗證或強制活動停用。

## <a name="emergency-remediation"></a>緊急補救

如果發生 DC 代理程式服務造成問題的情況，可以立即將 DC 代理程式服務關閉。 DC 代理程式密碼篩選 dll 仍會嘗試呼叫非執行中服務，且會記錄警告事件 (10012、10013)，但在這段期間傳入的所有密碼都會被接受。 隨後如有需要，也可以透過 Windows 服務控制管理員，在啟動類型設為 [已停用] 的情況下設定 DC 代理程式服務。

另一個補救措施是在 Azure AD 密碼保護入口網站中，將啟用模式設為 [否]。 下載更新的原則之後，每個 DC 代理程式服務都將進入靜止模式，在此模式中會依原樣接受所有密碼。 如需詳細資訊，請參閱[強制模式](howto-password-ban-bad-on-premises-operations.md#enforce-mode)。

## <a name="removal"></a>移除

如果決定卸載 Azure AD 密碼保護軟體, 並從網域和樹系清除所有相關的狀態, 則可以使用下列步驟來完成這項工作:

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

4. 手動移除在每個網域命名內容中所有的 DC 代理程式連接點。 樹系中的每個網域控制站可能會有一個這些物件, 這取決於軟體的部署範圍。 您可以使用下列 Active Directory PowerShell 命令來找出該物件的位置：

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
