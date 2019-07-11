---
title: 範例 - NIST SP 800-53 R4 藍圖 - 控制措施對應
description: NIST SP 800-53 R4 藍圖範例對於 Azure 原則和 RBAC 的控制措施對應。
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: b887c4e6812d201dc83465a578f71e1742e8e9cf
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343092"
---
# <a name="control-mapping-of-the-nist-sp-800-53-r4-blueprint-sample"></a>NIST SP 800-53 R4 藍圖範例的控制措施對應

下列文章將詳細說明 Azure 藍圖 NIST SP 800-53 R4 藍圖範例與 NIST SP 800-53 R4 控制措施的對應情形。 如需控制措施的詳細資訊，請參閱 [NIST SP 800-53](https://nvd.nist.gov/800-53) \(英文\)。

以下是與 **NIST SP 800-53 (第 4 版)** 控制措施的對應。 使用右側的導覽區可直接跳到特定的控制措施對應。 許多對應的控制措施都是以 [Azure 原則](../../../policy/overview.md)方案進行實作的。 若要檢閱完整方案，請在 Azure 入口網站中開啟 [原則]  ，然後選取 [定義]  頁面。 然後，找出並選取 **[預覽]：稽核 NIST SP 800-53 R4 控制措施並部署特定的 VM 延伸模組，以支援稽核需求**內建原則方案。

## <a name="ac-2-account-management"></a>AC-2 帳戶管理

此藍圖協助您檢閱可能不符合組織帳戶管理需求的帳戶。 此藍圖指派五項 Azure 原則定義，使用訂用帳戶與已取代帳戶上的讀取、寫入與擁有者權限來稽核外部帳戶。 檢閱這些原則所稽核的帳戶，您可以採取適當的動作，確保符合帳戶管理需求。

- [預覽]: Audit deprecated accounts on a subscription
- [預覽]: Audit deprecated accounts with owner permissions on a subscription
- [預覽]: Audit external accounts with owner permissions on a subscription
- [預覽]: Audit external accounts with read permissions on a subscription
- [預覽]: Audit external accounts with write permissions on a subscription

## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) 帳戶管理 | 角色型配置

Azure 會實作[角色型存取控制 (RBAC)](../../../../role-based-access-control/overview.md)，以協助您管理有權存取 Azure 資源的人員。 您可以使用 Azure 入口網站，檢閱可存取 Azure 資源的人員及其權限。 此藍圖還指派了兩個 [Azure 原則](../../../policy/overview.md)定義，用以稽核將 Azure Active Directory 驗證用於 SQL Server 和 Service Fabric 的情形。 使用 Azure Active Directory 驗證可針對資料庫使用者及其他 Microsoft 服務，簡化權限管理及集中管理身分識別。

- 稽核 SQL 伺服器的 Azure Active Directory 管理員佈建
- 稽核 Service Fabric 中 Azure Active Directory 用於用戶端驗證的使用方式

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>AC-2 (12) 帳戶管理 | 帳戶監視 / 非典型的使用方式

Just-In-Time (JIT) 虛擬機器存取可鎖定 Azure 虛擬機器的連入流量、降低暴露於攻擊的風險，同時能夠視需要輕鬆連線至 VM。 存取虛擬機器的所有 JIT 要求都會都記錄在「活動記錄」中，讓您監視非典型的使用方式。 此藍圖指派 [Azure 原則](../../../policy/overview.md)定義，可協助您監視可支援但尚未設定 Just-In-Time 存取的虛擬機器。

- [預覽]：監視 Azure 資訊安全中心中可能的網路 Just-In-Time (JIT) 存取

## <a name="ac-4-information-flow-enforcement"></a>AC-4 資訊流程強制

跨原始來源資源共用 (CORS) 允許從外部網域對應用程式服務資源提出要求。 Microsoft 建議您只允許必要網域與 API、功能和 Web 應用程式互動。 此藍圖指派 [Azure 原則](../../../policy/overview.md)定義，協助您在 Azure 資訊安全中心監視 CORS 資源存取限制。
了解 CORS 實作可協助您確認會實作資訊流程控制措施。

- [預覽]: Audit CORS resource access restrictions for a Web Application

## <a name="ac-5-separation-of-duties"></a>AC-5 權責區分

只有一個 Azure 訂用帳戶擁有者時，無法建立系統管理備援。 相反地，若有過多 Azure 訂用帳戶擁有者，因擁有者帳戶遭到入侵而產生缺口的可能性就會增加。 此藍圖指派了兩個 [Azure 原則](../../../policy/overview.md)定義，可稽核 Azure 訂用帳戶的擁有者數目，藉以協助您維護適當數量的 Azure 訂用帳戶擁有者。 這個藍圖還指派四個 Azure 原則定義，協助您控制 Windows 虛擬機器上 Administrators 群組的成員資格。 管理訂用帳戶擁有者與虛擬機器系統管理員權限可協助您實作適當的權責區分。

- [預覽]: Audit maximum number of owners for a subscription
- [預覽]: Audit minimum number of owners for subscription
- 稽核 Windows VM 內的 Administrators 群組將指定的成員排除
- 稽核 Windows VM 內的 Administrators 群組納入指定的成員
- 部署 VM 延伸模組以稽核 Windows VM 內的 Administrators 群組將指定的成員排除
- 部署 VM 延伸模組以稽核 Windows VM 內的 Administrators 群組納入指定的成員

## <a name="ac-6-7-least-privilege--review-of-user-privileges"></a>AC-6 (7) 最小權限 | 使用者權限檢閱

Azure 會實作[角色型存取控制 (RBAC)](../../../../role-based-access-control/overview.md)，以協助您管理有權存取 Azure 資源的人員。 您可以使用 Azure 入口網站，檢閱可存取 Azure 資源的人員及其權限。 此藍圖指派六個 [Azure 原則](../../../policy/overview.md)定義，稽核應該優先檢閱的帳戶。 檢閱這些帳戶指標，可協助您確保會實作最低權限控制措施。

- [預覽]: Audit maximum number of owners for a subscription
- [預覽]: Audit minimum number of owners for subscription
- 稽核 Windows VM 內的 Administrators 群組將指定的成員排除
- 稽核 Windows VM 內的 Administrators 群組納入指定的成員
- 部署 VM 延伸模組以稽核 Windows VM 內的 Administrators 群組將指定的成員排除
- 部署 VM 延伸模組以稽核 Windows VM 內的 Administrators 群組納入指定的成員

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) 遠端存取 | 自動化監視 / 控制

