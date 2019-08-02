---
title: Azure 澳大利亞的 Azure Key Vault
description: 針對澳大利亞地區內的金鑰管理設定及使用 Azure Key Vault 的指引, 以符合澳大利亞政府原則、規定和法規的特定需求。
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 263765c777f994134befc52f0c63c7f18e590b39
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602123"
---
# <a name="azure-key-vault-in-azure-australia"></a>Azure 澳大利亞的 Azure Key Vault

密碼編譯金鑰生命週期的安全存放和管理是密碼編譯系統中的重要元素。  在 Azure 中提供這項功能的服務就是 Azure Key Vault。 Key Vault 已 IRAP 存取安全性, 並 ACSC 認證以進行保護。  本文概述使用 Key Vault 符合澳大利亞信號情報局 (.ASD)[資訊安全手動控制項](https://acsc.gov.au/infosec/ism/)(ISM) 時的主要考慮。

Azure Key Vault 是一項雲端服務, 可保護加密金鑰和秘密。 由於這項資料是機密和商務關鍵, Key Vault 可讓您安全存取金鑰保存庫, 只允許授權的使用者和應用程式。 有三個主要成品受到 Key Vault 的管理和控制:

- 金鑰
- 祕密
- 憑證

本文將著重于使用 Key Vault 來管理金鑰。

![Azure Key Vault](media/azure-key-vault-overview.png)

*圖1– Azure Key Vault*

## <a name="key-design-considerations"></a>主要設計考慮

### <a name="deployment-options"></a>部署選項

有兩個選項可以建立 Azure 金鑰保存庫。 這兩個選項都使用 Thales nShield 系列的硬體安全性模組 (HSM), 並已驗證聯邦資訊處理標準 (FIPS), 並已核准將金鑰儲存在受保護的環境中。 可用選項包括：

- **受軟體保護的保存庫:** 已驗證 FIPS 140-2 層級1。 金鑰儲存在 HSM 上。 加密和解密作業是在 Azure 上的計算資源中執行。
- **受 HSM 保護的保存庫:** 已驗證 FIPS 140-2 層級2。 金鑰儲存在 HSM 上。 加密和解密作業會在 HSM 上執行。

Key Vault 支援 Rivest-Shamir-Adleman (RSA) 和橢圓曲線密碼編譯 (ECC) 金鑰。 預設值為 RSA 2048 位金鑰, 但有 RSA 3072-bit、RSA 4096-bit 和 ECC 金鑰的 advanced 選項。  所有索引鍵都符合 ISM 控制項, 但慣用的是橢圓曲線索引鍵。

### <a name="resource-operations"></a>資源作業

Azure Key Vault 涉及數個角色:

- **Key Vault 系統管理員:** 管理保存庫的生命週期
- **金鑰管理員:** 管理保存庫中金鑰的生命週期
- **開發人員/操作員:** 將金鑰從保存庫整合至應用程式和服務
- **稽核員**監視金鑰使用方式和存取
- **應用程式︰** 使用金鑰來保護資訊

Azure Key Vault 使用兩個不同的介面來保護:

- **管理平面:** 此平面負責管理保存庫, 並由 RBAC 保護。
- **資料平面:** 此平面負責管理和存取保存庫中的構件。  使用 Key Vault 存取原則來保護。

如 ISM 所需, 在呼叫者 (使用者或應用程式) 之前, 必須先有適當的驗證和 authorisation, 才可透過任一平面存取金鑰保存庫。

Azure RBAC 有一個內建角色, 可供 Key Vault ( [Key Vault 參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#key-vault-contributor)) 控制金鑰保存庫的管理。 建議建立與更細微角色對應的自訂角色, 以管理您的金鑰保存庫。

>[!WARNING]
>透過 Key Vault 存取原則啟用金鑰存取時, 使用者或應用程式會擁有金鑰保存庫中所有金鑰的存取權 (例如, 如果使用者具有「刪除」存取權, 他們就可以刪除所有金鑰)。  因此, 應部署多個金鑰保存庫, 以配合安全性網域/界限。

### <a name="networking"></a>網路功能

您可以設定 Key Vault 防火牆和虛擬網路, 以控制資料平面的存取權。  您可以允許存取指定網路上的使用者或應用程式, 同時拒絕存取所有其他網路上的使用者或應用程式。 如果已啟用 [允許信任的服務],[受信任的服務](https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints#trusted-services)就是此控制項的例外狀況。  虛擬網路控制不適用於管理平面。

金鑰保存庫的存取權應該明確限制為有使用者或應用程式需要存取金鑰的最小網路集合。

### <a name="bring-your-own-key-byok"></a>攜帶您自己的金鑰 (BYOK)

Key Vault 支援 BYOK。  BYOK 可讓使用者從現有的金鑰基礎結構匯入金鑰。  Thales 提供的[澳大利亞工具](https://www.microsoft.com/download/details.aspx?id=45345)組, 可支援從外部 HSM (例如, 以離線工作站產生的金鑰) 將金鑰安全傳輸和匯入至 Key Vault。

### <a name="key-vault-auditing-and-logging"></a>Key Vault 的審核和記錄

ACSC 需要有英的實體, 才能使用適當的 Azure 服務來對其 Azure 工作負載進行即時監視和報告。

藉由啟用索引鍵值上的 **_"AuditEvent"_** 診斷設定來啟用記錄。  Audit 事件會記錄到指定的儲存體帳戶。  **_"Retentionindays 設"_** 期間應根據資料保留原則進行設定。  系統會針對管理平面和資料平面上的[作業](https://docs.microsoft.com/azure/key-vault/key-vault-logging#interpret)進行審核和記錄。 [Azure 監視器中的 Azure Key Vault 解決方案](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault)可以用來審查 Key Vault AuditEvent 記錄。  有一些其他 Azure 服務可用來處理和散發 Key Vault AuditEvents。

### <a name="key-rotation"></a>金鑰輪替

將金鑰儲存在 Key Vault 提供單一點來維護應用程式以外的金鑰, 讓金鑰得以更新, 而不會影響應用程式的行為。 將金鑰儲存在 Azure Key Vault 可提供各種支援金鑰輪替的策略:

- 手動
- 以程式設計方式透過 Api
- 自動化腳本 (例如, 使用 PowerShell 和 Azure 自動化)

這些選項可讓您定期輪替金鑰, 以滿足合規性需求, 或在發生金鑰可能遭到入侵的問題時, 以特定方式進行。

#### <a name="key-rotation-strategies"></a>金鑰輪替策略

請務必為儲存在 KeyVault 中的金鑰開發適當的金鑰輪替策略。  使用錯誤的金鑰會導致資訊不正確地解密, 而遺失金鑰可能會導致資訊的存取權完全遺失。  不同案例的金鑰輪替策略範例包括:

- **傳遞資料:** 變動性資訊會在2方之間傳輸。  旋轉金鑰後, 雙方都必須具有可從金鑰保存庫同步抓取更新金鑰的機制。
- **作為 rest 的資料:** 一方會儲存加密的資料, 並在未來將其解密。  當金鑰要旋轉時, 必須使用舊的金鑰將資料解密, 然後使用新的輪替金鑰進行加密。  有一些方法可以使用金鑰加密金鑰, 將解密/加密程式的影響降至最低 (請參閱範例)。  Microsoft 會管理與 Azure 儲存體的金鑰輪替相關的大部分程式 (請參閱 ...)
- **存取金鑰:** 有一些 Azure 服務具有可儲存在 Key Vault 中的存取金鑰 (例如 CosmosDB)。  Azure 服務具有主要和次要存取金鑰。  請務必不要同時旋轉這兩個金鑰。  因此, 您應該在一段時間之後旋轉一個金鑰, 而且金鑰作業經過驗證後, 第二個按鍵就可以旋轉。

### <a name="high-availability"></a>高可用性

ISM 有數個與商務持續性相關的控制項。
Azure Key Vault 具有多層的冗余, 其中的內容會在區域內複寫, 以及複製到次要、[配對的區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。

當金鑰保存庫處於容錯回復狀態時, 它會處於唯讀模式, 並會回到讀寫模式, 主要服務會還原。

ISM 有數個與備份相關的控制項。  請務必為保存庫和其金鑰開發及執行適當的備份/還原計劃。

## <a name="key-lifecycle"></a>金鑰生命週期

### <a name="key-operations"></a>金鑰作業

Key Vault 支援金鑰的下列作業:

- **建立**可讓用戶端在 Key Vault 中建立金鑰。 金鑰值會由 Key Vault 產生並儲存，但不會發行給用戶端。 在 Key Vault 中可建立非對稱金鑰。
- **導**可讓用戶端將現有金鑰匯入至 Key Vault。 非對稱金鑰可使用 JWK 概念中的多種不同封裝方法來匯入至 Key Vault。
- **更新**可讓具有足夠權限的用戶端，修改與先前儲存在 Key Vault 內之金鑰相關的中繼資料 (金鑰屬性)。
- **delete**可讓具有足夠權限的用戶端從 Key Vault 中刪除金鑰。
- **名單**可讓用戶端列出所指定 Key Vault 中的所有金鑰。
- **列出版本:** 可讓用戶端列出指定的 Key Vault 中，所指定金鑰的所有版本。
- **獲取：** 可讓用戶端擷取 Key Vault 中所指定金鑰的公開部分。
- **備份**以受保護的形式匯出金鑰。
- **還原**匯入先前備份的金鑰。

有一組對應的許可權可授與使用者、服務主體, 或使用 Key Vault 存取控制專案的應用程式, 讓他們能夠執行金鑰作業。

Key Vault 具有虛刪除功能, 可讓您復原已刪除的保存庫和金鑰。 根據預設, 「虛 **_刪除_** 」未啟用, 但啟用後, 物件會保留90天 (保留期限), 而會顯示為已刪除。  如果停用 [ **_清除保護]_** 選項, 則會有額外的許可權「 **_清除_** 」, 允許永久刪除金鑰。

建立或匯入現有的金鑰會建立新版本的金鑰。

### <a name="cryptographic-operations"></a>密碼編譯作業

Key Vault 也支援使用金鑰的密碼編譯作業:

- **sign 和 verify:** 此作業是「簽署雜湊」或「驗證雜湊」。 在建立簽章時, Key Vault 不支援將內容雜湊處理。
- **金鑰加密/包裝:** 此作業是用來保護另一個金鑰。
- **加密和解密:** 預存金鑰用來加密或解密單一資料區塊

有一組對應的許可權可授與使用者、服務主體, 或使用 Key Vault 存取控制專案的應用程式, 讓他們能夠執行密碼編譯作業。

有三個主要屬性可設定來控制是否啟用金鑰, 以及密碼編譯作業是否可用:

- **enabled**
- **nbf:** 在指定的日期之前未啟用
- **exp:** 到期日

## <a name="storage-and-keys"></a>儲存體和金鑰

客戶管理的金鑰具有更大的彈性, 並可讓您對金鑰的評估和管理進行控制。 它們也可以讓您審核用來保護資料的加密金鑰。
Key Vault 中儲存的儲存體和金鑰有三個層面:

- Key Vault 受控儲存體帳戶金鑰
- 待用資料的 Azure 儲存體服務加密 (SSE)
- 受控磁片和 Azure 磁碟加密

Key Vault 的 Azure 儲存體帳戶金鑰管理是 Key Vault 的金鑰服務的延伸模組, 可支援儲存體帳戶金鑰的同步處理和重新產生 (輪替)。  [Azure 儲存體與 Azure Active Directory 整合](https://docs.microsoft.com/azure/storage/common/storage-auth-aad)(預覽) 建議在發行時使用, 因為它可提供更優異的安全性和易用性。
SSE 使用兩個金鑰來管理待用資料的加密:

- 金鑰加密金鑰 (KEK)
- 資料加密金鑰 (DEK)

雖然 Microsoft 會管理 Dek, 但是 SSE 可以選擇使用可儲存在 Key Vault 中客戶管理的 Kek。 這可根據適當的相容性原則, 在 Azure Key Vault 中輪替金鑰。 輪替金鑰時, Azure 儲存體重新加密該儲存體帳戶的帳戶加密金鑰。 這不會導致重新加密所有資料, 也不需要進行其他動作。

SSE 用於受控磁片, 但不支援客戶管理的金鑰。  受控磁片的加密可以在 Key Vault 中使用 Azure 磁碟加密搭配客戶管理的 KEK 金鑰來完成。

## <a name="next-steps"></a>後續步驟

查看關於身分[識別同盟](identity-federation.md)的文章

請參閱[參考程式庫](reference-library.md)中的其他 Azure Key Vault 檔和教學課程
