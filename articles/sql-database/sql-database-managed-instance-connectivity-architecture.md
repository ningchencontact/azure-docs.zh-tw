---
title: Azure SQL Database 受控執行個體連線架構 | Microsoft Docs
description: 此文章提供 Azure SQL Database 受控執行個體通訊概觀並說明連線架構，以及不同的元件如何運作以將流量導向到受控執行個體。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 12/10/2018
ms.openlocfilehash: b709bbacce23a89b8c60b77a524018b50ca1ca5e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55245662"
---
# <a name="azure-sql-database-managed-instance-connectivity-architecture"></a>Azure SQL Database 受控執行個體連線架構

此文章提供 Azure SQL Database 受控執行個體通訊概觀並說明連線架構，以及不同的元件如何運作以將流量導向到受控執行個體。  

Azure SQL Database 受控執行個體放在專供受控執行個體使用的 Azure VNet 和子網路內。 此部署適用於下列案例： 
- 安全的私人 IP 位址。
- 從內部部署網路直接連線到受控執行個體。
- 將受控執行個體連線到連結的伺服器或其他內部部署資料存放區。
- 將受控執行個體連線到 Azure 資源。

## <a name="communication-overview"></a>通訊概觀

下圖顯示連線到受控執行個體的實體，以及受控執行個體必須存取以正確運作的資源。

