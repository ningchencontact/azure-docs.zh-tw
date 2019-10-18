---
title: 使用 premium 檔案共用 SQL Server FCI-Azure 虛擬機器
description: 本文說明如何使用 Azure 虛擬機器上的 premium 檔案共用，建立 SQL Server 容錯移轉叢集實例。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/09/2019
ms.author: mathoma
ms.openlocfilehash: b281344084cb558ab490e9e3c24774311ede7866
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529428"
---
# <a name="configure-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>使用 Azure 上的 premium 檔案共用設定 SQL Server 容錯移轉叢集實例虛擬機器

本文說明如何使用[premium 檔案共用](../../../storage/files/storage-how-to-create-premium-fileshare.md)，在 Azure 虛擬機器上建立 SQL Server 容錯移轉叢集實例（FCI）。 

高階檔案共用是以 SSD 為基礎且一致的低延遲檔案共用，可在 Windows Server 2012 及更新版本上，與容錯移轉叢集實例的 SQL Server 2012 和更新版本完全支援搭配使用。 Premium 檔案共用為您提供更大的彈性，可讓您在不需停機的情況下調整檔案共用的大小和規模。 


## <a name="before-you-begin"></a>開始之前

開始之前，您必須先了解一些需要注意的事項，並先備妥幾個項目。

您應了解下列技術的操作方式：

- [Windows 叢集技術](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server 容錯移轉叢集執行個體](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)。

其中一個重要的差異是，在 Azure IaaS VM 容錯移轉叢集上，我們建議每個伺服器（叢集節點）和單一子網都有一個 NIC。 Azure 網路有實體冗余，讓 Azure IaaS VM 來賓叢集上不需要額外的 Nic 和子網。 雖然叢集驗證報告將會發出警告，指出節點只能在單一網路上連線，但您可以放心地在 Azure IaaS VM 容錯移轉叢集上忽略此警告。 

此外，您也應對下列技術有大概了解：

- [Azure premium 檔案共用](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Azure 資源群組](../../../azure-resource-manager/manage-resource-groups-portal.md)

> [!IMPORTANT]
> 目前，只有[SQL Server IaaS 代理程式擴充](virtual-machines-windows-sql-server-agent-extension.md)功能的[輕量](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider)管理模式才支援 Azure 虛擬機器上的 SQL Server 容錯移轉叢集實例。 若要從完整延伸模式變更為輕量，請刪除 correspinding Vm 的「SQL 虛擬機器」資源，然後在 `lightweight` 模式中向 SQL VM 資源提供者註冊。 使用 Azure 入口網站刪除「SQL 虛擬機器」資源時，請務必取消選取實際的虛擬機器。 完整延伸模組支援自動備份、修補和先進入口網站管理等功能。 在輕量管理模式中重新安裝代理程式之後，這些功能將無法在 SQL Vm 上使用。

### <a name="workload-consideration"></a>工作負載考慮

高階檔案共用可提供 IOPS 和整個容量，以符合許多工作負載的需求。 不過，對於需要大量 IO 的工作負載，請考慮使用以受控 premium 磁片或 ultra 磁片為基礎的[儲存空間直接存取 SQL SERVER FCI](virtual-machines-windows-portal-sql-create-failover-cluster.md) 。  

檢查您目前環境的 IOPS 活動，並確認高階檔案在開始部署或遷移之前，會提供您所需的 IOPS。 使用 Windows 效能監視器磁片計數器，並監視 SQL Server 資料、記錄檔和暫存資料庫檔案所需的總 IOPS （磁片傳輸/秒）和輸送量（磁片位元組/秒）。 許多工作負載都有高載 IO，因此最好在大量使用期間進行檢查，並記下最大 IOPS 和平均 IOPS。 Premium 檔案共用會根據共用大小來提供 IOPS。 高階檔案也提供免費的高載，讓您可以將 IO 高載到最多三個小時的基準量。 

