---
title: 常見問題集：Azure 專用硬體安全模組 (HSM) | Microsoft Docs
description: 常見問題集涵蓋了 Azure 專用硬體安全模組 (HSM) 的各種不同主題
services: dedicated-hsm
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.custom: mvc
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: concepts
ms.date: 12/11/2018
ms.author: barclayn
ms.openlocfilehash: ec814bf641dea6f47b907e9f9e2646dfcf84ade1
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2019
ms.locfileid: "54451743"
---
# <a name="frequently-asked-questions-faq"></a>常見問題集 (FAQ)

尋找 Microsoft Azure 專用硬體安全模組 (HSM) 常見問題的解答。

## <a name="the-basics"></a>基本概念

### <a name="q-what-is-a-hardware-security-module-hsm"></a>問：什麼是硬體安全模組 (HSM)？

HSM 是用來保護及管理密碼編譯金鑰的實體運算裝置。 儲存在 HSM 中的金鑰可用於密碼編譯作業。 金鑰內容會安全地存放於可防止及杜絕竄改的硬體模組中。 HSM 只會允許經過驗證和已授權的應用程式使用金鑰。 金鑰內容永遠不會離開 HSM 的保護界限。

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>問：Azure 專用硬體安全模組 (HSM) 的供應項目是什麼？

Azure 專用硬體安全模組 (HSM) 是雲端式服務，可提供裝載於 Azure 資料中心 (直接連線至客戶虛擬網路) 的 HSM。 這些 HSM是專用的網路設備 (Gemalto 的 SafeNet Network HSM 7 型號 A790)。 會直接部署至客戶的私人 IP 位址空間，Microsoft 對於 HSM 的密碼編譯功能並沒有任何存取權。 只有客戶對這些裝置擁有完整的系統管理和密碼編譯控制權。 客戶需負責管理裝置，且可以直接從裝置取得完整的活動記錄。 專用 HSM 可協助客戶滿足法規/合規性需求，例如 FIPS 140-2 等級 3、HIPAA、PCI DSS、eIDAS 以及許多其他需求。

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>問：專用 HSM 使用什麼硬體？

Microsoft 與 Gemalto 合作，提供 Azure 專用硬體安全模組 (HSM) 服務。 使用的特定裝置為 [SafeNet Luna Network HSM 7 型號 A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/)。 此裝置不只提供 FIPS 140-2 等級 3 驗證的韌體，也透過 10 個分割區提供低延遲、高效能和高容量服務。 

### <a name="q-what-is-an-hsm-used-for"></a>問：硬體安全模組 (HSM) 的用途為何？

HSM 可用於儲存密碼編譯金鑰，以供密碼編譯功能使用，例如 SSL (安全通訊端層)、資料加密、PKI (公開金鑰基礎結構)、DRM (數位版權管理) 及文件簽署等。

### <a name="q-how-does-dedicated-hsm-work"></a>問：專用硬體安全模組 (HSM) 如何運作？

客戶可以使用 PowerShell 或命令列介面將 HSM 佈建至特定區域。 客戶可指定 HSM 要連線至哪個虛擬網路，並且在佈建後，HSM 即可在客戶的私人 IP 位址空間中，位於指派 IP 位址處的指定子網路中使用。 接著，客戶可以使用 SSH 連線至 HSM 進行設備管理，以設定 HSM 用戶端連線、將 HSM 初始化、建立分割區、定義及指派角色 (例如分割區人員、密碼編譯人員及密碼編譯使用者)。 然後，客戶將使用 Gemalto 提供的 HSM 用戶端工具/SDK/軟體，從其應用程式執行密碼編譯作業。

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>問：專用硬體安全模組 (HSM) 服務會隨附哪些軟體？