此藍圖指派三個 [Azure 原則](../../../policy/overview.md)定義，監視 Azure App Service 應用程式的遠端偵錯已關閉，而且指派兩個原則定義，稽核允許帳戶無需密碼便可從遠端連線的 Linux 虛擬機器，來協助您監視和控制遠端存取。 這個藍圖還指派一個 Azure 原則定義，協助您監視無限制地存取儲存體帳戶。 監視這些指標可協助您確保遠端存取方法符合安全性原則。

- [預覽]: Audit remote debugging state for a Function App
- [預覽]: Audit remote debugging state for a Web Application
- [預覽]: Audit remote debugging state for an API App
- [預覽]: Audit that Linux VMs do not allow remote connections from accounts without passwords
- [預覽]: Deploy VM extension to audit that Linux VMs do not allow remote connections from accounts without passwords
- 稽核不受限制的儲存體帳戶網路存取

## <a name="au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>AU-3 (2) 稽核記錄的內容 | 集中管理已計劃的稽核記錄內容

「Azure 監視器」所收集的記錄資料會儲存在 Log Analytics 工作區中，進而啟用集中化設定與管理。 此藍圖指派七個 [Azure 原則](../../../policy/overview.md)定義，稽核和強制要求在 Azure 虛擬機器上部署 Log Analytics 代理程式，協助您確保會記錄事件。