如需 premium 檔案共用效能的詳細資訊，請參閱檔案[共用效能層級](https://docs.microsoft.com/en-us/azure/storage/files/storage-files-planning#file-share-performance-tiers)。 

### <a name="licensing-and-pricing"></a>授權和價格

在 Azure 虛擬機器上，您可以使用隨用隨付（PAYG）或攜帶您自己的授權（BYOL） VM 映射來授權 SQL Server。 您選擇的映像類型會影響向您收費的方式。

使用 PAYG 授權時，在 Azure 虛擬機器上，SQL Server 的容錯移轉叢集執行個體 (FCI) 會產生所有 FCI 節點的費用，包括被動節點。 如需詳細資訊，請參閱 [SQL Server Enterprise 虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/)。 

至於有簽訂軟體保證 Enterprise 合約的客戶，每個作用中的節點有權使用一個免費的被動 FCI 節點。 若要在 Azure 中利用這項權益，請使用 BYOL VM 映射，然後在 FCI 的主動和被動節點上使用相同的授權。 如需詳細資訊，請參閱 [Enterprise 合約](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx)。

若要比較 Azure 虛擬機器上 SQL Server 的 PAYG 和 BYOL 授權，請參閱[開始使用 SQL VM](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms)。

如需授權 SQL Server 的完整資訊，請參閱[定價](https://www.microsoft.com/sql-server/sql-server-2017-pricing)。

### <a name="limitations"></a>限制

- 具有 premium 檔案共用的容錯移轉叢集不支援 Filestream。 若要使用 filestream，請使用[儲存空間直接存取](virtual-machines-windows-portal-sql-create-failover-cluster.md)部署您的叢集。 

## <a name="prerequisites"></a>必要條件 

在依照本文中的指示進行之前，您應先備妥下列項目：

- Microsoft Azure 訂用帳戶。
- Azure 虛擬機器上的 Windows 網域。
- 擁有在 Azure 虛擬機器中建立物件之權限的帳戶。
- Azure 虛擬網路和子網路，且具有足夠 IP 位址空間以容納下列元件：
   - 兩部虛擬機器。
   - 容錯移轉叢集的 IP 位址。
   - 每個 FCI 上一個 IP 位址。
- 設定 Azure 網路上的 DNS，並指向網域控制站。
- 高階檔案[共用](../../../storage/files/storage-how-to-create-premium-fileshare.md)，是以您的資料庫儲存配額為基礎，供您的資料檔案使用。 
- 備份的檔案共用，與用於資料檔案的 premium 檔案共用不同。 此檔案共用可以是 standard 或 premium。 

備妥這些先決條件後，便可繼續建置您的容錯移轉叢集。 第一步是建立虛擬機器。

## <a name="step-1-create-virtual-machines"></a>步驟 1：建立虛擬機器

1. 使用您的訂用帳戶登入 [Azure 入口網站](https://portal.azure.com)。

1. [建立 Azure 可用性設定組](../tutorial-availability-sets.md)。

   可用性設定組群組虛擬機器遍佈在容錯網域與更新網域中， 可用性設定組可確保應用程式不受單一失敗點 (如網路交換器或伺服器機架的電源裝置) 所影響。

   若您尚未建立虛擬機器的資源群組，請在建立 Azure 可用性設定組時建立。 若您正在使用 Azure 入口網站建立可用性設定組，請執行下列步驟︰

   - 在 Azure 入口網站中，按一下 **+** 以開啟 Azure Marketplace。 搜尋 [可用性設定組]。
   - 按一下 [可用性設定組]。
   - 按一下 [建立]。
   - 在 [建立可用性設定組]刀鋒視窗中，設定下列值︰
      - **名稱**：可用性設定組的名稱。
      - **訂用帳戶**：您的 Azure 訂用帳戶。
      - **資源群組**︰若您想要使用現有的群組，請按一下 [使用現有的群組]並從下拉式清單中選取群組。 否則，選擇 [建立新的]並輸入群組名稱。
      - **位置**︰設定您計劃建立虛擬機器的位置。
      - **容錯網域**︰使用預設值 (3)。
      - **更新網域**︰使用預設值 (5)。
   - 按一下 [建立]來建立可用性設定組。

1. 在可用性設定組中建立虛擬機器。

   在 Azure 可用性設定組中佈建兩部 SQL Server 虛擬機器。 如需指示，請參閱[在 Azure 入口網站中佈建 SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)。

   將兩部虛擬機器放置於：

   - 與可用性設定組相同的 Azure 資源群組中。
   - 您網域控制站的相同網路上。
   - 有足夠 IP 位址空間存放兩部虛擬機器，與最終會用於此叢集的所有 FCI 的子網路上。
   - Azure 可用性設定組中。   

      >[!IMPORTANT]
      >建立虛擬機器後，您無法設定或變更可用性設定組。

   從 Azure Marketplace 中選擇映像。 您可以使用包含 Windows Server 與 SQL Server，或只包含 Windows Server 的 Marketplace 映像。 如需詳細資料，請參閱 [Azure 虛擬機器上的 SQL Server 概觀](virtual-machines-windows-sql-server-iaas-overview.md)

   Azure 資源庫中的官方 SQL Server 映像包含已安裝的 SQL Server 執行個體、SQL Server 安裝軟體與所需金鑰。

   >[!IMPORTANT]
   > 建立虛擬機器後，請移除預先安裝的獨立 SQL Server 執行個體。 將容錯移轉叢集和 premium 檔案共用設定為存放裝置之後，您將使用預先安裝的 SQL Server 媒體來建立 SQL Server FCI。 

   或者，您也可以只將 Azure Marketplace 映像與作業系統搭配使用。 將容錯移轉叢集和 premium 檔案共用設定為存放裝置之後，請選擇**Windows Server 2016 Datacenter**映射並安裝 SQL Server FCI。 此映像不包含 SQL Server 安裝媒體。 將安裝媒體放置於可在每個伺服器上執行 SQL Server 安裝的位置。 

1. Azure 建立虛擬機器後，請透過 RDP 連接至每部虛擬機器。

   首次透過 RDP 連接至虛擬機器時，電腦會詢問您是否允許此電腦在網路上可供搜尋。 按一下 [是]。

1. 若您正在使用其中一個 SQL Server 型虛擬機器映像，請移除 SQL Server 執行個體。

   - 在 [**程式和功能**] 中，以滑鼠右鍵按一下 **[Microsoft SQL Server 201_ （64位）** ]，然後按一下 [**卸載/變更**]。
   - 按一下 [移除]。
   - 選取預設執行個體。
   - 移除所有在 [Database Engine 服務] 下的功能。 請勿移除 [共用功能]。 請參閱下圖︰

      ![移除功能](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   - 按一下 [下一步]，然後按一下 [移除]。

1. <a name="ports"></a>開啟防火牆連接埠。

   在每部虛擬機器上，開啟 Windows 防火牆上的下列連接埠。

   | 目的 | TCP 連接埠 | 注意
   | ------ | ------ | ------
   | SQL Server | 1433 | 適用於 SDL Server 預設執行個體的一般連接埠。 若您曾使用來自資源庫的映像，此連接埠會自動開啟。
   | 健全狀況探查 | 59999 | 任何開啟的 TCP 連接埠。 在接下來的步驟中，設定負載平衝器[健全狀況探查](#probe)和要使用此連接埠的叢集。   
   | 檔案共用 | 445 | 檔案共用服務所使用的埠。 

1. [將虛擬機器新增至既存的網域](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain)。

建立並設定虛擬機器之後，您可以設定 premium 檔案共用。

## <a name="step-2-mount-premium-file-share"></a>步驟2：掛接 premium 檔案共用

1. 登入[Azure 入口網站](https://portal.azure.com)並移至您的儲存體帳戶。
1. 移至 [檔案**服務**] 底下的 [檔案**共用**]，然後選取您想要用於 SQL 儲存體的 premium 檔案共用。 
1. 選取 **[連線]** ，以顯示您檔案共用的連接字串。 
1. 從下拉式方塊中選取您想要使用的磁碟機號，然後將這兩個程式碼區塊複製到「記事本」。

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-storage/premium-file-storage-commands.png" alt-text="從檔案共用 connect 入口網站複製這兩個 PowerShell 命令":::

1. 使用您的 SQL Server FCI 將用於服務帳戶的帳戶，透過 RDP 連線到 SQL Server VM。 
1. 啟動系統管理 PowerShell 命令主控台。 
1. 從您稍早儲存的入口網站執行命令。 
1. 使用 [檔案瀏覽器] 或 [**執行**] 對話方塊（Windows 鍵 + r），流覽至共用，並使用網路路徑 `\\storageaccountname.file.core.windows.net\filesharename`。 範例：`\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. 在新連接的檔案共用上，至少建立一個資料夾，以便將您的 SQL 資料檔案放入其中。 
1. 在要參與叢集的每個 SQL Server VM 上重複這些步驟。 

  > [!IMPORTANT]
  > 請考慮針對備份檔案使用個別的檔案共用，以儲存此共用資料和記錄檔的 IOPS 和大小。 您可以針對備份檔案使用 Premium 或 Standard 檔案共用

## <a name="step-3-configure-failover-cluster-with-file-share"></a>步驟3：使用檔案共用設定容錯移轉叢集 

下一個步驟是設定容錯移轉叢集。 在此步驟中，您將執行下列子步驟：

1. 新增 Windows 容錯移轉叢集功能
1. 驗證叢集
1. 建立容錯移轉叢集
1. 建立雲端見證


### <a name="add-windows-failover-clustering-feature"></a>新增 Windows 容錯移轉叢集功能

1. 若要開始，請使用本機系統管理員成員，且具有在 Active Directory 中建立物件之權限的網域帳戶，透過 RDP 連接至第一部虛擬機器。 在之後的設定程序中繼續使用此帳戶。

1. [將容錯移轉叢集功能新增至每部虛擬機器](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms)。

   若要從 UI 安裝容錯移轉叢集功能，請於兩部虛擬機器上執行下列步驟。
   - 在 [伺服器管理員]中，按一下 [管理]，然後按一下 [新增角色及功能]。
   - 在 [新增角色及功能精靈] 中，連續按 [下一步] 直到到達 [選取功能]。
   - 在 [選取功能] 中，按一下 [容錯移轉叢集]。 選取所有所需功能與管理工具。 按一下 [新增功能]。
   - 按一下 [下一步]，然後按一下 [完成]以安裝功能。

   若要透過 PowerShell 安裝容錯移轉叢集功能，請在其中一部虛擬機器上執行下列來自系統管理員 PowerShell 工作階段的指令碼。

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

### <a name="validate-the-cluster"></a>驗證叢集

本指南參考[驗證叢集](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation)下的指示。

在 UI 中或透過 PowerShell 驗證叢集。

若要透過 UI 驗證叢集，請在其中一部虛擬機器上執行下列步驟。

1. 在 [伺服器管理員]中，按一下 [工具]，然後按一下 [容錯移轉叢集管理員]。
1. 在 [容錯移轉叢集管理員]中，按一下 [動作]，然後按一下 [驗證設定...]。
1. 按一下 [下一步]。
1. 在 [選取伺服器或叢集] 中，輸入這兩部虛擬機器的名稱。
1. 在 [測試選項]中，選擇 [僅執行我選取的測試]。 按一下 [下一步]。
1. 在 [**測試選擇**] 上，包含 [**儲存體**] 和 [**儲存空間直接存取**] 以外的所有測試。 請參閱下圖︰

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-storage/cluster-validation.png" alt-text="叢集驗證測試":::

1. 按一下 [下一步]。
1. 在 [確認] 中，，按一下 [下一步]。

[驗證設定精靈]會執行驗證測試。

若要透過 PowerShell 驗證叢集，請在其中一部虛擬機器上，執行下列來自系統管理員 PowerShell 工作階段的指令碼。

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

驗證叢集後，請建立容錯移轉叢集。

### <a name="create-the-failover-cluster"></a>建立容錯移轉叢集


若要建立容錯移轉叢集，您需要：
- 成為叢集節點的虛擬機器名稱。
- 容錯移轉叢集的名稱
- 容錯移轉叢集的 IP 位址。 您可以使用與叢集節點一樣，沒有在 Azure 虛擬網路和子網路上用過的 IP 位址。

#### <a name="windows-server-2012-2016"></a>Windows Server 2012-2016

下列 PowerShell 會建立適用于**Windows Server 2012-2016**的容錯移轉叢集。 使用節點名稱 (虛擬機器名稱) 和 Azure VNET 中可用的 IP 位址來更新指令碼：

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

下列 PowerShell 會建立適用于 Windows Server 2019 的容錯移轉叢集。  如需詳細資訊，請參閱 blog[容錯移轉叢集：叢集網路物件](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)。  使用節點名稱 (虛擬機器名稱) 和 Azure VNET 中可用的 IP 位址來更新指令碼：

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>建立雲端見證

雲端見證儲存在 Azure 儲存體 Blob 中，是一種新型的叢集仲裁見證。 這不需要裝載見證共用的個別 VM。

1. [建立容錯移轉叢集的雲端見證](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness)。

1. 建立 Blob 容器。

1. 儲存存取金鑰和容器 URL。

1. 設定容錯移轉叢集的叢集仲裁見證。 請參閱使用者介面中的[在使用者介面中設定仲裁見證](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness)。


## <a name="step-4-test-cluster-failover"></a>步驟4：測試叢集容錯移轉

測試叢集的容錯移轉。 在容錯移轉叢集管理員中，以滑鼠右鍵按一下您的叢集 >**其他動作** > **將核心叢集資源移** > **選取節點**，然後選取叢集的其他節點。 將核心叢集資源移到叢集的每個節點，然後將它移回主要節點。 如果您能夠成功地將叢集移至每個節點，則可以準備安裝 SQL Server。  

:::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-storage/test-cluster-failover.png" alt-text="藉由將核心資源移至其他節點來測試叢集容錯移轉":::

## <a name="step-5-create-sql-server-fci"></a>步驟5：建立 SQL Server FCI

設定容錯移轉叢集之後，您可以建立 SQL Server FCI。

1. 透過 RDP 連接至第一部虛擬機器。

1. 在**容錯移轉叢集管理員**中，請確認所有叢集核心資源皆位於第一部虛擬機器中。 如有必要，請將所有資源移至此虛擬機器。

1. 找出安裝媒體。 若虛擬機器是使用其中一個 Azure Marketplace 映像，則媒體會位於 `C:\SQLServer_<version number>_Full`。 按一下 [設定]。

1. 在 [SQL Server 安裝中心]中，按一下 [安裝]。

1. 按一下 [安裝新的 SQL Server 容錯移轉叢集]。 請遵循精靈內的指示安裝 SQL Server FCI。

   FCI 資料目錄必須位於 premium 檔案共用上。 輸入共用的完整路徑，格式為 `\\storageaccountname.file.core.windows.net\filesharename\foldername`。 隨即會出現警告，通知您已將檔案伺服器指定為數據目錄。 這是預期行為。 請確定您用來保存檔案共用的相同帳戶，是 SQL Server 服務用來避免可能發生失敗的相同帳戶。 

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-storage/use-file-share-as-data-directories.png" alt-text="使用檔案共用作為 SQL 資料目錄":::

1. 完成精靈中的指示後，安裝程式會在第一個節點上安裝 SQL Server FCI。

1. 安裝程式在第一個節點上成功安裝 FCI 後，請透過 RDP 連接至第二個節點。

1. 開啟 [SQL Server 安裝中心]。 按一下 [安裝]。

1. 按一下 [將節點新增到 SQL Server 容錯移轉叢集]。 請遵循精靈中的指示安裝 SQL Server FCI，並將此伺服器新增至 FCI。

   >[!NOTE]
   >若您曾透過 SQL Server 使用 Azure Marketplace 資源庫映像，映像會包含 SQL Server 工具。 若您沒有用過此映像，請另外安裝 SQL Server 工具。 請參閱[下載 Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)。

## <a name="step-6-create-azure-load-balancer"></a>步驟6：建立 Azure 負載平衡器

在 Azure 虛擬機器上，叢集會使用負載平衡器來保留每次均需在一個節點上的 IP 位址。 在此解決方案中，負載平衡器會保留 SQL Server FCI 的 IP 位址。

[建立並設定 Azure Load Balancer](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)。

### <a name="create-the-load-balancer-in-the-azure-portal"></a>在 Azure 入口網站中建立負載平衡器

若要建立負載平衡器：

1. 在 Azure 入口網站中，請透過虛擬機器移至資源群組。

1. 按一下 [+ 新增]。 在 Marketplace 內搜尋 [負載平衡器]。 按一下 [負載平衡器]。

1. 按一下 [建立]。

1. 使用下列項目設定負載平衡器：

   - **訂用帳戶**：您的 Azure 訂用帳戶。
   - **資源群組**︰使用與虛擬機器相同的資源群組。
   - **名稱**︰用以識別負載平衡器的名稱。
   - **區域**：使用與虛擬機器相同的 Azure 位置。
   - **類型**︰負載平衡器分為公開或私人兩種類型。 私人負載平衡器可從相同的 VNET 內存取。 大部分的 Azure 應用程式都能使用私人負載平衡器。 若您的應用程式需要直接透過網際網路存取 SQL Server，請使用公開負載平衡器。
   - **Sku**：負載平衡器的 sku 應該是標準。 
   - **虛擬網路**︰與虛擬機器相同的網路。
   - **Ip 位址指派**： ip 位址指派應為靜態。 
   - **私人 IP 位址**︰與您指派至 SQL Server FCI 叢集網路資源相同的 IP 位址。
   請參閱下圖︰

   ![CreateLoadBalancer](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)
   

### <a name="configure-the-load-balancer-backend-pool"></a>設定負載平衡器後端集區

1. 透過虛擬機器返回 Azure 資源群組，並尋找新的負載平衡器。 您可能需要重新整理資源群組的畫面。 按一下 [負載平衡器]。

1. 按一下 [後端集區]，然後按一下 [+ 新增] 以新增後端集區。

1. 將後端集區關聯至包含 VM 的可用性設定組。

1. 在 [目標網路 IP 設定] 下方，檢查**虛擬機器**並選擇將加入為叢集節點的虛擬機器。 請務必包含將裝載 FCI 的所有虛擬機器。 

1. 按一下 [確定] 以建立後端集區。

### <a name="configure-a-load-balancer-health-probe"></a>設定負載平衡器健全狀況探查

1. 在 [負載平衡器] 刀鋒視窗中，按一下 [健全狀況探查]。

1. 按一下 [+ 新增]。

1. 在 [新增健全狀況探查]刀鋒視窗中，<a name="probe"></a>設定健全狀況探查參數︰

   - **名稱**：健全狀況探查的名稱。
   - **通訊協定**：TCP。
   - **埠**：將設定為您在[此步驟](#ports)中為健康情況探查在防火牆中建立的埠。 在本文中，範例使用 TCP 埠 `59999`。
   - **間隔**：5 秒。
   - **狀況不良臨界值**：2 次連續失敗。

1. 按一下 [確定]。

### <a name="set-load-balancing-rules"></a>設定負載平衡規則

1. 在 [負載平衡器] 分頁上，按一下 [**負載平衡規則**]。

1. 按一下 [+ 新增]。

1. 設定負載平衡規則參數：

   - **名稱**：負載平衡規則的名稱。
   - **前端 IP 位址**︰使用 SQL Server FCI 叢集網路資源的 IP 位址。
   - **連接埠**：設定為 SQL Server FCI TCP 連接埠。 預設執行個體連接埠為 1433。
   - **後端連接埠**：此值使用的連接埠與您啟用**浮動 IP (伺服器直接回傳)** 時的**連接埠**值相同。
   - **後端集區**︰使用您稍早設定的後端集區名稱。
   - **健全狀況探查**：使用您稍早設定的健全狀況探查。
   - **工作階段持續性**：無。
   - **閒置逾時 (分鐘)** ：4。
   - **浮動 IP (伺服器直接回傳)** ：已啟用。

1. 按一下 [確定]。

## <a name="step-7-configure-cluster-for-probe"></a>步驟7：設定探查的叢集

設定 PowerShell 中的叢集探查連接埠參數。

若要設定叢集探查連接埠參數，請將下列指令碼中的變數更新為您環境中的值。 從指令碼移除角括弧 `<>`。 

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

在上述指令碼中，設定您環境的值。 下列清單說明這些值：

   - `<Cluster Network Name>`：Windows Server 容錯移轉叢集網路名稱。 在 **[容錯移轉叢集管理員]**  >  **[網路]** 中，以滑鼠右鍵按一下網路，然後按一下 [內容]。 正確的值在 [一般] 索引標籤的 [名稱] 底下。 

   - `<SQL Server FCI IP Address Resource Name>`：SQL Server FCI IP 位址資源名稱。 在**容錯移轉叢集管理員** > **角色**SQL Server 的 伺服器名稱 下，以**滑鼠右鍵按一下** **伺服器名稱** 底下的 IP 位址 資源，然後按一下 內容。 正確的值在 [一般] 索引標籤的 [名稱] 底下。 

   - `<ILBIP>`：ILB IP 位址。 此位址在 Azure 入口網站中會設定為 ILB 前端位址。 這也是 SQL Server FCI IP 位址。 您可以在 [容錯移轉叢集管理員] 中，您找到 `<SQL Server FCI IP Address Resource Name>` 所在位置的相同內容頁面上找到該位址。  

   - `<nnnnn>`：您在負載平衡器健全狀況探查中設定的探查連接埠。 任何未使用的 TCP 連接埠都有效。 

>[!IMPORTANT]
>叢集參數的子網路遮罩必須是 TCP IP 廣播位址：`255.255.255.255`。

設定叢集探查之後，您可以在 PowerShell 中看到所有叢集參數。 執行下列指令碼：

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-8-test-fci-failover"></a>步驟8：測試 FCI 容錯移轉

測試 FCI 的容錯移轉來驗證叢集功能。 請執行下列步驟：

1. 透過 RDP 連接至其中一個 SQL Server FCI 叢集節點。

1. 開啟 [容錯移轉叢集管理員]。 按一下 [角色]。 請注意哪個節點擁有 SQL Server FCI 角色。

1. 以滑鼠右鍵按一下 SQL Server FCI 角色。

1. 按一下 [移動]，然後按一下 [最佳可行節點]。

**容錯移轉叢集管理員**會顯示角色，其資源則會變成離線狀態。 接著資源會移動，並在另一個節點上線。

### <a name="test-connectivity"></a>測試連線能力

若要測試連線能力，請在相同的虛擬網路內登入至另一部虛擬機器。 開啟 [SQL Server Management Studio]，然後連接到 SQL Server FCI 名稱。

>[!NOTE]
>如有需要，您可以[下載 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。

## <a name="limitations"></a>限制

Azure 虛擬機器支援 Windows Server 2019 上的 Microsoft 分散式交易協調器 (MSDTC)，並且可使用叢集共用磁碟區 (CSV) 上的儲存體和[標準負載平衡器](../../../load-balancer/load-balancer-standard-overview.md)。

在 Azure 虛擬機器上，Windows Server 2016 或更早版本無法支援 MSDTC，因為：

- 叢集 MSDTC 資源無法設定為使用共用儲存體。 若在 Windows Server 2016 上建立 MSDTC 資源，即使有共用儲存體存在，系統也不會顯示任何可用的共用儲存體。 Windows Server 2019 中已修正此問題。
- 基本負載平衡器不會處理 RPC 連接埠。

## <a name="see-also"></a>另請參閱

- [Windows 叢集技術](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server 容錯移轉叢集執行個體](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)。
