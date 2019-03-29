---
title: 範例-ISO 27001 藍圖-控制項對應
description: ISO 27001 blueprint （藍圖） 範例控制項對應。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: c062759938652518ac3cafff64973050554ca19d
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579694"
---
# <a name="control-mapping-of-the-azure-blueprints-iso-27001-blueprint-sample"></a>Azure 藍圖 ISO 27001 blueprint （藍圖） 範例的控制項對應

下列文件詳細說明如何將 Azure 藍圖 ISO 27001 ASE/SQL 工作負載 blueprint （藍圖） 範例對應至 ISO 27001 控制項。 如需控制項的詳細資訊，請參閱[ISO 27001](https://www.iso.org/isoiec-27001-information-security.html)。

下列的對應**ISO 27001:2013**控制項。 使用右邊的瀏覽，直接跳到特定的控制項對應。 使用許多對應的控制項來實作[Azure 原則](../../../policy/overview.md)計劃。 若要檢閱完整的計劃，請開啟**原則**在 Azure 入口網站，然後選取**定義**頁面。 然後，尋找並選取**[預覽]稽核 ISO 27001:2013 控制及部署特定的 VM 擴充功能，以支援稽核需求**內建的原則計畫。

## <a name="a612-segregation-of-duties"></a>A.6.1.2 責任分隔

系統管理的冗餘的不允許有只有一個 Azure 訂用帳戶擁有者。 相反地，有太多的 Azure 訂用帳戶擁有者可以增加潛在的缺口，以透過遭入侵的擁有者帳戶。 此藍圖可協助您維護適當數目的 Azure 訂用帳戶擁有者指派兩個[Azure 原則](../../../policy/overview.md)稽核的 Azure 訂用帳戶的擁有者數目的定義。 管理訂用帳戶擁有者權限，可以協助您實作適當的責任區隔。

- [預覽]: Audit minimum number of owners for subscription
- [預覽]: Audit maximum number of owners for a subscription

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 存取網路和網路服務

Azure 會實作[角色型存取控制](../../../../role-based-access-control/overview.md)(RBAC) 來管理誰能夠存取 Azure 資源。 此藍圖可協助您控制 Azure 資源的存取權指派七[Azure 原則](../../../policy/overview.md)定義。 這些原則會稽核的資源類型使用，並可能會允許更寬鬆的設定資源的存取權。
了解資源，協助您採取修正動作，以確保 Azure 資源的存取權僅限於這些原則可以違反授權的使用者。

- [預覽]: Deploy VM extension to audit Linux VM accounts with no passwords
- [預覽]: Deploy VM extension to audit Linux VM allowing remote connections from accounts with no
  密碼s
- [預覽]: Audit Linux VM accounts with no passwords
- [預覽]: Audit Linux VM allowing remote connections from accounts with no passwords
- 稽核傳統儲存體帳戶的使用
- 稽核傳統虛擬機器的使用
- 稽核不是使用受控磁碟的 VM

## <a name="a922-user-access-provisioning"></a>A.9.2.2 使用者存取佈建

Azure 會實作[角色型存取控制](../../../../role-based-access-control/overview.md)(RBAC) 來管理誰能夠存取 Azure 資源。 此藍圖會將指派三個[Azure 原則](../../../policy/overview.md)若要稽核的定義使用的[Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) SQL server 驗證和[Service Fabric](../../../../service-fabric/service-fabric-overview.md)。 使用 Azure Active Directory 驗證可簡化的權限管理和集中式身分識別管理資料庫使用者和其他 Microsoft 服務。 這個藍圖也會指派要稽核的自訂 RBAC 規則使用的 Azure 原則定義。 了解自訂的 RBAC 規則所在實作可協助您確認需要並適當的實作中，為自訂的 RBAC 規則很容易發生錯誤。

- 稽核 SQL 伺服器的 Azure Active Directory 管理員佈建
- 稽核 Service Fabric 中 Azure Active Directory 用於用戶端驗證的使用方式
- 稽核自訂 RBAC 規則的使用方式

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 管理特殊權限的存取權限

此藍圖可協助您限制及控制特殊權限的存取權限，指派四個[Azure 原則](../../../policy/overview.md)稽核外部帳戶擁有者及/或寫入權限和帳戶擁有者及/或寫入權限的定義不需要啟用多重要素驗證。

- [預覽]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [預覽]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [預覽]: Audit external accounts with owner permissions on a subscription
- [預覽]: Audit external accounts with write permissions on a subscription

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A.9.2.4 管理使用者的密碼驗證資訊

此藍圖會將指派三個[Azure 原則](../../../policy/overview.md)定義来稽核不需要啟用多重要素驗證的帳戶。 Multi-factor authentication 協助保護帳戶安全，即使一段驗證資訊遭到入侵。 藉由監視帳戶，而不需要啟用多重要素驗證，您可以識別可能會更容易遭到入侵的帳戶。 這個藍圖也會指派兩個稽核的 Linux VM 的 Azure 原則定義密碼檔案的權限設定不正確時發出警示。 此設定可讓您採取更正動作以確保驗證器不會受到危害。

- [預覽]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [預覽]: Audit accounts with read permissions who are not MFA enabled on a subscription
- [預覽]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [預覽]: Deploy VM extension to audit Linux VM passwd file permissions
- [預覽]: Audit Linux VM /etc/passwd file permissions are set to 0644

## <a name="a925-review-of-user-access-rights"></a>使用者存取權限的 A.9.2.5 檢閱

Azure 會實作[角色型存取控制](../../../../role-based-access-control/overview.md)(RBAC) 來協助您管理 Azure 中擁有資源的存取權。 使用 Azure 入口網站，您可以檢閱誰可以存取 Azure 資源和其權限。 此藍圖指派四[Azure 原則](../../../policy/overview.md)定義稽核應該優先處理供檢閱，包括折舊的帳戶和外部帳戶，以更高權限的帳戶。

- [預覽]: Audit deprecated accounts on a subscription
- [預覽]: Audit deprecated accounts with owner permissions on a subscription
- [預覽]: Audit external accounts with owner permissions on a subscription
- [預覽]: Audit external accounts with write permissions on a subscription

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 移除或調整的存取權限

Azure 會實作[角色型存取控制](../../../../role-based-access-control/overview.md)(RBAC) 來協助您管理 Azure 中擁有資源的存取權。 使用[Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md)和 RBAC，您可以更新使用者角色，以反映組織的變更。 需要時，帳戶可以封鎖登入 （或移除），以立即移除 Azure 資源的存取權限。 此藍圖會指派兩個[Azure 原則](../../../policy/overview.md)定義稽核折舊應視為要移除的帳戶。

- [預覽]: Audit deprecated accounts on a subscription
- [預覽]: Audit deprecated accounts with owner permissions on a subscription

## <a name="a943-password-management-system"></a>A.9.4.3 密碼管理系統

此藍圖可協助您強制執行強式密碼指派 10 [Azure 原則](../../../policy/overview.md)稽核不會強制執行最小的強度和其他密碼需求的 Windows Vm 的定義。 Vm 的密碼強度原則違規的認知，可協助您採取矯正措施來確保 VM 的所有使用者帳戶的密碼符合原則。

- [預覽]: Deploy VM extension to audit Windows VM enforces password complexity requirements
- [預覽]: Deploy VM extension to audit Windows VM maximum password age 70 days
- [預覽]: Deploy VM extension to audit Windows VM minimum password age 1 day
- [預覽]: Deploy VM extension to audit Windows VM passwords must be at least 14 characters
- [預覽]: Deploy VM extension to audit Windows VM should not allow previous 24 passwords
- [預覽]: Audit Windows VM enforces password complexity requirements
- [預覽]: Audit Windows VM maximum password age 70 days
- [預覽]: Audit Windows VM minimum password age 1 day
- [預覽]: Audit Windows VM passwords must be at least 14 characters
- [預覽]: Audit Windows VM should not allow previous 24 passwords

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A.10.1.1 原則上使用密碼編譯控制項

此藍圖可協助您強制執行密碼編譯控制項的使用原則指派 13 [Azure 原則](../../../policy/overview.md)強制執行特定的密碼編譯控制項和稽核的定義使用弱式密碼編譯設定。
了解您的 Azure 資源可能有非最佳化的密碼編譯設定的位置，可協助您採取更正動作，以確保資源會根據您的資訊安全性原則設定。 具體而言，這個藍圖所指派的原則會要求加密 blob 儲存體帳戶和 data lake 儲存體帳戶;需要 SQL 資料庫透明資料加密稽核儲存體帳戶、 SQL 資料庫、 虛擬機器磁碟和自動化帳戶的變數; 遺漏加密稽核儲存體帳戶、 函式應用程式、 Web 應用程式、 API 應用程式和 Redis 快取; 不安全的連線稽核弱式的虛擬機器密碼加密;規則和稽核未加密的 Service Fabric 通訊。

- [預覽]: Audit HTTPS only access for a Function App
- [預覽]: Audit HTTPS only access for a Web Application
- [預覽]: Audit HTTPS only access for an API App
- [預覽]: Audit missing blob encryption for storage accounts
- [預覽]: Deploy VM extension to audit Windows VM should not store passwords using reversible
  加密n
- [預覽]: Audit Windows VM should not store passwords using reversible encryption
- [預覽]: Monitor unencrypted SQL database in Azure Security Center
- [預覽]: Monitor unencrypted VM Disks in Azure Security Center
- 稽核自動化帳戶變數加密的啟用
- 稽核只允許對您 Redis Cache 的安全連線
- 稽核儲存體帳戶的安全傳輸
- 稽核 Service Fabric 中的 ClusterProtectionLevel 屬性設定為 EncryptAndSign
- 稽核透明資料加密狀態

## <a name="a1241-event-logging"></a>A.12.4.1 事件記錄

此藍圖可協助您確保系統事件所記錄的指派七[Azure 原則](../../../policy/overview.md)稽核記錄檔設定 Azure 資源上的定義。 指派的原則也會稽核是否虛擬機器不將記錄傳送到指定的 log analytics 工作區。

- [預覽]：稽核相依性代理程式部署 VM 映像 (OS) 未列出
- [預覽]：稽核在 VMSS-VM 映像 (OS) 未列出的相依性代理程式部署
- [預覽]：Audit Log Analytics 代理程式部署 VM 映像 (OS) 未列出
- [預覽]：稽核在 VMSS-VM 映像 (OS) 未列出的 Log Analytics 代理程式部署
- [預覽]: Monitor unaudited SQL database in Azure Security Center
- 稽核診斷設定
- 稽核 SQL 伺服器層級稽核設定

## <a name="a121-management-of-technical-vulnerabilities"></a>技術漏洞的 A.12.1 管理

此藍圖可協助您藉由指派五個管理資訊系統弱點[Azure 原則](../../../policy/overview.md)監視遺漏系統更新、 作業系統弱點、 SQL 弱點，以及虛擬機器的定義弱點。 這些深入解析提供有關您所部署資源的安全性狀態的即時資訊，並可協助您排定優先順序的修復動作。

- [預覽]: Monitor missing Endpoint Protection in Azure Security Center
- [預覽]: Monitor missing system updates in Azure Security Center
- [預覽]: Monitor OS vulnerabilities in Azure Security Center
- [預覽]: Monitor SQL vulnerability assessment results in Azure Security Center
- [預覽]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>軟體安裝的 A.12.6.2 限制

自適性應用程式控制是能夠協助您控制哪些應用程式可以在 Azure 中的 Vm 上執行的 Azure 資訊安全中心的解決方案。 此藍圖會指派允許的應用程式集的變更會監視 Azure 原則定義。 軟體安裝的限制，可協助您減少軟體弱點引入的可能性。

- [預覽]: Monitor possible app Whitelisting in Azure Security Center

## <a name="a1311-network-controls"></a>A.13.1.1 網路控制

此藍圖可協助您管理和控制網路指派[Azure 原則](../../../policy/overview.md)監視寬鬆的規則的網路安全性群組的定義。 寬鬆的規則可能會允許非預期的網路存取，並應檢閱。

- [預覽]: Monitor permissive network access in Azure Security Center

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 資訊傳輸原則和程序

該藍圖有助於您確保資訊傳輸，與 Azure 服務的安全指派兩個[Azure 原則](../../../policy/overview.md)定義来稽核儲存體帳戶和 Redis 快取不安全的連線。

- 稽核只允許對您 Redis Cache 的安全連線
- 稽核儲存體帳戶的安全傳輸

## <a name="a1413-protecting-application-services-transactions"></a>A.14.1.3 保護應用程式服務交易

此藍圖可協助您保護資訊系統資產指派三個[Azure 原則](../../../policy/overview.md)監視未受保護的端點、 應用程式和儲存體帳戶的定義。 端點和防火牆，和不受限制存取的儲存體帳戶不受保護的應用程式可以允許意外的存取資訊系統內所包含的資訊。

- [預覽]: Monitor unprotected network endpoints in Azure Security Center
- [預覽]: Monitor unprotected web application in Azure Security Center
- 稽核不受限制的儲存體帳戶網路存取

## <a name="a1613-reporting-information-security-weaknesses"></a>A.16.1.3 報告資訊安全性弱點

此藍圖可協助您藉由指派五個維護系統弱點[Azure 原則](../../../policy/overview.md)監視弱點、 修補狀態和惡意程式碼警示，Azure 資訊安全中心的定義。 Azure 資訊安全中心會提供報表功能，可讓您有已部署的 Azure 資源安全性狀態的即時深入解析。

- [預覽]: Monitor missing Endpoint Protection in Azure Security Center
- [預覽]: Monitor missing system updates in Azure Security Center
- [預覽]: Monitor OS vulnerabilities in Azure Security Center
- [預覽]: Monitor SQL vulnerability assessment results in Azure Security Center
- [預覽]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="next-steps"></a>後續步驟

既然您已檢閱過的 ISO 27001 應用程式服務環境/SQL Database 工作負載 blueprint （藍圖） 範例控制項對應，請瀏覽下列文章以深入了解架構以及如何部署此範例：

> [!div class="nextstepaction"]
> [ISO 27001 應用程式服務環境/SQL Database 工作負載藍圖-概觀](./index.md)
> [ISO 27001 應用程式服務環境/SQL Database 工作負載藍圖-部署步驟](./deploy.md)

有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../../how-to/update-existing-assignments.md)。