- [預覽]：稽核記錄分析代理程式部署 - 未列出的 VM 映像 (OS)
- [預覽]：稽核 VMSS 中的記錄分析代理程式部署 - 未列出的 VM 映像 (OS)
- [預覽]: Audit Log Analytics Workspace for VM - Report Mismatch
- [預覽]: Deploy Log Analytics Agent for Linux VM Scale Sets (VMSS)
- [預覽]: Deploy Log Analytics Agent for Linux VMs
- [預覽]: Deploy Log Analytics Agent for Windows VM Scale Sets (VMSS)
- [預覽]: Deploy Log Analytics Agent for Windows VMs

## <a name="au-5-response-to-audit-processing-failures"></a>AU-5 稽核處理程序失敗時的回應

此藍圖指派五個 [Azure 原則](../../../policy/overview.md)定義，監視稽核與事件記錄設定。 監視這些設定可提供稽核系統失敗或設定不正確的指標，並協助您採取矯正措施。

- [預覽]: Monitor unaudited SQL servers in Azure Security Center
- 稽核診斷設定
- 在沒有「進階資料安全性」的情況下稽核 SQL 受控執行個體
- 稽核 SQL 伺服器層級稽核設定
- 在沒有「進階資料安全性」的情況下稽核 SQL 伺服器

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>AU-6 (4) 稽核檢閱、分析和報告 | 集中檢閱與分析

Azure 監視器所收集的記錄資料會儲存在 Log Analytics 工作區中，進而啟用集中化報告與分析。 此藍圖指派七個 [Azure 原則](../../../policy/overview.md)定義，稽核和強制要求在 Azure 虛擬機器上部署 Log Analytics 代理程式，協助您確保會記錄事件。

- [預覽]：稽核記錄分析代理程式部署 - 未列出的 VM 映像 (OS)
- [預覽]：稽核 VMSS 中的記錄分析代理程式部署 - 未列出的 VM 映像 (OS)
- [預覽]: Audit Log Analytics Workspace for VM - Report Mismatch
- [預覽]: Deploy Log Analytics Agent for Linux VM Scale Sets (VMSS)
- [預覽]: Deploy Log Analytics Agent for Linux VMs
- [預覽]: Deploy Log Analytics Agent for Windows VM Scale Sets (VMSS)
- [預覽]: Deploy Log Analytics Agent for Windows VMs

## <a name="au-12-audit-generation"></a>AU-12 稽核產生

此藍圖指派 15 個 [Azure 原則](../../../policy/overview.md)定義，稽核 Azure 資源的記錄設定，以協助您確保會記錄系統事件。 這些原則定義會稽核並強制要求在 Azure 虛擬機器上部署 Log Analytics 代理程式，以及設定其他 Azure 資源類型的稽核設定。 這些原則定義也會稽核診斷記錄的設定，以深入解析 Azure 資源內所執行的作業。 此外，會在 SQL 伺服器上設定稽核與「進階資料安全性」。

- [預覽]：稽核記錄分析代理程式部署 - 未列出的 VM 映像 (OS)
- [預覽]：稽核 VMSS 中的記錄分析代理程式部署 - 未列出的 VM 映像 (OS)
- [預覽]: Audit Log Analytics Workspace for VM - Report Mismatch
- [預覽]: Deploy Log Analytics Agent for Linux VM Scale Sets (VMSS)
- [預覽]: Deploy Log Analytics Agent for Linux VMs
- [預覽]: Deploy Log Analytics Agent for Windows VM Scale Sets (VMSS)
- [預覽]: Deploy Log Analytics Agent for Windows VMs
- [預覽]: Monitor unaudited SQL servers in Azure Security Center
- 套用網路安全性群組的診斷設定
- 稽核診斷設定
- 在沒有「進階資料安全性」的情況下稽核 SQL 受控執行個體
- 稽核 SQL 伺服器層級稽核設定
- 在沒有「進階資料安全性」的情況下稽核 SQL 伺服器
- 在 SQL 伺服器上部署進階資料安全性
- 在 SQL 伺服器上部署稽核

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>CM-7 (2) 最少的功能 | 防止程式執行

