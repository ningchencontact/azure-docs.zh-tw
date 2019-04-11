---
title: Azure SQL Database 中的 managed 執行個體的連線架構 |Microsoft Docs
description: 了解 Azure SQL Database 受控執行個體通訊，以及連線架構如何元件將流量導向至受管理的執行個體。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 02/26/2019
ms.openlocfilehash: 801294241f399097d363dd8dc2682f158c0bf2cc
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358275"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Azure SQL Database 中的 managed 執行個體的連線架構

這篇文章說明 Azure SQL Database 受控執行個體中的通訊。 此外，它也會說明連線架構以及如何元件將流量導向至受管理的執行個體。  

SQL Database 受控執行個體放置於 Azure 的虛擬網路和子網路專門用於受管理的執行個體。 提供此部署：

- 安全的私人 IP 位址。
- 能夠在內部部署網路連線至受控執行個體。
- 受管理的執行個體連接到連結的伺服器或另一個在內部部署資料存放區。
- 能夠連線到 Azure 資源的受管理的執行個體。

## <a name="communication-overview"></a>通訊概觀

下圖顯示連接到受管理的執行個體的實體。 它也會顯示與受管理的執行個體通訊所需要的資源。 在圖表底部的通訊程序表示客戶應用程式和工具連線到受管理的執行個體做為資料來源。  

