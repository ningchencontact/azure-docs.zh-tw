---
title: 保護及管理已移轉到 Azure 之工作負載的最佳做法 | Microsoft Docs
description: 在移轉至 Azure 之後，取得操作、管理及保護已移轉之工作負載的最佳做法。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: raynew
ms.openlocfilehash: 0bd19492c844e217dc520ae0c189ca467bb7ac0a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58011094"
---
# <a name="best-practices-for-securing-and-managing-workloads-migrated-to-azure"></a>保護及管理已移轉到 Azure 之工作負載的最佳做法

在您針對移轉進行規劃及設計的同時，除了思考有關移轉本身的事項之外，您也需要考慮移轉後在 Azure 中的安全性和管理模型。 本文會描述適用於在移轉之後保護您的 Azure 部署，以及適用於使部署以最佳狀態持續執行之工作的規劃與最佳做法。 

> [!IMPORTANT]
> 本文所述的最佳做法和意見是以本文撰寫當下可用的 Azure 平台和服務功能作為基礎。 特色與功能會隨著時間改變。

## <a name="secure-migrated-workloads"></a>保護已移轉的工作負載

在移轉之後，最重要的工作便是保護已移轉的工作負載，使其不受來自內部和外部的威脅。 這些最佳做法可以協助您執行此作業：

