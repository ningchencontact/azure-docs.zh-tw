---
title: 範例 - CIS Microsoft Azure 基礎基準測試藍圖 - 建議對應
description: CI Microsoft Azure 基礎基準測試藍圖範例與 Azure 原則的建議對應。
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/09/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 118ff46ca3b640c6ec24ab85fd598f213229417a
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014013"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>CI Microsoft Azure 基礎基準測試藍圖範例的建議對應。

下列文章將詳細說明 Azure 藍圖 CIS Microsoft Azure 基礎基準測試藍圖範例如何對應至 CIS Microsoft Azure 基礎基準測試建議。 如需關於建議的詳細資訊，請參閱 [CIS Microsoft Azure基礎基準測試](https://www.cisecurity.org/benchmark/azure/)。

以下是 **CIS Microsoft Azure 基礎基準測試 v1.1.0** 建議的對應。 使用右側的導覽區可直接跳到特定的建議對應。
許多對應的建議都是以 [Azure 原則](../../../policy/overview.md)方案進行實作的。 若要檢閱完整方案，請在 Azure 入口網站中開啟 [原則]  ，然後選取 [定義]  頁面。 然後，找出並選取 **\[預覽\] 稽核 CIS Microsoft Azure 基礎基準測試 v1.1.0 建議，並部署特定的 VM 延伸模組，以支援稽核需求**內建原則方案。

> [!NOTE]
> 完整的藍圖範例即將推出。 現已提供相關聯的 Azure 原則方案。

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1.1 確定已為所有具特殊權限的使用者啟用多重要素驗證

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- 應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA
- 應在您訂用帳戶上具有寫入權限的帳戶上啟用 MFA

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1.2 確定已為所有不具特殊權限的使用者啟用多重要素驗證

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- 應在您訂用帳戶上具有讀取權限的帳戶上啟用 MFA

## <a name="13-ensure-that-there-are-no-guest-users"></a>1.3 確定沒有任何來賓使用者

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除
- 具有讀取權限的外部帳戶應該從您的訂用帳戶中移除
- 應從訂用帳戶移除具有寫入權限的外部帳戶

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2.3 確定 ASC 預設原則設定 [監視系統更新] 不是 [已停用]

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- 您應在機器上安裝系統更新

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2.4 確定 ASC 預設原則設定 [監視 OS 弱點] 不是 [已停用]

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- 您應在機器上修復安全性組態的弱點

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2.5 確定 ASC 預設原則設定 [監視端點保護] 不是 [已停用]

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- 在 Azure 資訊安全中心中監視缺少的 Endpoint Protection

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2.6 確定 ASC 預設原則設定 [監視磁碟加密] 不是 [已停用]

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- 應在虛擬機器上套用磁碟加密

## <a name="28-ensure-asc-default-policy-setting-monitor-web-application-firewall-is-not-disabled"></a>2.8 確定 ASC 預設原則設定 [監視 Web 應用程式防火牆] 不是 [已停用]

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- 應在 IaaS 上強化 Web 應用程式的 NSG 規則

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2.10 確定 ASC 預設原則設定 [監視弱點評估] 不是 [已停用]

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- 弱點評量解決方案應修復弱點

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2.12 確定 ASC 預設原則設定 [監視 JIT 網路存取] 不是 [已停用]

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- Just-In-Time 網路存取控制應套用在虛擬機器上

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2.15 確定 ASC 預設原則設定 [監視 SQL 加密] 不是 [已停用]

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- 應在 SQL 資料庫上啟用透明資料加密

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3.1 確定 [需要安全傳輸] 設定為 [已啟用]

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- 應啟用儲存體帳戶的安全傳輸

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3.7 確定儲存體帳戶的預設網路存取規則設定為拒絕

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- 稽核不受限制的儲存體帳戶網路存取

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4.1 確定 [稽核] 設定為 [開啟]

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- 應該在 SQL Server 上的進階資料安全性設定上啟用稽核

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4.2 確定已在 SQL 伺服器的 [稽核] 原則中正確設定了 'AuditActionGroups' 原則

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- SQL 審核設定應已設定動作群組來擷取重要活動

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4.3 確定 [稽核] 保留期「大於 90 天」

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- SQL 伺服器應設定有 90 天以上的稽核保留天數。

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4.4 確定 SQL 伺服器上的 [進階資料安全性] 設定為 [開啟]

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- 應在 SQL 伺服器上啟用進階資料安全性

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4.5 確定 [威脅偵測類型] 設定為 [全部]

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- 在 SQL 伺服器的進階資料安全性設定中，進階威脅防護類型應設定為 [全部]
- 在 SQL 受控執行個體的進階資料安全性設定中，進階威脅防護類型應設定為 [全部]

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4.6 確定已設定 [傳送警示給]

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- SQL 伺服器的進階資料安全性設定應包含用來接收安全性警示的電子郵件地址

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4.7 確定 [電子郵件服務和共同管理員] 為 [已啟用]

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- SQL 受控執行個體的進階資料安全性設定應包含用來接收安全性警示的電子郵件地址

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4.8 確定已設定 Azure Active Directory 管理員

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- 應針對 SQL 伺服器佈建 Azure Active Directory 管理員

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4.9 確定 SQL Database 上的 [資料加密] 設定為 [開啟]

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- 應在 SQL 資料庫上啟用透明資料加密

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4.10 確定 SQL 伺服器的 TDE 保護裝置已使用 BYOK 加密 (使用您自己的金鑰)

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- SQL 伺服器的 TDE 保護裝置應以您自己的金鑰加密
- SQL 受控執行個體的 TDE 保護裝置應以您自己的金鑰加密

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 確定 Azure KeyVault 的記錄是 [已啟用]

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- 應啟用 Key Vault 中的診斷記錄

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7.1 確定 [OS 磁碟] 已加密

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- 應在虛擬機器上套用磁碟加密

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7.2 確定 [資料磁碟] 已加密

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- 應在虛擬機器上套用磁碟加密

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7.5 確定已為所有虛擬機器套用最新 OS 修補程式

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- 您應在機器上安裝系統更新

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7.6 確定已為所有虛擬機器安裝端點

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- 在 Azure 資訊安全中心中監視缺少的 Endpoint Protection

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8.5 在 Azure Kubernetes Services 中啟用角色型存取控制 (RBAC)

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- \[預覽\]：應在 Kubernetes 服務上使用角色型存取控制 (RBAC)

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9.2 確定 Web 應用程式在 Azure App Service 中會將所有 HTTP 流量重新導向至 HTTPS

此藍圖指派了與此 CIS 建議對應的 [Azure 原則](../../../policy/overview.md)定義。

- Web 應用程式應只可經由 HTTPS 存取

## <a name="next-steps"></a>後續步驟

您已檢閱 CIS Microsoft Azure 基礎基準測試藍圖的控制項對應，接下來請瀏覽下列文章以了解藍圖，或瀏覽 Azure 入口網站中的 Azure 原則來指派方案：

> [!div class="nextstepaction"]
> [CIS Microsoft Azure 基礎基準測試藍圖 - 概觀](./index.md)
> [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../../how-to/update-existing-assignments.md)。