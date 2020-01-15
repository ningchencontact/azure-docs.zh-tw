---
title: 使用客戶管理的金鑰適用於 MySQL 的 Azure 資料庫資料加密
description: 適用於 MySQL 的 Azure 資料庫使用客戶管理的金鑰進行資料加密，可讓您攜帶您自己的金鑰（BYOK）進行待用資料保護，並可讓組織在金鑰和資料的管理中執行責任分離。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 12e9ab9066449e8928d937d9c3f9f7f1522b6c60
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942108"
---
# <a name="azure-database-for-mysql-data-encryption-with-customer-managed-key"></a>使用客戶管理的金鑰適用於 MySQL 的 Azure 資料庫資料加密

> [!NOTE]
> 此時，您必須要求存取權才能使用這項功能。 若要這麼做，請洽詢 AskAzureDBforMySQL@service.microsoft.com。

適用於 MySQL 的 Azure 資料庫使用客戶管理的金鑰進行資料加密，可讓您攜帶您自己的金鑰（BYOK）進行待用資料保護，並可讓組織在金鑰和資料的管理中執行責任分離。 有了客戶管理的加密，您就可以完全掌控金鑰的生命週期（金鑰建立、上傳、輪替、刪除）、金鑰使用許可權，以及對金鑰進行作業的審核。

針對適用於 MySQL 的 Azure 資料庫，資料加密是在伺服器層級設定。 使用這種形式的資料加密，金鑰會用來加密資料庫加密金鑰（DEK），這是以雲端為基礎的外部金鑰管理系統，儲存在客戶擁有和客戶管理的[Azure Key Vault （AKV）](../key-vault/key-Vault-secure-your-key-Vault.md)中的客戶管理的非對稱金鑰。 AKV 是高可用性，並為 RSA 密碼編譯金鑰提供可擴充的安全儲存體，並選擇性地支援由 FIPS 140-2 Level 2 驗證的硬體安全性模組（Hsm）。 它不允許直接存取預存金鑰，而是使用授權實體的金鑰提供加密/解密服務。 金鑰可由 Key Vault、匯入或[從內部部署 HSM 裝置傳輸至 Key Vault](../key-vault/key-Vault-hsm-protected-keys.md)。

> [!NOTE]
> 這項功能適用于所有 Azure 區域，其中適用於 MySQL 的 Azure 資料庫支援一般用途和記憶體優化定價層。

## <a name="benefits"></a>優勢

適用於 MySQL 的 Azure 資料庫的資料加密提供下列優點：

* 增加加密金鑰的透明度、細微控制和管理。
* 集中管理和金鑰組織，方法是將它們裝載在 Azure Key Vault 中。
* 能夠在組織內的金鑰和資料的管理中，執行責任區隔的區分。
* 將金鑰管理與組織內的資料管理分開，讓 Key Vault 系統管理員可以撤銷金鑰存取權限，使加密的資料庫無法存取。
* 因為 Azure Key Vault 的設計是讓 Microsoft 無法看到也不會解壓縮加密金鑰，所以您的終端客戶會有更大的信任。

## <a name="terminology-and-description"></a>術語和描述

**資料加密金鑰 (DEK)** – 用於將分割區或資料區塊加密的對稱 AES256 金鑰。 使用不同金鑰將每個資料區塊進行加密，會使密碼編譯分析攻擊更加困難。 資源提供者或要將特定區塊加密和解密的應用程式執行個體都需要存取 DEK。 當 DEK 取代為新的索引鍵時，只有其相關聯區塊中的資料必須以新的金鑰重新加密。

**金鑰加密金鑰（KEK）** -用來加密資料加密金鑰的加密金鑰。 使用永遠不會離開 Key Vault 的金鑰加密金鑰，允許加密和控制資料加密金鑰本身。 可存取 KEK 的實體可能不同於需要 DEK 的實體。 因為需要 KEK 才能將 DEK 解密，KEK 實際上就是單一點，藉以透過刪除 KEK 來有效地刪除 DEK。

以金鑰加密金鑰加密的資料加密金鑰會分開儲存，而且只有具備金鑰加密金鑰存取權的實體，才可以解密這些資料加密金鑰。 如需詳細資訊，請參閱[靜態加密中的安全性](../security/fundamentals/encryption-atrest.md)。

