---
title: Azure AD Domain Services 的管理概念 |Microsoft Docs
description: 瞭解如何管理 Azure Active Directory Domain Services 受控網域，以及使用者帳戶和密碼的行為
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: iainfou
ms.openlocfilehash: f239bab48e732755361fe734fdc24b37d3823c63
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481013"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 中的使用者帳戶、密碼和管理的管理概念

當您建立並執行 Azure Active Directory Domain Services （AD DS）受控網域時，相較于傳統內部部署 AD DS 環境，其行為會有一些差異。 您使用 Azure AD DS 中的相同系統管理工具作為自我管理的網域，但是您無法直接存取網域控制站（DC）。 根據使用者帳戶建立的來源而定，密碼原則和密碼雜湊的行為也有一些差異。

此概念性文章詳述如何管理 Azure AD DS 受控網域，以及使用者帳戶的不同行為（視其建立方式而定）。

## <a name="domain-management"></a>定義域管理

在 Azure AD DS 中，包含所有資源（例如使用者和群組、認證和原則）的網域控制站（Dc）是受控服務的一部分。 針對冗余，會建立兩個 Dc 做為 Azure AD DS 受控網域的一部分。 您無法登入這些 Dc 來執行管理工作。 相反地，您會建立已加入 Azure AD DS 受控網域的管理 VM，然後安裝一般的 AD DS 管理工具。 例如，您可以使用 Active Directory 管理中心或 Microsoft Management Console （MMC）嵌入式管理單元（如 DNS）或群組原則物件。

## <a name="user-account-creation"></a>建立使用者帳戶

您可以透過多種方式，在 Azure AD DS 中建立使用者帳戶。 大部分的使用者帳戶會在中從 Azure AD 同步處理，也可以包含從內部部署 AD DS 環境同步處理的使用者帳戶。 您也可以直接在 Azure AD DS 中手動建立帳戶。 某些功能（例如初始密碼同步處理或密碼原則）的行為會根據建立使用者帳戶的方式和位置而有所不同。

* 使用者帳戶可以從 Azure AD 同步處理。 這包括直接在 Azure AD 中建立的僅限雲端使用者帳戶，以及使用 Azure AD Connect 從內部部署 AD DS 環境同步處理的混合式使用者帳戶。
    * Azure AD DS 中的大部分使用者帳戶都是透過 Azure AD 的同步處理常式來建立。
* 使用者帳戶可以在 Azure AD DS 受控網域中手動建立，而且不存在於 Azure AD 中。
    * 如果您需要為只在 Azure AD DS 中執行的應用程式建立服務帳戶，您可以在受控網域中手動建立它們。 由於同步處理是從 Azure AD 單向的，因此在 Azure AD DS 中建立的使用者帳戶不會同步處理回 Azure AD。

## <a name="password-policy"></a>密碼原則

Azure AD DS 包含預設密碼原則，可定義帳戶鎖定、密碼最長使用期限和密碼複雜度等專案的設定。 帳戶鎖定原則之類的設定會套用至 Azure AD DS 中的所有使用者，而不論上一節中所述的使用者建立方式為何。 一些設定（例如最小密碼長度和密碼複雜度）僅適用于直接在 Azure AD DS 中建立的使用者。

您可以建立自己的自訂密碼原則，以覆寫 Azure AD DS 中的預設原則。 然後，這些自訂原則就可以視需要套用到特定的使用者群組。

如需有關如何根據使用者建立的來源套用密碼原則之差異的詳細資訊，請參閱[受控網域上的密碼和帳戶鎖定原則][password-policy]。

## <a name="password-hashes"></a>密碼雜湊

若要在受控網域上驗證使用者，Azure AD DS 需要其格式適用 NT LAN Manager (NTLM) 和 Kerberos 驗證的密碼雜湊。 除非您針對租用戶啟用 Azure AD DS，否則 Azure AD 不會以 NTLM 或 Kerberos 驗證所需的格式產生或儲存密碼雜湊。 基於安全性考量，Azure AD 也不會儲存任何純文字形式的密碼認證。 因此，Azure AD 無法根據使用者的現有認證自動產生這些 NTLM 或 Kerberos 密碼雜湊。