- [運用 Azure 資訊安全中心](#best-practice-follow-azure-security-center-recommendations)：了解如何運用由 Azure 資訊安全中心所提供的監視、評量及建議
- [對您的資料進行加密](#best-practice-encrypt-data)：取得對您在 Azure 中的資料進行加密的最佳做法。
- [設定反惡意程式碼軟體](#best-practice-protect-vms-with-antimalware)：保護您的 VM 不受惡意程式碼和惡意攻擊的危害。
- [保護 Web 應用程式](#best-practice-secure-web-apps)：保護已移轉之 Web 應用程式中的機密資訊。
- [檢視訂用帳戶](#best-practice-review-subscriptions-and-resource-permissions)：確認在移轉後可以存取您 Azure 訂用帳戶和資源的人員。
- [運用記錄](#best-practice-review-audit-and-security-logs)：定期檢閱您的 Azure 稽核和安全性記錄。
- [檢閱其他安全性功能](#best-practice-evaluate-other-security-features)：了解及評估 Azure 提供的進階安全性功能。

## <a name="best-practice-follow-azure-security-center-recommendations"></a>最佳做法：遵循 Azure 資訊安全中心的建議

Microsoft 努力確保 Azure 租用戶系統管理員能擁有必要的資訊，以提供能保護工作負載不受攻擊的安全性功能。  Azure 資訊安全中心能提供統一的安全性管理。 您可以從從資訊安全中心跨工作負載套用安全性原則、限制暴露在威脅下的風險，以及偵測並回應攻擊。 資訊安全中心會跨 Azure 租用戶分析資源及設定，然後提供安全性建議，其中包括：

- **集中式原則管理** – 集中管理所有混合式雲端工作負載的安全性原則，確保符合公司或法規的安全性需求。
- **連續安全性評估** – 監視機器、網路、儲存體與資料服務以及應用程式的安全性狀態，以找出潛在的安全性問題。
- **的建議** – 運用已排定優先順序和可操作的安全性建議，在攻擊者入侵之前修正安全性弱點。
- **已排定優先順序的警示和事件** - 藉由已排定優先順序的安全性警示和事件，首先專注處理最嚴重的威脅。

除了評量和建議之外，資訊安全中心也能提供數個可針對特定資源啟用的其他安全性功能。

- **Just-In-Time (JIT) 存取**：使用 Just-In-Time 來控制針對 Azure VM 上管理連接埠的存取，以減少您網路的受攻擊面。
    - 在網際網路上開啟 VM RDP 連接埠 3389，將會使 VM 持續暴露在不良執行者活動之下。 由於 Azure IP 位址是眾所周知的，因此駭客會持續對它們進行探查，以對開啟的 3389 連接埠發動攻擊。 
    - Just-In-Time 會使用能限制指定連接埠開啟時間的網路安全性群組 (NSG) 及傳入規則。
    - 在啟用 Just-In-Time 的情況下，資訊安全中心會檢查使用者是否具有 VM 的角色型存取控制 (RBAC) 寫入存取權限。 此外，還會指定使用者如何能連線到 VM 的規則。 如果權限沒問題，系統便會核准要求，且資訊安全中心會設定 NSG 以在您指定時間內允許針對選取連接埠的傳入流量。 時間到期時，NSG 便會返回其先前的狀態。
- **自適性應用程式控制**：透過使用動態應用程式允許清單來控制有哪些應用程式可在 VM 上執行，來使 VM 不受惡意軟體和惡意程式碼的危害。
    - 自適性應用程式控制可讓您將應用程式置於允許清單中，並防止惡意使用者或系統管理員在 VM 上安裝未經核准或具檢查功能的應用程式。
    - 您可以對執行惡意應用程式的嘗試進行封鎖或警示、避免垃圾或惡意的應用程式，以及確保與您組織應用程式安全性原則之間的合規性。
- **檔案完整性監視**：確保在 VM 上執行之檔案的完整性。
    - 並非只有安裝軟體才會導致 VM 問題。  變更系統檔案也可能會造成 VM 失敗或效能降低。  檔案完整性監視會檢查系統檔案及登錄設定是否發生變更，並在項目被更新的情況下通知您。
    - 資訊安全中心會建議您應監視的檔案。


**深入了解：**

- [深入了解](https://docs.microsoft.com/azure/security-center/security-center-intro) Azure 資訊安全中心。
- [深入了解](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) Just-in-Time VM 存取。
- [了解](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)套用自適性應用程式控制。
- [開始使用](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring)檔案完整性監視。


## <a name="best-practice-encrypt-data"></a>最佳做法：加密資料 

加密是 Azure 安全性作法相當重要的一部分。 確保加密已在所有層級啟用，可協助防止未經授權的對象取得敏感性資料 (包括傳輸及待用中的資料) 的存取權。 

### <a name="encryption-for-iaas"></a>適用於 IaaS 的加密

- **VM**：針對 VM，您可以使用 Azure 磁碟加密來對 Windows 和 Linux IaaS VM 磁碟進行加密。
    - 磁碟加密會利用適用於 Windows 的 BitLocker，以及適用於 Linux 的 DM-Crypt 來為 OS 和資料磁碟提供磁碟區加密。
    - 您可以使用由 Azure 所建立的加密金鑰，或是自行提供保護於 Azure Key Vault 中的加密金鑰。 
    - 透過磁碟加密，IaaS VM 資料將會在待用 (位於磁碟上) 及 VM 開機期間被保護。 
    - Azure 資訊安全中心會在您有未加密的 VM 時向您傳送警示。
- **儲存體**：保護儲存在 Azure 儲存體中的待用資料。
    - 針對儲存在 Azure 儲存體帳戶中的資料，您可以使用由 Microsoft 產生且符合 FIPS 140-2 規範的 AES 金鑰，或是使用您自己的金鑰來進行加密。
    - 系統會針對所有新的和現有的儲存體帳戶啟用「儲存體服務加密」且無法停用。


### <a name="encryption-for-paas"></a>適用於 PaaS 的加密

和需要自行管理 VM 及基礎結構的 IaaS 不同，PaaS 模型平台及基礎結構是由提供者來管理，這讓您可以專注於核心應用程式邏輯和功能上。 由於 PaaS 服務類型眾多，因此基於安全性目的，我們會對每個服務進行個別評估。 例如，讓我們看看要如何針對 Azure SQL 資料庫啟用加密。

- **Always Encrypted**：使用 SQL Server Management Studio 中的 [Always Encrypted] 精靈來保護待用資料。
    - 您會建立 Always Encrypted 金鑰來對個別的資料行資料進行加密。
    - Always Encrypted 金鑰能以加密的形式儲存在資料庫中繼資料中，或是儲存在受信任的金鑰存放區中，例如 Azure Key Vault。
    - 若要使用此功能，很可能會需要進行應用程式變更。
- **透明資料加密 (TDE)**：透過對資料庫、相關聯的備份，以及待用的交易記錄檔進行即時加密和解密，來保護 Azure SQL 資料庫。
    - TDE 可在不對應用程式層級進行變更的前提下，允許執行加密活動。
    - TDE 可以使用由 Microsoft 提供的加密金鑰，或者您也可以使用「攜帶您自己的金鑰」支援來自行提供金鑰。


**深入了解：**
- [了解](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) IaaS VM 適用的 Azure 磁碟加密。
- [啟用](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-windows) IaaS Windows VM 適用的加密。
- [了解](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)待用資料的 Azure 儲存體服務加密。
- [閱讀](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) Always Encrypted 的概觀。
- [閱讀](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?view=sql-server-2017)適用於 Azure SQL Database 的 TDE。
- [了解](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql)搭配「攜帶您自己的金鑰」的 TDE。

## <a name="best-practice-protect-vms-with-antimalware"></a>最佳做法：搭配反惡意程式碼軟體保護 VM

特別是較舊的 Azure 已移轉 VM，可能沒有安裝適當層級的反惡意程式碼軟體。  Azure 提供免費的端點解決方案，可協助保護 VM 不受病毒、間諜軟體及其他惡意程式碼的威脅。
- 適用於 Azure 的 Microsoft Antimalware 會在已知的惡意或垃圾軟體嘗試自行安裝時產生警示。
- 它是能在無人為介入的情況下於背景中執行的單一代理程式解決方案。
- 在 Azure 資訊安全中心中，您可以輕鬆地識別出沒有執行端點保護的 VM，然後視需要安裝 Microsoft Antimalware。

![適用於 VM 的 Antimalware](./media/migrate-best-practices-security-management/antimalware.png)
*適用於 VM 的 Antimalware*

**深入了解：**

- [深入了解](https://docs.microsoft.com/azure/security/azure-security-antimalware) Microsoft Antimalware。

## <a name="best-practice-secure-web-apps"></a>最佳做法：保護 Web 應用程式

已移轉的 Web 應用程式會面臨幾個問題：

- 大部分的舊版 Web 應用程式，在設定檔中通常都會有機密資訊。  在備份應用程式，或是將應用程式程式碼簽入或簽出原始程式碼控制時，包含這類資訊的檔案便可能會造成安全性問題。
- 此外，當您移轉位於 VM 中的 Web 應用程式時，您很可能會將該電腦從內部部署的網路和受防火牆保護的環境中，移動到面向網際網路的環境。 您必須確定自己已設定能執行與內部部署保護資源相同工作的解決方案。


Azure 能提供數個解決方案：

- **Azure Key Vault**：現今的 Web 應用程式開發人員都會採取相對應的措施，以確保這些檔案中的機密資訊不會外洩。 保護資訊的其中一個方法，是將它從檔案中擷取出來，並置於 Azure Key Vault 中。
    - 您可以使用 Key Vault 來集中儲存應用程式祕密，並控制它們的散發。 它能免除在應用程式檔案中儲存安全性資訊的必要性。
    - 應用程式可在無需使用自訂程式碼的情況下，使用 URI 來安全地存取保存庫中的資訊。
    - Azure Key Vault 可讓您透過 Azure 安全性控制項來鎖定存取，以及順暢地實作「輪替金鑰」。 Microsoft 並無法查看或擷取您的資料。
- **App Service 環境**：如果您移轉的應用程式需要額外的保護，您可以考慮加入 App Service 環境和 Web 應用程式防火牆來保護應用程式資源。
    - Azure App Service 環境能提供完全隔離且專用的環境，可讓您在其中執行各種 App Service 應用程式，例如 Windows 和 Linux Web 應用程式、Docker 容器、行動應用程式及函式。
    - 它很適合具非常高的延展性、需要隔離和安全的網路存取，或是具有高記憶體使用量的應用程式使用
- **Web 應用程式防火牆**：應用程式閘道的功能，可為 Web 應用程式提供集中式的保護。
    - 它能在無需後端程式碼修改的情況下保護 Web 應用程式。
    - 它能在應用程式閘道背後同時保護多個 Web 應用程式。
    - Web 應用程式防火牆可以使用 Azure 監視器進行監視，並已整合至 Azure 資訊安全中心。



![保護 Web 應用程式](./media/migrate-best-practices-security-management/web-apps.png)
*Azure Key Vault*

**深入了解：**

- [取得](https://docs.microsoft.com/azure/key-vault/key-vault-overview) Azure Key Vault 的概觀。
- [了解](https://docs.microsoft.com/azure/application-gateway/waf-overview) Web 應用程式防火牆。
- [取得](https://docs.microsoft.com/azure/app-service/environment/intro) App Service 環境簡介。
- [了解如何](https://docs.microsoft.com/azure/key-vault/tutorial-web-application-keyvault)設定 Web 應用程式以從 Key Vault 讀取祕密。
- [了解](https://docs.microsoft.com/azure/application-gateway/waf-overview) Web 應用程式防火牆

## <a name="best-practice-review-subscriptions-and-resource-permissions"></a>最佳做法：檢閱訂用帳戶和資源權限

在您移轉工作負載並在 Azure 中執行它們的同時，具有工作負載存取權的人員也會四處移動。 您的安全性小組應該定期檢閱針對您 Azure 租用戶和資源群組的存取權。 Azure 提供數個供應項目以用來進行身分識別管理和存取控制安全性，其中包括角色型存取控制 (RBAC) 以針對存取 Azure 資源的權限進行授權。

- RBAC 會針對安全性主體指派存取權限。 安全性主體代表使用者、群組 (使用者的集合)、服務主體 (由應用程式和服務使用的身分識別)，以及受控識別 (由 Azure 自動管理的 Azure Active Directory 身分識別)。
- RBAC 可以將角色指派給安全性主體 (例如擁有者、參與者及讀者)，以及能定義角色可執行之作業的角色定義 (權限的集合)。
- RBAC 也可以設定範圍以設定角色的界線。 範圍可以設定於數個層級上，包括管理群組、訂用帳戶、資源群組，或資源
- 確定具有 Azure 存取權的系統管理員只能存取您想要允許的資源。  如果 Azure 中預先定義的角色不夠細微，您可以建立自訂角色以區分並限制存取權限。

![存取控制](./media/migrate-best-practices-security-management/subscription.png)
*存取控制 - IAM*

**深入了解：**

- [關於](https://docs.microsoft.com/azure/role-based-access-control/overview) RBAC。
- [了解](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)如何使用 RBAC 和 Azure 入口網站來管理存取權。
- [了解](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)自訂角色。

## <a name="best-practice-review-audit-and-security-logs"></a>最佳做法：檢閱稽核和安全性記錄

Azure Active Directory (AD) 提供會在 Azure 監視器中顯示的活動記錄。 記錄會擷取在 Azure 租用戶中執行的作業、其發生的時間，以及執行它們的人員。 

- 稽核記錄會顯示租用戶中的工作歷程記錄。 登入活動記錄會顯示執行該工作的人員。 
- 安全性報告的存取方式取決於您的 Azure AD 授權。 在「免費」和「基本」中，您會取得具風險之使用者和登入的清單。在「Premium 1」和「Premium 2」版本中，您會取得基礎事件資訊。
- 您可以將活動記錄路由傳送到數個端點，以進行長期保留並取得資料見解。
- 請養成經常檢閱記錄的習慣，或與您的安全性資訊與事件管理 (SIEM) 工具整合以自動檢閱異常狀況。  如果您不是使用「Premium 1」或「Premium 2」，便必須自行進行許多分析，或是使用您的 SIEM 系統。  分析包含尋找具風險的登入和事件，以及其他使用者攻擊模式。


![使用者和群組](./media/migrate-best-practices-security-management/azure-ad.png)
*Azure AD 使用者和群組*

**深入了解：**

- [了解](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor) Azure 監視器中的 Azure AD 活動記錄。
- [了解如何](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs)在 Azure AD 入口網站中對活動報告進行稽核。

## <a name="best-practice-evaluate-other-security-features"></a>最佳做法：評估其他安全性功能

Azure 提供數個能提供進階安全性選項的其他安全性功能。 這些最佳做法有一部分需要附加元件授權和進階選項。

- **實作 Azure AD 管理單位 (AU)**：使用基本的 Azure 存取控制來委派系統管理工作以支援人員，可能會是一件相當困難的事。  給予支援人員存取權以管理 Azure AD 中的所有群組，對組織的安全性而言可能不是理想的方法。  使用 AU 可讓您以和內部部署組織單位 (OU) 類似的方式，將 Azure 資源隔離在容器內。  若要使用 AU，AU 系統管理員必須擁有進階 Azure AD 授權。 [深入了解](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units)。
- **使用 Multi-Factor Authentication (MFA)**：如果您有進階 Azure AD 授權，您可以在系統管理員帳戶上啟用並強制執行 MFA。 網路釣魚是用來入侵帳戶認證的最常見方式。  當不良執行者擁有系統管理員帳戶認證之後，便沒有任何方法可以阻止他們進行會造成嚴重影響的動作 (例如刪除您所有的資源群組)。 您可以用數種方式來設定 MFA，包括電子郵件、驗證器應用程式，以及手機簡訊。 身為系統管理員，您可以選取最不具侵入性的選項。 MFA 會與威脅分析和條件式存取原則整合，以隨機要求 MFA 挑戰回應。 深入了解[安全性指引](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication-security-best-practices)，以及[如何設定](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication-security-best-practices) MFA。
- **實作條件式存取**：在大部分的小型和中型組織中，Azure 系統管理員和支援小組通常都會位於相同的地理位置中。 在此情況下，大部分的登入都會來自相同的區域。 如果這些位置的 IP 位址都相當固定，您應該不會看見系統管理員從這些區域以外的地方進行登入。 就算在遠端不良執行者成功入侵系統管理員認證的情況下，您也可以實作如搭配 MFA 之條件式存取的安全性功能，以防止從遠端位置或來自隨機 IP 位址從詐騙位置進行的登入。 [深入了解](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)條件式存取，並檢閱在 Azure AD 中進行條件式存取的[最佳做法](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices)。
- **檢閱企業應用程式權限**：隨著時間的經過，系統管理員可能會習慣直接按一下來自 Microsoft 和協力廠商的連結，而未留意到該動作會對組織帶來什麼影響。 這些連結可能會顯示能將權限指派給 Azure 應用程式的同意畫面，且可能會允許讀取 Azure AD 資料的存取權，甚至是管理整個 Azure 訂用帳戶的完整存取權。 您應該定期檢閱由您的系統管理員和使用者允許存取 Azure 資源的應用程式。 您應該確保這些應用程式只具有必要的權限。 此外，您可以在每季或每半年便傳送具有應用程式頁面連結的電子郵件給使用者，使他們能知道自己已允許哪些應用程式存取其組織資料。 [深入了解](https://docs.microsoft.com/azure/active-directory/manage-apps/application-types)應用程式類型，以及[如何控制](https://docs.microsoft.com/azure/active-directory/manage-apps/remove-user-or-group-access-portal)Azure AD 中的應用程式指派。



## <a name="managed-migrated-workloads"></a>管理已移轉的工作負載

在本節中，我們將會建議適用於 Azure 管理的一些最佳做法，包括：

- [管理資源](#best-practice-name-resource-groups)：適用於 Azure 資源群組和資源的最佳做法，包括智慧型命名、防止意外刪除、管理資源權限，以及有效的資源標記。
- [使用藍圖](#best-practice-implement-blueprints)：取得使用藍圖來建置及管理部署環境的快速概觀。
- [檢閱架構](#best-practice-review-azure-reference-architectures)：在建置移轉後部署期間檢閱範例 Azure 架構來取得了解。
- [設定管理群組](#best-practice-manage-resources-with-management-groups)：如果您有多個訂用帳戶，您可以將它們收集成管理群組，並將治理設定套用至那些群組。
- [設定存取原則](#best-practice-deploy-azure-policy)：將合規性原則套用到您的 Azure 資源。
- [實作 BCDR 策略](#best-practice-implement-a-bcdr-strategy)：規劃商務持續性和災害復原 (BCDR) 策略來保護資料、確保環境的可復原性，並使資源能在發生中斷時持續運作。
- [管理 VM](#best-practice-use-managed-disks-and-availability-sets)：將 VM 分組為可用性群組，以確保復原性和高可用性。 使用受控磁碟來輕鬆進行 VM 磁碟和儲存體管理。
- [監視資源使用量](#best-practice-monitor-resource-usage-and-performance)：針對 Azure 資源啟用診斷記錄、建置警示和劇本以主動進行疑難排解，以及使用 Azure 儀表板來取得部署健康情況和狀態的統一檢視。
- [管理支援及更新](#best-practice-manage-updates)：了解您的 Azure 支援方案及其實作方式、取得將 VM 保持於最新狀態的最佳做法，以及針對變更管理設立程序。


## <a name="best-practice-name-resource-groups"></a>最佳做法：為資源群組命名

透過確保資源群組具備有意義的名稱，讓系統管理員和支援小組能輕鬆辨識及瀏覽，將能大幅改善生產力與效率。
- 我們建議遵循 Azure 命名慣例。
- 如果您會使用 AD Connect 將內部部署 AD DS 同步至 Azure AD，請考慮使內部部署安全性群組的名稱與 Azure 中資源群組的名稱相符。

![命名](./media/migrate-best-practices-security-management/naming.png)
*資源群組命名*


**深入了解：**

- [了解](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)命名慣例

## <a name="best-practice-implement-delete-locks-for-resource-groups"></a>最佳做法：針對資源群組實作刪除鎖定

您最不想遇到的情況，便是資源群組被意外刪除而消失不見。 我們建議您實作刪除鎖定來避免發生此情況。


![刪除鎖定](./media/migrate-best-practices-security-management/locks.png)
*刪除鎖定*

**深入了解：**

- [了解](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)鎖定資源以防止非預期的變更。


## <a name="best-practice-understand-resource-access-permissions"></a>最佳做法：了解資源存取權限 

訂用帳戶擁有者會具有訂用帳戶中所有資源群組和資源的存取權。
- 請謹慎地將這個貴重的權限指派給其他人員。 了解指派這些權限類型的後果，將能大幅協助保持環境的安全及穩定性。
- 請確定您將資源置於適當的資源群組中：
    - 將具有類似生命週期的資源配對在一起。 在理想情況下，您應該不需要在刪除整個資源群組時移動某個資源。
    - 支援某個功能或工作負載的資源應該被放置在一起，以簡化管理工作。

**深入了解：**

- [了解](https://azure.microsoft.com/blog/organizing-subscriptions-and-resource-groups-within-the-enterprise/) \(英文\) 管理訂用帳戶和資源群組。

## <a name="best-practice-tag-resources-effectively"></a>最佳做法：有效地標記資源

僅使用與資源相關的資源群組名稱，通常沒辦法提供足夠的中繼資料來有效實作各種機制，例如訂用帳戶之內的內部帳單或管理。
- 作為最佳做法，您應該使用 Azure 標記來新增可查詢及報告的有用中繼資料。 
- 標記能提供搭配您所定義的屬性，以邏輯方式組織資源的方式。  標記可以直接套用到資源群組或資源。
- 標記可以套用到資源群組或是個別的資源之上。 資源群組標記不會被群組中的資源所繼承。
- 您可以使用 PowerShell 或 Azure 自動化來將標記自動化，您也可以標記個別的群組和資源。 -標記方法或自助服務的方式。  如果您具有要求或變更管理系統，則可以輕鬆地在要求中運用該資訊，以填入公司特定的資源標記。


![標記](./media/migrate-best-practices-security-management/tagging.png)
*標記*

**深入了解：**

- [了解](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)標記和標記的限制。
- [檢閱](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#powershell) PowerShell 和 CLI 範例來設定標記，然後將標記從資源群組套用到其資源。
- [閱讀](https://www.azurefieldnotes.com/2016/07/18/azure-resource-tagging-best-practices/) \(英文\) Azure 標記最佳做法。


## <a name="best-practice-implement-blueprints"></a>最佳做法：實作藍圖

正如藍圖可讓工程師或架構設計人員勾勒出專案的設計參數一樣，Azure 藍圖可讓雲端架構人員和中央 IT 人員定義一組可重複使用的 Azure 資源，其中實作並遵循組織的標準、模式和需求。 透過 Azure 藍圖，開發小組可快速地建置及建立符合組織合規性要求，且包含一組內建元件 (例如網路) 的新環境，以加速開發和交貨。

- 使用藍圖來協調資源群組、Azure Resource Manager 範本，以及原則和角色指派的部署。
- Azure 藍圖會儲存在全域散佈的 Azure Cosmos DB 中。 藍圖物件會複寫至多個 Azure 區域。 複寫可針對藍圖提供低延遲、高可用性且一致的存取，無論藍圖部署資源的區域為何。

**深入了解：**

- [閱讀](https://docs.microsoft.com/azure/governance/blueprints/overview)藍圖的相關資訊。
- [檢閱](https://azure.microsoft.com/blog/customizing-azure-blueprints-to-accelerate-ai-in-healthcare/) \(英文\) 用來加速醫療保健中 AI 的藍圖範例。

## <a name="best-practice-review-azure-reference-architectures"></a>最佳做法：檢閱 Azure 參考架構

在 Azure 中建置安全、可擴充且可管理的工作負載，可能會是一件令人望之卻步的工作。  由於變更會持續發生，因此在顧及各種不同功能之下維持最佳化的環境，可能會相當困難。 在設計及移轉工作負載時，擁有可從中學習的參考可能會很有幫助。  Azure 和 Azure 合作夥伴已針對各種環境類型建置數個範例參考架構。 這些範例是設計來提供各種想法，以供您從中學習並將它作為建置範本。 

參考架構會依案例編排。 它們包含針對管理、可用性、可延展性及安全性上的最佳做法及建議。
Azure App Service 環境能提供完全隔離且專用的環境，可讓您在其中執行各種 App Service 應用程式，包括 Windows 和 Linux Web 應用程式、Docker 容器、行動應用程式及函式。 App Service 能將 Azure 的功能新增到您的應用程式，其中包括安全性、負載平衡、自動調整和自動化管理。 您也可以利用它的 DevOps 功能，例如來自 Azure DevOps 和 GitHub 的持續部署、套件管理、預備環境、自訂網域和 SSL 憑證。 App Service 很適合需要隔離且安全網路存取的應用程式，以及那些會使用大量記憶體和其他需調整之資源的應用程式。
**深入了解：**

- [了解](https://docs.microsoft.com/azure/architecture/reference-architectures/) Azure 參考架構。
- [檢閱](https://docs.microsoft.com/azure/architecture/example-scenario/) Azure 範例案例。

## <a name="best-practice-manage-resources-with-management-groups"></a>最佳做法：利用管理群組組織資源

如果您的組織具有多個訂用帳戶，您便需要為它們管理存取、原則及合規性。 Azure 管理群組可以在訂用帳戶之上提供範圍層級。

- 您會將訂用帳戶整理到稱為管理群組的容器中，並將治理條件套用至它們。
- 管理群組中的所有訂用帳戶都會自動繼承管理群組條件。
- 無論具有何種類型的訂用帳戶，管理群組都可提供企業級的大規模管理功能。
- 例如，您可以套用能限制可建立 VM 之區域的管理群組原則。 此原則接著會套用至所有的管理群組、訂用帳戶以及該管理群組下的資源。
- 您可以建置管理群組和訂用帳戶的彈性結構，將資源組織到一個階層中，以便執行統一原則與存取管理。

下圖顯示使用管理群組建立治理階層的範例。

![管理群組](./media/migrate-best-practices-security-management/management-groups.png)
*管理群組*

**深入了解：**
- [了解](https://docs.microsoft.com/azure/governance/management-groups/index)將資源組織為管理群組。

## <a name="best-practice-deploy-azure-policy"></a>最佳做法：部署 Azure 原則

Azure 原則是 Azure 中的一個服務，您可以用來建立、指派和管理原則。

- 原則會對您的資源強制執行不同的規則和效果，讓這些資源能符合公司標準和服務等級協定的規範。
- Azure 原則會評估您的資源，掃描那些不符合您原則規範的資源。
- 例如，您可以建立對環境中的 VM 僅允許特定 SKU 大小的原則。 Azure 原則在建立和更新資源，以及掃描現有資源時，將會評估此設定。 
- Azure 會提供一些可供您指派的內建原則，或者您也可以自行建立原則。


![Azure 原則](./media/migrate-best-practices-security-management/policy.png)
*Azure 原則*

**深入了解：**
- [取得](https://docs.microsoft.com/azure/governance/policy/overview) Azure 原則概觀。
- [了解](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)建立和管理原則來強制執行合規性。


## <a name="best-practice-implement-a-bcdr-strategy"></a>最佳做法：實作 BCDR 策略

規劃商務持續性和災害復原 (BCDR) 是一件非常重要的工作，且您應該在規劃針對 Azure 的移轉時便完成它。 就法律條款而言，您的合約會包含能因不可抗力 (例如颶風或地震) 而免除義務的不可抗力條款。 不過，您也有確保服務在發生災害時會持續執行，並於必要時進行復原的義務。 您達成此義務的能力，可能會左右您公司的未來發展。

廣義來說，您的 BCDR 策略必須考慮：
- **資料備份**：如何保護資料的安全，使您能在發生中斷時輕鬆復原它。
- **災害復原**：如何在發生中斷時維持應用程式的復原性和可用性。 

### <a name="azure-resiliency-features"></a>Azure 復原功能
Azure 平台提供數個復原功能。

- **區域配對**：Azure 會配對區域以在資料存留處界線內提供區域性保護。 Azure 會確保區域配對之間的實體隔離、在發生大範圍中斷時優先針對配對中的其中一個區域進行復原、在每個區域中個別部署系統更新，以及允許 Azure 異地備援儲存體之類的功能跨區域配對進行複寫。
- **可用性區域**：可用性區域 (Zone) 能透過搭配 Azure 區域 (Region) 建立實體分離區域 (Zone)，來保護不受 Azure 資料中心發生整體失敗的影響。 每個區域 (Zone) 都具有獨立的電源、網路基礎結構及冷卻機制。
- **可用性設定組**：可用性設定組能保護不受資料中心內失敗的影響。 您會將 VM 分組為可用性設定組，以使它們保持高可用性。 在每個可用性設定組內，Azure 會實作能將具有共同電源和網路交換器的基礎硬體分組在一起的多個容錯網域，以及能將可同時進行維護或重新啟動的基礎硬體分組在一起的更新網域。 舉例來說，當某個工作負載分佈在多個 Azure VM 上時，您可以針對每個應用程式層級將兩個或多個 VM 置於集合中。 例如，您可以將前端 VM 置於一個集合中，並將資料層級 VM 置於另一個集合中。 由於每個集合中一次只會重新啟動一個更新網域，且 Azure 會確保集合中的 VM 會分散在多個容錯網域上，您便能確定所有 VM 不會同時都發生失敗。

### <a name="set-up-bcdr"></a>設定 BCDR

移轉到 Azure 時，請務必了解雖然 Azure 平台會提供這些內建的復原功能，您仍需自行設計 Azure 部署以利用能提供高可用性、災害復原及備份的 Azure 功能和服務。

- 您的 BCDR 解決方案將取決於您公司的目標，並會被您的 Azure 部署策略所影響。 基礎結構即服務 (IaaS) 和平台即服務 (PaaS) 部署會為 BCDR 帶來不同的挑戰。
- 實作完畢之後，您應該定期測試 BCDR 解決方案，以檢查您的策略是否仍然有效。


## <a name="best-practice-back-up-your-data"></a>最佳做法：備份您的資料

在大部分的情況下，內部部署工作負載會在移轉後淘汰，且您必須對用於備份資料的內部部署策略進行擴充或加以取代。 如果您將整個資料中心移轉到 Azure，您必須使用 Azure 技術或協力廠商的整合解決方案來設計並實作完整的備份解決方案。 


### <a name="back-up-an-iaas-deployment"></a>備份 IaaS 部署

針對在 Azure IaaS VM 上執行的工作負載，請考慮這些備份解決方案：

- **Azure 備份**：能針對 Azure Windows 和 Linux VM 提供應用程式一致的備份。
- **儲存體快照集**：能擷取 Blob 儲存體的快照集。

#### <a name="azure-backup"></a>Azure 備份

Azure 備份會建立儲存在 Azure 儲存體中的資料復原點。 Azure 備份可以備份 Azure VM 磁碟，以及 Azure 檔案 (預覽)。 Azure 檔案能提供位於雲端中並可透過 SMB 存取的檔案共用。
   
您可以使用 Azure 備份以數種方式備份 VM。

- **從 VM 設定直接備份**：您可以直接從 Azure 入口網站中的 VM 選項，直接搭配 Azure 備份來備份 VM。 您可以每天備份一次 VM，並視需要還原 VM 磁碟。 Azure 備份會擷取能感知應用程式的資料快照集 (VSS)，系統不會在 VM 上安裝任何代理程式。
- **在復原服務保存庫中直接備份**：您可以部署 Azure 備份復原服務保存庫，來備份您的 IaaS VM。 這能提供單一位置以追蹤及管理備份，並提供更細微的備份與還原選項。 備份一天最多三次，並於檔案/資料夾層級執行。 它無法感知應用程式，且不支援 Linux。 您必須在每個想要備份的 VM 上安裝 Microsoft Azure 復原服務 (MARS) 代理程式。
- **Azure 備份伺服器：將 VM 保護至 Azure 備份伺服器**：Azure 備份伺服器是搭配 Azure 備份免費提供。 VM 會被備份到本機 Azure 備份伺服器儲存體。 您接著會將 Azure 備份伺服器備份到 Azure 的保存庫中。 備份可感知應用程式，並針對備份頻率和保留期提供完整的細微控制。 您可以在應用程式層級進行備份。 例如透過備份 SQL Server 或 SharePoint。

針對安全性，Azure 備份會使用 AES 256 對執行中的資料進行加密，並將它透過 HTTPS 傳送至 Azure。 Azure 中已備份的待用資料會使用[儲存體服務加密 (SSE)](https://docs.microsoft.com/azure/storage/common/storage-service-encryption?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) 進行加密，以及適用於傳輸與儲存的資料。


![Azure 備份](./media/migrate-best-practices-security-management/iaas-backup.png)
*Azure 備份*

**深入了解：**

- [了解](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup)不同類型的備份。
- 針對 Azure VM [規劃備份基礎結構](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)。

#### <a name="storage-snapshots"></a>儲存體快照集

Azure VM 會以分頁 Blob 的形式儲存在 Azure 儲存體中。 

- 快照集會擷取特定時間點的 Blob 狀態。
- 作為 Azure VM 磁碟的替代備份方法，您可以擷取儲存體 Blob 的快照集，然後將它們複製到另一個儲存體帳戶。 
- 您可以複製整個 Blob，或使用增量快照複製以僅複製差異變更，並減少儲存空間。
- 作為額外的預防措施，您可以啟用 Blob 儲存體帳戶的虛刪除。 啟用此功能時，系統會將已刪除的 Blob 標示為刪除，但不會立即清除它。 可以在過渡期間還原該 Blob。

**深入了解：**

- [了解](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) Azure Blob 儲存體。
- [了解如何](https://docs.microsoft.com/azure/storage/blobs/storage-blob-snapshots)建立 Blob 快照集。
- 檢閱 Blob 儲存體備份的[範例案例](https://azure.microsoft.com/blog/microsoft-azure-block-blob-storage-backup) \(英文\)。
- [閱讀](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)虛刪除的相關資訊。
- [Azure 儲存體中的災害復原和強制容錯移轉 (預覽)](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

#### <a name="third-party-backup"></a>協力廠商備份

除此之外，您可以使用協力廠商解決方案來將 Azure VM 和儲存體容器備份到本機儲存體或其他雲端服務提供者。 [深入了解](https://azuremarketplace.microsoft.com/marketplace/apps?search=backup&page=1) \(英文\) Azure Marketplace 中的備份解決方案。 


### <a name="back-up-a-paas-deployment"></a>備份 PaaS 部署


和需要自行管理 VM 及基礎結構的 IaaS 不同，PaaS 模型平台及基礎結構是由提供者來管理，這讓您可以專注於核心應用程式邏輯和功能上。 由於 PaaS 服務類型眾多，因此基於備份目的，我們會對每個服務進行個別評估。 我們將探討兩個常見的 Azure PaaS 服務：Azure SQL Database 和 Azure Functions。

#### <a name="back-up-azure-sql-database"></a>備份 Azure SQL Database

Azure SQL Database 是完全受控的 PaaS 資料庫引擎。 它能提供數個商務持續性功能，包括自動化備份。

- SQL Database 每週會自動執行完整資料庫備份，且每 12 小時便會執行差異備份。 系統每五到十分鐘便會擷取交易記錄備份，以保護資料庫不受資料遺失的影響。
- 備份是以透明的方式進行，且不會產生額外費用。
- 備份會儲存在 RA-GRS 儲存體中以取得異地備援，並會複寫到配對的地理區域。
- 備份保留期取決於購買模型。 對以 DTU 為基礎的服務層來說，基本層的保留期是七天，而其他層級則是 35 天。
- 您可以將資料庫還原至保留期限內的某一個時間點。 您也可以還原已刪除的資料庫、還原到不同的地理區域，或在資料庫具有長期保留原則 (LTR) 的情況下從長期備份中還原。


![Azure SQL 備份](./media/migrate-best-practices-security-management/sql-backup.png)
*Azure SQL 備份*

**深入了解：**
- 適用於 SQL Database 的[自動化備份](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)。
- 使用自動備份[復原資料庫](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups)。

 
#### <a name="back-up-azure-functions"></a>備份 Azure Functions

由於 Azure Functions 的運作方式基本上和程式碼相同，您應該使用和保護程式碼相同的方式 (例如 GitHub 中的原始程式碼控制或 Azure DevOps Services) 來備份它們

**深入了解：**

- 適用於 Azure DevOps 的[資料保護](https://docs.microsoft.com/azure/devops/articles/team-services-security-whitepaper?view=vsts) \(英文\)。

## <a name="best-practice-set-up-disaster-recovery"></a>最佳做法：設定災害復原 

除了保護資料之外，BCDR 規劃也必須考慮在發生災害的情況下使應用程式和工作負載持續可用的方法。 

### <a name="set-up-disaster-recovery-for-iaas-apps"></a>針對 IaaS 應用程式設定災害復原

針對在 Azure IaaS VM 和 Azure 儲存體上執行的工作負載，請考慮這些解決方案：

- **Azure Site Recovery**：協調將主要區域中的 Azure VM 複寫到次要區域。 發生中斷時，您會從主要區域容錯移轉到次要區域，而使用者將能繼續存取應用程式。 當情況返回正常時，您便能容錯回復到主要區域。
- **Azure 儲存體**：Azure 能針對不同的儲存體類型提供內建的復原性和高可用性：

#### <a name="azure-site-recovery"></a>Azure Site Recovery 

Azure Site Recovery 是在發生中斷的情況下確保 Azure VM 可以上線且 VM 應用程式可供使用的主要 Azure 服務。 Site Recovery 會將 VM 從主要 Azure 區域複寫到次要 Azure 區域。 發生災害時，您會從主要區域將 VM 容錯移轉到次要區域，並如往常一般地繼續存取它們。 當作業返回正常時，您便可以將 VM 容錯回復到主要區域。


![Azure Site Recovery](./media/migrate-best-practices-security-management/site-recovery.png)
*Site Recovery*

**深入了解：**
- [檢閱](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-disaster-recovery-guidance) Azure VM 的災害復原案例。
- [了解如何](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-replicate-after-migration)在移轉之後設定 Azure VM 的災害復原。

#### <a name="azure-storage"></a>Azure 儲存體

Azure 儲存體會進行複寫以取得內建的復原性和高可用性。

-   **異地備援儲存體 (GRS)**：保護不受權區域服務中斷的影響，並能在一年的期間內提供至少 99.99999999999999% (16 個 9) 的物件耐久性。
    - 儲存體資料會複寫到與主要區域配對的次要區域。
    - 如果主要區域中斷，且 Microsoft 起始對次要區域的容錯移轉，您將能有對您自己資料的讀取權限。
- **讀取權限異地備援儲存體 (RA-GRS)**：保護不受全區域服務中斷的影響。
    - 儲存體資料會覆寫到次要區域。
    - 無論 Microsoft 是否起始容錯移轉，我們都會保證您擁有次要區域中已複寫資料的讀取權限。 在相同區域中的兩個或多個資料中心發生問題的情況下，您的資料仍會在地理區隔的區域中提供使用。
-   **區域備援儲存體 (ZRS)**：保護不受資料中心失敗的影響。
    - ZRS 會以同步方式將資料複寫到單一區域中的三個儲存體叢集。 叢集的實體位置都不同，而且都位於自己的可用性區域中。
    - 如果發生災害，您的儲存體將會持續可用。 ZRS 應該作為任務關鍵性工作負載的最小目標。



**深入了解：**

- [了解](https://docs.microsoft.com/azure/storage/common/storage-redundancy) Azure 儲存體複寫。


### <a name="set-up-disaster-recovery-for-paas-workloads"></a>針對 PaaS 工作負載設定災害復原

讓我們針對 PaaS 工作負載範例考慮災害復原選項。

#### <a name="disaster-recovery-of-azure-sql-server"></a>適用於 Azure SQL Server 的災害復原

有數個不同的選項可用，每個都會影響資料遺失、復原時間，以及成本。

您可以使用容錯移轉群組和主動式異地複寫，來針對區域中斷和嚴重失敗提供復原性

- **主動式異地複寫**：部署主動式異地複寫來在發生資料中心中斷，或無法連線至主要資料庫時迅速提供災害復原。
    - 異地複寫會在相同或不同區域中最多四個次要資料庫中，持續建立您資料庫的可讀取複本。
    - 在發生中斷時，您會容錯移轉到其中一個次要區域，並使您的資料庫得以重新上線。
- **自動容錯移轉群組**：自動容錯移轉群組會搭配多資料庫的透明容錯移轉來延伸主動式異地複寫。
    - 自動容錯移轉群組能搭配群組層級的複寫和自動容錯移轉，提供強大的主動式異地複寫抽象概念。
    - 您會建立包含裝載一或多個主要資料庫的主要伺服器、裝載主要資料庫之唯讀複本的次要伺服器、指向每個伺服器的接聽程式，以及自動化容錯移轉原則的容錯移轉群組。
    - 指定的接聽程式端點能免除在容錯移轉後變更 SQL 連接字串的需求。
- **異地還原**： 
    -   異地還原可讓您將資料庫復原到不同的區域。 所有 Azure 資料庫的自動化備份都會在背景被複寫到次要區域。 它一律會從儲存在次要區域中的備份檔案複本還原資料庫。
-   **區域備援資料庫**能針對 Azure 可用性區域提供內建支援。
    - 區域備援資料庫能在發生資料中心失敗時，強化 Azure SQL Server 的高可用性。
    - 透過區域備援，您可以將備援資料庫複本置於某個區域 (Region) 中不同的可用性區域 (Zone) 之中。 



![異地複寫](./media/migrate-best-practices-security-management/geo-replication.png)
*異地複寫*

**深入了解：**
- [了解](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability)適用於 Azure SQL Server 的高可用性。
- [閱讀](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/) \(英文\) 適用於災害復原的 Azure SQL 資料庫入門課程。
- 取得主動式異地複寫和自動容錯移轉群組的[概觀](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)。
- [了解](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery)針對災害復原進行設計。
- 取得適用於容錯移轉群組的[最佳做法](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) \(英文\)。
- 取得在異地還原或容錯移轉後確保安全性的[最佳做法](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-security-config)。
- [了解](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability#zone-redundant-configuration)區域備援
- [了解如何](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery-drills)針對 SQL 資料庫執行災害復原演練。

### <a name="disaster-recovery-for-azure-functions"></a>適用於 Azure Functions 的災害復原

如果 Azure 中的計算基礎結構失敗，Azure 函數應用程式可能會無法使用。

- 為了將這類停機的可能性降到最低，請使用部署到不同區域的兩個函數應用程式。
- Azure 流量管理員可以設定為偵測主要函數應用程式中的問題，並自動將流量重新導向至次要區域中的函數應用程式
- 具有異地備援儲存體的流量管理員可讓您在多個區域中擁有相同的函式，以防發生區域失敗的情況


![流量管理員](./media/migrate-best-practices-security-management/traffic-manager.png)
*流量管理員*

**深入了解：**

- [了解](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications) Azure 應用程式的災害復原。
- [了解](https://docs.microsoft.com/azure/azure-functions/durable/durable-functions-disaster-recovery-geo-distribution)適用於持久 Azure 函式的災害復原和異地複寫。


## <a name="best-practice-use-managed-disks-and-availability-sets"></a>最佳做法：使用受控磁碟和可用性設定組

Azure 會使用可用性設定組來以邏輯方式將 VM 分組在一起，以及將設定組中的 VM 與其他資源隔離。 可用性設定組中的 VM 會分散於多個具有個別子系統的容錯網域中以防止受到本機失敗的影響，且也會分散於多個更新網域中，以確保所有 VM 不會被設定為同時重新啟動。

Azure 受控磁碟會管理與 VM 磁碟相關的儲存體帳戶，從而簡化 Azure IaaS VM 的磁碟管理。

- 建議您在可以的情況下優先使用受控磁碟。 您只需指定要使用的儲存體類型，以及所需的磁碟大小，Azure 就會在幕後替您建立並管理磁碟。
- 您可以將現有磁碟轉換為受控磁碟。
- 您應該在可用性設定組中建立 VM，以取得高復原性和可用性。 發生計畫性或非計畫性的中斷時，可用性設定組能確保設定組中至少有一個 VM 會持續可用。


![受控磁碟](./media/migrate-best-practices-security-management/managed-disks.png)
*受控磁碟*

**深入了解：**
- [取得](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)受控磁碟的概觀。
- [了解](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks)如何將磁碟轉換成受控。
- [了解如何](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)管理 Azure 中 Windows VM 的可用性。


## <a name="best-practice-monitor-resource-usage-and-performance"></a>最佳做法：監視資源使用量和效能 

您將工作負載移轉到 Azure 的原因，可能是為了它強大的擴充能力。 不過在移動工作負載之後，Azure 並不會在沒有來自您任何輸入的情況下自動實作調整。 例如：

- 如果您的行銷組織正在推送一則會產生額外 300% 流量的電視廣告，這可能會導致網站可用性問題。 您剛移轉完畢的工作負載可能會達到指派的限制並當機。
- 另一個範例則是對您已移轉的工作負載發動的分散式阻斷服務 (DDoS) 攻擊。 在此情況下，您可能不會想要進行調整，而是防止攻擊的來源處達您的資源。

這兩個案例有著不同的解決方式，但都需要您透過使用量和效能監控，對正在發生的情況取得見解。

- Azure 監視器可協助顯示這些計量，並搭配警示、自動調整、事件中樞、邏輯應用程式等功能提供回應。
- 除了 Azure 監視之外，您可以整合自己的協力廠商 SIEM 應用程式來監視 Azure 記錄，以進行稽核和效能事件。


![Azure 監視器](./media/migrate-best-practices-security-management/monitor.png)
*Azure 監視器*

**深入了解：**

- [了解](https://docs.microsoft.com/azure/azure-monitor/overview) Azure 監視器。
- 取得監視和診斷的[最佳做法](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)。
- [了解](https://docs.microsoft.com/azure/architecture/best-practices/auto-scaling)自動調整。
- [了解如何](https://docs.microsoft.com/azure/security-center/security-center-export-data-to-siem)將 Azure 資料路由傳送至 SIEM 工具。

## <a name="best-practice-enable-diagnostic-logging"></a>最佳做法：啟用診斷記錄

Azure 資源會產生相當多的記錄計量和遙測資料。

- 根據預設，大部分的資源類型都沒有啟用診斷記錄。
- 透過在資源上啟用診斷記錄，您可以查詢記錄資料，並根據它建置警示和劇本。
- 當您啟用診斷記錄時，每個資源都會有特定的類別集合。 您可以選取一或多個記錄類別，以及記錄資料的儲存位置。 儲存體帳戶、 事件中樞或 Azure 監視器記錄檔，就可以傳送記錄檔。 


![診斷記錄](./media/migrate-best-practices-security-management/diagnostics.png)
*診斷記錄*

**深入了解：**

- [了解](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) \(英文\) 收集和取用記錄資料。
- [了解](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema)診斷記錄的支援項目。


## <a name="best-practice-set-up-alerts-and-playbooks"></a>最佳做法：設定警示和劇本

在針對 Azure 資源啟用診斷記錄的情況下，您可以開始使用記錄資料來建立自訂警示。

- 當您的監視資料中發現條件時，警示會主動通知您。 您接著便可以在系統使用者注意到這些問題之前解決他們。 您可以針對如計量值、記錄搜尋查詢、活動記錄事件、平台健康情況，以及網站可用性等取得警示。
- 觸發警示時，您便可以執行邏輯應用程式劇本。 劇本可協助您針對特定警示自動化並協調回應。 劇本是以 Azure Logic Apps 為基礎。 您可以使用 Logic Apps 範本來建立劇本，或是自行建立劇本。
- 作為一個簡單的範例，您可以建立會在針對網路安全性群組發生連接埠掃描時觸發的警示。  您可以設定能執行並封鎖掃描來源之 IP 位址的劇本。
- 另一個範例是發生記憶體流失的應用程式。  當記憶體使用量達到某個程度時，範本便可以回收處理程序。


![警示](./media/migrate-best-practices-security-management/alerts.png)
*警示*

**深入了解：**
- [了解](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts)警示。
- [了解](https://docs.microsoft.com/azure/security-center/security-center-playbooks)回應資訊安全中心警示的安全性劇本。

## <a name="best-practice-use-the-azure-dashboard"></a>最佳做法：使用 Azure 儀表板

Azure 入口網站是網頁型的統一主控台，可讓您建置、管理及監控所有項目，從簡單 Web 應用程式到複雜的雲端應用程式皆包含在內。 它也包含了自訂的儀表板各種協助工具選項。
- 您可以建立多個儀表板，並與可存取您 Azure 訂用帳戶的其他人共用。
- 透過此共用模型，您的團隊便能檢視 Azure 環境，讓他們可以主動管理雲端中的系統。


![Azure 儀表板](./media/migrate-best-practices-security-management/dashboard.png)
*Azure 儀表板*

**深入了解：**

- [了解如何](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards)建立儀表板。
- [了解](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards-structure)儀表板結構。


## <a name="best-practice-understand-support-plans"></a>最佳做法：了解支援方案

在某個時間點，您將必須與支援人員或 Microsoft 支援服務人員進行共同作業。 擁有一系列原則和程序以在災害復原等案例期間提供支援，是一件非常重要的事。 此外，您的系統管理員和支援人員都應該接受訓練以實作那些原則。

- 在 Azure 服務問題影響到您工作負載的罕見情況下，系統管理員應該要知道如何以最適當且有效率的方式向 Microsoft 提交支援票證。
- 請熟悉各種針對 Azure 所提供的支援方案。 它們的範圍從開發人員執行個體專有的回應時間，到回應時間少於 15 分鐘的頂級支援。


![支援方案](./media/migrate-best-practices-security-management/support.png)
*支援方案*

**深入了解：**
- [取得](https://azure.microsoft.com/support/options/) Azure 支援方案的概觀。
- [了解](https://azure.microsoft.com/support/legal/sla/)服務等級協定 (SLA)。

## <a name="best-practice-manage-updates"></a>最佳做法：管理更新

搭配最新的作業系統和軟體更新來將 Azure VM 保持在最新狀態，是一件規模龐大的工作。 因此，能同時顯示所有 VM 以了解個別 VM 所需要得更新，並自動推送那些更新的功能，顯得非常可貴。

- 您可以使用 Azure 自動化中的更新管理，來管理部署在 Azure、內部部署或其他雲端提供者中，執行 Windows 和 Linux 電腦之機器的作業系統更新。 
- 使用更新管理以快速評估所有代理程式電腦上可用更新的狀態，並管理更新安裝。
- 您可以從 Azure 自動化帳戶直接為 VM 啟用更新管理。 您也可以在 Azure 入口網站中從 VM 頁面更新單一 VM。
- 此外，Azure VM 可以向 System Center Configuration Manager 註冊。 接著，您可以將 Configuration Manager 工作負載移轉到 Azure，並從單一 Web 介面進行報告和軟體更新。


![VM 更新](./media/migrate-best-practices-security-management/updates.png)
*更新*

**深入了解：**

- [了解](https://docs.microsoft.com/azure/automation/automation-update-management) Azure 中的更新管理。
- [了解如何](https://docs.microsoft.com/azure/automation/oms-solution-updatemgmt-sccmintegration)整合 Configuration Manager 與更新管理。
- 關於 Azure 中 Configuration Manager 的[常見問題集](https://docs.microsoft.com/sccm/core/understand/configuration-manager-on-azure)。


## <a name="implement-a-change-management-process"></a>實作變更管理程序

如同任何生產系統，任何類型的變更都可能會對您的環境產生影響。 變更管理程序能讓使用者需要提交要求才能對生產系統進行變更，這對於已移轉的環境來說是一個相當有價值的額外功能。

- 您可以針對變更管理建置最佳做法架構，以協助系統管理員和支援人員熟悉這個程序。
- 您可以使用 Azure 自動化來協助針對已移轉的工作流程進行組態管理及變更追蹤。
- 強制執行變更管理程序時，您可以使用稽核記錄來將 Azure 變更記錄檔連結至應該是 (也可以不是) 現有的變更要求。 因此，如果您看見沒有搭配相對應變更要求所做的變更，便可以調查程序中發生了什麼問題。

Azure 在 Azure 自動化中具有變更追蹤解決方案：

- 這個解決方案會追蹤對 Windows 與 Linux 軟體和檔案、Windows 登錄機碼、Windows 服務以及 Linux 精靈所做的變更。
- 在受監視的伺服器上的變更會傳送至 Azure 監視器服務在雲端中處理。
- 會將邏輯套用至接收的資料，且雲端服務會記錄資料。
- 在 [變更追蹤] 儀表板上，您可以輕鬆地看到在您的伺服器基礎結構中所做的變更。


![變更管理](./media/migrate-best-practices-security-management/change.png)
*變更管理*

**深入了解：**

- [了解](https://docs.microsoft.com/azure/automation/automation-change-tracking)變更追蹤。
- [了解](https://docs.microsoft.com/azure/automation/automation-intro) Azure 自動化功能。




## <a name="next-steps"></a>後續步驟 

檢閱其他最佳做法：


- 移轉後的網路功能[最佳做法](migrate-best-practices-networking.md)。
- 移轉後的成本管理[最佳做法](migrate-best-practices-costs.md)。