## <a name="how-data-encryption-with-customer-managed-key-works"></a>使用客戶管理的金鑰來加密資料的方式

![攜帶您自己的金鑰總覽](media/concepts-data-access-and-security-data-encryption/mysqloverview.png)

若要讓 MySQL 伺服器能夠使用儲存在 AKV 中的客戶管理金鑰來加密 DEK，Key Vault 系統管理員必須使用其唯一的身分識別，授與伺服器的下列存取權限：

* **get** -用於在 Key Vault 中抓取金鑰的公用元件和屬性
* **wrapKey** -能夠保護（加密） DEK
* **unwrapKey** -能夠解除保護（解密） DEK

Key Vault 系統管理員也可以[啟用 Key Vault audit 事件的記錄](../azure-monitor/insights/azure-key-vault.md)，以便稍後再進行審核。

當伺服器設定為使用儲存在 Key Vault 中客戶管理的金鑰時，伺服器會將 DEK 傳送至加密的 Key Vault。 Key Vault 會傳回加密的 DEK，這會儲存在使用者資料庫中。 同樣地，在需要時，伺服器會將受保護的 DEK 傳送至 Key Vault 進行解密。 如果啟用記錄功能，則審計員可以使用 Azure 監視器來審查 Key Vault AuditEvent 記錄。

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-mysql"></a>設定適用於 MySQL 的 Azure 資料庫資料加密的需求

### <a name="requirements-for-configuring-akv"></a>設定 AKV 的需求

* Key Vault 和適用於 MySQL 的 Azure 資料庫必須屬於相同的 Azure Active Directory （AAD）租使用者。 不支援跨租使用者 Key Vault 和伺服器互動。 之後移動資源需要您重新設定資料加密。 深入瞭解如何移動資源。
* 必須在 Key Vault 上啟用虛刪除功能，以防止意外遺失的金鑰（或 Key Vault）刪除作業發生。 虛刪除的資源會保留90天，除非客戶在此期間復原或清除。 「復原」和「清除」動作在 Key Vault 存取原則中具有相關聯的許可權。 虛刪除功能預設為關閉，並可透過 Powershell 或 CLI 啟用。 無法透過 Azure 入口網站啟用。
* 使用**get、wrapKey、unwrapKey 許可權，** 授與對 Key Vault 的適用於 MySQL 的 Azure 資料庫存取權，其方式是使用其唯一的受控識別。 使用 Azure 入口網站時，在 MySQL 上啟用資料加密時，會自動建立唯一的識別。 如需使用 Azure 入口網站時的詳細逐步指示，請參閱[設定 MySQL 的資料加密](howto-data-encryption-portal.md)。

* 使用防火牆搭配 AKV 時，您必須啟用 *[允許受信任的 Microsoft 服務略過防火牆*] 選項。

### <a name="requirements-for-configuring-customer-key"></a>設定客戶金鑰的需求

* 用來加密 DEK 的客戶管理金鑰只能是非對稱的 RSA 2028。
* 金鑰啟用日期（若已設定）必須是過去的日期和時間。 到期日（如果設定）必須是未來的日期和時間。
* 金鑰必須處於 [*已啟用*] 狀態。
* 如果您要將現有的金鑰匯入 Key Vault，請務必以支援的檔案格式（`.pfx`、`.byok`、`.backup`）提供它。

## <a name="recommendations-when-using-data-encryption-using-customer-managed-key"></a>使用客戶管理的金鑰進行資料加密時的建議

### <a name="recommendation-for-configuring-akv"></a>設定 AKV 的建議

* 在 Key Vault 上設定資源鎖定，以控制誰可以刪除此重要資源，並防止意外或未經授權的刪除。 深入瞭解資源鎖定。
* 啟用所有加密金鑰的審核和報告功能： Key Vault 提供容易插入其他安全性資訊和事件管理工具的記錄檔。 Azure 監視器 Log Analytics 是已整合之服務的其中一個範例。

* 請確定 Key Vault 和適用於 MySQL 的 Azure 資料庫位於相同的區域，以確保更快存取 DEK wrap/解除包裝作業。

