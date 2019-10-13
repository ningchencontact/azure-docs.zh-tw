---
title: 英國官方 & 英國 NHS 藍圖範例-控制項對應
description: 英國官方和英國 NHS 藍圖範例的控制項對應。 每個控制項都會對應至一或多個可協助評量的 Azure 原則。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: blueprints
ms.openlocfilehash: 72b7b370158f228d725e9c6970dfcffb09e7986b
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2019
ms.locfileid: "72297110"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>英國官方和英國 NHS 藍圖範例的控制項對應

下列文章將詳細說明英國官方和英國 NHS 藍圖範例如何對應到英國官方和英國 NHS 控制項。 如需控制項的詳細資訊，請參閱[英國官方](https://www.gov.uk/government/publications/government-security-classifications)。

以下是**英國官方**和**英國 NHS**控制項的對應。 使用右側的導覽區可直接跳到特定的控制項對應。 許多對應的控制項都是以 [Azure 原則](../../../policy/overview.md)方案進行實作的。 若要檢閱完整方案，請在 Azure 入口網站中開啟 [原則]，然後選取 [定義] 頁面。 然後，尋找並選取  **\[Preview @ no__t-2 AUDIT UK 官方] 和 [英國 NHS] 控制項並部署特定的 VM 延伸模組，以支援 Audit 需求**內建原則計畫。

> [!IMPORTANT]
> 下列每個控制措施都與一或多個 [Azure 原則](../../../policy/overview.md)定義相關聯。 這些原則可協助您使用工具[存取合規性](../../../policy/how-to/get-compliance-data.md)；不過，控制措施和一或多個原則之間，通常不是 1：1 或完整對應。 因此，Azure 原則中的**符合規範**只是指原則本身，這不保證您符合控制措施所有需求的規範。 此外，合規性標準包含目前未由任何 Azure 原則定義解決的控制措施。 因此，Azure 原則中的合規性只是整體合規性狀態的部分觀點。 此合規性藍圖範例的控制措施與 Azure 原則定義之間的關聯，可能會隨著時間而改變。 若要檢視變更歷程記錄，請參閱 [GitHub 認可歷程記錄](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ukofficial/control-mapping.md) \(英文\)。

## <a name="1-data-in-transit-protection"></a>1傳輸保護中的資料

藍圖藉由指派[Azure 原則](../../../policy/overview.md)定義來對儲存體帳戶和 Redis 快取進行不安全的連線，協助您確保 Azure 服務的資訊傳輸是安全的。

- 僅應啟用與您 Redis Cache 的安全連線
- 應啟用與儲存體帳戶的安全傳輸

## <a name="23-data-at-rest-protection"></a>2.3 待用資料保護

此藍圖藉由指派[Azure 原則](../../../policy/overview.md)定義來強制執行特定如何控制項並使用弱式密碼編譯設定，協助您在使用如何控制項時強制執行原則。
了解您的 Azure 資源在哪些層面可能使用非最佳化的密碼編譯組態，有助於您採取更正措施，以確保資源會根據您的資訊安全性原則進行設定。 具體而言，此藍圖所指派的原則需要 data lake storage 帳戶的加密;需要 SQL 資料庫的透明資料加密;在儲存體帳戶、SQL 資料庫、虛擬機器磁片和自動化帳戶變數上審核遺失的加密;對儲存體帳戶和 Redis Cache 的不安全連線進行審核;audit 弱式虛擬機器密碼加密;和會在未加密的 Service Fabric 通訊中進行審核。

- 應啟用 SQL 資料庫上的透明資料加密
- 虛擬機器上應套用磁碟加密
- 應加密自動化帳戶變數
- 應啟用與儲存體帳戶的安全傳輸
- Service Fabric 叢集應將 ClusterProtectionLevel 屬性設定為 EncryptAndSign
- 應啟用 SQL 資料庫上的透明資料加密
- 部署 SQL DB 透明資料加密
- 需要加密 Data Lake Store 帳戶
- 允許的位置（已硬式編碼為「英國南部」和「英國西部」）
- 允許的資源群組位置（已硬式編碼為「英國南部」和「英國西部」）

## <a name="52-vulnerability-management"></a>5.2 弱點管理

此藍圖藉由指派[Azure 原則](../../../policy/overview.md)定義來監視遺失的 endpoint protection、遺失系統更新、作業系統弱點、SQL 弱點和虛擬，協助您執行資訊系統弱點電腦弱點。 這些深入解析可讓您即時檢視已部署資源的安全性狀態，並且可協助您排定補救措施的優先順序。

- 在 Azure 資訊安全中心內監視缺少的 Endpoint Protection
- 機器上應安裝系統更新
- 應補救您機器上安全性設定的弱點
- 您的 SQL 資料庫上應補救的弱點
- 應由弱點評定解決方案補救弱點

## <a name="53-protective-monitoring"></a>5.3 防護監視

此藍圖會指派[Azure 原則](../../../policy/overview.md)定義來協助您保護資訊系統資產，以提供不受限制的存取、白名單活動和威脅的防護監視。

- 稽核不受限制的儲存體帳戶網路存取
- 虛擬機器上應啟用自適性應用程式控制
- 在 SQL 伺服器上部署威脅偵測
- 部署適用于 Windows Server 的預設 Microsoft IaaS 反惡意程式碼擴充功能

## <a name="9-secure-user-management--10-identity-and-authentication"></a>9安全的使用者管理/10 身分識別和驗證

Azure 會實行角色型存取控制（RBAC），以協助您管理可存取 Azure 資源的人員。 您可以使用 Azure 入口網站，檢閱可存取 Azure 資源的人員及其權限。 此藍圖藉由指派[Azure 原則](../../../policy/overview.md)定義來審查具有擁有者和/或讀取/寫入權限的外部帳戶，以及具有擁有者、讀取和/或不含多重要素之寫入權限的帳戶，以協助您限制和控制存取權限已啟用驗證。

- 具有您訂用帳戶擁有者權限的帳戶上應啟用 MFA
- 在訂用帳戶上具有寫入權限的帳戶，應啟用 MFA
- 具有您訂用帳戶讀取權限的帳戶上應啟用 MFA
- 應從訂用帳戶中移除具備擁有者權限的外部帳戶
- 應從訂用帳戶中移除具備寫入權限的外部帳戶
- 應從訂用帳戶中移除具備讀取權限的外部帳戶

此藍圖會指派 Azure 原則定義，以針對 SQL server 和 Service Fabric 來審查 Azure Active Directory authentication 的使用。 使用 Azure Active Directory 驗證可針對資料庫使用者及其他 Microsoft 服務，簡化權限管理及集中管理身分識別。

- 應針對 SQL 伺服器佈建 Azure Active Directory 管理員
- Service Fabric 叢集只應使用 Azure Active Directory 進行用戶端驗證

此藍圖也會指派 Azure 原則定義來審查應優先進行審查的帳戶，包括折舊的帳戶和外部帳戶。 必要時可以封鎖帳戶的登入 (或移除帳戶)，而立即移除對 Azure 資源的存取權限。 此藍圖會指派兩個 Azure 原則定義來審查應視為移除的折舊帳戶。

- 已淘汰的帳戶應從您的訂用帳戶移除
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除
- 應從訂用帳戶中移除具備擁有者權限的外部帳戶
- 應從訂用帳戶中移除具備寫入權限的外部帳戶

此藍圖也會指派一個 Azure 原則定義來審查 Linux VM 密碼檔案許可權，以便在設定錯誤時發出警示。 這種設計可讓您採取更正動作，以確保驗證器不會遭到入侵。

- \[預覽\]：稽核 Linux VM /etc/passwd 檔案權限已設定為 0644

此藍圖藉由指派 Azure 原則定義來協助您強制執行強式密碼，以審核不會強制執行最小強度和其他密碼需求的 Windows Vm。 確知有哪些 VM 違反密碼強度原則可協助您採取更正措施，以確保所有 VM 使用者帳戶的密碼均符合原則。

- \[預覽\]：部署需求以稽核未啟用密碼複雜度設定的 Windows VM
- \[預覽\]：部署需求以稽核最大密碼存留期不是 70 天的 Windows VM
- \[預覽\]：部署需求以稽核最小密碼存留期不是 1 天的 Windows VM
- \[預覽\]：部署需求以稽核最小密碼長度未限制為 14 個字元的 Windows VM
- \[預覽\]：部署需求以稽核允許重複使用前 24 個舊密碼的 Windows VM
- \[預覽\]：稽核未啟用密碼複雜度設定的 Windows VM
- \[預覽\]：稽核最大密碼存留期不是 70 天的 Windows VM
- \[預覽\]：稽核最小密碼存留期不是 1 天的 Windows VM
- \[預覽\]：稽核最小密碼長度未限制為 14 個字元的 Windows 虛擬機器
- \[預覽\]：稽核允許重複使用前 24 個舊密碼的 Windows 虛擬機器

此藍圖也會藉由指派 Azure 原則定義，協助您控制對 Azure 資源的存取。 這些原則會稽核資源類型和組態的使用方式是否可能會允許更寬鬆資源存取。 了解有哪些資源違反這些原則可協助您採取修正動作，以確保 Azure 資源僅限定給經授權的使用者存取。

- \[預覽\]：部署需求以稽核有不具密碼帳戶的 Linux VM
- \[預覽\]：部署需求以稽核允許不使用密碼從帳戶遠端連線的 Linux VM
- \[預覽\]：稽核有不具密碼帳戶的 Linux VM
- \[預覽\]：稽核允許不使用密碼從帳戶遠端連線的 Linux VM
- 儲存體帳戶應移轉至新的 Azure Resource Manager 資源
- 虛擬機器應移轉至新的 Azure Resource Manager 資源
- 稽核不是使用受控磁碟的 VM

## <a name="11-external-interface-protection"></a>11外部介面保護

除了使用超過25個原則來進行適當的安全使用者管理之外，此藍圖還會指派監視不受限制之儲存體帳戶的[Azure 原則](../../../policy/overview.md)定義，協助您保護服務介面不受未經授權的存取。 具有不受限制存取權的儲存體帳戶，可能會允許非預期的存取訊號系統內所包含的資訊。 此藍圖也會指派一個原則，在虛擬機器上啟用彈性應用程式控制。

- 稽核不受限制的儲存體帳戶網路存取
- 虛擬機器上應啟用自適性應用程式控制

## <a name="12-secure-service-administration"></a>12安全服務管理

Azure 會實行角色型存取控制（RBAC），以協助您管理可存取 Azure 資源的人員。 您可以使用 Azure 入口網站，檢閱可存取 Azure 資源的人員及其權限。 此藍圖藉由指派五個[Azure 原則](../../../policy/overview.md)定義來審查外部帳戶的擁有者及/或寫入權限，以及具有擁有者及/或不具有之寫入權限的帳戶，以協助您限制及控制特殊許可權存取權限。已啟用多重要素驗證。

用於雲端服務管理的系統將會有該服務的高度存取權限。 其洩露會有重大影響，包括規避安全性控制及竊取或操控大量資料。 服務提供者的系統管理員用來管理操作服務的方法，應該設計來降低可能會破壞服務安全性的任何入侵風險。 如果未實行此準則，攻擊者可能會有略過安全性控制項並竊取或操作大量資料的方法。

- 具有您訂用帳戶擁有者權限的帳戶上應啟用 MFA
- 在訂用帳戶上具有寫入權限的帳戶，應啟用 MFA
- 應從訂用帳戶中移除具備擁有者權限的外部帳戶
- 應從訂用帳戶中移除具備寫入權限的外部帳戶

此藍圖會指派 Azure 原則定義，以針對 SQL server 和 Service Fabric 來審查 Azure Active Directory authentication 的使用。 使用 Azure Active Directory 驗證可針對資料庫使用者及其他 Microsoft 服務，簡化權限管理及集中管理身分識別。

- 應針對 SQL 伺服器佈建 Azure Active Directory 管理員
- Service Fabric 叢集只應使用 Azure Active Directory 進行用戶端驗證

此藍圖也會指派 Azure 原則定義來審查應優先進行審查的帳戶，包括以較高的許可權來折舊帳戶和外部帳戶。 必要時可以封鎖帳戶的登入 (或移除帳戶)，而立即移除對 Azure 資源的存取權限。 此藍圖會指派兩個 Azure 原則定義來審查應視為移除的折舊帳戶。

- 已淘汰的帳戶應從您的訂用帳戶移除
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除
- 應從訂用帳戶中移除具備擁有者權限的外部帳戶
- 應從訂用帳戶中移除具備寫入權限的外部帳戶

此藍圖也會指派一個 Azure 原則定義來審查 Linux VM 密碼檔案許可權，以便在設定錯誤時發出警示。 這種設計可讓您採取更正動作，以確保驗證器不會遭到入侵。

- \[預覽\]：稽核 Linux VM /etc/passwd 檔案權限已設定為 0644

## <a name="13-audit-information-for-users"></a>13個使用者的審核資訊

此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，稽核 Azure 資源的記錄設定，以協助您確保會記錄系統事件。 指派的原則也會稽核虛擬機器是否未將記錄傳送至指定的記錄分析工作區。

- 應對 SQL Server 上的進階資料安全性設定，啟用稽核
- 稽核診斷設定
- 稽核 SQL 伺服器層級稽核設定
- \[預覽\]：部署適用於 Linux VM 的 Log Analytics 代理程式
- \[預覽\]：部署適用於 Windows VM 的 Log Analytics 代理程式
- 於虛擬網路建立時部署網路監看員

## <a name="next-steps"></a>後續步驟

既然您已複習過英國官方和英國 NHS 藍圖的控制項對應，請流覽下列文章以瞭解此範例的總覽和部署方式：

> [!div class="nextstepaction"]
> [英國官方和英國 NHS 藍圖-總覽](./index.md)
> [英國官方和英國 NHS 藍圖-部署步驟](./deploy.md)

有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../../how-to/update-existing-assignments.md)。