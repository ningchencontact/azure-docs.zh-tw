---
title: 範例 - ISO 27001 共用服務藍圖 - 控制項對應
description: ISO 27001 共用服務藍圖範例對於 Azure 原則和 RBAC 的控制項對應。
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 93a26311216ca5682c02a319f989b3d342a33ce1
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59256476"
---
# <a name="control-mapping-of-the-iso-27001-shared-services-blueprint-sample"></a>ISO 27001 共用服務藍圖範例的控制項對應

下列文章將詳細說明 Azure 藍圖 ISO 27001 共用服務藍圖範例與 ISO 27001 控制項的對應情形。 如需控制項的詳細資訊，請參閱 [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html)。

以下是與 **ISO 27001:2013** 控制項的對應。 使用右側的導覽區可直接跳到特定的控制項對應。 許多對應的控制項都是以 [Azure 原則](../../../policy/overview.md)方案進行實作的。 若要檢閱完整方案，請在 Azure 入口網站中開啟 [原則]，然後選取 [定義] 頁面。 然後，找出並選取 **[預覽] 稽核 ISO 27001:2013 控制項並部署特定的 VM 延伸模組，以支援稽核需求**內建原則方案。

## <a name="a612-segregation-of-duties"></a>A.6.1.2 權責區分

只有一個 Azure 訂用帳戶擁有者時，無法建立系統管理備援。 相反地，若有過多 Azure 訂用帳戶擁有者，因擁有者帳戶遭到入侵而產生缺口的可能性就會增加。 此藍圖指派了兩項 [Azure 原則](../../../policy/overview.md)定義，可稽核 Azure 訂用帳戶的擁有者數目，藉以協助您維護適當數量的 Azure 訂用帳戶擁有者。 管理訂用帳戶擁有者權限可協助您實作適當的權責區分。

- [預覽]: Audit minimum number of owners for subscription
- [預覽]: Audit maximum number of owners for a subscription

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 存取網路和網路服務

Azure 會實作[角色型存取控制 (RBAC)](../../../../role-based-access-control/overview.md)，以管理有權存取 Azure 資源的人員。 此藍圖指派了七項 [Azure 原則](../../../policy/overview.md)定義以協助您控制對 Azure 資源的存取。 這些原則會稽核資源類型和組態的使用方式是否可能會允許更寬鬆資源存取。
了解有哪些資源違反這些原則可協助您採取修正動作，以確保 Azure 資源僅限定給經授權的使用者存取。

- [預覽]: Deploy VM extension to audit Linux VM accounts with no passwords
- [預覽]: Deploy VM extension to audit Linux VM allowing remote connections from accounts with no
  密碼s
- [預覽]: Audit Linux VM accounts with no passwords
- [預覽]: Audit Linux VM allowing remote connections from accounts with no passwords
- 稽核傳統儲存體帳戶的使用
- 稽核傳統虛擬機器的使用
- 稽核不是使用受控磁碟的 VM

## <a name="a922-user-access-provisioning"></a>A.9.2.2 使用者存取佈建

Azure 會實作[角色型存取控制 (RBAC)](../../../../role-based-access-control/overview.md)，以管理有權存取 Azure 資源的人員。 此藍圖指派了三項 [Azure 原則](../../../policy/overview.md)定義，用以稽核將 [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) 驗證用於 SQL Server 和 [Service Fabric](../../../../service-fabric/service-fabric-overview.md) 的情形。 使用 Azure Active Directory 驗證可針對資料庫使用者及其他 Microsoft 服務，簡化權限管理及集中管理身分識別。 此藍圖也指派了一項 Azure 原則定義來稽核自訂 RBAC 規則的使用情形。 了解自訂 RBAC 規則的實作之處，有助於您確認需求和適當的實作，因為自訂的 RBAC 規則很容易發生錯誤。

- 稽核 SQL 伺服器的 Azure Active Directory 管理員佈建
- 稽核 Service Fabric 中 Azure Active Directory 用於用戶端驗證的使用方式
- 稽核自訂 RBAC 規則的使用方式

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 管理特殊權限的存取權

此藍圖指派了四項 [Azure 原則](../../../policy/overview.md)定義，用以稽核具有擁有者及/或寫入權限的外部帳戶，和具有擁有者及/或寫入權限、且未啟用多重要素驗證的帳戶，以協助您限制及控制特殊權限的存取權。

