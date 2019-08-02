---
title: 在 Azure 澳大利亞保護閘道的遠端系統管理
description: 在澳大利亞地區設定安全遠端系統管理的指導方針, 以符合澳大利亞政府政策、法規和法規的特定需求。
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 827dffc1c7544d9373b5f8d4426ea8c448fa25ab
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571597"
---
# <a name="secure-remote-administration-of-your-gateway-in-azure-australia"></a>在 Azure 澳大利亞保護閘道的遠端系統管理

系統管理活動在安全且受到控制的情況之下, 其可用性和完整性非常重要。 系統管理活動應該從安全的裝置、透過安全的連線來完成, 並由強式驗證和 authorisation 程式提供支援。 安全的遠端系統管理可確保只有經過授權的系統管理員, 才會執行授權的動作。

本文提供的資訊是關於為裝載于 Azure 中的網際網路可存取系統, 執行安全的遠端系統管理功能, 並配合澳大利亞網路安全中心 (ACSC) 消費者指導方針和 ACSC 資訊的意圖安全性手冊 (ISM)。

## <a name="australian-cyber-security-centre-acsc-requirements"></a>澳大利亞網路安全中心 (ACSC) 需求

美國聯邦系統的整體安全性需求是在 ISM 中定義。 為了協助提供安全管理的「聯邦」實體, ACSC 已[發行 ACSC 保護:安全管理](https://www.acsc.gov.au/publications/protect/secure-administration.htm)

本檔討論安全管理的重要性, 並建議執行安全管理環境的一種方法。 本檔說明安全管理解決方案的元素, 如下所示:

|元素   |描述   |
|---|---|
|特殊許可權存取控制   |控制特殊許可權帳戶的存取是一種基本的安全性控制, 可保護特殊許可權帳戶免于誤用。 存取控制方法將包含「最低許可權」和「需要擁有」的概念, 以及管理服務帳戶和員工移動的進程和程式。   |
|多重要素驗證   |除了使用者名稱和複雜密碼 (例如實體權杖或智慧卡) 以外, 執行額外的驗證因素有助於保護重要資產。 如果敵人危害特殊許可權帳戶的認證, 由於所有系統管理動作都必須經過某種形式的多重要素驗證, 因此可能會大幅降低結果。|
|具有特殊許可權的工作站|使用已知的安全環境進行系統管理工作, 可能會導致網路因為執行額外的安全性控制而遭到入侵。|
|記錄與稽核   |自動產生、收集及分析來自工作站、伺服器、網路裝置及跳躍箱的安全性和系統管理相關事件, 將可偵測危害並嘗試進行危害。 自動化可讓組織更快速地回應, 減少危害的影響。|
|網路分割和隔離|將網路分割成邏輯區域 (例如不同的安全性網域), 並藉由限制從某個區域流向另一個區域的資料類型, 進一步隔離這些邏輯網路, 會限制橫向移動。 分割可防止敵人取得其他資源的存取權。|
|跳躍方塊|「跳躍箱」是一種強化的遠端存取服務器, 通常會使用 Microsoft 的遠端桌面服務或安全殼層 (SSH) 軟體。 跳躍框可做為系統管理員的逐步執行點, 其中包含從專用主機執行的所有系統管理動作。|
|

本文提供的參考架構, 可讓您瞭解如何將上述元素用於 Azure 中所部署系統的安全管理。

## <a name="architecture"></a>架構

提供安全的系統管理功能, 需要多個元件共同合作來形成一致的解決方案。 在提供的參考架構中, 元件會對應至 ACSC 保護中[所述的元素:安全管理](https://www.acsc.gov.au/publications/protect/secure-administration.htm)

![Azure 安全的遠端系統管理架構](media/remote-admin.png)

## <a name="components"></a>元件

此架構的設計是為了確保特殊許可權帳戶只被授與必要的許可權, 安全地識別, 然後只從授權的裝置和透過安全的通訊, 提供系統管理介面的存取權。控制和審核的機制。

|方案| 元件|元素|
|---|---|---|
|保護裝置 |<ul><li>具有特殊許可權的工作站</li><li>行動裝置</li><li>Microsoft Intune</li><li>群組原則</li><li>跳躍伺服器/防禦主機</li><li>即時 (JIT) 系統管理</li></ul> |<ul><li>具有特殊許可權的工作站</li><li>跳躍方塊</li></ul>|
|安全通訊 |<ul><li>Azure 入口網站</li><li>Azure VPN 閘道</li><li>遠端桌面 (RD) 閘道</li><li>網路安全性群組 (NSG)</li></ul> |<ul><li>網路分割和隔離</li></ul>|
|增強式驗證 |<ul><li>網域控制站 (DC)</li><li>Azure Active Directory (Azure AD)</li><li>網路原則伺服器 (NPS)</li><li>Azure MFA</li></ul> |<ul><li>多重要素驗證</li></ul> |
|強式 Authorisation |<ul><li>Identity and Access Management (IAM)</li><li>Privileged Identity Management (PIM)</li><li>條件式存取</li></ul>|<ul><li>特殊許可權存取控制</li></ul>|
|||

>[!NOTE]
>如需記錄和審核元素的詳細資訊, 請參閱關於[閘道記錄、審核和可見度](gateway-log-audit-visibility.md)的文章

## <a name="administration-workflow"></a>系統管理工作流程

管理 Azure 中部署的系統分成兩種不同的類別, 管理 Azure 設定和管理 Azure 中部署的工作負載。 Azure 設定是透過 Azure 入口網站進行, 而且工作負載管理是透過系統管理機制完成, 例如遠端桌面通訊協定 (RDP)、安全殼層 (SSH) 或 PaaS 功能 (使用 SQL Management Studio 之類的工具)。

取得系統管理的存取權是一個多步驟的程式, 其中包含架構中列出的元件, 而且需要存取 Azure 入口網站和 Azure 設定, 才能對 Azure 工作負載進行存取。

>[!NOTE]
> 這裡所述的步驟是使用 Azure 圖形化使用者介面 (GUI) 元件的一般程式。 您也可以使用 PowerShell 之類的其他介面來完成這些步驟。

### <a name="azure-configuration-and-azure-portal-access"></a>Azure 設定和 Azure 入口網站存取

|步驟 |描述 |
|---|---|
|具有特殊許可權的工作站登入 |系統管理員使用系統管理認證來登入特殊許可權工作站。 群組原則控制項可防止非系統管理帳戶向特殊許可權工作站進行驗證, 並防止系統管理帳戶向非特殊許可權的工作站進行驗證。 Microsoft Intune 會管理特殊許可權工作站的相容性, 以確保它是最新的軟體修補程式、反惡意程式碼和其他合規性需求。 |
|Azure 入口網站登入 |系統管理員會使用傳輸層安全性 (TLS) 來開啟 web 瀏覽器, 並登入使用管理認證的 Azure 入口網站。 驗證要求會透過 Azure Active Directory 直接處理, 或透過 Active Directory 同盟服務 (AD FS) 或傳遞驗證等驗證機制來處理。 |
|Azure MFA |Azure MFA 會將驗證要求傳送至特殊許可權帳戶的已註冊行動裝置。 行動裝置是由 Intune 管理, 以確保符合安全性需求。 系統管理員必須先向行動裝置進行驗證, 然後再使用 PIN 或生物特徵辨識系統向 Microsoft Authenticator 應用程式進行驗證, 才會向 Azure MFA 授權嘗試驗證。 |
|條件式存取 |條件式存取原則會檢查驗證嘗試, 以確保它符合必要的需求, 例如連線來源的 IP 位址、特殊許可權帳戶的群組成員資格, 以及的管理與合規性狀態。Intune 所報告的特殊許可權工作站。 |
|Privileged Identity Management (PIM) |透過 Azure 入口網站, 系統管理員現在可以啟用或要求啟用已透過 PIM authorisation 的特殊許可權角色。 PIM 可確保特殊許可權帳戶沒有任何的系統管理許可權, 而且所有特殊許可權存取的要求都只是執行系統管理工作所需的時間。 PIM 也會記錄所有要求和啟用, 以供審核之用。 |
|Identity and Access Management|一旦安全地識別許可權帳戶並啟動角色之後, 系統管理員就會透過身分識別和存取管理, 提供他們已獲指派許可權的 Azure 訂用帳戶和資源的存取權。|
|

一旦特殊許可權帳戶完成取得 Azure 入口網站系統管理存取權的步驟之後, 即可設定工作負載的存取權, 並進行系統管理連接。

### <a name="azure-workload-administration"></a>Azure 工作負載管理

|步驟 |描述|
|---|---|
|即時 (JIT) 存取|若要取得虛擬機器的存取權, 系統管理員可以使用 JIT 從 RD 閘道 IP 位址, 以及從跳躍伺服器到相關工作負載虛擬機器的 RDP 或 SSH, 向跳躍伺服器要求存取 RDP。|
|Azure VPN 閘道|系統管理員現在會從其特殊許可權的工作站建立點對站 IPSec VPN 連線至 Azure VPN 閘道, 以執行憑證驗證來建立連線。|
|RD 閘道|系統管理員現在會使用遠端桌面連線設定中指定的 RD 閘道, 嘗試與跳躍伺服器的 RDP 連線。 RD 閘道具有可透過 Azure VPN 閘道連線來連接的私人 IP 位址。 RD 閘道上的原則控制是否授權特殊許可權帳戶存取要求的跳躍伺服器。 RD 閘道會提示系統管理員提供認證, 並將驗證要求轉寄到網路原則伺服器 (NPS)。|
|網路原則伺服器 (NPS)|NPS 會接收來自 RD 閘道的驗證要求, 並針對 Active Directory 驗證使用者名稱和密碼, 然後再將要求傳送至 Azure Active Directory 以觸發 Azure MFA 驗證要求。|
|Azure MFA|Azure MFA 會將驗證要求傳送至特殊許可權帳戶的已註冊行動裝置。 行動裝置是由 Intune 管理, 以確保符合安全性需求。 系統管理員必須先向行動裝置進行驗證, 然後再使用 PIN 或生物特徵辨識系統向 Microsoft Authenticator 應用程式進行驗證, 才會向 Azure MFA 授權嘗試驗證。|
|跳躍伺服器|成功驗證之後, 會使用傳輸層安全性 (TLS) 來加密 RDP 連線, 然後透過 RD 閘道並在跳躍伺服器上透過加密的 IPSec 通道傳送至 Azure VPN 閘道。 從跳躍伺服器, 系統管理員現在可以透過 RDP 或 SSH 連線至 JIT 要求中所指定的工作負載虛擬機器。|
|

## <a name="general-guidance"></a>一般指導

在執行本文中列出的元件時, 適用下列一般指引:

* 驗證服務的區域可用性, 確保所有資料都保留在授權位置, 並部署至 AU Central 或 AU Central 2 作為受保護工作負載的第一個喜好設定

* 如需個別服務的認證狀態, 請參閱*AZURE ACSC 認證報告-受保護的 2018*發行集, 並根據 ACSC 取用者指南, 對未包含在報表中的任何相關元件執行自我評估 *–受保護的 Microsoft Azure*

* 請確定網路連線和任何必要的 proxy 設定, 以存取必要的驗證元件, 例如 Azure AD、ADFS 和 PTA

* 使用 Azure 原則來監視和強制符合需求

* 確保虛擬機器 (尤其是 Active Directory 網網域控制站) 儲存在加密的儲存體帳戶和利用中 Azure 磁碟加密

* 建立和維護強大的身分識別和系統管理版權管理程式和治理, 以加強本文中所列的技術控制項

|Resource|URL|
|---|---|
|澳大利亞法規和原則合規性檔|[澳大利亞法規和原則合規性檔](https://aka.ms/au-irap)|
|Azure 產品-澳大利亞地區和非地區|[Azure 產品-澳大利亞地區和非地區](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast)|
|減輕網路安全性事件的策略|[減輕網路安全性事件的策略](https://acsc.gov.au/infosec/mitigationstrategies.htm)|
|ACSC 保護:安全管理|[ACSC 保護:安全管理](https://acsc.gov.au/publications/protect/secure-administration.htm)|
|如何：使用網路原則伺服器 (NPS) 擴充功能和 Azure AD 整合遠端桌面閘道基礎結構|[將 RD 閘道與 NPS 和 Azure AD 整合](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension-rdg)|

## <a name="component-guidance"></a>元件指引

本節提供在整體安全遠端系統管理架構中, 每個元件及其角色用途的資訊。 提供額外的連結來存取有用的資源, 例如參考檔、指南和教學課程。

## <a name="secure-devices"></a>保護裝置

有許可權的使用者用來執行系統管理功能的實體裝置, 對於惡意執行者而言是重要的目標。 維護實體裝置的安全性和完整性, 並確保它們不受惡意軟體的威脅, 並保護它們免于入侵, 是提供安全遠端系統管理功能的重要部分。 這牽涉到 ACSC 的基本八個策略中所指定的高優先順序安全性設定, 以減輕網路安全性事件, 例如應用程式允許清單、修補應用程式、應用程式強化和修補作業系統。 您必須安裝、設定、審查、驗證和報告這些功能, 以確保裝置的狀態符合組織需求。

### <a name="privileged-workstation"></a>具有特殊許可權的工作站

「特殊許可權工作站」是一種強化的機器, 可用於執行系統管理工作, 而且只能供管理帳戶存取。 具有特殊許可權的工作站應具備原則和設定, 以限制可執行檔軟體、存取網路資源和網際網路, 以及在裝置遭竊或遭到入侵的情況下, 應保護認證。 |

|資源|連結|
|---|---|
|特殊許可權存取工作站架構總覽|[https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)|
|保護特殊許可權存取的參考資料|[https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)|
|

### <a name="mobile-device"></a>行動裝置

行動裝置因可攜性和大小而發生意外遺失或遭竊的風險, 因此必須適當地加以保護。 行動裝置提供強式額外的驗證因素, 因為它可以強制執行裝置存取的驗證、透過位置服務進行追蹤、加密功能, 以及從遠端抹除的能力。 當您使用行動裝置做為 Azure 的其他驗證因素時, 應該將裝置設定為使用具有 PIN 或生物識別驗證的 Microsoft Authenticator 應用程式, 而不是透過電話或文字訊息。

|資源|連結|
|---|---|
|Azure AD 驗證方法|[https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)|
|如何使用 Microsoft Authenticator 應用程式|[https://support.microsoft.com/help/4026727/microsoft-account-how-to-use-the-microsoft-authenticator-app](https://support.microsoft.com/help/4026727/microsoft-account-how-to-use-the-microsoft-authenticator-app)|
|

### <a name="microsoft-intune"></a>Microsoft Intune

Intune 是管理行動裝置和應用程式 Enterprise Mobility + Security 的元件。 它與其他元件緊密整合, 例如身分識別和存取控制的 Azure Active Directory, 以及用於資料保護的 Azure 資訊保護。 Intune 提供工作站和行動裝置的原則, 以設定存取資源的相容性需求, 並提供報告和審核功能來深入瞭解系統管理裝置的狀態。

|資源|連結|
|---|---|
|Microsoft Intune 檔|[https://docs.microsoft.com/intune/](https://docs.microsoft.com/intune/)|
|Intune 中的裝置合規性入門|[https://docs.microsoft.com/intune/device-compliance-get-started](https://docs.microsoft.com/intune/device-compliance-get-started)|
|

### <a name="group-policy"></a>群組原則

群組原則可用來控制作業系統和應用程式的設定。 安全性原則控制系統的驗證、authorisation 和審核設定。 群組原則可用來強化特殊許可權的工作站、保護系統管理認證, 並限制非特殊許可權的帳戶存取特殊許可權裝置。

|資源|連結|
|---|---|
|允許本機登入群組原則設定|[https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/allow-log-on-locally](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/allow-log-on-locally)|
|

### <a name="jump-server--bastion-host"></a>跳躍伺服器/防禦主機

跳躍伺服器/防禦主機是管理的集中式點。 它具有執行系統管理工作所需的工具, 但也具有連接到系統管理埠上資源所需的網路存取權。 跳躍伺服器是在本文中管理虛擬機器工作負載的中心點, 但也可以設定為管理平臺即服務 (PaaS) 功能 (例如 SQL) 的授權點。 使用身分識別和網路控制, 可以根據每個服務來限制對 PaaS 功能的存取。

|資源|連結|
|---|---|
|執行安全的系統管理主機|[https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-secure-administrative-hosts](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-secure-administrative-hosts)|
|

### <a name="just-in-time-jit-access"></a>即時 (JIT) 存取

JIT 是一項 Azure 資訊安全中心功能, 運用網路安全性群組 (Nsg) 封鎖虛擬機器上的系統管理通訊協定 (例如 RDP 和 SSH) 的存取。 裝載于虛擬機器上的應用程式會繼續正常運作, 但若要取得系統管理存取權, 則必須要求只能授與一段設定的時間。 系統會記錄所有要求, 以供審核之用。

|資源 |連結 |
|---|---|
|管理即時 (JIT) 存取|[https://docs.microsoft.com/azure/security-center/security-center-just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)|
|將 Azure 即時 VM 存取自動化|[https://blogs.technet.microsoft.com/motiba/2018/06/24/automating-azure-just-in-time-vm-access](https://blogs.technet.microsoft.com/motiba/2018/06/24/automating-azure-just-in-time-vm-access)|
|

## <a name="secure-communication"></a>安全通訊

系統管理活動的通訊流量可以包含高度敏感的資訊 (例如系統管理認證), 而且必須據此進行管理和保護。 提供安全的通訊牽涉到可靠的加密功能, 以防止竊聽和網路分割和限制, 限制已授權端點的管理流量, 並控制在系統遭到入侵時的橫向移動。

### <a name="azure-portal"></a>Azure 入口網站

Azure 入口網站的通訊是使用傳輸層安全性 (TLS) 進行加密, 且使用 Azure 入口網站已通過 ACSC 認證。 美國聯邦實體應遵循*ACSC 取用者指南*中的建議, 並設定其網頁瀏覽器, 以確保其使用最新版本的 TLS 和支援的密碼編譯演算法。

|資源 |連結 |
|---|---|
|Azure 加密總覽-傳輸中的加密|[https://docs.microsoft.com/azure/security/security-azure-encryption-overview#encryption-of-data-in-transit](https://docs.microsoft.com/azure/security/security-azure-encryption-overview#encryption-of-data-in-transit)|
|

### <a name="azure-vpn-gateway"></a>Azure VPN 閘道

Azure VPN 閘道提供從具特殊許可權的工作站到 Azure 的安全加密連線。 Azure VPN 閘道已通過 ACSC 的認證, 可提供安全的 IPSec 通訊。 美國聯邦實體應根據 ACSC 取用者指南、ACSC 認證報告和其他特定指引來設定 Azure VPN 閘道。

|資源 |連結 |
|---|---|
|關於點對站連線|[https://docs.microsoft.com/azure/vpn-gateway/point-to-site-about](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-about)|
|Azure VPN 閘道密碼編譯詳細資料|[https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-compliance-crypto](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-compliance-crypto)|
|Azure VPN 閘道設定|[https://aka.ms/AzGovAUSecurity](https://aka.ms/AzGovAUSecurity)|
|

### <a name="remote-desktop-rd-gateway"></a>遠端桌面 (RD) 閘道

RD 閘道是控制和 authorising 系統 RDP 連線的安全機制。 其運作方式是在超文字安全傳輸通訊協定 (HTTPS) 中封裝 RDP 流量, 並使用 TLS 進行加密。 TLS 為系統管理流量提供一層額外的安全性。

|資源 |連結 |
|---|---|
|遠端桌面服務架構|[https://docs.microsoft.com/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)|
|

### <a name="network-security-groups-nsgs"></a>網路安全性群組 (NSG)

Nsg 函式做為輸入或離開子網或虛擬機器的網路流量存取控制清單 (Acl)。 Nsg 提供網路分割, 並提供機制來控制和限制系統之間允許的通訊流程。 Nsg 是即時系統管理 (JIT) 的核心元件, 可允許或拒絕存取系統管理通訊協定。

|資源 |連結 |
|---|---|
|Azure 安全性群組總覽|[https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|HOW TO：規劃虛擬網路|[https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)|
|

## <a name="strong-authentication"></a>增強式驗證

在授與系統的存取權之前安全地識別具特殊許可權的使用者, 是安全管理的核心元件。 您必須備妥機制來保護與特殊許可權帳戶相關聯的認證, 以及防止惡意執行者透過模擬或認證竊取來取得系統的存取權。

### <a name="domain-controller-dc"></a>網域控制站 (DC)

就高層級而言, DC 會裝載一份 Active Directory 資料庫, 其中包含網域內的所有使用者、電腦和群組。 Dc 會執行使用者和電腦的驗證。 此架構中的 Dc 會以虛擬機器的形式裝載在 Azure 中, 並為連接到跳躍伺服器和工作負載虛擬機器的特殊許可權帳戶提供驗證服務。

|資源 |連結 |
|---|---|
|Active Directory Domain Services 總覽|[https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)|
|

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Azure AD 是 Azure 的驗證服務。 其中包含雲端

身分識別並提供 Azure 環境的驗證和 authorisation。 Azure AD 可以使用 Active Directory 透過 Azure AD Connect 來 synchronised, 而且可以透過 Active Directory 同盟服務 (AD FS) 和 Azure AD Connect 提供同盟驗證。 Azure AD 是安全管理的核心元件。

|資源 |連結 |
|---|---|
|Azure Active Directory 文件|[https://docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory)|
|混合式身分識別檔|[https://docs.microsoft.com/azure/active-directory/hybrid](https://docs.microsoft.com/azure/active-directory/hybrid)|
|

### <a name="network-policy-server-nps"></a>網路原則伺服器 (NPS)

NPS 是一種驗證和原則伺服器, 可提供先進的驗證和 authorisation 處理常式。 此架構中的 NPS 伺服器是用來整合 Azure MFA 驗證與 RD 閘道驗證要求。 NPS 具有特定外掛程式, 可支援在 Azure AD 中與 Azure MFA 整合。

|資源 |連結 |
|---|---|
|網路原則伺服器檔|[https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)|
|

### <a name="azure-mfa"></a>Azure MFA

Azure MFA 是 Azure Active Directory 中提供的驗證服務, 可讓驗證要求超過使用者名稱和密碼, 以存取雲端資源, 例如 Azure 入口網站。 Azure MFA 支援一系列的驗證方法, 而此架構會運用 Microsoft Authenticator 應用程式, 以增強安全性和與 NPS 的整合。

|資源 |連結 |
|---|---|
|運作方式：Azure Multi-Factor Authentication|[https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)|
|HOW TO：部署雲端式 Azure Multi-Factor Authentication|[https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)|
|

## <a name="strong-authorisation"></a>強式 authorisation

一旦安全地識別出特殊許可權帳戶之後, 即可將資源的存取權授與它。 Authorisation 會控制並管理指派給特定帳戶的許可權。 強式 Authorisation 程式會與 ACSC 的基本八個策略一致, 以減少限制系統管理許可權的網路安全性事件。

### <a name="identity-and-access-management"></a>身分識別和存取管理

在 Azure 中執行特殊許可權動作的存取權是以指派給該帳戶的角色為基礎。 Azure 包含廣泛且細微的角色範圍, 具有特定的許可權可進行特定工作。 您可以在多個層級 (例如訂用帳戶或資源群組) 授與這些角色。 角色指派和版權管理是以 Azure Active Directory 中的帳戶和群組為基礎, 而且是透過 Azure 中的存取控制 (IAM) 進行管理。

|資源 |連結 |
|---|---|
|以 Azure 角色為基礎的存取控制|[https://docs.microsoft.com/azure/role-based-access-control](https://docs.microsoft.com/azure/role-based-access-control)|
|瞭解角色定義|[https://docs.microsoft.com/azure/role-based-access-control/role-definitions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions)|
|

### <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

PIM 是一個 Azure Active Directory 元件, 可控制特殊許可權角色的存取權。 特殊許可權帳戶不需要永久或具有特殊許可權的存取權, 但可以改為授與要求特殊許可權存取一段時間, 以完成特殊許可權活動的能力。 PIM 提供維護和限制特殊許可權存取的其他控制, 以及記錄和審核以追蹤許可權使用的實例。

|資源 |連結 |
|---|---|
|Privileged Identity Management (PIM) 檔|[https://docs.microsoft.com/azure/active-directory/privileged-identity-management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management)|
|開始使用 PIM|[https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-getting-started](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-getting-started)|
|

### <a name="conditional-access"></a>條件存取

條件式存取是 Azure Active Directory 的元件, 可根據條件來允許或拒絕存取資源。 這些條件可以是以網路位置為基礎、裝置類型、合規性狀態、群組成員資格等等。 條件式存取是用來透過 Intune 和系統管理帳戶的群組成員資格, 強制執行 MFA、裝置管理和合規性。

|資源 |連結 |
|---|---|
|條件式存取文件|[https://docs.microsoft.com/azure/active-directory/conditional-access](https://docs.microsoft.com/azure/active-directory/conditional-access)|
|HOW TO：需要受控裝置以使用條件式存取進行雲端應用程式存取|[https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|
|

## <a name="next-steps"></a>後續步驟

請參閱關於[閘道輸入流量管理和控制](gateway-ingress-traffic.md)的文章, 以取得在 Azure 中透過閘道元件控制流量的詳細資訊。
