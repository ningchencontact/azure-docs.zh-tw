---
title: 範例-英國官方藍圖-控制項對應
description: 英國官方 blueprint （藍圖） 範例的控制項對應。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/13/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 2eb6e62bc891a147a89107f5e3de7c2b605bbd09
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276973"
---
# <a name="control-mapping-of-the-uk-official-blueprint-sample"></a>英國官方 blueprint （藍圖） 範例的控制項對應

下列文章會詳述英國官方藍圖範例如何對應至英國官方的控制項。
如需控制項的詳細資訊，請參閱[英國官方](https://www.gov.uk/government/publications/government-security-classifications)。

下列的對應**英國官方**控制項。 使用右側的導覽區可直接跳到特定的控制項對應。 許多對應的控制項都是以 [Azure 原則](../../../policy/overview.md)方案進行實作的。 若要檢閱完整方案，請在 Azure 入口網站中開啟 [原則]  ，然後選取 [定義]  頁面。 然後，尋找並選取 **[預覽]稽核英國官方的控制和部署特定的 VM 擴充功能，以支援稽核需求**內建的原則計畫。

## <a name="1-data-in-transit-protection"></a>1 保護傳輸中的資料

此藍圖指派了兩項 [Azure 原則](../../../policy/overview.md)定義來稽核不安全的儲存體帳戶和 Redis 快取連線，以協助您確保 Azure 服務的資訊傳輸安全無虞。

- 應該啟用只安全連線至 Redis 快取
- 應該啟用安全傳輸至儲存體帳戶

## <a name="23-data-at-rest-protection"></a>2.3 待用資料保護

此藍圖可協助您強制執行密碼編譯控制項的使用原則指派 11 [Azure 原則](../../../policy/overview.md)強制執行特定的密碼編譯控制項和稽核的定義使用弱式密碼編譯設定。
了解您的 Azure 資源在哪些層面可能使用非最佳化的密碼編譯組態，有助於您採取更正措施，以確保資源會根據您的資訊安全性原則進行設定。 具體而言，此藍圖所指派的原則會要求加密的 data lake 儲存體帳戶;需要 SQL 資料庫透明資料加密稽核儲存體帳戶、 SQL 資料庫、 虛擬機器磁碟和自動化帳戶的變數; 遺漏加密稽核儲存體帳戶和 Redis 快取; 不安全的連線稽核弱式的虛擬機器密碼加密;規則和稽核未加密的 Service Fabric 通訊。

- 監視未加密的 SQL 資料庫，Azure 資訊安全中心
- 應該在虛擬機器上套用磁碟加密
- 自動化帳戶的變數應該加密
- 應該啟用安全傳輸至儲存體帳戶
- 應該啟用安全傳輸至儲存體帳戶
- Service Fabric 叢集應該有 ClusterProtectionLevel 屬性設定為 EncryptAndSign
- 應該啟用 SQL database 上的透明資料加密
- 部署 SQL DB 透明資料加密
- Data Lake Store 帳戶上要求加密
- 允許的位置 （已被硬式編碼為 「 英國南部 」 和 「 英國西部 」）
- 允許的資源群組的位置 （已被硬式編碼為 「 英國南部 」 和 「 英國西部 」）

## <a name="52-vulnerability-management"></a>5.2 弱點管理

此藍圖可協助您藉由指派五個管理資訊系統弱點[Azure 原則](../../../policy/overview.md)監視缺少的 endpoint protection，遺漏系統更新、 operating system 弱點、 SQL 的定義弱點和虛擬機器的弱點。 這些深入解析可讓您即時檢視已部署資源的安全性狀態，並且可協助您排定補救措施的優先順序。

- 在 Azure 資訊安全中心中監視缺少的 Endpoint Protection
- 應該在機器上安裝系統更新
- 應該修復您的電腦上的安全性設定中的弱點
- 應該修復您的 SQL database 中的弱點
- 應該修復弱點的弱點評量解決方案

## <a name="53-protective-monitoring"></a>5.3 防護監視

此藍圖可協助您保護資訊系統資產指派[Azure 原則](../../../policy/overview.md)監視的定義不受限制的儲存體帳戶。 這個藍圖也會將指派監視列入白名單的活動，Azure 原則定義。

- 稽核不受限制的儲存體帳戶網路存取
- 應該在虛擬機器上啟用自適性應用程式控制
- 部署 SQL server 上的威脅偵測
- 部署適用於 Windows Server 的預設 Microsoft IaaS 反惡意程式碼擴充功能

## <a name="9-secure-user-management--10-identity-and-authentication"></a>9 安全的使用者管理 / 10 身分識別和驗證

Azure 會實作角色型存取控制 (RBAC 來協助您管理可在 Azure 中的資源的存取權的人員)。 您可以使用 Azure 入口網站，檢閱可存取 Azure 資源的人員及其權限。 此藍圖可協助您限制及控制特殊權限的存取權限指派六[Azure 原則](../../../policy/overview.md)稽核外部帳戶擁有者及/或讀取/寫入權限和帳戶擁有者、 讀取和/或寫入的定義不需要啟用多重要素驗證的權限。

- 應該與您的訂用帳戶的擁有者權限帳戶啟用 MFA
- MFA 應該具有寫入權限已啟用訂用帳戶的帳戶
- 應該與您的訂用帳戶的讀取權限帳戶啟用 MFA
- 稽核在訂用帳戶上具有擁有者權限的外部帳戶
- 稽核在訂用帳戶上具有寫入權限的外部帳戶
- 稽核在訂用帳戶上具有讀取權限的外部帳戶

此藍圖會指派兩個 Azure 原則定義，若要稽核的 SQL server 和 Service Fabric 使用 Azure Active Directory 驗證。 使用 Azure Active Directory 驗證可針對資料庫使用者及其他 Microsoft 服務，簡化權限管理及集中管理身分識別。

- Azure Active Directory 系統管理員應該佈建 SQL server
- Service Fabric 叢集應該只使用 Azure Active Directory 用戶端驗證

這個藍圖也會指派五個的 Azure 原則定義，以稽核應該優先處理供檢閱，包括折舊的帳戶和外部帳戶的帳戶。 必要時可以封鎖帳戶的登入 (或移除帳戶)，而立即移除對 Azure 資源的存取權限。 此藍圖會將兩個 Azure 原則定義指派給應該視為移除的稽核停用帳戶。

- 請移除已被取代的帳戶，從您的訂用帳戶
- 應該從您訂用帳戶中移除具有擁有者權限已被取代的帳戶
- 應該從您的訂用帳戶中移除具有擁有者權限的外部帳戶
- 應從訂用帳戶移除具有寫入權限的外部帳戶

這個藍圖也會指派會稽核 Linux VM 的 Azure 原則定義密碼檔案的權限設定不正確時發出警示。 此設計可讓您採取更正動作以確保驗證器不會受到危害。

- [預覽]: Audit Linux VM /etc/passwd file permissions are set to 0644

此藍圖可協助您強制執行強式密碼指派 10 個稽核不會強制執行最小的強度和其他密碼需求的 Windows Vm 的 Azure 原則定義。 確知有哪些 VM 違反密碼強度原則可協助您採取更正措施，以確保所有 VM 使用者帳戶的密碼均符合原則。

- [預覽]: Deploy requirements to audit Windows VMs that do not have the password complexity setting enabled
- [預覽]: Deploy requirements to audit Windows VMs that do not have a maximum password age of 70 days
- [預覽]: Deploy requirements to audit Windows VMs that do not have a minimum password age of 1 day
- [預覽]: Deploy requirements to audit Windows VMs that do not restrict the minimum password length to 14 characters
- [預覽]: Deploy requirements to audit Windows VMs that allow re-use of the previous 24 passwords
- [預覽]: Audit Windows VMs that do not have the password complexity setting enabled
- [預覽]: Audit Windows VMs that do not have a maximum password age of 70 days
- [預覽]: Audit Windows VMs that do not have a minimum password age of 1 day
- [預覽]: Audit Windows VMs that do not restrict the minimum password length to 14 characters
- [預覽]: Audit Windows VMs that allow re-use of the previous 24 passwords

這個藍圖也能夠協助您控制 Azure 資源的存取權，指派 7 Azure 原則定義。 這些原則會稽核資源類型和組態的使用方式是否可能會允許更寬鬆資源存取。 了解有哪些資源違反這些原則可協助您採取修正動作，以確保 Azure 資源僅限定給經授權的使用者存取。

- [預覽]: Deploy requirements to audit Linux VMs that have accounts without passwords
- [預覽]: Deploy requirements to audit Linux VMs that allow remote connections from accounts without passwords
- [預覽]: Audit Linux VMs that have accounts without passwords
- [預覽]: Audit Linux VMs that allow remote connections from accounts without passwords
- 儲存體帳戶應該移轉至新的 Azure Resource Manager 資源
- 虛擬機器應該移轉至新的 Azure Resource Manager 資源
- 稽核不是使用受控磁碟的 VM

## <a name="11-external-interface-protection"></a>11 的外部介面保護

非使用適當的安全使用者管理超過 25 個原則，此藍圖可協助您防止未經授權存取中的服務介面，藉由指派[Azure 原則](../../../policy/overview.md)監視不受限制的定義儲存體帳戶。 存取不受限制的儲存體帳戶可以允許非預期的存取資訊系統內所包含的資訊。 這個藍圖也會將指派可讓虛擬機器上的自適性應用程式控制原則。

- 稽核不受限制的儲存體帳戶網路存取
- 應該在虛擬機器上啟用自適性應用程式控制

## <a name="12-secure-service-administration"></a>12 安全的服務管理

Azure 會實作角色型存取控制 (RBAC 來協助您管理可在 Azure 中的資源的存取權的人員)。 您可以使用 Azure 入口網站，檢閱可存取 Azure 資源的人員及其權限。 此藍圖可協助您限制及控制特殊權限的存取權限指派五[Azure 原則](../../../policy/overview.md)稽核外部帳戶擁有者及/或寫入權限和帳戶擁有者，及/或寫入權限的定義沒有啟用多重要素驗證。

用於雲端服務管理的系統將會有該服務的高度存取權限。 其洩露會有重大影響，包括規避安全性控制及竊取或操控大量資料。 服務提供者的系統管理員用來管理作業的服務方法應可緩和任何可能破壞的服務安全性的入侵的風險。 如果未實作此準則，攻擊者可能略過安全性控制及竊取或操控大量資料的方法。

- 應該與您的訂用帳戶的擁有者權限帳戶啟用 MFA
- MFA 應該具有寫入權限已啟用訂用帳戶的帳戶
- 應該從您的訂用帳戶中移除具有擁有者權限的外部帳戶
- 應從訂用帳戶移除具有寫入權限的外部帳戶

此藍圖會指派兩個 Azure 原則定義，若要稽核的 SQL server 和 Service Fabric 使用 Azure Active Directory 驗證。 使用 Azure Active Directory 驗證可針對資料庫使用者及其他 Microsoft 服務，簡化權限管理及集中管理身分識別。

- Azure Active Directory 系統管理員應該佈建 SQL server
- Service Fabric 叢集應該只使用 Azure Active Directory 用戶端驗證

這個藍圖也會將指派四個 Azure 原則定義，以稽核應該優先處理供檢閱，包括折舊的帳戶和外部帳戶，以更高權限的帳戶。 必要時可以封鎖帳戶的登入 (或移除帳戶)，而立即移除對 Azure 資源的存取權限。 此藍圖會將兩個 Azure 原則定義指派給應該視為移除的稽核停用帳戶。

- 請移除已被取代的帳戶，從您的訂用帳戶
- 應該從您訂用帳戶中移除具有擁有者權限已被取代的帳戶
- 應該從您的訂用帳戶中移除具有擁有者權限的外部帳戶
- 應從訂用帳戶移除具有寫入權限的外部帳戶

這個藍圖也會指派會稽核 Linux VM 的 Azure 原則定義密碼檔案的權限設定不正確時發出警示。 此設計可讓您採取更正動作以確保驗證器不會受到危害。

- [預覽]: Audit Linux VM /etc/passwd file permissions are set to 0644

## <a name="13-audit-information-for-users"></a>適用於使用者的 13 的稽核資訊

此藍圖可協助您確保系統事件所記錄的指派 6 [Azure 原則](../../../policy/overview.md)稽核記錄檔設定 Azure 資源上的定義。 指派的原則也會稽核虛擬機器是否未將記錄傳送至指定的記錄分析工作區。

- 監視未稽核的 SQL server，Azure 資訊安全中心
- 稽核診斷設定
- 稽核 SQL 伺服器層級稽核設定
- [預覽]: Deploy Log Analytics Agent for Linux VMs
- [預覽]: Deploy Log Analytics Agent for Windows VMs
- 建立虛擬網路時，部署網路監看員

## <a name="next-steps"></a>後續步驟

有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../../how-to/update-existing-assignments.md)。