- [預覽]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [預覽]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [預覽]: Audit external accounts with owner permissions on a subscription
- [預覽]: Audit external accounts with write permissions on a subscription

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A.9.2.4 管理使用者的秘密驗證資訊

此藍圖指派了三項 [Azure 原則](../../../policy/overview.md)定義來稽核未啟用多重要素驗證的帳戶。 多重要素驗證有助於確保帳戶即使有部分驗證資訊遭到入侵，仍然安全無虞。 藉由監視未啟用多重要素驗證的帳戶，您將可識別較可能遭到入侵的帳戶。 此藍圖也指派了兩項 Azure 原則定義來稽核 Linux VM 密碼檔案權限，以在其設定不正確時發出警示。 此設定可讓您採取更正措施，以確保驗證器不會遭到入侵。

- [預覽]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [預覽]: Audit accounts with read permissions who are not MFA enabled on a subscription
- [預覽]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [預覽]: Deploy VM extension to audit Linux VM passwd file permissions
- [預覽]: Audit Linux VM /etc/passwd file permissions are set to 0644

## <a name="a925-review-of-user-access-rights"></a>A.9.2.5 檢閱使用者存取權限

Azure 會實作[角色型存取控制 (RBAC)](../../../../role-based-access-control/overview.md)，以協助您管理有權存取 Azure 資源的人員。 您可以使用 Azure 入口網站，檢閱可存取 Azure 資源的人員及其權限。 此藍圖指派了四項 [Azure 原則](../../../policy/overview.md)定義來稽核應優先檢閱的帳戶，包括已停用的帳戶和已提高權限的外部帳戶。

- [預覽]: Audit deprecated accounts on a subscription
- [預覽]: Audit deprecated accounts with owner permissions on a subscription
- [預覽]: Audit external accounts with owner permissions on a subscription
- [預覽]: Audit external accounts with write permissions on a subscription

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 移除或調整存取權限

Azure 會實作[角色型存取控制 (RBAC)](../../../../role-based-access-control/overview.md)，以協助您管理有權存取 Azure 資源的人員。 使用 [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) 和 RBAC，可更新使用者角色以反映組織變更。 必要時可以封鎖帳戶的登入 (或移除帳戶)，而立即移除對 Azure 資源的存取權限。 此藍圖指派了兩項 [Azure 原則](../../../policy/overview.md)定義來稽核應考慮移除的停用帳戶。

- [預覽]: Audit deprecated accounts on a subscription
- [預覽]: Audit deprecated accounts with owner permissions on a subscription

## <a name="a943-password-management-system"></a>A.9.4.3 密碼管理系統

此藍圖指派了 10 項 [Azure 原則](../../../policy/overview.md)定義，用以稽核未強制執行最低強度和其他密碼需求的 Windows VM，以協助您強制執行強式密碼。 確知有哪些 VM 違反密碼強度原則可協助您採取更正措施，以確保所有 VM 使用者帳戶的密碼均符合原則。

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

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A.10.1.1 使用密碼編譯控制項的原則

此藍圖指派了 13 項 [Azure 原則](../../../policy/overview.md)定義，據以強制執行特定的密碼編譯控制項及稽核弱式密碼編譯設定的使用，以協助您強制執行密碼編譯控制項的使用原則。
了解您的 Azure 資源在哪些層面可能使用非最佳化的密碼編譯組態，有助於您採取更正措施，以確保資源會根據您的資訊安全性原則進行設定。 具體而言，此藍圖所指派的原則會要求 Blob 儲存體帳戶和 Data Lake Storage 帳戶必須加密；SQL 資料庫必須進行透明資料加密；稽核儲存體帳戶、SQL 資料庫、虛擬機器磁碟和自動化帳戶變數是否缺少加密；稽核儲存體帳戶、函式應用程式、Web 應用程式、API 應用程式和 Redis 快取的連線是否不安全；稽核弱式虛擬機器密碼加密；稽核未加密的 Service Fabric 通訊。

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

此藍圖指派了七項 [Azure 原則](../../../policy/overview.md)定義來稽核 Azure 資源的記錄設定，以協助您確保會記錄系統事件。 指派的原則也會稽核虛擬機器是否未將記錄傳送至指定的記錄分析工作區。