![連線架構中的實體](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

受管理的執行個體是一個平台即服務 (PaaS) 供應項目。 Microsoft 會使用自動化代理程式 （管理、 部署和維護） 來管理此遙測資料流為基礎的服務。 因為 Microsoft 負責管理，客戶無法在透過遠端桌面通訊協定 (RDP) 存取的受管理的執行個體叢集虛擬機器。

由使用者或應用程式啟動的作業可能需要一些 SQL Server 受管理的平台與互動的執行個體。 其中一個案例是資料庫的建立受控執行個體。 此資源是透過 Azure 入口網站、 PowerShell、 Azure CLI 和 REST API 公開。

受管理的執行個體取決於 Azure 儲存體進行備份、 針對遙測的 Azure 服務匯流排、 Azure Active Directory 驗證和 Azure Key Vault 的 Azure 服務的透明資料加密 (TDE)。 受管理的執行個體建立這些服務的連接。

所有的通訊會使用憑證進行加密和簽章。 若要檢查可信度通訊方，受管理的執行個體持續連絡憑證授權單位確認這些憑證。 如果憑證已撤銷，或無法驗證，受管理的執行個體就會關閉連線的資料保護。

## <a name="high-level-connectivity-architecture"></a>高層級連線架構

概括而言，managed 執行個體是一組服務元件。 這些元件裝載於一組專用隔離客戶的虛擬網路子網路內執行的虛擬機器。 這些電腦會形成虛擬的叢集。

虛擬叢集可以裝載多個受管理的執行個體。 如有需要叢集將會自動展開或折疊客戶變更子網路中佈建的執行個體數目時。

客戶應用程式可以連線到受管理的執行個體，以及查詢和更新資料庫內的虛擬網路中，執行時，才對等互連虛擬網路或透過 VPN 或 Azure ExpressRoute 連接的網路。 此網路必須使用端點，並將私人 IP 位址。  

![連線能力的架構圖表](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Microsoft 管理及部署服務執行的虛擬網路外部。 透過有公用 IP 位址的端點，連線的受管理的執行個體和 Microsoft 服務。 當受管理的執行個體建立輸出連線，接收端連線看起來好像來自此公用 IP 位址的網路位址轉譯 (NAT) 會。

管理流量會流經客戶的虛擬網路。 這表示虛擬網路的基礎結構元素可能傷害管理流量，藉由執行個體失敗，而且變成無法使用。

> [!IMPORTANT]
> 若要改善客戶體驗和服務可用性，Microsoft 會將網路意圖原則套用在 Azure 虛擬網路基礎結構項目上。 原則可能會影響受管理的執行個體的運作方式。 此平台的機制以透明的方式進行通訊給使用者的網路需求。 原則的主要目標是防止網路設定不正確，並確保一般的 managed 執行個體的作業。 當您刪除受控執行個體時，也會移除網路意圖原則。

## <a name="virtual-cluster-connectivity-architecture"></a>虛擬叢集連線架構

讓我們看到受管理的執行個體的連線架構的深入剖析。 下圖顯示虛擬叢集的概念性配置。

![虛擬叢集的連線架構](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

使用主機名稱的格式，用戶端連線到受管理的執行個體`<mi_name>.<dns_zone>.database.windows.net`。 此主機名稱解析的私人 IP 位址，雖然它會在公用網域名稱系統 (DNS) 區域中註冊，而且是可公開解析。 `zone-id`會自動產生，當您建立叢集。 如果新建立的叢集裝載的次要的 managed 執行個體，它會與主要叢集共用其區域識別碼。 如需詳細資訊，請參閱 <<c0> [ 使用自動容錯移轉群組來啟用多個資料庫的透明且協調容錯移轉](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets)。

此私人 IP 位址所屬的 managed 執行個體的內部負載平衡器。 負載平衡器會將導向至受管理的執行個體閘道的流量。 由於多個受管理的執行個體可以在相同叢集內執行，閘道會使用受管理的執行個體的主機名稱，將流量重新導向至正確的 SQL 引擎服務。

藉由連線至受控執行個體的管理和部署服務[管理端點](#management-endpoint)會對應至外部負載平衡器。 只有當它一組預先定義的受管理的執行個體管理元件使用的連接埠上接收流量會路由至節點。 在節點上內建的防火牆設定為允許只能從 Microsoft IP 範圍的流量。 憑證相互驗證所有管理元件與管理平面之間的通訊。

## <a name="management-endpoint"></a>管理端點

Microsoft 會管理受管理的執行個體所使用的管理端點。 這個端點是在執行個體的虛擬叢集內。 管理端點是由內建網路層級防火牆保護。 應用程式層級中，它是由相互憑證驗證保護。 若要尋找端點的 IP 位址，請參閱[判斷管理端點的 IP 位址](sql-database-managed-instance-find-management-endpoint-ip-address.md)。

當連線開頭內的 managed 執行個體 （如備份和稽核記錄檔），流量會從管理端點的公用 IP 位址啟動出現。 您可以限制存取公用服務從受管理的執行個體設定防火牆規則以允許受管理的執行個體的 IP 位址。 如需詳細資訊，請參閱 <<c0> [ 確認受管理的執行個體的內建防火牆](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)。

> [!NOTE]
> 不同於受管理的執行個體中啟動的連線防火牆，受控執行個體的區域內的 Azure 服務會有防火牆，適用於這些服務之間的流量。

## <a name="network-requirements"></a>網路需求

部署虛擬網路內的專用子網路中的 managed 執行個體。 子網路必須具有下列特性：

- **專用的子網路：** 受管理的執行個體子網路不能包含與其相關聯的任何其他雲端服務並不能是閘道子網路。 子網路不能包含任何資源，但受管理的執行個體，並稍後，您無法將資源新增子網路中。
- **網路安全性群組 (NSG)：** 必須定義與虛擬網路相關聯的 NSG[輸入安全性規則](#mandatory-inbound-security-rules)並[輸出安全性規則](#mandatory-outbound-security-rules)之前的任何其他規則。 您可以使用 NSG 來篩選流量的連接埠 1433年上控制受管理的執行個體資料端點的存取權。
- **使用者定義的路由 (UDR) 資料表：** 相關聯的虛擬網路的 UDR 資料表必須包含特定[項目](#user-defined-routes)。
- **沒有服務端點：** 沒有服務端點應該與受管理的執行個體子網路相關聯。 請確定當您建立虛擬網路服務端點選項已停用。
- **足夠的 IP 位址：** 受管理的執行個體子網路必須有至少 16 個 IP 位址。 建議的最小為 32 個 IP 位址。 如需詳細資訊，請參閱 <<c0> [ 判斷受管理的執行個體的子網路大小](sql-database-managed-instance-determine-size-vnet-subnet.md)。 您可以部署在受管理的執行個體[現有的網路](sql-database-managed-instance-configure-vnet-subnet.md)設定它以滿足之後[受管理的執行個體的網路需求](#network-requirements)。 否則，請建立[新的網路和子網路](sql-database-managed-instance-create-vnet-subnet.md)。

> [!IMPORTANT]
> 如果目的地子網路缺少這些特性，您無法部署新的受控執行個體。 當您建立的受管理的執行個體時，網路意圖原則會套用至子網路，以防止網路安裝程式不相容的變更。 從子網路移除最後一個執行個體之後，網路意圖原則也會移除。

### <a name="mandatory-inbound-security-rules"></a>必要輸入安全性規則

| 名稱       |Port                        |通訊協定|來源           |目的地| 動作|
|------------|----------------------------|--------|-----------------|-----------|------|
|管理  |9000、9003、1438、1440、1452|TCP     |任意              |任意        |允許 |
|mi_subnet   |任意                         |任意     |MI SUBNET        |任意        |允許 |
|health_probe|任意                         |任意     |AzureLoadBalancer|任意        |允許 |

### <a name="mandatory-outbound-security-rules"></a>必要輸出安全性規則

| 名稱       |Port          |通訊協定|來源           |目的地| 動作|
|------------|--------------|--------|-----------------|-----------|------|
|管理  |80、443、12000|TCP     |任意              |AzureCloud  |允許 |
|mi_subnet   |任意           |任意     |任意              |MI SUBNET*  |允許 |

> [!IMPORTANT]
> 請確定只有一個輸入的規則連接埠 9000，9003，1438年、 1440年、 1452年和一個輸出的規則，用於連接埠 80、 443、 12000。 輸入和輸出規則針對每個連接埠分別設定受控執行個體佈建透過 ARM 部署將會失敗。 如果這些連接埠位在不同的規則，部署將會失敗，錯誤碼 `VnetSubnetConflictWithIntendedPolicy`

\* MI 的子網路是指表單 10.x.x.x/y 中的子網路的 IP 位址範圍。 您可以在 Azure 入口網站中，子網路屬性中找到這項資訊。

> [!IMPORTANT]
> 必要的輸入的安全性規則允許流量從雖然_任何_來源連接埠 9000，9003、 1438年、 1440 和 1452，內建的防火牆所保護這些連接埠。 如需詳細資訊，請參閱 <<c0> [ 判斷管理端點位址](sql-database-managed-instance-find-management-endpoint-ip-address.md)。
> [!NOTE]
> 如果您使用異動複寫中的受管理的執行個體，而且您可以使用任何執行個體的資料庫當做發行者或散發者，請在子網路的安全性規則中開啟連接埠 445 (TCP 輸出)。 此連接埠可讓 Azure 檔案共用的存取權。

### <a name="user-defined-routes"></a>使用者定義的路由

|名稱|位址首碼|下一個躍點|
|----|--------------|-------|
|subnet_to_vnetlocal|[mi_subnet]|虛擬網路|
|mi-0-5-next-hop-internet|0.0.0.0/5|Internet|
|mi-11-8-nexthop-internet|11.0.0.0/8|Internet|
|mi-12-6-nexthop-internet|12.0.0.0/6|Internet|
|mi-128-3-nexthop-internet|128.0.0.0/3|Internet|
|mi-16-4-nexthop-internet|16.0.0.0/4|Internet|
|mi-160-5-nexthop-internet|160.0.0.0/5|Internet|
|mi-168-6-nexthop-internet|168.0.0.0/6|Internet|
|mi-172-12-nexthop-internet|172.0.0.0/12|Internet|
|mi-172-128-9-nexthop-internet|172.128.0.0/9|Internet|
|mi-172-32-11-nexthop-internet|172.32.0.0/11|Internet|
|mi-172-64-10-nexthop-internet|172.64.0.0/10|Internet|
|mi-173-8-nexthop-internet|173.0.0.0/8|Internet|
|mi-174-7-nexthop-internet|174.0.0.0/7|Internet|
|mi-176-4-nexthop-internet|176.0.0.0/4|Internet|
|mi-192-128-11-nexthop-internet|192.128.0.0/11|Internet|
|mi-192-160-13-nexthop-internet|192.160.0.0/13|Internet|
|mi-192-169-16-nexthop-internet|192.169.0.0/16|Internet|
|mi-192-170-15-nexthop-internet|192.170.0.0/15|Internet|
|mi-192-172-14-nexthop-internet|192.172.0.0/14|Internet|
|mi-192-176-12-nexthop-internet|192.176.0.0/12|Internet|
|mi-192-192-10-nexthop-internet|192.192.0.0/10|Internet|
|mi-192-9-nexthop-internet|192.0.0.0/9|Internet|
|mi-193-8-nexthop-internet|193.0.0.0/8|Internet|
|mi-194-7-nexthop-internet|194.0.0.0/7|Internet|
|mi-196-6-nexthop-internet|196.0.0.0/6|Internet|
|mi-200-5-nexthop-internet|200.0.0.0/5|Internet|
|mi-208-4-nexthop-internet|208.0.0.0/4|Internet|
|mi-224-3-nexthop-internet|224.0.0.0/3|Internet|
|mi-32-3-nexthop-internet|32.0.0.0/3|Internet|
|mi-64-2-nexthop-internet|64.0.0.0/2|Internet|
|mi-8-7-nexthop-internet|8.0.0.0/7|Internet|
||||

此外，您可以在將做為目的地，以透過虛擬網路閘道或虛擬網路設備 (NVA) 已在內部部署私人 IP 範圍的流量路由的路由表中加入項目。

如果虛擬網路包含自訂的 DNS，請新增 Azure 遞迴解析程式 IP 位址 （例如 168.63.129.16) 的項目。 如需詳細資訊，請參閱 <<c0> [ 設定自訂 DNS](sql-database-managed-instance-custom-dns.md)。 自訂的 DNS 伺服器必須能夠解析這些網域和他們的子網域中的主機名稱： *microsoft.com*， *windows.net*， *windows.com*， *msocsp.com*， *digicert.com*， *live.com*， *microsoftonline.com 底下*，以及*microsoftonline secure.aadcdn.microsoftonline-p.com*.

## <a name="next-steps"></a>後續步驟

- 如需概觀，請參閱 [SQL Database 的進階資料安全性](sql-database-managed-instance.md)。
- 了解如何[設定新的 Azure 虛擬網路](sql-database-managed-instance-create-vnet-subnet.md)該[現有的 Azure 虛擬網路](sql-database-managed-instance-configure-vnet-subnet.md)您可以在其中部署受管理的執行個體。
- [計算的子網路大小](sql-database-managed-instance-determine-size-vnet-subnet.md)您要部署受管理的執行個體的位置。
- 了解如何建立受控執行個體：
  - 從 [Azure 入口網站](sql-database-managed-instance-get-started.md)。
  - 藉由使用[PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)。
  - 藉由使用[Azure Resource Manager 範本](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)。
  - 藉由使用[（使用 JumpBox，具有包含 SSMS） 的 Azure Resource Manager 範本](https://portal.azure.com/)。
