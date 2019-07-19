---
title: 範例-英國官方和英國 NHS 藍圖-控制項對應
description: 英國官方和英國 NHS 藍圖範例的控制項對應。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 945898105aab7261ee494a86aeff10337599feb3
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226014"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>英國官方和英國 NHS 藍圖範例的控制項對應

下列文章將詳細說明英國官方和英國 NHS 藍圖範例如何對應到英國官方和英國 NHS 控制項。 如需控制項的詳細資訊, 請參閱[英國官方](https://www.gov.uk/government/publications/government-security-classifications)。

以下是**英國官方**和**英國 NHS**控制項的對應。 使用右側的導覽區可直接跳到特定的控制項對應。 許多對應的控制項都是以 [Azure 原則](../../../policy/overview.md)方案進行實作的。 若要檢閱完整方案，請在 Azure 入口網站中開啟 [原則]  ，然後選取 [定義]  頁面。 然後, 尋找並選取 [  **\[預覽\] ] [英國官方] 和 [uk NHS] 控制項並部署特定的 VM 延伸模組, 以支援**內建原則計畫。

## <a name="1-data-in-transit-protection"></a>1傳輸保護中的資料

藍圖藉由指派[Azure 原則](../../../policy/overview.md)定義來對儲存體帳戶和 Redis 快取進行不安全的連線, 協助您確保 Azure 服務的資訊傳輸是安全的。

- 只應啟用您 Redis 快取的安全連線
- 應啟用安全傳輸至儲存體帳戶

## <a name="23-data-at-rest-protection"></a>2.3 待用資料保護

此藍圖藉由指派[Azure 原則](../../../policy/overview.md)定義來強制執行特定如何控制項並使用弱式密碼編譯設定, 協助您在使用如何控制項時強制執行原則。
了解您的 Azure 資源在哪些層面可能使用非最佳化的密碼編譯組態，有助於您採取更正措施，以確保資源會根據您的資訊安全性原則進行設定。 具體而言, 此藍圖所指派的原則需要 data lake storage 帳戶的加密;需要 SQL 資料庫的透明資料加密;在儲存體帳戶、SQL 資料庫、虛擬機器磁片和自動化帳戶變數上審核遺失的加密;對儲存體帳戶和 Redis Cache 的不安全連線進行審核;audit 弱式虛擬機器密碼加密;和會在未加密的 Service Fabric 通訊中進行審核。

- 在 Azure 資訊安全中心中監視未加密的 SQL 資料庫
- 應該在虛擬機器上套用磁片加密
- 自動化帳戶變數應加密
- 應啟用安全傳輸至儲存體帳戶
- Service Fabric 叢集應將 ClusterProtectionLevel 屬性設定為 EncryptAndSign
- 應該啟用 SQL 資料庫上的透明資料加密
- 部署 SQL DB 透明資料加密
- Data Lake Store 帳戶上需要加密
- 允許的位置 (已硬式編碼為「英國南部」和「英國西部」)
- 允許的資源群組位置 (已硬式編碼為「英國南部」和「英國西部」)

## <a name="52-vulnerability-management"></a>5.2 弱點管理

此藍圖藉由指派[Azure 原則](../../../policy/overview.md)定義來監視遺失的 endpoint protection、遺失系統更新、作業系統弱點、SQL 弱點和虛擬, 協助您執行資訊系統弱點電腦弱點。 這些深入解析可讓您即時檢視已部署資源的安全性狀態，並且可協助您排定補救措施的優先順序。

- 在 Azure 資訊安全中心中監視缺少的 Endpoint Protection
- 系統更新應該安裝在您的電腦上
- 應補救您電腦上安全性設定中的弱點
- 應補救 SQL 資料庫上的弱點
- 弱點評估解決方案應補救漏洞

## <a name="53-protective-monitoring"></a>5.3 防護監視

此藍圖會指派[Azure 原則](../../../policy/overview.md)定義來協助您保護資訊系統資產, 以提供不受限制的存取、白名單活動和威脅的防護監視。

- 稽核不受限制的儲存體帳戶網路存取
- 應該在虛擬機器上啟用適應性應用程式控制
- 在 SQL 伺服器上部署威脅偵測
- 部署適用于 Windows Server 的預設 Microsoft IaaS 反惡意程式碼擴充功能

## <a name="9-secure-user-management--10-identity-and-authentication"></a>9安全的使用者管理/10 身分識別和驗證

Azure 會實行角色型存取控制 (RBAC), 以協助您管理可存取 Azure 資源的人員。 您可以使用 Azure 入口網站，檢閱可存取 Azure 資源的人員及其權限。 此藍圖藉由指派[Azure 原則](../../../policy/overview.md)定義來審查具有擁有者和/或讀取/寫入權限的外部帳戶, 以及具有擁有者、讀取和/或不含多重要素之寫入權限的帳戶, 以協助您限制和控制存取權限已啟用驗證。

- 必須在訂用帳戶上具有擁有者許可權的帳戶上啟用 MFA
- MFA 應啟用具有您訂用帳戶寫入權限的帳戶
- 必須在訂用帳戶上具有讀取權限的帳戶上啟用 MFA
- 必須從您的訂用帳戶中移除具有擁有者許可權的外部帳戶
- 應從訂用帳戶移除具有寫入權限的外部帳戶
- 應從您的訂用帳戶移除具有讀取權限的外部帳戶

此藍圖會指派 Azure 原則定義, 以針對 SQL server 和 Service Fabric 來審查 Azure Active Directory authentication 的使用。 使用 Azure Active Directory 驗證可針對資料庫使用者及其他 Microsoft 服務，簡化權限管理及集中管理身分識別。

- 應為 SQL 伺服器布建 Azure Active Directory 系統管理員
- Service Fabric 叢集應該只使用 Azure Active Directory 進行用戶端驗證

此藍圖也會指派 Azure 原則定義來審查應優先進行審查的帳戶, 包括折舊的帳戶和外部帳戶。 必要時可以封鎖帳戶的登入 (或移除帳戶)，而立即移除對 Azure 資源的存取權限。 此藍圖會指派兩個 Azure 原則定義來審查應視為移除的折舊帳戶。

- 應從您的訂用帳戶移除已淘汰的帳戶
- 具有擁有者許可權的已淘汰帳戶應從您的訂用帳戶中移除
- 必須從您的訂用帳戶中移除具有擁有者許可權的外部帳戶
- 應從訂用帳戶移除具有寫入權限的外部帳戶

此藍圖也會指派一個 Azure 原則定義來審查 Linux VM 密碼檔案許可權, 以便在設定錯誤時發出警示。 這種設計可讓您採取更正動作, 以確保驗證器不會遭到入侵。

- \[預覽\]：Audit Linux VM/etc/passwd 檔案許可權已設定為0644

此藍圖藉由指派 Azure 原則定義來協助您強制執行強式密碼, 以審核不會強制執行最小強度和其他密碼需求的 Windows Vm。 確知有哪些 VM 違反密碼強度原則可協助您採取更正措施，以確保所有 VM 使用者帳戶的密碼均符合原則。

- \[預覽\]：部署需求以審查未啟用密碼複雜性設定的 Windows Vm
- \[預覽\]：部署需求以審查沒有最大密碼存留期70天的 Windows Vm
- \[預覽\]：部署需求以審查不具有最小密碼存留期 (1 天) 的 Windows Vm
- \[預覽\]：部署需求以審查不會將最小密碼長度限制為14個字元的 Windows Vm
- \[預覽\]：部署需求以審查允許重複使用前24個密碼的 Windows Vm
- \[預覽\]：針對未啟用密碼複雜性設定的 Windows Vm 進行審核
- \[預覽\]：審核沒有最大密碼存留期70天的 Windows Vm
- \[預覽\]：審查不具有最小密碼存留期的 Windows Vm 1 天
- \[預覽\]：將不會將最小密碼長度限制為14個字元的 Windows Vm
- \[預覽\]：審核允許重複使用前24個密碼的 Windows Vm

此藍圖也會藉由指派 Azure 原則定義, 協助您控制對 Azure 資源的存取。 這些原則會稽核資源類型和組態的使用方式是否可能會允許更寬鬆資源存取。 了解有哪些資源違反這些原則可協助您採取修正動作，以確保 Azure 資源僅限定給經授權的使用者存取。

- \[預覽\]：部署需求以審核具有不含密碼帳戶的 Linux Vm
- \[預覽\]：部署需求以審核 Linux Vm, 允許不需要密碼的帳戶進行遠端連線
- \[預覽\]：在沒有密碼的情況下, 審核具有帳戶的 Linux Vm
- \[預覽\]：Audit Linux Vm, 允許不含密碼的帳戶進行遠端連線
- 儲存體帳戶應遷移至新的 Azure Resource Manager 資源
- 虛擬機器應該遷移至新的 Azure Resource Manager 資源
- 稽核不是使用受控磁碟的 VM

## <a name="11-external-interface-protection"></a>11外部介面保護

除了使用超過25個原則來進行適當的安全使用者管理之外, 此藍圖還會指派監視不受限制之儲存體帳戶的[Azure 原則](../../../policy/overview.md)定義, 協助您保護服務介面不受未經授權的存取。 具有不受限制存取權的儲存體帳戶, 可能會允許非預期的存取訊號系統內所包含的資訊。 此藍圖也會指派一個原則, 在虛擬機器上啟用彈性應用程式控制。

- 稽核不受限制的儲存體帳戶網路存取
- 應該在虛擬機器上啟用適應性應用程式控制

## <a name="12-secure-service-administration"></a>12安全服務管理

Azure 會實行角色型存取控制 (RBAC), 以協助您管理可存取 Azure 資源的人員。 您可以使用 Azure 入口網站，檢閱可存取 Azure 資源的人員及其權限。 此藍圖藉由指派五個[Azure 原則](../../../policy/overview.md)定義來審查外部帳戶的擁有者及/或寫入權限, 以及具有擁有者及/或不具有之寫入權限的帳戶, 以協助您限制及控制特殊許可權存取權限。已啟用多重要素驗證。

用於雲端服務管理的系統將會有該服務的高度存取權限。 其洩露會有重大影響，包括規避安全性控制及竊取或操控大量資料。 服務提供者的系統管理員用來管理操作服務的方法, 應該設計來降低可能會破壞服務安全性的任何入侵風險。 如果未實行此準則, 攻擊者可能會有略過安全性控制項並竊取或操作大量資料的方法。

- 必須在訂用帳戶上具有擁有者許可權的帳戶上啟用 MFA
- MFA 應啟用具有您訂用帳戶寫入權限的帳戶
- 必須從您的訂用帳戶中移除具有擁有者許可權的外部帳戶
- 應從訂用帳戶移除具有寫入權限的外部帳戶

此藍圖會指派 Azure 原則定義, 以針對 SQL server 和 Service Fabric 來審查 Azure Active Directory authentication 的使用。 使用 Azure Active Directory 驗證可針對資料庫使用者及其他 Microsoft 服務，簡化權限管理及集中管理身分識別。

- 應為 SQL 伺服器布建 Azure Active Directory 系統管理員
- Service Fabric 叢集應該只使用 Azure Active Directory 進行用戶端驗證

此藍圖也會指派 Azure 原則定義來審查應優先進行審查的帳戶, 包括以較高的許可權來折舊帳戶和外部帳戶。 必要時可以封鎖帳戶的登入 (或移除帳戶)，而立即移除對 Azure 資源的存取權限。 此藍圖會指派兩個 Azure 原則定義來審查應視為移除的折舊帳戶。

- 應從您的訂用帳戶移除已淘汰的帳戶
- 具有擁有者許可權的已淘汰帳戶應從您的訂用帳戶中移除
- 必須從您的訂用帳戶中移除具有擁有者許可權的外部帳戶
- 應從訂用帳戶移除具有寫入權限的外部帳戶

此藍圖也會指派一個 Azure 原則定義來審查 Linux VM 密碼檔案許可權, 以便在設定錯誤時發出警示。 這種設計可讓您採取更正動作, 以確保驗證器不會遭到入侵。

- \[預覽\]：Audit Linux VM/etc/passwd 檔案許可權已設定為0644

## <a name="13-audit-information-for-users"></a>13個使用者的審核資訊

此藍圖藉由指派在 Azure 資源上審核記錄設定的[Azure 原則](../../../policy/overview.md)定義, 協助您確保系統事件的記錄。 指派的原則也會稽核虛擬機器是否未將記錄傳送至指定的記錄分析工作區。

- 監視 Azure 資訊安全中心中的未稽核 SQL server
- 稽核診斷設定
- 稽核 SQL 伺服器層級稽核設定
- \[預覽\]：部署適用於 Linux VM 的 Log Analytics 代理程式
- \[預覽\]：部署適用於 Windows VM 的 Log Analytics 代理程式
- 於虛擬網路建立時部署網路監看員

## <a name="next-steps"></a>後續步驟

既然您已複習過英國官方和英國 NHS 藍圖的控制項對應, 請流覽下列文章以瞭解此範例的總覽和部署方式:

> [!div class="nextstepaction"]
> [英國官方和英國 NHS 藍圖-總覽](./index.md)
> [英國官方和英國 NHS 藍圖-部署步驟](./deploy.md)

有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../../how-to/update-existing-assignments.md)。
