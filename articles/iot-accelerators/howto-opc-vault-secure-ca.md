---
title: 如何安全地執行 OPC 保存庫憑證管理服務-Azure |Microsoft Docs
description: 說明如何在 Azure 中安全地執行 OPC 保存庫憑證管理服務, 以及要考慮的其他安全性指導方針。
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: f35836f60fae11c0955c128e96a4cea188681942
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997669"
---
# <a name="how-to-run-the-opc-vault-certificate-management-service-securely"></a>如何安全地執行 OPC 保存庫憑證管理服務

本文說明如何在 Azure 中安全地執行 OPC 保存庫憑證管理服務, 以及要考慮的其他安全性指導方針。

## <a name="roles"></a>角色

### <a name="trusted-and-authorized-roles"></a>信任和授權的角色

OPC 保存庫微服務已設定為允許不同的角色存取服務的各個部分。

> [!IMPORTANT]
> 在部署期間, 腳本只會將執行部署腳本的使用者新增為所有角色的使用者。
> 應針對生產部署審查此角色指派, 並遵循下列指導方針來適當地重新設定。
> 這項工作需要在 Azure AD 企業應用程式入口網站中手動指派角色和服務。

### <a name="certificate-management-service-roles"></a>憑證管理服務角色

OPC 保存庫微服務會定義下列角色:

- **讀者**：根據預設, 租使用者中所有已驗證的使用者都具有「讀取」存取權。 
  - 應用程式和憑證要求的讀取存取權。 可以列出及查詢應用程式和憑證要求。 此外, 也可以使用讀取權限來存取裝置探索資訊和公用憑證。
- **寫入器**:寫入者角色會指派給使用者, 以新增特定工作的寫入權限。 
  - 應用程式和憑證要求的讀取/寫入存取權。 可以註冊、更新及取消註冊應用程式。 可以建立憑證要求, 並取得核准的私密金鑰和憑證。 也可以刪除私密金鑰。
- **核准者**:核准者角色會指派給使用者, 以核准或拒絕憑證要求。 角色不包含任何其他角色。
  - 除了用來存取 OPC 保存庫微服務 Api 的核准者角色之外, 使用者也必須具有 Key Vault 中的金鑰簽署許可權, 才能簽署憑證。
  - [寫入器] 和 [核准者] 角色應指派給不同的使用者。
  - 核准者的主要角色是核准憑證要求的產生和拒絕。
- **系統管理員**：系統管理員角色會指派給使用者, 以管理憑證群組。 角色不支援核准者角色, 但包含寫入器角色。
  - 系統管理員可以管理憑證群組、變更設定, 以及藉由發出新的 CRL 來撤銷應用程式憑證。
  - 在理想情況下, 寫入器、核准者和系統管理員角色會指派給不同的使用者。 為了增加安全性, 擁有核准者或系統管理員角色的使用者也需要 KeyVault 中的金鑰簽署許可權, 以頒發證書或更新簽發者 CA 憑證。
  - 除了微服務系統管理角色之外, 此角色還包含 (但不限於):
    - 負責管理 CA 安全性實務的實施。
    - 管理憑證的產生、撤銷和暫停。 
    - 密碼編譯金鑰生命週期管理 (例如, 更新簽發者 CA 金鑰)。
    - 安裝、設定及維護操作 CA 的服務。
    - 服務的日常作業。 
    - CA 和資料庫備份與復原。

### <a name="other-role-assignments"></a>其他角色指派

執行服務時, 也應該考慮並指派下列角色:

- 具有外部根憑證授權單位之憑證採購合約的商務擁有者 (如果擁有者是從外部 CA 購買憑證, 或操作屬於外部 CA 的 CA)。
- 憑證授權單位單位的開發和驗證。
- 審查記錄。
- 協助支援 CA 或管理實體與雲端設施, 但不直接受信任來執行 CA 作業的人員, 會定義為獲得授權的角色。 允許執行授權角色的一組工作人員也必須記載。

### <a name="memberships-of-trusted-and-authorized-roles-must-be-reviewed-annually"></a>每年都必須檢查信任和授權角色的成員資格

信任和授權角色的成員資格必須至少每季檢查一次, 以確保每個角色中的一組人員 (用於手動程式) 或服務身分識別 (用於自動化程式) 都會保持最低。

### <a name="certificate-issuance-process-must-enforce-role-separation-between-certificate-requester-and-approver"></a>憑證發佈程式必須在憑證要求者與核准者之間強制執行角色分離