![連線架構實體](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

圖表底部描述的通訊代表連線到受控執行個體做為資料來源的客戶應用程式與工具。  

受控執行個體是平台即服務 (PaaS) 供應項目，Microsoft 使用自動化代理程式 (管理、部署與維護) 來管理此服務。 因為受控執行個體完全是 Microsoft 的責任，客戶無法透過 RDP 來存取受控執行個體虛擬叢集機器。

由使用者或應用程式起始的某些 SQL Server 作業可能需要受控執行個體才能與該平台互動。 其中一個案例是建立受控執行個體資料庫，這是透過入口網站、PowerShell 或 Azure CLI 公開的資源。

受控執行個體依賴其他 Azure 服務以正常運作 (例如依賴 Azure 儲存體來進行備份、依賴 Azure 服務匯流排來進行遙測、依賴 Azure AD 來進行驗證、依賴 Azure Key Vault 來進行 TDE 等) 並據以起始連到這些服務的連線。

上述所有通訊都經過加密並使用憑證簽署。 為確認通訊方受信任，受控執行個體會持續連絡「憑證授權單位」來驗證這些憑證。 若憑證已被撤銷或受控執行個體無法驗證憑證，它會關閉連線以保護資料。

## <a name="high-level-connectivity-architecture"></a>高層級連線架構

在較高的層級中，受控執行個體是服務元件集合，在專用隔離虛擬機器集合上裝載，這些虛擬機器在客戶虛擬網路子網路內從虛擬叢集執行。

可以在單一虛擬叢集中裝載多個受控執行個體。 當客戶變更子網路中佈建的執行個體數目時，叢集會視需要自動擴充或縮減。

客戶應用程式可以連線到受控執行個體、查詢及更新資料庫，但它們必須在虛擬網路或對等虛擬網路或 VPN / Express Route 連線網路內使用具有私人 IP 位址的端點來執行。  

![連線架構圖](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Microsoft 管理與部署服務是在虛擬網路外部執行，因此受控執行個體與 Microsoft 服務之間的連線會經過具有私人 IP 位址的端點。 由於網路位址轉譯 (NAT)，當受控執行個體建立連出連線時，在接收端它看起來就像是來自此公開 IP。

管理流量會流經客戶虛擬網路。 這表示虛擬網路基礎結構的元素會影響且甚至可能會傷害管理流量，導致執行個體進入失敗狀態並變得無法使用。

> [!IMPORTANT]
> 為要改善客戶體驗與服務可用性，Microsoft 在會影響受控執行個體運作的 Azure 虛擬網路基礎結構元素上套用「網路意圖原則」。 這是一個平台機制，能以背景方式向使用者溝通網路需求，主要目標是防止網路設定錯誤並確保正常的受控執行個體作業。 刪除受控執行個體時，會一併移除「網路意圖原則」。

## <a name="virtual-cluster-connectivity-architecture"></a>虛擬叢集連線架構

讓我們深入了解受控執行個體連線架構。 下圖顯示虛擬叢集的概念性配置。

![連線架構圖 - 虛擬叢集](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

客戶使用具有 `<mi_name>.<dns_zone>.database.windows.net` 格式的主機名稱連線到受控執行個體。 雖然此主機名稱已在公開 DNS 區域中註冊而且可由公眾解析，但它會解析為私人 IP 位址。 `zone-id` 會在建立叢集時自動產生。 如果新建立的叢集裝載次要受控執行個體，會與主要叢集共用其區域識別碼。 如需詳細資訊，請參閱[自動容錯移轉群組](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets)。

這個私人 IP 位址屬於受控執行個體內部負載平衡器 (ILB)，此負載平衡器會將流量導向到受控執行個體閘道 (GW)。 多個受控執行個體可能會刻意在相同的叢集中執行，GW 使用受控執行個體主機名稱來將流量導向到正確的 SQL Engine 服務。

管理與部署服務會使用對應到外部負載平衡器的[管理端點](#management-endpoint)來連線到受控執行個體。 只有在已預先定義並專門由受控執行個體管理元件所使用之連接埠集合上收到的流量才會路由傳送到那些節點。 節點上的內建防火牆是設定為只允許從 Microsoft 特定的 IP 範圍的流量。 管理元件與管理平面之間的所有通訊都會互相進行憑證驗證。

## <a name="management-endpoint"></a>管理端點

Azure SQL Database 受控執行個體的虛擬叢集包含 Microsoft 用來管理受控執行個體的管理端點。 管理端點會受到網路層級內建防火牆和應用程式層級的相互憑證驗證所保護。 您可以[尋找管理端點 IP 位址](sql-database-managed-instance-find-management-endpoint-ip-address.md)。

從受控執行個體 (稽核記錄檔中的備份) 內起始連線時，流量似乎來自管理端點的公用 IP 位址。 您可以設定防火牆規則從受控執行個體限制公用服務的存取，以便只允許受控執行個體 IP 位址。 深入了解可[確認受控執行個體內建防火牆](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)的方法。

> [!NOTE]
> 這不適用於位於受控執行個體所在區域的 Azure 服務防火牆規則，因為 Azure 平台有共置服務之間的流量最佳化。

## <a name="network-requirements"></a>網路需求

您可以在符合下列需求的虛擬網路內專用子網路 (受控執行個體子網路) 部署受控執行個體：
- **專用子網路**：受控執行個體子網路不能包含與其建立關聯的任何其他雲端服務，且不能是閘道子網路。 您無法在包含受控執行個體以外資源的子網路中建立受控執行個體，而且您稍後無法在子網路中新增其他資源。
- **相容的網路安全性群組 (NSG)**：與受控執行個體子網路建立關聯的 NSG 必須在其他任何規則前面包含下列各表中顯示的規則 (必要輸入安全性規則和必要輸出安全性規則)。 您可以篩選連接埠 1433 上的流量，以使用 NSG 完全控制對受控執行個體資料端點的存取。 
- **相容的使用者定義路由表 (UDR)**：受控執行個體子網路必須具有使用者路由表，其包含將 **0.0.0.0/0 下一個躍點網際網路**作為指派給它的必要 UDR。 此外，您可以新增 UDR，以透過虛擬網路閘道或虛擬網路設備 (NVA) 路由傳送內部部署私人 IP 範圍作為目的地的流量。 
- **選擇性自訂 DNS**：如果在虛擬網路上指定自訂 DNS，則必須將 Azure 的遞迴解析程式 IP 位址 (例如 168.63.129.16) 新增至清單。 如需詳細資訊，請參閱[設定自訂 DNS](sql-database-managed-instance-custom-dns.md)。 自訂 DNS 伺服器必須可以解析下列網域和其子網域中的主機名稱：*microsoft.com*、*windows.net*、*windows.com*、*msocsp.com*、*digicert.com*、*live.com*、*microsoftonline.com* 和 *microsoftonline-p.com*。 
- **沒有服務端點**：受控執行個體子網路不能有與其建立關聯的服務端點。 請確定在建立虛擬網路時停用 [服務端點] 選項。
- **足夠的 IP 位址**：受控執行個體子網路必須有最少 16 個 IP 位址 (建議最小值為 32 個 IP 位址)。 如需詳細資訊，請參閱[決定受控執行個體的子網路大小](sql-database-managed-instance-determine-size-vnet-subnet.md)。 您設定現有網路滿足[受控執行個體網路需求](#network-requirements)或建立[新的網路和子網路](sql-database-managed-instance-create-vnet-subnet.md)後，即可在[現有網路](sql-database-managed-instance-configure-vnet-subnet.md)中部署受控執行個體。

> [!IMPORTANT]
> 如果目的地子網路與上述所有需求不相容，您就無法部署新的受控執行個體。 建立受控執行個體時，會在子網路上套用「網路意圖原則」，防止不相容的網路設定變更。 移除子網路中的最後一個執行個體之後，也會移除「網路意圖原則」

### <a name="mandatory-inbound-security-rules"></a>必要輸入安全性規則 

| Name       |Port                        |通訊協定|來源           |目的地| 動作|
|------------|----------------------------|--------|-----------------|-----------|------|
|管理  |9000、9003、1438、1440、1452|TCP     |任意              |任意        |允許 |
|mi_subnet   |任意                         |任意     |MI SUBNET        |任意        |允許 |
|health_probe|任意                         |任意     |AzureLoadBalancer|任意        |允許 |

### <a name="mandatory-outbound-security-rules"></a>必要輸出安全性規則 

| Name       |Port          |通訊協定|來源           |目的地| 動作|
|------------|--------------|--------|-----------------|-----------|------|
|管理  |80、443、12000|TCP     |任意              |Internet   |允許 |
|mi_subnet   |任意           |任意     |任意              |MI SUBNET  |允許 |

  > [!Note]
  > MI SUBNET 指的是子網路的 IP 位址範圍，其格式為 10.x.x.x/y。 您可以在 Azure 入口網站 (透過子網路屬性) 找到此資訊。
  
  > [!Note]
  > 雖然必要的輸入的安全性規則允許連接埠 9000、9003、1438、1440、1452 的_任何_來源流量，但是這些連接埠受到內建防火牆的保護。 這篇[文章](sql-database-managed-instance-find-management-endpoint-ip-address.md)說明如何探索管理端點 IP 位址並確認防火牆規則。 
  
  > [!Note]
  > 如果您在受控執行個體中使用異動複寫，並且受控執行個體中的任何資料庫都作為發行者或散發者使用，則還需要在子網路的安全性規則中開啟連接埠 445 (TCP 輸出) 以存取 Azure 檔案共用。
  
## <a name="next-steps"></a>後續步驟

- 如需概觀，請參閱 [受控執行個體是什麼](sql-database-managed-instance.md)。
- 了解如何[設定新的 VNet](sql-database-managed-instance-create-vnet-subnet.md) 或[設定現有 VNet](sql-database-managed-instance-configure-vnet-subnet.md) 以便在其中部署受控執行個體。
- [計算出子網路大小](sql-database-managed-instance-determine-size-vnet-subnet.md)以便在其中部署受控執行個體。 
- 如需快速入門，請參閱＜如何建立受控執行個體＞：
  - 從 [Azure 入口網站](sql-database-managed-instance-get-started.md)
  - 使用 [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/)
  - 使用 [Azure Resource Manager 範本](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)
  - 使用 [Azure Resource Manager 範本 (包含 SSMS)](https://portal.azure.com/)
