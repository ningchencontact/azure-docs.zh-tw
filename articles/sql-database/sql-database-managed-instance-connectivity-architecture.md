---
title: Azure SQL Database 中受控實例的連線架構 |Microsoft Docs
description: 深入瞭解 Azure SQL Database 受控實例通訊和連線架構, 以及元件如何將流量導向至受控實例。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 04/16/2019
ms.openlocfilehash: aac328806e2570bd124626e916c250d481a11311
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567596"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Azure SQL Database 中受控實例的連線架構

本文說明 Azure SQL Database 受控實例中的通訊。 它也會說明連線架構, 以及元件如何將流量導向至受控實例。  

SQL Database 受控實例會放在 Azure 虛擬網路內, 以及專門用於受控實例的子網中。 此部署提供:

- 安全的私人 IP 位址。
- 將內部部署網路連線至受控實例的能力。
- 將受控實例連線到連結的伺服器或其他內部部署資料存放區的能力。
- 將受控實例連線到 Azure 資源的能力。

## <a name="communication-overview"></a>通訊概觀

下圖顯示連接到受控實例的實體。 它也會顯示需要與受控實例通訊的資源。 圖表底部的通訊進程代表客戶應用程式和工具, 可連接到受控實例做為資料來源。  

![連線性架構中的實體](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

受控實例是一種平臺即服務 (PaaS) 供應專案。 Microsoft 會使用自動化代理程式 (管理、部署和維護) 來根據遙測資料流程來管理此服務。 由於 Microsoft 負責管理, 因此客戶無法透過遠端桌面通訊協定 (RDP) 來存取受管理的實例虛擬叢集機器。

某些由終端使用者或應用程式啟動的 SQL Server 作業, 可能需要受控實例與平臺互動。 其中一個案例是建立受控實例資料庫。 此資源會透過 Azure 入口網站、PowerShell、Azure CLI 和 REST API 公開。

受控實例取決於 Azure 服務, 例如備份 Azure 儲存體、遙測 Azure 事件中樞、用於驗證的 Azure Active Directory、Azure Key Vault 透明資料加密 (TDE), 以及提供的幾個 Azure 平臺服務安全性和可支援性功能。 受控實例會連接到這些服務。

所有通訊都會使用憑證來加密和簽署。 為了檢查通訊方的可信度, 受控實例會透過憑證撤銷清單持續驗證這些憑證。 如果憑證遭到撤銷, 受控實例就會關閉連線來保護資料。

## <a name="high-level-connectivity-architecture"></a>高層級連線架構

就高層級而言, 受控實例是一組服務元件。 這些元件裝載于一組專用的獨立虛擬機器上, 並在客戶的虛擬網路子網內執行。 這些機器會形成虛擬叢集。

虛擬叢集可以裝載多個受控實例。 如有需要, 當客戶變更子網中已布建的實例數目時, 叢集會自動展開或合約。

客戶應用程式可以連線到受控實例, 並且可以查詢和更新虛擬網路、對等互連虛擬網路內的資料庫, 或透過 VPN 或 Azure ExpressRoute 連接的網路。 此網路必須使用端點和私人 IP 位址。  

![連線性架構圖表](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Microsoft 管理和部署服務會在虛擬網路外部執行。 受控實例和 Microsoft 服務會透過具有公用 IP 位址的端點進行連接。 當受控實例建立輸出連線時, 接收端網路位址轉譯 (NAT) 會讓連線看起來就像是來自此公用 IP 位址。

管理流量會流經客戶的虛擬網路。 這表示虛擬網路基礎結構的元素可能會讓實例失敗並變成無法使用, 來傷害管理流量。

> [!IMPORTANT]
> 為了改善客戶體驗和服務可用性, Microsoft 會在 Azure 虛擬網路基礎結構元素上套用網路意圖原則。 此原則可能會影響受控實例的運作方式。 此平臺機制會以透明的方式將網路需求傳達給使用者。 原則的主要目標是要防止網路設定錯誤, 並確保正常的受控實例作業。 當您刪除受控實例時, 也會一併移除網路意圖原則。

## <a name="virtual-cluster-connectivity-architecture"></a>虛擬叢集連線架構

讓我們進一步深入探討受控實例的連線架構。 下圖顯示虛擬叢集的概念性配置。

![虛擬叢集的連線架構](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

用戶端會使用具有格式`<mi_name>.<dns_zone>.database.windows.net`的主機名稱來連接到受控實例。 此主機名稱會解析為私人 IP 位址, 雖然它是在公用網域名稱系統 (DNS) 區域中註冊, 而且可公開解析。 `zone-id`當您建立叢集時, 會自動產生。 如果新建立的叢集裝載次要受控實例, 它會與主要叢集共用其區域識別碼。 如需詳細資訊, 請參閱[使用自動容錯移轉群組來啟用多個資料庫的透明和協調容錯移轉](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets)。

此私人 IP 位址屬於受控實例的內部負載平衡器。 負載平衡器會將流量導向至受控實例的閘道。 因為多個受控實例可以在相同的叢集中執行, 所以閘道會使用受控實例的主機名稱, 將流量重新導向至正確的 SQL 引擎服務。

管理和部署服務會使用對應到外部負載平衡器的[管理端點](#management-endpoint), 連接到受控實例。 只有在只有受控實例的管理元件使用的一組預先定義的埠上接收到時, 才會將流量路由傳送至節點。 節點上的內建防火牆設定為只允許來自 Microsoft IP 範圍的流量。 憑證會相互驗證管理元件與管理平面之間的所有通訊。

## <a name="management-endpoint"></a>管理端點

Microsoft 會使用管理端點來管理受控實例。 此端點位於實例的虛擬叢集內。 管理端點會受到網路層級內建防火牆的保護。 在應用層級上, 它會受到相互憑證驗證的保護。 若要尋找端點的 IP 位址, 請參閱[判斷管理端點的 ip 位址](sql-database-managed-instance-find-management-endpoint-ip-address.md)。

當連線在受控實例內啟動時 (如同備份和 audit 記錄), 流量會顯示為從管理端點的公用 IP 位址開始。 您可以設定防火牆規則, 只允許受控實例的 IP 位址, 以限制從受控實例存取公用服務。 如需詳細資訊, 請參閱[驗證受控實例的內建防火牆](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)。

> [!NOTE]
> 移至受控實例區域內部 Azure 服務的流量會經過優化, 因此不會 NATed 至受控實例管理端點的公用 IP 位址。 基於這個理由, 如果您需要使用以 IP 為基礎的防火牆規則, 最常見的是儲存體, 服務必須與受控實例位於不同的區域。

## <a name="network-requirements"></a>網路需求

在虛擬網路內的專用子網中部署受控實例。 子網必須具有下列特性:

- **專用子網:** 受控實例的子網不能包含與其相關聯的任何其他雲端服務, 也不能是閘道子網。 子網不能包含任何資源, 而是受控實例, 而且您稍後無法在子網中新增其他類型的資源。
- **網路安全性群組 (NSG)：** 與虛擬網路相關聯的 NSG 必須在任何其他規則之前定義[輸入安全性規則](#mandatory-inbound-security-rules)和[輸出安全性規則](#mandatory-outbound-security-rules)。 當受控實例設定為重新導向連線時, 您可以使用 NSG 來控制對受控實例資料端點的存取權, 方法是篩選埠1433和埠11000-11999 上的流量。
- **使用者定義的路由 (UDR) 資料表:** 與虛擬網路相關聯的 UDR 資料表必須包含特定的[專案](#user-defined-routes)。
- **沒有服務端點:** 不應該有任何服務端點與受控實例的子網相關聯。 當您建立虛擬網路時, 請確定已停用 [服務端點] 選項。
- **足夠的 IP 位址:** 受控實例子網必須至少有16個 IP 位址。 建議的最小值為32個 IP 位址。 如需詳細資訊, 請參閱[決定受控實例的子網大小](sql-database-managed-instance-determine-size-vnet-subnet.md)。 設定受控實例以滿足[受控實例的網路需求](#network-requirements)之後, 您就可以在[現有的網路](sql-database-managed-instance-configure-vnet-subnet.md)中部署受管理的實例。 否則, 請建立[新的網路和子網](sql-database-managed-instance-create-vnet-subnet.md)。

> [!IMPORTANT]
> 如果目的地子網缺少這些特性, 您就無法部署新的受控實例。 當您建立受控實例時, 會在子網上套用網路意圖原則, 以防止不相容的網路設定變更。 從子網移除最後一個實例之後, 也會一併移除網路意圖原則。

### <a name="mandatory-inbound-security-rules"></a>必要輸入安全性規則

| 名稱       |連接埠                        |Protocol|Source           |目的地|Action|
|------------|----------------------------|--------|-----------------|-----------|------|
|管理  |9000、9003、1438、1440、1452|TCP     |Any              |MI SUBNET  |允許 |
|mi_subnet   |Any                         |Any     |MI SUBNET        |MI SUBNET  |允許 |
|health_probe|Any                         |Any     |AzureLoadBalancer|MI SUBNET  |允許 |

### <a name="mandatory-outbound-security-rules"></a>必要輸出安全性規則

| 名稱       |連接埠          |Protocol|Source           |目的地|Action|
|------------|--------------|--------|-----------------|-----------|------|
|管理  |80、443、12000|TCP     |MI SUBNET        |AzureCloud |允許 |
|mi_subnet   |Any           |Any     |MI SUBNET        |MI SUBNET  |允許 |

> [!IMPORTANT]
> 請確定埠9000、9003、1438、1440、1452和一個用於埠80、443、12000的輸出規則都只有一個輸入規則。 如果為每個埠分別設定輸入和輸出規則, 透過 Azure Resource Manager 部署的受控執行個體布建將會失敗。 如果這些埠是在不同的規則中, 部署將會失敗, 並出現錯誤代碼`VnetSubnetConflictWithIntendedPolicy`

\*MI 子網指的是子網的 IP 位址範圍, 格式為 10. x. x/y。 您可以在 [Azure 入口網站] 的 [子網] 屬性中找到這項資訊。

> [!IMPORTANT]
> 雖然必要的輸入安全性規則允許來自埠9000、9003、1438、1440和1452的_任何_來源流量, 但這些埠會受到內建防火牆的保護。 如需詳細資訊, 請參閱[判斷管理端點位址](sql-database-managed-instance-find-management-endpoint-ip-address.md)。
> [!NOTE]
> 如果您在受控實例中使用異動複寫, 而且您使用任何實例資料庫做為發行者或散發者, 請在子網的安全性規則中開啟埠 445 (TCP 輸出)。 此埠可讓您存取 Azure 檔案共用。

### <a name="user-defined-routes"></a>使用者定義的路由

|名稱|位址首碼|下一個躍點|
|----|--------------|-------|
|subnet_to_vnetlocal|MI SUBNET|虛擬網路|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|網際網路|
|mi-13-96-13-nexthop-internet|13.96.0.0/13|網際網路|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|網際網路|
|mi-20-8-nexthop-網際網路|20.0.0.0/8|網際網路|
|mi-23-96-13-nexthop-網際網路|23.96.0.0/13|網際網路|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|網際網路|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|網際網路|
|mi-51-8-nexthop-internet|51.0.0.0/8|網際網路|
|mi-52-8-nexthop-internet|52.0.0.0/8|網際網路|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|網際網路|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|網際網路|
|mi-66-119-144-20-nexthop-網際網路|66.119.144.0/20|網際網路|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|網際網路|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|網際網路|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|網際網路|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|網際網路|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|網際網路|
|mi-103-25-156-22-nexthop-internet|103.25.156.0/22|網際網路|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|網際網路|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|網際網路|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|網際網路|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|網際網路|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|網際網路|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|網際網路|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|網際網路|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|網際網路|
|mi-131-253-16-nexthop-internet|131.253.0.0/16|網際網路|
|mi-132-245-16-nexthop-internet|132.245.0.0/16|網際網路|
|mi-134-170-16-nexthop-internet|134.170.0.0/16|網際網路|
|mi-134-177-16-nexthop-internet|134.177.0.0/16|網際網路|
|mi-137-116-15-nexthop-internet|137.116.0.0/15|網際網路|
|mi-137-135-16-nexthop-internet|137.135.0.0/16|網際網路|
|mi-138-91-16-nexthop-internet|138.91.0.0/16|網際網路|
|mi-138-196-16-nexthop-internet|138.196.0.0/16|網際網路|
|mi-139-217-16-nexthop-internet|139.217.0.0/16|網際網路|
|mi-139-219-16-nexthop-internet|139.219.0.0/16|網際網路|
|mi-141-251-16-nexthop-internet|141.251.0.0/16|網際網路|
|mi-146-147-16-nexthop-internet|146.147.0.0/16|網際網路|
|mi-147-243-16-nexthop-internet|147.243.0.0/16|網際網路|
|mi-150-171-16-nexthop-internet|150.171.0.0/16|網際網路|
|mi-150-242-48-22-nexthop-internet|150.242.48.0/22|網際網路|
|mi-157-54-15-nexthop-internet|157.54.0.0/15|網際網路|
|mi-157-56-14-nexthop-internet|157.56.0.0/14|網際網路|
|mi-157-60-16-nexthop-internet|157.60.0.0/16|網際網路|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|網際網路|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|網際網路|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|網際網路|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|網際網路|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|網際網路|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|網際網路|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|網際網路|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|網際網路|
|mi-192-100-102-24-nexthop-internet|192.100.102.0/24|網際網路|
|mi-192-100-103-24-nexthop-internet|192.100.103.0/24|網際網路|
|mi-192-197-157-24-nexthop-internet|192.197.157.0/24|網際網路|
|mi-193-149-64-19-nexthop-internet|193.149.64.0/19|網際網路|
|mi-193-221-113-24-nexthop-internet|193.221.113.0/24|網際網路|
|mi-194-69-96-19-nexthop-internet|194.69.96.0/19|網際網路|
|mi-194-110-197-24-nexthop-internet|194.110.197.0/24|網際網路|
|mi-198-105-232-22-nexthop-internet|198.105.232.0/22|網際網路|
|mi-198-200-130-24-nexthop-網際網路|198.200.130.0/24|網際網路|
|mi-198-206-164-24-nexthop-internet|198.206.164.0/24|網際網路|
|mi-199-60-28-24-nexthop-網際網路|199.60.28.0/24|網際網路|
|mi-199-74-210-24-nexthop-internet|199.74.210.0/24|網際網路|
|mi-199-103-90-23-nexthop-internet|199.103.90.0/23|網際網路|
|mi-199-103-122-24-nexthop-internet|199.103.122.0/24|網際網路|
|mi-199-242-32-20-nexthop-internet|199.242.32.0/20|網際網路|
|mi-199-242-48-21-nexthop-internet|199.242.48.0/21|網際網路|
|mi-202-89-224-20-nexthop-網際網路|202.89.224.0/20|網際網路|
|mi-204-13-120-21-nexthop-網際網路|204.13.120.0/21|網際網路|
|mi-204-14-180-22-nexthop-網際網路|204.14.180.0/22|網際網路|
|mi-204-79-135-24-nexthop-網際網路|204.79.135.0/24|網際網路|
|mi-204-79-179-24-nexthop-網際網路|204.79.179.0/24|網際網路|
|mi-204-79-181-24-nexthop-網際網路|204.79.181.0/24|網際網路|
|mi-204-79-188-24-nexthop-網際網路|204.79.188.0/24|網際網路|
|mi-204-79-195-24-nexthop-網際網路|204.79.195.0/24|網際網路|
|mi-204-79-196-23-nexthop-網際網路|204.79.196.0/23|網際網路|
|mi-204-79-252-24-nexthop-網際網路|204.79.252.0/24|網際網路|
|mi-204-152-18-23-nexthop-網際網路|204.152.18.0/23|網際網路|
|mi-204-152-140-23-nexthop-網際網路|204.152.140.0/23|網際網路|
|mi-204-231-192-24-nexthop-網際網路|204.231.192.0/24|網際網路|
|mi-204-231-194-23-nexthop-網際網路|204.231.194.0/23|網際網路|
|mi-204-231-197-24-nexthop-網際網路|204.231.197.0/24|網際網路|
|mi-204-231-198-23-nexthop-網際網路|204.231.198.0/23|網際網路|
|mi-204-231-200-21-nexthop-網際網路|204.231.200.0/21|網際網路|
|mi-204-231-208-20-nexthop-網際網路|204.231.208.0/20|網際網路|
|mi-204-231-236-24-nexthop-網際網路|204.231.236.0/24|網際網路|
|mi-205-174-224-20-nexthop-internet|205.174.224.0/20|網際網路|
|mi-206-138-168-21-nexthop-internet|206.138.168.0/21|網際網路|
|mi-206-191-224-19-nexthop-internet|206.191.224.0/19|網際網路|
|mi-207-46-16-nexthop-網際網路|207.46.0.0/16|網際網路|
|mi-207-68-128-18-nexthop-網際網路|207.68.128.0/18|網際網路|
|mi-208-68-136-21-nexthop-internet|208.68.136.0/21|網際網路|
|mi-208-76-44-22-nexthop-internet|208.76.44.0/22|網際網路|
|mi-208-84-21-nexthop-網際網路|208.84.0.0/21|網際網路|
|mi-209-240-192-19-nexthop-網際網路|209.240.192.0/19|網際網路|
|mi-213-199-128-18-nexthop-internet|213.199.128.0/18|網際網路|
|mi-216-32-180-22-nexthop-internet|216.32.180.0/22|網際網路|
|mi-216-220-208-20-nexthop-internet|216.220.208.0/20|網際網路|
||||

此外, 您可以將專案新增至路由表, 以透過虛擬網路閘道或虛擬網路設備 (NVA) 將具有內部部署私人 IP 範圍的流量路由傳送至目的地。

如果虛擬網路包含自訂 DNS, 則自訂 DNS 伺服器必須能夠解析公用 dns 記錄。 使用 Azure AD 驗證之類的其他功能可能需要解析額外的 Fqdn。 如需詳細資訊, 請參閱[設定自訂 DNS](sql-database-managed-instance-custom-dns.md)。

## <a name="next-steps"></a>後續步驟

- 如需總覽, 請參閱 [SQL Database advanced data security](sql-database-managed-instance.md)。
- 瞭解如何[設定新的 azure 虛擬網路](sql-database-managed-instance-create-vnet-subnet.md)或[現有的 azure 虛擬網路](sql-database-managed-instance-configure-vnet-subnet.md), 您可以在其中部署受控實例。
- 計算您要在其中部署受控實例的[子網大小](sql-database-managed-instance-determine-size-vnet-subnet.md)。
- 瞭解如何建立受控實例:
  - 從 [Azure 入口網站](sql-database-managed-instance-get-started.md)。
  - 使用[PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)。
  - 藉由使用[Azure Resource Manager 範本](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)。
  - 藉由使用[Azure Resource Manager 範本 (使用 JumpBox, 隨附 SSMS)](https://portal.azure.com/)。 