### <a name="recommendation-for-configuring-customer-managed-key"></a>設定客戶管理金鑰的建議

* 將客戶管理的金鑰（KEK）複本保留在安全的位置，或將它證書處理到憑證服務。

* 如果 Key Vault 中產生金鑰，請在第一次使用 AKV 中的金鑰之前，先建立金鑰備份。 只能將備份還原到 Azure Key Vault。 深入瞭解[AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey)命令。

## <a name="inaccessible-customer-managed-key-condition"></a>無法存取客戶管理的金鑰狀況

在 Azure Key Vault （AKV）中使用客戶管理的金鑰設定資料加密時，伺服器必須能夠持續存取此金鑰才能保持線上狀態。 如果伺服器在10分鐘內無法存取 AKV 中客戶管理的金鑰，伺服器就會開始拒絕所有連線與對應的錯誤訊息，並將伺服器狀態變更為「**無法**存取」。 在無法存取狀態的資料庫上，唯一允許的動作是刪除它。

### <a name="accidental-key-access-revocation-from-the-azure-key-vault-akv"></a>從 Azure Key Vault 意外撤銷金鑰存取權（AKV）

這可能是因為具有足夠存取權限 Key Vault 的人不小心停用伺服器對金鑰的存取權，方法是：

* 從伺服器撤銷 Key Vault 的 get、wrapKey、unwrapKey 許可權
* 刪除金鑰
* 刪除 Key Vault
* 變更 Key Vault 的防火牆規則

* 在 Azure Active Directory 中刪除伺服器的受控識別

## <a name="monitoring-of-the-customer-managed-key-in-the-key-vault"></a>監視 Key Vault 中客戶管理的金鑰

若要監視資料庫狀態並啟用遺失 TDE 保護裝置存取的警示，請設定下列 Azure 功能：

* [Azure 資源健康狀態](../service-health/resource-health-overview.md)-拒絕存取客戶金鑰的資料庫，在第一次連接到資料庫之後，將會顯示為「無法存取」。
* [活動記錄](../service-health/alerts-activity-log-service-notifications.md)-當客戶管理的 Key Vault 中的客戶金鑰存取失敗時，會將專案新增至活動記錄。 建立這些事件的警示可讓您儘快恢復存取。

* 您可以定義[動作群組](../azure-monitor/platform/action-groups.md)，以根據您的喜好設定傳送通知和警示，例如電子郵件/SMS/推播/語音、邏輯應用程式、WEBHOOK、ITSM 或自動化 Runbook。

## <a name="restore-and-replica-with-customers-managed-key-in-the-key-vault"></a>在 Key Vault 中使用客戶的受控金鑰還原和複製複本

適用於 MySQL 的 Azure 資料庫使用儲存在 Key Vault 中的客戶管理金鑰加密之後，任何新建立的伺服器複本（即使是本機或異地還原作業，或透過讀取複本）也會使用相同客戶的受控金鑰進行加密。 不過，它們可以變更以反映新客戶的加密管理金鑰。 當客戶管理的金鑰變更時，舊的伺服器備份將會開始使用最新的金鑰。

為了避免在還原或讀取複本期間建立客戶管理的資料加密時遇到問題，請務必在主要和還原/複本伺服器上執行下列步驟：

* 從主要適用於 MySQL 的 Azure 資料庫起始還原或讀取複本建立程式。
* 新建立的伺服器（還原/複本）會保持無法存取的狀態，因為它的唯一身分識別尚未獲得 Azure Key Vault 的許可權（AKV）
* 在還原/複本伺服器上，重新驗證資料加密設定中客戶管理的金鑰，以確保新建立的伺服器會獲得儲存在 AKV 中之金鑰的包裝/解除封裝許可權。

* 上述兩個步驟都必須完成，以確保資料加密會保留在 master 上，以及還原/複本伺服器上。

## <a name="next-steps"></a>後續步驟

瞭解如何[使用 Azure 入口網站，為適用于 MySQL 的 Azure 資料庫設定以客戶管理的金鑰進行資料加密](howto-data-encryption-portal.md)。