「Azure 資訊安全中心」的自適性應用程式控制是智慧型的自動化端對端應用程式白名單解決方案，可封鎖或防止在虛擬機器上執行特定軟體。 應用程式控制能以強制模式執行，禁止執行未經核准的應用程式。 此藍圖指派一個 Azure 原則定義，協助您監視已建議但尚未設定白名單解決方案的虛擬機器。

- [預覽]: Monitor possible app whitelisting in Azure Security Center

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM-7 (5) 最少的功能 | 已授權的軟體 / 加入白名單

「Azure 資訊安全中心」的自適性應用程式控制是智慧型的自動化端對端應用程式白名單解決方案，可封鎖或防止在虛擬機器上執行特定軟體。 應用程式控制可協助您建立虛擬機器的已核准應用程式清單。 此藍圖指派一個 [Azure 原則](../../../policy/overview.md)定義，協助您監視已建議但尚未設定白名單解決方案的虛擬機器。

- [預覽]: Monitor possible app whitelisting in Azure Security Center

## <a name="cm-11-user-installed-software"></a>CM-11 使用者安裝的軟體

「Azure 資訊安全中心」的自適性應用程式控制是智慧型的自動化端對端應用程式白名單解決方案，可封鎖或防止在虛擬機器上執行特定軟體。 應用程式控制可協助您強制執行軟體限制原則，並監視其合規性。 此藍圖指派一個 [Azure 原則](../../../policy/overview.md)定義，協助您監視已建議但尚未設定白名單解決方案的虛擬機器。

- [預覽]: Monitor possible app whitelisting in Azure Security Center

## <a name="cp-7-alternate-processing-site"></a>CP-7 替代處理地點

Azure Site Recovery 會將虛擬機器上執行的工作負載從主要位置複寫到次要位置。 如果主要站台發生中斷，工作負載會容錯移轉到次要位置。 此藍圖指派一個 [Azure 原則](../../../policy/overview.md)定義，稽核未設定災害復原的虛擬機器。 監視此指標可協助您確保已備妥必要的應變控制。

- 稽核未設定災害復原的虛擬機器

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) 識別與驗證 (組織使用者) | 特殊權限帳戶的網路存取

此藍圖指派兩個 [Azure 原則](../../../policy/overview.md)定義，稽核具有擁有者和/或寫入權限且未啟用多重要素驗證的帳戶，協助您限制和控制特殊權限存取。 多重要素驗證有助於確保帳戶即使有部分驗證資訊遭到入侵，仍然安全無虞。 藉由監視未啟用多重要素驗證的帳戶，您將可識別較可能遭到入侵的帳戶。

- [預覽]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [預覽]: Audit accounts with write permissions who are not MFA enabled on a subscription

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>IA-2 (2) 識別與驗證 (組織使用者) | 非特殊權限帳戶的網路存取

此藍圖指派一個 [Azure 原則](../../../policy/overview.md)定義，稽核具有讀取權限且未啟用多重要素驗證的帳戶，協助您限制和控制存取。 多重要素驗證有助於確保帳戶即使有部分驗證資訊遭到入侵，仍然安全無虞。 藉由監視未啟用多重要素驗證的帳戶，您將可識別較可能遭到入侵的帳戶。

- [預覽]: Audit accounts with read permissions who are not MFA enabled on a subscription

## <a name="ia-5-authenticator-management"></a>IA-5 驗證器管理

此藍圖指派五個 [Azure 原則](../../../policy/overview.md)定義，稽核允許帳戶無需密碼便可從遠端連線和/或在 passwd 檔案設定不正確權限的 Linux 虛擬機器。 此藍圖還指派一個原則定義，稽核 Windows 虛擬機器的密碼加密類型搭配。 監視這些指標可協助您確保系統驗證器符合貴組織的識別與驗證原則。