對於僅限雲端的使用者帳戶，使用者必須變更其密碼，才能使用 Azure AD DS。 此密碼變更程序會在 Azure AD 中產生並儲存 Kerberos 和 NTLM 驗證的密碼雜湊。

針對使用 Azure AD Connect 從內部部署 AD DS 環境同步處理的使用者，[啟用密碼雜湊的同步][hybrid-phs]處理。

> [!IMPORTANT]
> Azure AD Connect 只有在為 Azure AD 租使用者啟用 Azure AD DS 時，才會同步處理舊版密碼雜湊。 如果您只使用 Azure AD Connect 來同步處理內部部署 AD DS 環境與 Azure AD，則不會使用舊版密碼雜湊。
>
> 如果您的繼承應用程式未使用 NTLM 驗證或 LDAP 簡單系結，我們建議您停用 Azure AD DS 的 NTLM 密碼雜湊同步處理。 如需詳細資訊，請參閱[停用弱式加密套件和 NTLM 認證雜湊同步][secure-domain]處理。

一旦正確設定之後，可用的密碼雜湊就會儲存在 Azure AD DS 受控網域中。 如果您刪除 Azure AD DS 受控網域，此時儲存的任何密碼雜湊也會一併刪除。 如果您稍後建立 Azure AD DS 受控網域，則 Azure AD 中同步處理的認證資訊無法重複使用-您必須重新設定密碼雜湊同步處理，以再次儲存密碼雜湊。 先前加入網域的 VM 或使用者將無法立即進行驗證 - Azure AD 需要在新的 Azure AD DS 受控網域中產生並儲存密碼雜湊。 如需詳細資訊，請參閱 [Azure AD DS 和 Azure AD Connect 的密碼雜湊同步程序][azure-ad-password-sync]。

> [!IMPORTANT]
> Azure AD Connect 應該只安裝並設定為與內部部署 AD DS 環境同步處理。 不支援在 Azure AD DS 受控網域中安裝 Azure AD Connect，以將物件同步處理回 Azure AD。

## <a name="forests-and-trusts"></a>樹系和信任

*樹*系是 Active Directory Domain Services （AD DS）用來將一或多個*網域*分組的邏輯結構。 然後，網域會儲存使用者或群組的物件，並提供驗證服務。

在 Azure AD DS 中，樹系僅包含一個網域。 內部部署 AD DS 樹系通常包含許多網域。 在大型組織中，尤其是在合併和收購之後，您最後可能會有多個內部部署樹系，每個樹系都包含多個網域。

根據預設，系統會將 Azure AD DS 受控網域建立為*使用者*樹系。 這種類型的樹系會同步處理 Azure AD 的所有物件，包括在內部部署 AD DS 環境中建立的任何使用者帳戶。 使用者帳戶可以直接針對 Azure AD DS 受控網域進行驗證，例如登入已加入網域的 VM。 使用者樹系適用于可以同步處理密碼雜湊，而使用者不使用獨佔式登入方法（例如智慧卡驗證）的情況。

在 Azure AD DS*資源*樹系中，使用者會從其內部部署 AD DS 對單向樹系*信任*進行驗證。 使用此方法時，使用者物件和密碼雜湊不會同步處理至 Azure AD DS。 使用者物件和認證僅存在於內部部署 AD DS。 這種方法可讓企業裝載 Azure 中的資源和應用程式平臺，而這些是依賴傳統驗證（例如 LDAPS、Kerberos 或 NTLM），但會移除任何驗證問題或考慮。 Azure AD DS 資源樹系目前為預覽狀態。

如需有關 Azure AD DS 中樹系類型的詳細資訊，請參閱[什麼是資源][concepts-forest]樹系？和[樹系信任在 Azure AD DS 中如何運作？][concepts-trust]

## <a name="next-steps"></a>後續步驟

若要開始使用，請[建立 AZURE AD DS 受控網域][create-instance]。

<!-- INTERNAL LINKS -->
[password-policy]: password-policy.md
[hybrid-phs]: tutorial-configure-password-hash-sync.md#enable-synchronization-of-password-hashes
[secure-domain]: secure-your-domain.md
[azure-ad-password-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[create-instance]: tutorial-create-instance.md
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
