---
title: "在移轉後進行管理 - Azure SQL Database | Microsoft Docs"
description: "了解如何在移轉至 Azure SQL Database 之後管理資料庫。"
services: sql-database
documentationcenter: 
author: joesackmsft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 12/06/2016
ms.author: Joe.Sack
ms.suite: sql
ms.prod_service: sql-database
ms.component: migration
ms.openlocfilehash: b65236fb2d11473d626ee2602237ed4a49380702
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2017
---
# <a name="new-dba-in-the-cloud--managing-your-database-in-azure-sql-database"></a>雲端 – 管理您在 Azure SQL Database 的資料庫中的新 DBA

移動與傳統自我管理，自我受控制的環境，PaaS 環境可以看起來有點人不知所措一開始。 身為應用程式開發人員或 DBA，您會想一定知道平台，可協助您保持可用，應用程式的高效能安全且有彈性的核心功能。 本文旨在這樣。 發行項簡潔組織資源，並提供如何最適合使用 SQL Database 的重要功能來管理並讓您有效率地執行的應用程式，並達到最佳結果，在雲端中的一些指引。 典型的對象，如這篇文章會是誰:-
- 要評估其應用程式到 Azure SQL DB – 種您的應用程式的移轉。
- 正在移轉其應用程式-進行中的移轉案例。
- 最近完成移轉至 Azure SQL DB – 新的 DBA 在雲端中。

這篇文章會討論一些 Azure SQL DB 的核心特性做為平台，您可以輕易地運用。 它們會如下所示:- 
- 業務續航力和災害復原 (BCDR)
- 安全性與合規性
- 智慧型資料庫監視和維護
- 資料移動