由 Microsoft 佈建 HSM 後，Gemalto 會提供 HSM 裝置的所有軟體。 您可在 [Gemalto 客戶支援入口網站](https://supportportal.gemalto.com/csm/)取得軟體。 使用專用 HSM 服務的客戶必須註冊 Gemalto 支援，並擁有可存取和下載相關軟體的客戶識別碼。 受支援的用戶端軟體是 7.2 版，與 FIPS 140-2 等級 3 驗證韌體 7.0.3 版相容。 

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>問：Azure 專用硬體安全模組 (HSM) 是否會提供密碼型和 PED 型的驗證？

目前 Azure 專用硬體安全模組 (HSM) 只對硬體安全模組 (HSM) 提供密碼型驗證。

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>問：Azure 專用硬體安全模組 (HSM) 是否會幫我裝載我的硬體安全模組 (HSM)？

Microsoft 僅透過專用硬體安全模組 (HSM) 服務提供 Gemalto SafeNet Luna Network HSM，而無法裝載任何客戶提供的裝置。

### <a name="q-does-azure-dedicated-hsm-support-pinetf-features-or-meet-pci-hsm-v1-or-v2-certification-requirements"></a>問：Azure 專用硬體安全模組 (HSM) 是否支援 PIN/ETF 功能或符合 PCI HSM v1 或 v2 憑證需求？

Azure 專用硬體安全模組 (HSM) 服務使用 SafeNet Luna Network HSM 7 (型號 A790) 裝置。 這些裝置不支援付款 HSM 的特定功能 (例如 PIN 或 ETF) 或認證。 如果您希望 Azure 專用硬體安全模組 (HSM) 服務未來可支援付款硬體安全模組 (HSM)，請於下方提供意見反應。

## <a name="interoperability"></a>互通性

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>問：我的應用程式應如何連線至專用硬體安全模組 (HSM)？

您將使用 Gemalto 提供的 HSM 用戶端工具/SDK/軟體，從您的應用程式執行密碼編譯作業。 您可在 [Gemalto 客戶支援入口網站](https://supportportal.gemalto.com/csm/)取得軟體。 使用專用 HSM 服務的客戶必須註冊 Gemalto 支援，並擁有可存取和下載相關軟體的客戶識別碼。

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>問：問：應用程式是否可以從不同 VNET 中，或是跨區域連線至專用硬體安全模組 (HSM)？

是，您必須使用區域內的 [VNET 對等互連](../virtual-network/virtual-network-peering-overview.md)，建立虛擬網路之間的連線。 針對跨區域連線，您必須使用 [VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)。

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>問：我是否能透過內部部署 HSM 同步專用硬體安全模組 (HSM)？

是，您可以透過專用 HSM 同步內部部署 HSM。 [點對點 VPN 或點對站](../vpn-gateway/vpn-gateway-about-vpngateways.md)連線可用於透過您的內部部署網路建立連線。

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>問：問：我是否可以使用儲存在專用硬體安全模組 (HSM) 的金鑰，來加密用於其他 Azure 服務的資料？

沒有。 您只能從您的虛擬網路內存取 Azure 專用硬體安全模組 (HSM)。

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>問：我是否可以從現有內部部署硬體安全模組 (HSM) 將金鑰匯入專用硬體安全模組 (HSM)？

是，如果您有內部部署 Gemalto SafeNet HSM。 有數個方法可使用。 請參考 Gemalto HSM 文件。

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>問：問：專用硬體安全模組 (HSM) 用戶端軟體支援哪些作業系統？

* Windows、Linux、Solaris、AIX、HP-UX、FreeBSD
* 虛擬：VMware、hyperv、Xen、KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>問：如何設定我的用戶端應用程式，從多個硬體安全模組 (HSM) 以多個分割區來建立高可用性設定？

若要具備高可用性，您需要將 HSM 用戶端應用程式設定設為使用每個 HSM 的分割區。 請參閱 Gemalto HSM 用戶端軟體文件。

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>問：專用硬體安全模組 (HSM) 支援哪些驗證機制？

Azure 專用硬體安全模組 (HSM) 使用 SafeNet Network HSM 7 設備 (型號 A790)，並且支援密碼型驗證。

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>問：問：哪些 SDK、API、用戶端軟體可搭配專用硬體安全模組 (HSM) 使用？

PKCS #11、Java (JCA/JCE)、Microsoft CAPI 和 CNG、OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>問：我是否可以從 Luna 5/6 將金鑰匯入/遷移至 Azure 專用硬體安全模組 (HSM)？

是。 請參閱 Gemalto 移轉指南。 

## <a name="using-your-hsm"></a>使用您的 HSM

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>問：我應如何決定要使用 Azure Key Vault 還是 Azure 專用硬體安全模組 (HSM)？

對於要遷移至使用 HSM 之 Azure 內部部署應用程式的企業來說，Azure 專用硬體安全模組 (HSM) 是適當的選擇。 專用 HSM 可讓您以最低限度的變更來遷移應用程式。 如果密碼編譯作業在執行 Azure VM 或 Web 應用程式的程式碼中執行，則這些作業可以使用專用 HSM。 一般情況下，在 IaaS (基礎結構即服務) 模型 (支援 HSM 作為金鑰存放區) 中執行的壓縮包裝軟體，可以使用專用 HSM，例如用於無金鑰 SSL 的應用程式閘道或流量管理員、ADCS (Active Directory 憑證服務) 或類似的 PKI 工具、用於文件簽署與程式碼簽署的工具/應用程式，或使用 EKM (可延伸金鑰管理) 提供者在 HSM 中使用主要金鑰設定 TDE (透明資料庫加密) 的 SQL Server (IaaS)。 Azure Key Vault 適用於「雲端原生」的應用程式，或其他案例 (其中客戶資料由 Paas (平台即服務) 處理) 中的加密，或 SaaS (軟體即服務) 案例 (例如 Office 365 客戶金鑰、Azure 資訊保護、Azure 磁碟加密、使用客戶管理金鑰的 Azure Data Lake Store 加密、使用客戶管理金鑰的 Azure 儲存體加密以及使用客戶管理金鑰的 Azure SQL)。

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>問：哪些使用案例最適用於 Azure 專用硬體安全模組 (HSM)？

Azure 專用硬體安全模組 (HSM) 最適合移轉案例。 意即，如果您要將內部部署應用程式遷移至已使用 HSM 的 Azure， 這可讓遷移至 Azure 的過程更為平順，且幾乎不需要變更應用程式。 如果密碼編譯作業在執行 Azure VM 或 Web 應用程式的程式碼中執行，則可以使用專用 HSM。 一般情況下，在 IaaS (基礎結構即服務) 模型 (支援 HSM 作為金鑰存放區) 中執行的壓縮封裝軟體，可以使用專用 HSM，例如：

* 無金鑰 SSL 的應用程式閘道或流量管理員
* ADCS (Active Directory 憑證服務)
* 類似的 PKI 工具
* 用於簽署文件的工具/應用程式
* 程式碼簽署
* 使用 EKM (可延伸金鑰管理) 提供者之 HSM 中的主要金鑰，以 TDE (透明資料庫加密) 設定的 SQL Server (IaaS)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>問：專用硬體安全模組 (HSM) 是否可以搭配 Office 365 客戶金鑰、Azure 資訊保護、Azure Data Lake Store、磁碟加密、Azure 儲存體加密、Azure SQL TDE 使用？

沒有。 專用 HSM 直接佈建於客戶的私人 IP 位址空間，因此無法由其他 Azure 或 Microsoft 服務存取。

## <a name="administration-access-and-control"></a>管理、存取和控制

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-hsms"></a>問：客戶是否能以專用硬體安全模組 (HSM) 取得對硬體安全模組 (HSM) 的完整專屬控制權？

是。 每個 HSM 裝置都由單一客戶完全專用，一旦佈建並變更系統管理員密碼之後，即沒有任何其他人具有管理控制權。

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>問：Microsoft 對我的硬體安全模組 (HSM) 具有何種層級的存取權？

Microsoft 對您的 HSM 並沒有任何管理或密碼編譯控制權。 Microsoft 具有監視層級存取權 (透過序列埠連線)，以便擷取基本的遙測 (例如溫度和元件的健康狀態)。 這可讓 Microsoft 提供健康狀態問題的主動式通知。 如有需要，客戶可以停用此帳戶。

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>問：Microsoft 或任何 Microsoft 員工是否可在我的專用硬體安全模組 (HSM) 中存取金鑰？

沒有。 對於儲存在客戶配置專用 HSM 中的金鑰，Microsoft 並不具有任何存取權。

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>問：是否可以為配置給我的硬體安全模組 (HSM) 升級軟體/韌體？

為獲得最佳支援，Microsoft 強烈建議不要升級 HSM 上的軟體/韌體。 不過，如果需要不同韌體版本的特定功能，則客戶擁有完整的管理控制權 (包括升級軟體/韌體)。 在進行變更之前請務必了解其含意，因為這可能造成例如影響 FIPS 驗證狀態的結果。 

### <a name="q-how-do-i-manage-dedicated-hsm"></a>問：如何管理專用硬體安全模組 (HSM)？

您可以使用 SSH 來存取專用 HSM，以便對其進行管理。

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>問：如何管理專用硬體安全模組 (HSM) 上的分割區？

Gemalto HSM 用戶端軟體可用於管理 HSM 和分割區。

### <a name="q-how-do-i-monitor-my-hsm"></a>問：如何監視我的硬體安全模組 (HSM)？

客戶可透過 syslog 和 SNMP 完整存取 HSM 的活動記錄。 客戶需要設定 syslog 伺服器或 SNMP 伺服器，以取得 HSM 中的記錄或事件。

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>問：我是否可取得專用硬體安全模組 (HSM) 中所有硬體安全模組 (HSM) 作業記錄的完整存取權？

是。 您可以將 HSM 設備的記錄傳送至 Syslog 伺服器

## <a name="high-availability"></a>高可用性

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>問：是否可以在相同區域或跨多個區域中設定高可用性？

是。 高可用性設定和設定，會在 Gemalto 提供的 HSM 用戶端軟體中執行。 在相同區域中或跨區域中、相同 VNET 或其他 VNET 的HSM，或使用站對站或點對點 VPN 連線至 VNET 的內部部署 HSM，可以新增至相同的高可用性設定中。

### <a name="can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>我是否能使用 Azure 專用硬體安全模組 (HSM) 將 HSM 從我的內部部署網路新增至高可用性群組？

是。 您的 HSM 必須符合 SafeNet Luna Network HSM 7 的高可用性需求。

### <a name="can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>我是否能使用 Azure 專用硬體安全模組 (HSM) 將 Luna 5/6 HSM 從內部部署網路新增至高可用性群組？

沒有。

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>問：問：我可以從單一應用程式將多少硬體安全模組 (HSM) 新增至相同高可用性設定？

16.

## <a name="support"></a>支援

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>問：什麼是專用硬體安全模組 (HSM) 服務的 SLA？

目前並未提供專用 HSM 服務的 SLA。 Microsoft 會確保裝置的網路層級存取，因此會套用標準 Azure 網路 SLA。

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>問：用於 Azure 專用硬體安全模組 (HSM) 的 HSM 如何受保護？

Azure 資料中心具有大量的實體和程序安全性控制。 此外，專用 HSM 裝載於資料中心內更嚴格限制存取權的區域中。 這些區域具有額外的實體存取控制和攝影機監視，以提高安全性。

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>問：如果出現安全性缺口或硬體竄改事件，會發生什麼事？

專用 HSM 服務使用 SafeNet Network HSM 7 設備。 這些設備支援實體和邏輯的竄改偵測。 如果發生竄改事件，這些 HSM 會自動歸零。

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>問：如何確保專用硬體安全模組 (HSM) 中的金鑰不會因發生錯誤或惡意內部攻擊而遺失？

強烈建議使用內部部署 HSM 備份裝置來執行 HSM 的一般定期備份，以便用於災害復原。 您必須對連線至 HSM 備份裝置的內部部署工作站使用對等或站對站 VPN 連線。

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>問：如何取得專用硬體安全模組 (HSM) 的支援？

與您取得所有其他 Azure 服務支援的方式一樣。 Azure 支援小組將視案例所需，提升為 Gemalto 支援。

### <a name="q-how-do-i-get-access-to-dedicated-hsm-client-software-documentation-firmware-images"></a>問：如何取得專用硬體安全模組 (HSM) 的用戶端軟體、文件、韌體映像？

客戶必須直接與 Gemalto 合作以取得 HSM 用戶端軟體、文件、OS/韌體映像。

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-gemalto-who-is-responsible-for-upgradingpatching-osfirmware"></a>問：如果發現安全性弱點，且 Gemalto 發行了修補程式，誰將負責升級/修補 OS/韌體？

Microsoft 無法連線至配置給客戶的 HSM。 客戶必須自行升級與修補其 HSM。

## <a name="cryptography-and-standards"></a>密碼編譯和標準

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>問：將最重要的資料以加密金鑰儲存於專用硬體安全模組 (HSM) 中是否安全？

是，專用 HSM 佈建使用 FIPS 140-2 等級 3 驗證的 SafeNet Network HSM 7 設備。 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>問：哪些密碼編譯金鑰和演算法受到專用硬體安全模組 (HSM) 支援？

專用 HSM 服務佈建 SafeNet Network HSM 7 設備。 這些設備支援各種不同密碼編譯金鑰的類型和演算法，包括：完整的 Suite B 支援

* 非對稱：
  * RSA
  * DSA
  * Diffie-Hellman
  * 橢圓曲線
  * 使用具名、使用者定義和 Brainpool 曲線、KCDSA 的密碼編譯 (ECDSA、ECDH、Ed25519、ECIES)
* 對稱：
  * AES-GCM
  * 三重 DES
  * DES
  * ARIA，種子
  * RC2
  * RC4
  * RC5
  * CAST
  * 雜湊/訊息摘要/HMAC：SHA-1、SHA-2、SM3
  * 金鑰衍生：SP800-108 計數器模式
  * 金鑰換行：SP800-38F
  * 亂數產生：FIPS 140-2 已核准 DRBG (SP 800-90 CTR 模式)，遵守 BSI DRG.4

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>問：專用硬體安全模組 (HSM) 是否已通過 FIPS 140-2 等級 3 驗證？

是。 專用 HSM 服務佈建使用 FIPS 140-2 等級 3 驗證的 SafeNet Network HSM 7 設備。

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>問：我如何確定我的專用硬體安全模組 (HSM) 在 FIPS 140-2 等級 3 驗證模式下運作？

專用 HSM 服務佈建 SafeNet Luna Network HSM 7 設備。 這些設備使用經 FIPS 140-2 等級 3 驗證的 HSM。 預設部署設定、作業系統和韌體也已通過 FIPS 驗證。 您不需要為 FIPS 140-2 等級 3 的合規性採取任何動作。

### <a name="how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>取消佈建 HSM 時，客戶可如何確保抹除所有金鑰內容？

在要求取消佈建前，客戶必須先使用 Gemalto 提供的 HSM 用戶端工具，將 HSM 歸零。

## <a name="performance-and-scale"></a>效能和延展性

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>問：專用硬體安全模組 (HSM) 支援每秒多少個密碼編譯作業？

專用 HSM 佈建 SafeNet Network HSM 7 設備 (型號 A790)。 以下是針對某些作業的最大效能摘要： 

* RSA-2048：每秒 10,000 筆交易
* ECC P256：每秒 20,000 筆交易
* AES-GCM：每秒 17,000 筆交易

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>問：我可以在專用硬體安全模組 (HSM) 中建立多少分割區？

根據 HSM 所使用的特定型號，有 10 個可用的分割區。

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>問：專用硬體安全模組 (HSM) 可支援多少金鑰？

金鑰的數目上限。 如果使用非對稱金鑰，這些數目也適用於金鑰組。

* RSA-2048 - 19,000
* ECC-P256 - 91,000
* AES-256 - 218,000

容量會根據金鑰產生範本中設定的特定金鑰屬性和分割區數量，而有所不同。