- [預覽]: Audit that Linux VMs do not have accounts without passwords
- [預覽]: Deploy VM extension to audit that Linux VMs do not have accounts without passwords
- [預覽]: Audit that Linux VMs have the passwd file permissions set to 0644
- [預覽]: Audit that Windows VMs store passwords using reversible encryption
- [預覽]: Deploy VM extension to audit that Linux VMs have the passwd file permissions set to 0644

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) 驗證器管理 | 密碼型驗證

此藍圖指派 12 個 [Azure 原則](../../../policy/overview.md)定義，稽核未強制執行最低強度和其他密碼需求的 Windows 虛擬機器，以協助您強制要求使用強式密碼。 了解有哪些虛擬機器違反密碼強度原則可協助您採取矯正措施，以確保所有虛擬機器使用者帳戶的密碼都符合貴組織的密碼原則。

- [預覽]: Audit that Windows VMs cannot re-use the previous 24 passwords
- [預覽]: Audit that Windows VMs have a maximum password age of 70 days
- [預覽]: Audit that Windows VMs have a minimum password age of 1 day
- [預覽]: Audit that Windows VMs have the password complexity setting enabled
- [預覽]: Audit that Windows VMs restrict the minimum password length to 14 characters
- [預覽]: Audit that Windows VMs store passwords using reversible encryption
- [預覽]: Deploy VM extension to audit that Windows VMs cannot re-use the previous 24 passwords
- [預覽]: Deploy VM extension to audit that Windows VMs have a maximum password age of 70 days
- [預覽]: Deploy VM extension to audit that Windows VMs have a minimum password age of 1 day
- [預覽]: Deploy VM extension to audit that Windows VMs have the password complexity setting enabled
- [預覽]: Deploy VM extension to audit that Windows VMs restrict the minimum password length to 14 characters
- [預覽]: Deploy VM extension to audit that Windows VMs store passwords using reversible encryption

## <a name="ra-5-vulnerability-scanning"></a>RA-5 弱點掃描

此藍圖指派了五個 [Azure 原則](../../../policy/overview.md)定義，監視 Azure 資訊安全中心內的作業系統弱點、SQL 弱點與虛擬機器弱點，以協助您管理資訊系統弱點。 Azure 資訊安全中心提供報告功能，可讓您即時深入檢視已部署 Azure 資源的安全性狀態。 此藍圖還指派三個原則定義，針對 SQL 伺服器稽核並強制套用「進階資料安全性」。 「進階資料安全性」包含弱點評定和進階威脅防護功能，可協助您了解所部署資源的弱點。

- 在沒有「進階資料安全性」的情況下稽核 SQL 受控執行個體
- 在沒有「進階資料安全性」的情況下稽核 SQL 伺服器
- 在 SQL 伺服器上部署進階資料安全性
- [預覽]: Audit OS vulnerabilities on your virtual machine scale sets in Azure Security Center
- [預覽]: Monitor OS vulnerabilities in Azure Security Center
- [預覽]: Monitor SQL vulnerability assessment results in Azure Security Center
- [預覽]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="sc-5-denial-of-service-protection"></a>SC-5 阻斷服務保護

Azure 的分散式阻斷服務 (DDoS) 標準層提供基本服務層以外的額外功能與防護功能。 這些額外的功能包括「Azure 監視器」整合以及能夠檢閱攻擊後風險降低報告。 此藍圖指派一個 [Azure 原則](../../../policy/overview.md)定義，稽核是否已啟用 DDoS 標準層。 了解服務層之間的功能差異，協助您為 Azure 環境選取解決阻斷服務保護的最佳解決方案。

- [預覽]: Audit standard tier of DDoS protection is enabled for a virtual network

## <a name="sc-7-boundary-protection"></a>SC-7 界限保護