憑證發佈程式必須在憑證要求者和憑證核准者角色 (人員或自動化系統) 之間強制執行角色分隔。 憑證發行必須由憑證核准者角色授權, 確認憑證要求者已獲授權可取得憑證。 持有憑證核准者角色的人員必須是正式授權的人員。

### <a name="privileged-role-management-must-restrict-access-and-be-reviewed-quarterly"></a>特殊許可權角色管理必須限制存取權, 並每季檢查一次

特殊許可權角色的指派 (例如, 授權系統管理員和核准者群組的成員資格) 必須限制為一組有限的授權人員。 任何特殊許可權角色變更都必須在24小時內撤銷存取權。 最後, 必須每季檢查特殊許可權角色指派, 而且必須移除任何不必要或過期的指派。

### <a name="privileged-roles-should-use-two-factor-authentication"></a>特殊許可權角色應該使用雙重要素驗證

多重要素驗證 (雙因素驗證、MFA 或 TFA) 必須用於對服務的核准者和系統管理員進行互動式登入。

## <a name="certificate-service-operation-guidelines"></a>憑證服務操作指導方針

### <a name="operational-contacts"></a>營運連絡人

憑證服務必須在檔案上具有最新的安全性回應計畫, 其中包含詳細的操作事件回應連絡人。

### <a name="security-updates"></a>安全性更新

所有系統都必須以最新的安全性更新/修補程式合規性持續監視和更新。

> [!IMPORTANT]
> OPC 保存庫服務的 GitHub 儲存機制會持續更新安全性修補程式。 應監視 GitHub 上的更新, 並定期將更新套用至服務。

### <a name="security-monitoring"></a>安全性監控