- [預覽]：稽核相依性代理程式部署 - 未列出的 VM 映像 (OS)
- [預覽]：稽核 VMSS 中的相依性代理程式部署 - 未列出的 VM 映像 (OS)
- [預覽]：稽核記錄分析代理程式部署 - 未列出的 VM 映像 (OS)
- [預覽]：稽核 VMSS 中的記錄分析代理程式部署 - 未列出的 VM 映像 (OS)
- [預覽]: Monitor unaudited SQL database in Azure Security Center
- 稽核診斷設定
- 稽核 SQL 伺服器層級稽核設定

## <a name="a121-management-of-technical-vulnerabilities"></a>A.12.1 管理技術弱點

此藍圖指派了五項 [Azure 原則](../../../policy/overview.md)定義，用以監視遺漏的系統更新、作業系統弱點、SQL 弱點和虛擬機器弱點，以協助您管理資訊系統弱點。 這些深入解析可讓您即時檢視已部署資源的安全性狀態，並且可協助您排定補救措施的優先順序。

- [預覽]: Monitor missing Endpoint Protection in Azure Security Center
- [預覽]: Monitor missing system updates in Azure Security Center
- [預覽]: Monitor OS vulnerabilities in Azure Security Center
- [預覽]: Monitor SQL vulnerability assessment results in Azure Security Center
- [預覽]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 軟體安裝的限制

自適性應用程式控制是 Azure 資訊安全中心提供的解決方案，可協助您控制哪些應用程式可在您位於 Azure 中的 VM 上執行。 此藍圖指派了一項 Azure 原則定義，用以監視允許的應用程式集所進行的變更。 軟體安裝的限制有助於降低引入軟體弱點的可能性。

- [預覽]: Monitor possible app Whitelisting in Azure Security Center

## <a name="a1311-network-controls"></a>A.13.1.1 網路控制

此藍圖指派了一項 [Azure 原則](../../../policy/overview.md)定義來監視使用寬鬆規則的網路安全性群組，以協助您管理和控制網路。 太寬鬆的規則可能會允許非預期的網路存取，您應加以檢閱。

- [預覽]: Monitor permissive network access in Azure Security Center

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 資訊傳輸原則和程序

此藍圖指派了兩項 [Azure 原則](../../../policy/overview.md)定義來稽核不安全的儲存體帳戶和 Redis 快取連線，以協助您確保 Azure 服務的資訊傳輸安全無虞。

- 稽核只允許對您 Redis Cache 的安全連線
- 稽核儲存體帳戶的安全傳輸

## <a name="a1413-protecting-application-services-transactions"></a>A.14.1.3 保護應用程式服務交易

此藍圖指派了三項 [Azure 原則](../../../policy/overview.md)定義來監視未受保護的端點、應用程式和儲存體帳戶，以協助您保護資訊系統資產。 不受防火牆保護的端點和應用程式，以及存取不受限制的儲存體帳戶，都可能允許資訊系統內所包含的資訊受到非預期的存取。

- [預覽]: Monitor unprotected network endpoints in Azure Security Center
- [預覽]: Monitor unprotected web application in Azure Security Center
- 稽核不受限制的儲存體帳戶網路存取

## <a name="a1613-reporting-information-security-weaknesses"></a>A.16.1.3 報告資訊安全性弱點

此藍圖指派了五項 [Azure 原則](../../../policy/overview.md)定義來監視 Azure 資訊安全中心提供的弱點、修補狀態和惡意程式碼警示，以協助您持續關注系統弱點。 Azure 資訊安全中心提供報告功能，可讓您即時深入檢視已部署 Azure 資源的安全性狀態。

- [預覽]: Monitor missing Endpoint Protection in Azure Security Center
- [預覽]: Monitor missing system updates in Azure Security Center
- [預覽]: Monitor OS vulnerabilities in Azure Security Center
- [預覽]: Monitor SQL vulnerability assessment results in Azure Security Center
- [預覽]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="next-steps"></a>後續步驟

您已檢閱過 ISO 27001 共用服務藍圖的控制項對應，接下來請瀏覽下列文章以了解架構及如何部署此範例：

> [!div class="nextstepaction"]
> [ISO 27001 共用服務藍圖 - 概觀](./index.md)
> [ISO 27001 共用服務藍圖 - 部署步驟](./deploy.md)

有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../../how-to/update-existing-assignments.md)。