此藍圖指派一個 [Azure 原則](../../../policy/overview.md)定義，監視使用寬鬆規則的網路安全性群組，以協助您管理和控制系統界限。 太寬鬆的規則可能會允許非預期的網路存取，您應加以檢閱。 此藍圖還指派一個原則定義，監視「Azure 資訊安全中心」的網路安全性群組強化建議。 「Azure 資訊安全中心」會分析網際網路面向虛擬機器的流量模式，並提供網路安全群組規則建議，以降低潛在的受攻擊面。
此外，此藍圖還指派三個原則定義，以監視未受保護的端點、應用程式與儲存體帳戶。 不受防火牆保護的端點和應用程式，以及存取不受限制的儲存體帳戶，都可能允許資訊系統內所包含的資訊受到非預期的存取。

- [預覽]: Monitor Internet-facing virtual machines for Network Security Group traffic hardening recommendations
- [預覽]: Monitor permissive network access in Azure Security Center
- [預覽]: Monitor unprotected network endpoints in Azure Security Center
- [預覽]: Monitor unprotected web application in Azure Security Center
- 稽核不受限制的儲存體帳戶網路存取

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7 (3) 界限保護 | 存取點

Just-In-Time (JIT) 虛擬機器存取可鎖定 Azure 虛擬機器的連入流量、降低暴露於攻擊的風險，同時能夠視需要輕鬆連線至 VM。 JIT 虛擬機器存取可協助您限制 Azure 中資源的外部連線數目。 此藍圖指派 [Azure 原則](../../../policy/overview.md)定義，可協助您監視可支援但尚未設定 Just-In-Time 存取的虛擬機器。

- [預覽]：監視 Azure 資訊安全中心中可能的網路 Just-In-Time (JIT) 存取

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7 (4) 界限保護 | 外部電信服務

Just-In-Time (JIT) 虛擬機器存取可鎖定 Azure 虛擬機器的連入流量、降低暴露於攻擊的風險，同時能夠視需要輕鬆連線至 VM。 JIT 虛擬機器存取可加快存取要求與核准程序，協助您管理流量原則的例外。 此藍圖指派 [Azure 原則](../../../policy/overview.md)定義，可協助您監視可支援但尚未設定 Just-In-Time 存取的虛擬機器。

- [預覽]：監視 Azure 資訊安全中心中可能的網路 Just-In-Time (JIT) 存取

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>SC-28 (1) 待用資訊的保護 | 密碼編譯保護

此藍圖指派 9 個 [Azure 原則](../../../policy/overview.md)定義，強制執行特定的密碼編譯控制措施及稽核弱式密碼編譯設定的使用，協助您強制執行密碼編譯控制措施的使用原則，以保護待用資訊。 了解您的 Azure 資源在哪些層面可能使用非最佳化的密碼編譯組態，有助於您採取更正措施，以確保資源會根據您的資訊安全性原則進行設定。 具體而言，此藍圖所指派的原則定義會要求加密 Data Lake Storage 帳戶、要求在 SQL 資料庫進行透明資料加密，以及稽核 SQL 資料庫、虛擬機器磁碟與自動化帳戶變體是否缺少加密。

- [預覽]: Monitor unencrypted SQL databases in Azure Security Center
- [預覽]: Monitor unencrypted VM Disks in Azure Security Center
- 稽核儲存體帳戶的安全傳輸
- 在沒有「進階資料安全性」的情況下稽核 SQL 受控執行個體
- 在沒有「進階資料安全性」的情況下稽核 SQL 伺服器
- 稽核透明資料加密狀態
- 在 SQL 伺服器上部署進階資料安全性
- 部署 SQL DB 透明資料加密
- 強制加密 Data Lake Store 帳戶

## <a name="si-2-flaw-remediation"></a>SI-2 缺陷補救

此藍圖指派了六個 [Azure 原則](../../../policy/overview.md)定義，用以監視 Azure 資訊安全中心內的遺漏系統更新、作業系統弱點、SQL 弱點和虛擬機器弱點，以協助您管理資訊系統缺陷。 Azure 資訊安全中心提供報告功能，可讓您即時深入檢視已部署 Azure 資源的安全性狀態。 此藍圖還指派一個原則定義，確保會自動升級虛擬機器擴展集的作業系統。