## <a name="business-continuity-and-disaster-recovery-bcdr"></a>業務續航力和災害復原 (BCDR)
業務續航力和災害復原功能可讓您像往常一樣，萬一發生災害繼續您的業務。 在損毀，可能是資料庫層級的事件 （例如，其他人不小心卸除重要資料表） 或資料中心層級事件 （區域災難，例如 tsunami）。 

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>如何建立及管理 SQL Database 上的備份？
您未在 Azure SQL DB 上建立備份，而這是因為您不需要。 SQL Database 會自動備份資料庫，因此您無法再必須擔心排程、 取得及管理備份。 平台會進行完整備份，每週，差異備份每幾小時和記錄備份每隔 5 分鐘，以確保有效率，災害復原和資料遺失最少。 當您建立的資料庫，就會發生第一個完整備份。 這些備份可提供給您稱為 「 保留期限 」 的一段時間，而且您選擇的效能層而異。  SQL Database 會提供能夠還原至時間內此保留期間使用的任何點[點中時間復原 (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore)。

|效能層|以天為單位的保留期限|
|---|:---:|
|基本|7|
|標準|35|
|進階|35|
|||

此外，[長期保留 (LTR)](sql-database-long-term-retention.md)功能可讓您保留備份檔案更長期間的具體而言，最多 10 年的資料，並在該期間內，從這些備份在任何時間點還原資料。 此外，資料庫備份會保留在異地備援儲存體以確保從區域災難復原能力。 您也可以還原這些備份在任何時間點的時間，在保留期限內的任何 Azure 區域中。 請參閱[業務持續性概觀](sql-database-business-continuity.md)。

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>如何確保在資料中心層級的災害或區域災難時業務持續性？
因為您的資料庫備份儲存在異地備援儲存體子系統，以確保發生地區性災難時，您可以備份還原到另一個 Azure 區域。 這稱為 「 地理還原 」。 RPO （復原點目標），這通常是 < 1 小時和 ERT （預估復原時間 – 幾分鐘到小時）。

關鍵任務的資料庫，Azure SQL DB 方案、 作用中地理複寫。 這樣基本上會是它會將原始資料庫，進行地理複寫的次要複本建立另一個區域中。 例如，如果資料庫最初裝載於 Azure 美國西部地區，而且您想地區的災害復原能力。 您會在美國西部說出美國東部建立資料庫的作用中異地複本。 在美國西部，萬一不幸，您可以容錯移轉至在美東地區。 它們設定在自動容錯移轉群組是更好，因為這可確保，資料庫會自動容錯移轉至次要資料庫在美國東部發生嚴重損壞。 < 5 秒而 ERT < 30 秒的 RPO。

如果未設定自動容錯移轉群組，您的應用程式則需要主動監視災害，並開始容錯移轉至次要資料庫。 您可以在不同的 Azure 地區中建立最多 4 個這類作用中異地複本。 它會取得更好。 您也可以存取這些次要作用中地理複本的唯讀存取權。 這樣的處理方式非常實用，以降低延遲地理分散的應用程式案例。 

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>如何沒有我的災害復原計劃變更從內部部署至 SQL Database？
總而言之，傳統的內部部署 SQL Server 安裝程式會要求您主動使用容錯移轉叢集，資料庫鏡像，異動複寫，記錄傳送等之類的功能來管理您的可用性和維護和管理備份，確保業務持續性。 使用 SQL Database 平台管理這些，讓您可以著重於開發和最佳化資料庫應用程式，而不必擔心災害一樣多的管理。 您可以備份和災害復原方案設定以及如何使用幾個點擊在 Azure 入口網站 （或幾個命令使用 PowerShell 應用程式開發介面）。 

若要深入了解災害復原，請參閱： [Azure SQL Db 災害復原 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)

## <a name="security-and-compliance"></a>安全性與合規性
SQL Database 慎重採用安全性和隱私權。 SQL Database 中的安全性時，可使用資料庫層級的平台層級，並加以了解當分成數個層級。 每個層級取得控制，並提供最佳的安全性應用程式。 3 層分別是：
- 身分識別與驗證 ([Windows/SQL 驗證和 Azure Active Directory [AAD] 驗證](sql-database-control-access.md))。
- 監視活動 ([稽核](sql-database-auditing.md)和[威脅偵測](sql-database-threat-detection.md))。
- 保護實際的資料 ([透明資料加密 [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)和[永遠加密 [AE]](/sql/relational-databases/security/encryption/always-encrypted-database-engine))。 
- 控制存取至敏感和特殊權限資料 ([資料列層級安全性](/sql/relational-databases/security/row-level-security)和[動態資料遮罩](/sql/relational-databases/security/dynamic-data-masking))。

[Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)提供跨越在 Azure、內部部署和其他雲端中執行之工作負載的集中式安全性管理。 您可以檢視是否必要 SQL 資料庫保護，例如[稽核](sql-database-auditing.md)和[透明資料加密 [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)設定的所有資源，並建立您自己的需求為基礎的原則。

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>SQL 資料庫中提供使用者驗證方法？
有[兩種驗證方法](sql-database-control-access.md#authentication)SQL 資料庫中提供： 
- [Azure Active Directory 驗證](sql-database-aad-authentication.md)
- SQL 驗證。 

不支援傳統的 windows 驗證。 Azure Active Directory (AD) 是集中式身分識別和存取管理服務。 與此您可以非常輕鬆地提供單一登入存取 (SSO) 給所有人員組織中。 這表示是認證會共用所有的 Azure 服務的簡單驗證。 AAD 支援[MFA （多重要素驗證）](sql-database-ssms-mfa-authentication.md)和與[按幾下滑鼠](../active-directory/connect/active-directory-aadconnect-get-started-express.md)AAD 可與 Windows Server Active Directory 整合。 正如您所使用它在過去，適用於 SQL 驗證。 您提供使用者名稱/密碼，您可以驗證指定的邏輯伺服器上的任何資料庫的使用者。 這也可讓 SQL Database 和 SQL 資料倉儲提供多重要素驗證和 Azure AD 網域內的來賓使用者帳戶。 如果您已經有內部部署 Active Directory，您可以使目錄與 Azure Active Directory 結成同盟，將您的目錄延伸至 Azure。

|**如果您...**|**SQL 資料庫 / SQL 資料倉儲**|
|---|---|
|不想在 Azure 中使用 Azure Active Directory (AD)|使用 [SQL 驗證](sql-database-security-overview.md)|
|已在內部部署 SQL Server 上使用 AD|[使 AD 與 Azure AD 結成同盟](../active-directory/connect/active-directory-aadconnect.md)，並使用 Azure AD 驗證。 如此一來，您即可使用單一登入。|
|需要強制執行多重要素驗證 (MFA)|透過 [Microsoft 條件式存取](sql-database-conditional-access.md)要求 MFA 作為原則，並使用[具有 MFA 支援的 Azure AD 通用驗證](sql-database-ssms-mfa-authentication.md)。|
|具有來自 Microsoft 帳戶 (live.com、outlook.com) 或其他網域 (gmail.com) 的來賓帳戶|在 SQL Database/資料倉儲中使用 [Azure AD 通用驗證](sql-database-ssms-mfa-authentication.md)，它會利用 [Azure AD B2B 共同作業](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)。|
|已使用 Azure AD 認證從同盟網域登入 Windows|使用 [Azure AD 整合式驗證](sql-database-aad-authentication-configure.md)。|
|已使用認證從不與 Azure 同盟的網域登入 Windows|使用 [Azure AD 整合式驗證](sql-database-aad-authentication-configure.md)。|
|需要將其連接到 SQL 資料庫或 SQL 資料倉儲的中介層服務|使用 [Azure AD 整合式驗證](sql-database-aad-authentication-configure.md)。|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>如何限制或控制我的資料庫連線存取？
在您運用，您可以使用以達到最佳的連線能力組織您的應用程式有多個技術。 
- 防火牆規則
- VNET 服務端點
- 保留的 IP

#### <a name="firewall"></a>防火牆
防火牆防止存取您的伺服器的外部實體藉由允許只有特定的實體存取邏輯伺服器。 根據預設，所有連接和內部邏輯伺服器的資料庫不都允許的除了來自其他 Azure 服務的連接。 與防火牆規則中，您可以開啟存取至您的伺服器可以僅由您核准，藉由允許通過防火牆的電腦的 IP 位址的實體 （例如，開發人員電腦）。 它也可讓您指定 Ip 範圍，您會想要允許存取邏輯伺服器。 比方說，可以一次加入您組織中的開發人員電腦 IP 位址的防火牆設定 頁面中指定的範圍。 

在伺服器層級或資料庫層級，您可以建立防火牆規則。 伺服器層級防火牆規則可以建立透過入口網站或透過 SSMS。 深入了解如何設定伺服器和資料庫層級防火牆規則，請參閱： [SQL 資料庫中建立防火牆規則](sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal)。

#### <a name="service-endpoints"></a>服務端點
根據預設，SQL database 是設定為 「 允許所有 Azure 服務 」 – 這表示在 Azure 中的任何虛擬機器可能會嘗試連接到您的資料庫。 這些嘗試仍需就會獲得驗證。 不過，如果您不想要您的資料庫可供任何 Azure Ip，您可以停用 「 允許所有 Azure 服務 」。 此外，您可以設定[VNET 服務端點](sql-database-vnet-service-endpoint-rule-overview.md)。

服務端點 (SE) 可讓您公開您重要的 Azure 資源，只會以自己在 Azure 中的私人虛擬網路。 如此一來，您基本上排除公用存取您的資源。 Azure 虛擬網路之間的流量將會存留在網路的 Azure 中樞。 Se 取得強制通道封包路由。 您的虛擬網路會強制對您的組織的網際網路流量和 Azure 服務流量通過相同的路由。 與服務端點，您可以最佳化這因為封包流程直接從您的虛擬網路到 Azure 的骨幹網路上的服務。

![VNET 服務端點](./media/sql-database-manage-after-migration/vnet-service-endpoints.png) 

#### <a name="reserved-ips"></a>保留的 IP
另一個選項是佈建[保留 Ip](../virtual-network/virtual-networks-reserved-public-ip.md) Vm，和白名單特定的伺服器中的 VM 的 IP 位址的防火牆設定。 藉由指派保留的 IP，即可免去必須以不斷變更的 IP 位址更新防火牆規則的麻煩。

### <a name="what-port-do-i-connect-to-sql-database-on"></a>哪些連接埠連接到 SQL Database 上？

通訊埠 1433年。 SQL Database 會透過此連接埠進行通訊。 若要從連接公司網路內，您必須加入您的組織的防火牆設定中的輸出規則。 一般來說，避免公開 Azure 界限以外的通訊埠 1433年。 您可以在 Azure 中，使用執行 SSMS [Azure RemoteApp](https://www.microsoft.com/cloud-platform/azure-remoteapp-client-apps)。 不需要您開啟傳出的連線通訊埠 1433年，IP 是靜態的因此可以開啟僅 remoteapp DB，它支援多重要素驗證 (MFA)。

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>如何監視和管理我的伺服器和 SQL Database 中的資料庫上的活動嗎？
#### <a name="sql-database-auditing"></a>SQL Database 稽核
使用 SQL 資料庫時，您可以開啟 ON 稽核追蹤資料庫事件。 [SQL Database 稽核](sql-database-auditing.md)資料庫事件記錄，並將其寫入 Azure 儲存體帳戶中的稽核記錄檔。 稽核是特別有用，如果您想要了解潛在的安全性與原則違規，維護法規相符性等等。它可讓您定義及設定您所認為需要稽核，並根據您可以取得預先設定的報表和儀表板，以概略了解您的資料庫上發生的事件的事件特定分類。 您可以套用這些資料庫層級或伺服器層級的稽核原則。 請參閱有關如何為您的伺服器/資料庫，開啟稽核功能的指南：[啟用 SQL Database 稽核](sql-database-security-tutorial.md#enable-sql-database-auditing-if-necessary)。

#### <a name="threat-detection"></a>威脅偵測
與[威脅偵測](sql-database-threat-detection.md)，取得可以非常輕鬆地稽核所探索到的安全性或原則違規所操作的能力。 您不需要是安全性專家解決您的系統中的潛在威脅或違規。 威脅偵測也有一些內建的功能，例如 SQL 資料隱碼偵測。 SQL 資料隱碼是嘗試變更或危及資料和一般攻擊的資料庫應用程式很常見的作法。 SQL Database 威脅偵測會執行多組演算法，以偵測潛在弱點和 SQL 插入式攻擊，以及異常資料庫存取模式 (例如從不尋常的位置存取或由不熟悉的主體存取)。 如果在資料庫上偵測到威脅，安全性人員或其他指定的管理員會收到一封電子郵件通知。 每個通知都會提供可疑活動的詳細資料，以及建議如何進一步調查並減輕威脅。 若要了解如何開啟威脅偵測功能，請參閱：[啟用 SQL Database 威脅偵測](sql-database-security-tutorial.md#enable-sql-database-threat-detection)。 
### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>如何保護我的資料通常 SQL Database 上？
加密提供強式機制來保護及安全入侵者從您的機密資料。 沒有用，則入侵者不需要解密金鑰是加密的資料。 因此，它會加入一層額外的保護，在現有的 SQL 資料庫中建立的安全性層級之上。 有保護 SQL 資料庫中的資料的兩個層面： 
- 您會在其餘的資料和記錄檔中的資料 
- 您是執行中的資料。 

在 SQL 資料庫中，依預設，在存放子系統上的資料和記錄檔中的靜止資料完全，一律會加密透過[透明資料加密 [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)。 也會加密您的備份。 與 TDE 沒有在您正在存取此資料的應用程式端所需的變更。 加密和解密透明地; 發生因此名稱。 SQL 資料庫對於保護機密資料執行中和靜止，提供稱為功能[一律加密 (AE)](/sql/relational-databases/security/encryption/always-encrypted-database-engine)。 AE 是一種用戶端加密可加密您的資料庫中的敏感資料行 （使其資料庫系統管理員和未經授權的使用者的加密文字）。 伺服器會開始接收加密的資料。 永遠加密金鑰也會儲存在用戶端，因此只有授權的用戶端可以解密敏感資料行。 伺服器和資料管理員無法查看機密資料，因為加密金鑰存放在用戶端上。 AE 加密敏感資料行，端對端，未經授權的用戶端實體磁碟從資料表中。 AE，支援相等比較，所以 Dba 可以繼續查詢加密資料行做為其 SQL 命令的一部分。 Always Encrypted 可以搭配各種金鑰存放區選項使用，例如 [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md)、Windows 憑證存放區和本機硬體安全性模組。

|**特性**|**一律加密**|**透明資料加密**|
|---|---|---|
|**加密的範圍**|端對端|在其餘的資料|
|**資料庫伺服器可以存取敏感資料**|否|是的因為加密待用資料|
|**允許的 T-SQL 作業**|相等比較|所有的 T-SQL 介面區可用|
|**若要使用的功能所需的應用程式變更**|最低|很少|
|**加密的資料粒度**|資料行層級|資料庫層級|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>如何在我的資料庫中限制存取敏感性資料？
每個應用程式中無法看見每個人都必須保護的資料庫有機密資料的某些位元。 組織內的特定人員需要若要檢視此資料，但是其他人不應該能夠檢視這項資料。 例如，員工薪資。 經理不過需要其直屬員工的薪資資訊的存取權，個別團隊成員不應該有其對等的薪資資訊的存取權。 另一個案例是資料開發人員可能會在開發階段互動的機密資料或測試，例如，客戶的 s。 公開給開發人員不需要這項資訊。 在這種情況下，您的機密資料可能必須遮罩，或不完全公開。 SQL Database 提供這類以防止未經授權的使用者檢視機密資料的兩種方法：

[動態資料遮罩](sql-database-dynamic-data-masking-get-started.md)是可讓您限制遮罩應用程式層上的非特殊權限使用者的敏感性資料的資料遮罩功能。 定義遮罩規則，可以建立遮罩模式 (比方說，只顯示的最後 4 位數國家 （地區) 的識別碼 SSN: XXX-XX 0000，並將標示為 Xs 大部分)，並識別哪些使用者會從遮罩規則中排除。 遮罩發生上即時並沒有可供各種不同的資料類別的各種遮罩函數。 動態資料遮罩可讓您自動偵測您的資料庫中的敏感性資料，並對其套用遮罩。

[資料列層級安全性](/sql/relational-databases/security/row-level-security)可讓您控制資料列層級的存取。 這表示，根據使用者執行查詢 （群組成員資格或執行內容） 的資料庫資料表中的某些資料列會隱藏起來。 在應用程式層 (而非資料庫層) 上進行存取限制，可簡化您的應用程式邏輯。 您開始建立篩選器述詞，篩選出不會公開的資料列和安全性原則下一個定義可以存取這些資料列。 最後，終端使用者執行查詢，並根據使用者的權限，他們檢視這些限制的資料列或無法完全看到它們。

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>如何管理雲端中的加密金鑰？

Always Encrypted (用戶端加密) 和透明資料加密 (待用資料加密) 有金鑰管理選項。 建議您定期輪替加密金鑰。 循環頻率應符合您的內部組織法規和相容性需求的。

**透明資料加密 (TDE)**：TDE 有雙重金鑰階層 – 每個使用者資料庫中的資料是由對稱 AES-256 資料庫唯一的資料庫加密金鑰 (DEK) 加密，接著由伺服器唯一的非對稱 RSA 2048 主要金鑰加密。 主索引鍵可以是 受管理：
- 以自動執行的平台-SQL Database。
- 或使用您[Azure 金鑰保存庫](sql-database-always-encrypted-azure-key-vault.md)做為金鑰存放區。

根據預設，為了方便起見，透明資料加密的主要金鑰是由 SQL Database 服務管理。 如果您的組織想要主要金鑰的控制權，但沒有選項以使用 Azure 金鑰 Vault](sql-database-always-encrypted-azure-key-vault.md) 做為金鑰存放區。 使用 Azure Key Vault，您的組織即可取得金鑰佈建、輪替和權限控制權。 [輪替或切換 TDE 主索引鍵的類型](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation)很快速，因為它只會將 DEK 重新加密。 對於使用的安全性與管理資料間的角色隔離的組織而言，安全性系統管理員無法佈建 TDE 主要金鑰，Azure 金鑰保存庫中的索引鍵內容和 Azure 金鑰保存庫金鑰識別碼提供給要用於資料庫管理員在伺服器上的靜態加密。 金鑰保存庫被設計 Microsoft 不會看到或擷取任何加密金鑰。 您也會為您的組織取得索引鍵的集中的的管理。 

**永遠加密**： 另外還有[兩個索引鍵階層](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted)AES 256 資料行加密金鑰 (CEK)，接著資料行主要金鑰 (CMK) 來加密在永遠加密的加密機密資料的資料行。 針對 Always Encrypted 提供的用戶端驅動程式沒有 CMK 長度限制。 CEK 的加密值會儲存在資料庫中，而 CMK 會儲存在受信任的金鑰存放區中，例如 Windows 憑證存放區、Azure Key Vault 或硬體安全性模組。 
- [CEK 和 CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell)可以輪替。 
- CEK 輪替的資料作業大小，而且可以需要大量時間的資料表大小而定包含加密的資料行。 因此最好將據此規劃 CEK 旋轉。 
- CMK 旋轉，不過，不會干擾資料庫效能，而且可以使用分隔的角色。
下圖顯示 Always Encrypted 中資料行主要金鑰的金鑰存放區選項

![Always Encrypted CMK 存放區提供者](./media/sql-database-manage-after-migration/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>如何最佳化及保護我的組織和 SQL Database 之間的流量？
您的組織和 SQL Database 之間的網路流量通常會透過公用網路進行路由。 不過，如果您選擇最佳化此路徑，並使其更安全，您可以查看 Express Route。 快速路由基本上可讓您將您的公司網路延伸至 Azure 平台，透過私人連線。 如此一來，您不會經過公用網際網路。 您也會取得較高的安全性、 可靠性和路由會轉譯為較低的網路延遲的最佳化，而更快的速度比您通常會遇到將透過公用網際網路。 如果您打算在您的組織和 Azure 之間傳輸大量資料的區塊，請使用 Express Route 可以產生的成本優勢。 您可以選擇從連接的三種不同的連接模型從您的組織至 Azure: 
- [雲端 Exchange 代管](../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [Any-any](../expressroute/expressroute-connectivity-models.md#IPVPN)
- [點對點](../expressroute/expressroute-connectivity-models.md#Ethernet)

快速路由也可讓您在高載最多 2x 購買不另收費用頻寬限制。 您也可以使用 Express Route 設定跨區域連線。 若要查看 ER 連線提供者的清單，請參閱： [Express 路由合作夥伴和互連位置](../expressroute/expressroute-locations.md)。 下列文章描述更詳細的 Express Route:
- [Express Route 簡介](../expressroute/expressroute-introduction.md)
- [先決條件](../expressroute/expressroute-prerequisites.md)
- [工作流程](../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>SQL Database 是否符合任何法規需求，以及這對於自己組織的合規性有何幫助？
SQL Database 會符合法規符合的範圍。 若要檢視最新已符合的符合一組，請瀏覽[Microsoft 信任中心](https://www.microsoft.com/trustcenter/compliance/complianceofferings)和向下切入對是否 SQL 資料庫就會包含在相容的 Azure 服務下，請參閱您的組織來說很重要的符合。 請務必注意，雖然 SQL Database 可能認證為相容的服務，它可以協助您組織的服務的相容性，但是不會自動保證它。

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>智慧型資料庫監視及維護移轉之後

一旦您的資料庫移轉至 SQL 資料庫之後，您會移至想要監視您的資料庫 （如需範例，請檢查資源使用量的就像是或 DBCC 檢查），並執行定期維護 （例如，重建或重新組織索引、 統計資料等等）。 幸運的是，SQL Database 是 Intelligent 在概念上，它會使用歷史趨勢和錄製的度量資訊和統計資料來主動幫助您監視及維護您的資料庫，使您的應用程式永遠以最佳方式執行。 在某些情況下，Azure SQL DB 可以自動執行根據組態設定的維護工作。 有三個 facet 來監視 SQL Database 中的資料庫：
- 效能監視與最佳化。
- 安全性最佳化。
- 成本的最佳化。

**效能監控與最佳化**： 與 「 查詢效能深入資訊中，您可以取得量身訂做的建議資料庫工作負載，讓您的應用程式在達到最佳等級的-一律可以繼續執行。 您也可以設定它，使這些建議會自動套用您不必擔心執行維護工作。 與索引建議程式，您可以自動實作您的工作負載為基礎的索引建議-這稱為 「 自動調整。 建議發展為您的應用程式工作負載變更為您提供最相關的建議。 您也可以取得手動檢閱這些建議，並將其套用自行斟酌選項。  

**安全性最佳化**: SQL Database 提供可採取動作的安全性建議可幫助您用來識別及調查可疑的資料庫活動可能會造成潛在的執行緒，可保護您的資料和威脅偵測資料庫。 [SQL 漏洞評估](sql-vulnerability-assessment.md)是資料庫掃描並將報告服務，可讓您監視您的資料庫規模的安全性狀態、 找出安全性風險並從您所定義的安全性基準漂移。 每次掃描後，系統會提供自訂的可行步驟和補救指令碼清單，以及可用於協助符合規範的評估報告。

與 Azure 資訊安全中心，您可以在棋盤上識別安全性建議，並將其套用只要按一下。 

**成本最佳化**: SQL Azure 平台分析跨伺服器來評估並建議成本最佳化選項，讓您在資料庫的使用量歷程記錄。 這項分析程序通常需要 fortnight 來分析及建置可採取動作的建議。 彈性集區是一個這類的選項。 做為橫幅，建議會出現在入口網站上：

![彈性集區建議](./media/sql-database-manage-after-migration/elastic-pool-recommendations.png) 

您也可以檢視這項分析的 「 建議程式 」 區段底下：

![彈性集區建議 advisor](./media/sql-database-manage-after-migration/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>如何監視效能與資源使用率，SQL Database？

SQL Database 中，您可以利用智慧型觀點來監視效能和據以調整的平台。 您可以監視效能和資源使用量，在 SQL 資料庫中使用下列方法：
- **Azure 入口網站**：選取資料庫，然後按一下 [概觀] 窗格中的圖表，Azure 入口網站即可顯示單一資料庫的使用率。 您可以修改此圖表來顯示多種計量，包括 CPU 百分比、DTU 百分比、資料 IO 百分比、工作階段百分比，以及資料庫大小百分比。

   ![監視圖表](./media/sql-database-manage-after-migration/monitoring-chart.png)

   ![監視 chart2](./media/sql-database-manage-after-migration/chart.png)

從這個圖表中，您也可以設定警示的資源。 這些警示可讓您回應的電子郵件資源條件、 寫入至 HTTPS/HTTP 端點或執行動作。 請參閱[監視 SQL Database 中的資料庫效能](sql-database-single-database-monitor.md)如需詳細指示。

- **動態管理檢視**： 您可以查詢[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)動態管理檢視，以傳回從過去一小時內的資源耗用量統計資料記錄和[sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)系統目錄檢視，以傳回過去 14 天的記錄。
- **查詢效能深入解析**：[查詢效能深入解析](sql-database-query-performance.md)可讓您針對特定的資料庫，查看前幾名資源耗用查詢和長時間執行查詢的歷程記錄。 您可以快速識別排名最前面查詢的資源使用量、 期間和執行的頻率。 您可以追蹤查詢，並偵測迴歸。 這項功能需要啟用[查詢存放區](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)，而且對資料庫有作用。

   ![查詢效能深入解析](./media/sql-database-manage-after-migration/query-performance-insight.png)

- **Azure SQL 分析 （預覽） 中記錄分析**: [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)可讓您收集，並以視覺化方式檢視重要的 Azure SQL Azure 效能度量，最多 150,000 SQL 資料庫和 5000 的 SQL 彈性集區，每個支援工作區。 您可以使用它來監視和收到通知。 您可以監視 SQL Database 和彈性集區度量跨多個 Azure 訂用帳戶和彈性集區，並可用來識別應用程式堆疊的每個層級的問題。

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>我正在注意效能問題： 我的 SQL Database 疑難排解方法不同的 SQL Server 的方式？
您會用來診斷查詢的疑難排解技術的主要部分，資料庫效能問題，維持不變。 之後在相同的 SQL Server 引擎可提供雲端功能。 不過，「 intelligence 」 具有內建的平台-Azure SQL DB。 它可以協助您進行疑難排解及更輕鬆地診斷效能問題。 它可以也執行其中某些修正動作，在您的身分，在某些情況下，主動-自動修正。 

朝向疑難排解效能問題的方法可以大幅獲益使用智慧型功能，例如[查詢效能 Insight(QPI)](sql-database-query-performance.md)和[Database Advisor](sql-database-advisor.md)搭配和讓不同方法中的差異的地方，在於尊重 – 您不再需要執行探究出必要的詳細資訊，有助於您的手動工作會對手邊的問題進行疑難排解。 平台會為您的困難工作。 一個例子是 QPI。 與 QPI，您可以一直向下鑽研查詢層級和查閱歷史趨勢並時完全查詢迴歸找出。 Database Advisor 提供您建議事項，以幫助您改善整體效能通常類似-遺漏的索引，卸除索引，將參數化查詢等。 

進行效能的疑難排解，請務必識別是否在應用程式或資料庫備份，會影響到應用程式效能。 通常效能問題是出在應用程式層。 它可能是架構或資料存取模式。 例如，請考慮對網路延遲很敏感的多對話應用程式。 在此情況下，您的應用程式因因為有許多來回傳送的簡短要求 （「 多對話 」） 應用程式和伺服器之間，以及網路壅塞時，這些往返作業加總快速。 若要在此情況下改善效能，您可以使用[批次查詢](sql-database-performance-guidance.md#batch-queries)。 使用批次可協助您大幅因為現在您的要求取得處理批次。因此，協助您減少往返延遲，並改善您的應用程式效能。 

此外，如果您注意到您的資料庫的整體效能降低，您可以監視[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)和[sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)動態管理檢視以了解 CPU、 I/O 和記憶體耗用量。 您的效能可能受到影響，因為您的資料庫會耗盡資源。 這可能是您可能要變更效能層級和/或服務層為基礎的擴張和縮小工作負載需求。 

一組完整的微調效能問題的建議，請參閱：[微調您資料庫](sql-database-performance-guidance.md#tune-your-database)。

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-performance-level"></a>如何確保我是使用適當的服務層和效能層級？
SQL Database 提供各種服務層，Basic、 Standard 和 Premium。 每個服務層您取得繫結至該服務等級保證可預測的效能。 根據您的工作負載，您可能活動暴增的資源使用率可能會叫用您是在目前效能層級上限。 在這種情況下，很有用藉由評估的第一開始是否任何調整可協助 （例如，新增或改變索引等。）。 如果您仍然遇到限制問題，請考慮將移到較高的效能層級或服務層級。 

|**服務層級**|**常見的使用案例**|
|---|---|
|**基本**|少數使用者與資料庫不是高並行、 小數位數和效能需求的應用程式。 |
|**標準**|具有相當大的並行、 小數位數和效能需求的應用程式搭配低到中等的 IO 要求。 |
|**高級**|應用程式具有許多並行使用者、 高 CPU/記憶體和高 IO 要求。 高並行、 高輸送量和延遲敏感應用程式可以利用高階層級。 |
|||

確定您已正確效能層級上，您可以監視透過 「 如何監視 SQL Database 中的效能與資源使用率 」 的上述方式的其中一個您查詢和資料庫資源耗用量。 您應該尋找查詢/資料庫一致的方式執行 CPU/記憶體等等。 您可以考慮向上擴充至更高的效能層級上作用。 同樣地，如果您請注意，即使在尖峰時間期間，您似乎不使用資源一樣多。請考慮從目前的效能層級向下調整。 

如果您有 SaaS 應用程式模式或資料庫的彙總案例，請考慮使用成本最佳化彈性集區。 彈性集區已達到資料庫合併和成本最佳化的好方法。 若要閱讀更多關於管理使用彈性集區的多個資料庫，請參閱：[管理集區和資料庫](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal)。 

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>頻率必須要執行資料庫完整性檢查，為我的資料庫？
SQL Database 會使用之一些智慧技術可讓它自動並不會遺失任何資料處理的資料損毀的特定類別。 這些技術內建的服務，以及可利用服務需要時，就會發生。 定期執行，您的資料庫備份，跨服務是藉由還原它們在其上執行 DBCC CHECKDB 經過測試。 如果有問題，SQL Database 主動解決它們。 [自動修復頁面](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring)運用以修正損毀或具有資料完整性問題的頁面。 資料庫頁面一律會驗證使用預設的總和檢查碼設定，用來驗證頁面的完整性。 SQL Database 主動監視並檢閱您的資料庫的資料完整性，如果問題發生，具有最高優先順序解決這些。 除了這些項目，您可以選擇選擇性地在您將會執行完整性檢查。  如需詳細資訊，請參閱[SQL Database 中的資料完整性](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="data-movement-after-migration"></a>移轉後的資料移動

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database"></a>如何匯出和匯入資料作為從 SQL Database 的 BACPAC 檔案？

- **匯出**： 您可以將您的 Azure SQL database 匯出 BACPAC 檔案，從 Azure 入口網站的形式

   ![資料庫匯出](./media/sql-database-export/database-export.png)

- **匯入**： 您也可以匯入資料以 BACPAC 檔案至使用 Azure 入口網站的資料庫。

   ![資料庫匯入](./media/sql-database-import/import.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>如何同步處理 SQL Database 和 SQL Server 之間的資料？
您有幾種方式可以達到這個目的： 
- **[資料同步](sql-database-sync-data.md)** – 這項功能可協助您多個內部部署 SQL Server 資料庫和 SQL Database 之間的雙向同步處理資料。 若要同步與內部部署 SQL Server 資料庫，您需要安裝並設定在本機電腦上的同步處理代理程式開啟傳出的 TCP 通訊埠 1433年。
- **[交易複寫](https://azure.microsoft.com/en-us/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)** – 異動複寫與您可以同步處理您資料在內部部署到 Azure SQL DB 與內部被 「 發行者 」 和 Azure SQL DB 在 「 訂閱者 」。 現在，支援此安裝程式。 如需有關如何將資料從內部移轉到 Azure SQL，具有最少停機時間的詳細資訊，請參閱：[使用異動複寫](sql-database-cloud-migrate.md#method-2-use-transactional-replication)

## <a name="next-steps"></a>後續步驟
深入了解[SQL Database](sql-database-technical-overview.md)。