訂閱或執行適當的安全性監視 (例如, 藉由訂閱中央監視解決方案 (例如, Azure 資訊安全中心、O365 監視解決方案) 並適當設定, 以確保安全性事件會傳送至監視解決方案。

> [!IMPORTANT]
> 根據預設, OPC 保存庫服務會以[Azure 應用程式 Insights](https://docs.microsoft.com/azure/azure-monitor/app/devops)作為監視解決方案進行部署。 強烈建議您新增[Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)之類的安全性解決方案。

### <a name="assess-security-of-open-source-software-components"></a>評估開放原始碼軟體元件的安全性

在產品或服務內使用的所有開放原始碼元件, 都必須擁有中等或更高的安全性弱點。

> [!IMPORTANT]
> OPC 保存庫服務的 GitHub 儲存機制會在持續整合組建期間掃描所有元件, 以瞭解弱點。 應監視 GitHub 上的更新, 並定期將更新套用至服務。

### <a name="maintain-an-inventory"></a>維護清查

所有生產主機 (包括持續性虛擬機器)、裝置、所有內部 IP 位址範圍、Vip 和公用 DNS 功能變數名稱都必須維護資產清查。 您必須在30天內新增或移除系統、裝置 IP 位址、VIP 或公用 DNS 網域, 才能更新此清查。

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>預設 Azure OPC 保存庫微服務生產環境部署的清查: 

在**Azure**中:
- **App Service 方案**：服務主機的 App service 方案。 預設值為 S1。
- 適用于微服務的**App Service** :OPC 保存庫服務主機。
- 範例應用程式的**App Service** :OPC 保存庫範例應用程式主機。
- **KeyVault 標準**:以儲存 web 服務的秘密和 Cosmos DB 金鑰。
- **KeyVault Premium**:裝載簽發者 CA 金鑰 (適用于簽署服務), 適用于保存庫設定和應用程式私密金鑰的儲存。
- **Cosmos DB**：應用程式和憑證要求的資料庫。 
- **Application Insights**: (選擇性) 適用于 web 服務和應用程式的監視解決方案。
- **AzureAD 應用程式註冊**:範例應用程式、服務和 edge 模組的註冊。

針對雲端服務, 您應該記錄所有主機名稱、資源群組、資源名稱、訂用帳戶識別碼、用來部署服務的 TenantId。 

在**IoT Edge**或本機**IoT Edge 伺服器**中:
- **OPC 保存庫 IoT Edge 模組**:支援 factory 網路 OPC UA 全域探索伺服器。 

針對 IoT Edge 裝置, 必須記載主機名稱和 IP 位址。 

### <a name="document-the-certification-authorities-cas"></a>記錄憑證授權單位單位 (Ca)

CA 階層檔必須包含所有操作中的 Ca, 包括所有相關的附屬 Ca、父 Ca 和根 Ca, 即使它們不是由服務所管理。 可能會提供一組完整的未過期 CA 憑證, 而不是正式檔。

> [!NOTE]
> OPC 保存庫範例應用程式支援下載服務中所使用和產生的所有憑證, 以提供檔。

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>記錄所有憑證授權單位單位 (Ca) 發行的憑證

在過去12個月內發出的一組完整憑證, 應提供給檔。

> [!NOTE]
> OPC 保存庫範例應用程式支援下載服務中所使用和產生的所有憑證, 以提供檔。

### <a name="document-the-sop-for-securely-deleting-cryptographic-keys"></a>記錄安全刪除密碼編譯金鑰的程式

金鑰刪除可能只會在 CA 存留期間發生, 這就是為什麼沒有任何使用者已指派 KeyVault 憑證刪除許可權, 以及為什麼未公開任何 Api 來刪除簽發者 CA 憑證。 安全地刪除憑證授權單位單位密碼編譯金鑰的手動標準操作程式, 僅適用于直接存取 Azure 入口網站中的 KeyVault, 以及刪除 KeyVault 中的憑證群組。 若要確保立即刪除 KeyVault, 應停用虛[刪除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)。

## <a name="certificates"></a>憑證

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>憑證必須符合最低憑證設定檔

OPC 保存庫服務是線上憑證授權單位單位 (Ca), 會向訂閱者發出終端實體憑證。
OPC 保存庫微服務會遵循預設執行中的這些指導方針。

- 所有憑證都必須包含下列 x.509 欄位, 如下所示:
  - [版本] 欄位的內容必須是 v3。 
  - SerialNumber 欄位的內容必須包含至少8個位元組的熵, 從 FIPS (聯邦資訊處理標準) 140 核准的亂數產生器取得。<br>
    > [!IMPORTANT]
    > OPC 保存庫序號預設為20個位元組, 並從 OS 密碼編譯亂數字產生器取得。 亂數字產生器是在 Windows 裝置上核准的 FIPS 140, 但不是在 Linux 上。 選擇使用 Linux Vm 或 Linux docker 容器的服務部署時, 必須考慮此事實, 基礎技術 OpenSSL 則不會核准 FIPS 140。
  - IssuerUniqueID 和 subjectUniqueID 欄位不得存在。
  - 您必須根據 IETF RFC 5280, 使用基本限制延伸來識別終端實體憑證。
  - 發行 CA 憑證的 pathLenConstraint 欄位必須設定為0。 
  - 擴充金鑰使用方式延伸必須存在, 且包含最小的擴充金鑰使用方法物件識別碼 (Oid) 集合。 不得指定 anyExtendedKeyUsage OID (2.5.29.37.0)。 
  - CRL 發佈點 (CDP) 延伸模組必須存在於簽發者 CA 憑證中。<br>
    > [!IMPORTANT]
    > 「CRL 發佈點 (CDP)」延伸模組存在於 OPC 保存庫 CA 憑證中, 不過 OPC UA 裝置會使用自訂方法來散發 Crl。
  - 授權單位資訊存取延伸模組必須存在於訂閱者憑證中。<br>
    > [!IMPORTANT]
    > 許可權資訊存取延伸模組存在於 OPC 保存庫訂閱者憑證中, 不過 OPC UA 裝置會使用自訂方法來散發簽發者 CA 資訊。
- 必須使用已核准的非對稱演算法、金鑰長度、雜湊函數和填補模式。
  - **RSA**和**sha-1**是唯一支援的演算法 (*)。
  - RSA 可用於加密、金鑰交換和簽章。
  - RSA 加密必須僅使用 OAEP、RSA-KEM 或 RSA-PSS 填補模式。
  - 需要金鑰長度 > = 2048 位。
  - 使用 SHA-2 系列雜湊演算法 (SHA256、SHA384 和 SHA512)。
  - 具有一般存留期 > = 20 年的 RSA 根 CA 金鑰必須是4096位或更高。
  - RSA 簽發者 CA 金鑰必須至少為2048位;如果 CA 憑證到期日在2030之後, CA 金鑰必須是4096位或更大。
- 憑證存留期
  - 根 CA 憑證:根 Ca 的憑證有效期間上限不得超過25年。
  - 子 CA 或線上簽發者 CA 憑證:已上線且只發出訂閱者憑證之 Ca 的憑證有效期間上限不得超過六年。 對於這些 Ca, 相關的私用簽章金鑰不得超過三年的時間來發行新的憑證。<br>
    > [!IMPORTANT]
    > 在預設的 OPC 保存庫微服務中產生的簽發者憑證 (沒有外部根 CA) 會視為具有個別需求和存留期的線上子 CA。 預設存留期設定為5年, 金鑰長度為 > = 2048。
  - 所有非對稱金鑰都必須有最多五年的存留期, 建議使用一年的存留期。<br>
    > [!IMPORTANT]
    > 根據預設, 使用 OPC 保存庫發行之應用程式憑證的存留期為兩年, 每年應更換一次。 
  - 每當更新憑證時, 就會以新的金鑰更新。
- 應用程式實例憑證中的 OPC UA 特定延伸模組
  - SubjectAltName 擴充功能包括應用程式 Uri 和主機名稱, 其中也可能包含 FQDN、IPv4 和 IPv6 位址。
  - KeyUsage 包括 digitalSignature、不可否認性、keyEncipherment 和 dataEncipherment。
  - ExtendedKeyUsage 包含 serverAuth 和/或 clientAuth。
  - AuthorityKeyIdentifier 是在已簽署的憑證中指定。

### <a name="certificate-authority-ca-keys-and-certificates-must-meet-minimum-requirements"></a>憑證授權單位單位 (CA) 金鑰和憑證必須符合最低需求

- **私密金鑰**:**RSA**金鑰必須至少有2048位;如果 CA 憑證到期日在2030之後, CA 金鑰必須是4096位或更大。
- **存留期**:已上線且只發出訂閱者憑證之 Ca 的憑證有效期間上限不得超過六年。 對於這些 Ca, 相關的私用簽章金鑰不得超過三年的時間來發行新的憑證。

### <a name="ca-keys-are-protected-using-hardware-security-modules-hsm"></a>使用硬體安全模組 (HSM) 來保護 CA 金鑰

- OpcVault 會使用 Azure KeyVault Premium, 而金鑰會受到 FIPS 140-2 Level 2 硬體安全模組 (HSM) 的保護。 

Key Vault 使用的密碼編譯模組 (HSM 或軟體) 皆經過 FIPS (聯邦資訊處理標準) 驗證。<br>
建立或匯入為受 HSM 保護的金鑰會在 HSM 內進行處理, 並驗證為 FIPS 140-2 層級2。<br>
建立或匯入為受軟體保護的金鑰, 會在驗證為 FIPS 140-2 層級1的密碼編譯模組內進行處理。

## <a name="operational-practices"></a>操作實務

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>記錄及維護憑證註冊的標準操作 PKI 實務

記載及維護 Ca 如何頒發證書的標準操作程式 (Sop), 包括: 
- 如何識別和驗證訂閱者 
- 憑證要求的處理和驗證方式 (如果適用的話, 也包括如何處理憑證更新和重設金鑰要求) 
- 發行的憑證如何散發給訂閱者 

「OPC 保存庫微服務處理時間」會在[總覽](overview-opc-vault-architecture.md)和[如何管理](howto-opc-vault-manage.md)檔中說明。 做法遵循 OPC 統一架構規格第12部分:探索和全域服務。


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>記載及維護憑證撤銷的標準操作 PKI 作法

憑證撤銷程式會在[總覽](overview-opc-vault-architecture.md)和[如何管理](howto-opc-vault-manage.md)檔中說明。
    
### <a name="document-certification-authority-ca-key-generation-ceremony"></a>檔憑證授權單位單位 (CA) 金鑰產生儀式 

因為 Azure KeyVault 中的安全儲存體, 以及[如何管理](howto-opc-vault-manage.md)檔中所述, 所以 OPC 保存庫微服務中的簽發者 CA 金鑰產生已簡化。

不過, 當使用外部根憑證授權單位時, 「憑證授權單位單位」 (CA) 金鑰產生儀式必須符合下列需求:

CA 金鑰產生儀式必須針對已記載的腳本執行, 其中至少包含下列專案: 
1. 角色和參與者責任的定義
2. 進行 CA 金鑰產生儀式的核准
3. 儀式所需的密碼編譯硬體和啟用材質
4. 硬體準備 (包括資產/設定資訊更新和登出)
5. 作業系統安裝
6. CA 金鑰產生儀式期間所執行的特定步驟, 例如: 
7. CA 應用程式安裝和設定
8. CA 金鑰產生
9. CA 金鑰備份
10. CA 憑證簽署
9. 在服務的受保護 HSM 中匯入帶正負號的金鑰。
11. CA 系統關機
12. 準備儲存資料


## <a name="next-steps"></a>後續步驟

既然您已瞭解如何安全地管理 OPC 保存庫, 以下是建議的後續步驟:

> [!div class="nextstepaction"]
> [使用 OPC 保存庫保護 OPC UA 裝置](howto-opc-vault-secure.md)