- [預覽]: Audit any missing system updates on virtual machine scale sets in Azure Security Center
- [預覽]: Audit OS vulnerabilities on your virtual machine scale sets in Azure Security Center
- [預覽]: Monitor missing system updates in Azure Security Center
- [預覽]: Monitor OS vulnerabilities in Azure Security Center
- [預覽]: Monitor SQL vulnerability assessment results in Azure Security Center
- [預覽]: Monitor VM Vulnerabilities in Azure Security Center
- 對 VMSS 上的應用程式健康情況檢查套用 OS 自動升級

## <a name="si-3-malicious-code-protection"></a>SI-3 惡意程式碼防護

此藍圖指派三個 [Azure 原則](../../../policy/overview.md)定義，以在「Azure 資訊安全中心」監視虛擬機器缺少的 Endpoint Protection，並在 Windows 虛擬機器上強制執行 Microsoft 反惡意程式碼軟體解決方案，協助您管理 Endpoint Protection，包括惡意程式碼防護。

- [預覽]: Audit the endpoint protection solution on virtual machine scale sets in Azure Security Center
- [預覽]: Monitor missing Endpoint Protection in Azure Security Center
- 為 Windows Server 部署預設的 Microsoft IaaSAntimalware 延伸模組

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI-3 (1) 惡意程式碼防護 | 集中管理

此藍圖指派兩個 [Azure 原則](../../../policy/overview.md)定義，以在「Azure 資訊安全中心」監視虛擬機器缺少的 Endpoint Protection，協助您管理 Endpoint Protection，包括惡意程式碼防護。 「Azure 資訊安全中心」提供集中管理與報告功能，可讓您即時深入檢視已部署 Azure 資源的安全性狀態。

- [預覽]: Audit the endpoint protection solution on virtual machine scale sets in Azure Security Center
- [預覽]: Monitor missing Endpoint Protection in Azure Security Center

## <a name="si-4-information-system-monitoring"></a>SI-4 資訊系統監視

此藍圖可稽核並強制執行所有 Azure 資源的記錄和資料安全性，協助您監視系統。 具體來說，指派的原則會稽核並強制部署 Log Analytics 代理程式，以及 SQL 資料庫、儲存體帳戶與網路資源的增強式安全性設定。 這些功能可協助您偵測異常行為和攻擊的指標，以便採取適當動作。

- [預覽]：稽核記錄分析代理程式部署 - 未列出的 VM 映像 (OS)
- [預覽]：稽核 VMSS 中的記錄分析代理程式部署 - 未列出的 VM 映像 (OS)
- [預覽]: Audit Log Analytics Workspace for VM - Report Mismatch
- [預覽]: Deploy Log Analytics Agent for Linux VM Scale Sets (VMSS)
- [預覽]: Deploy Log Analytics Agent for Linux VMs
- [預覽]: Deploy Log Analytics Agent for Windows VM Scale Sets (VMSS)
- [預覽]: Deploy Log Analytics Agent for Windows VMs
- 在沒有「進階資料安全性」的情況下稽核 SQL 受控執行個體
- 在沒有「進階資料安全性」的情況下稽核 SQL 伺服器
- 在 SQL 伺服器上部署進階資料安全性
- 在儲存體帳戶部署進階威脅防護
- 在 SQL 伺服器上部署稽核
- 於虛擬網路建立時部署網路監看員
- 在 SQL 伺服器上部署威脅偵測

## <a name="si-4-18-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>SI-4 (18) 資訊系統監視 | 分析流量 / 掩飾外洩

Azure 儲存體的進階威脅防護可偵測儲存體帳戶中異常而且可能有害的存取或攻擊意圖。 防護警示包含異常存取模式、異常擷取/上傳以及可疑的儲存體活動。 這些指標可以協助您偵測掩飾資訊外洩的情況。

- 在儲存體帳戶部署進階威脅防護

## <a name="next-steps"></a>後續步驟

有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../../how-to/update-existing-assignments.md)。