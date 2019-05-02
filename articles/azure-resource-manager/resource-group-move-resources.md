---
title: 將 Azure 資源移至新的訂用帳戶或資源群組 | Microsoft Docs
description: 使用 Azure Resource Manager 將資源移到新的資源群組或訂用帳戶。
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.assetid: ab7d42bd-8434-4026-a892-df4a97b60a9b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: tomfitz
ms.openlocfilehash: 4e94bc7686203bfbcd93200e5a1fb65b43ceeb91
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64698479"
---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>將資源移動到新的資源群組或訂用帳戶

本文示範如何將 Azure 資源移至其他 Azure 訂用帳戶或相同訂用帳戶內的其他資源群組。 您可以使用 Azure 入口網站、Azure PowerShell、Azure CLI 或 REST API 來移動資源。

移動作業期間會同時鎖定來源群組和目標群組。 資源群組上的寫入和刪除作業將會封鎖，直到移動完成。 此鎖定表示您無法新增、更新或刪除資源群組中的資源，但不表示資源已遭到凍結。 例如，如果您將 SQL Server 和其資料庫移至新的資源群組，使用該資料庫的應用程式不會發生停機時間。 它仍可對資料庫讀取和寫入。

移動資源只會將它移動到新的資源群組。 移動作業無法變更資源的位置。 新的資源群組可能會有不同的位置，但那樣不會變更資源的位置。

> [!NOTE]
> 本文說明如何在現有的 Azure 訂用帳戶之間移動資源。 如果您實際上是想要升級 Azure 訂用帳戶 (例如從免費切換成預付型方案)，則必須轉換您的訂用帳戶。
> * 若要將免費試用升級，請參閱[將免費試用或 Microsoft Imagine Azure 訂用帳戶升級至隨用隨付](..//billing/billing-upgrade-azure-subscription.md)。
> * 若要變更預付型方案帳戶，請參閱[將您的 Azure 預付型方案訂閱變更為其他供應項目](../billing/billing-how-to-switch-azure-offer.md)。
> * 如果您無法轉換訂用帳戶，請[建立 Azure 支援要求](../azure-supportability/how-to-create-azure-support-request.md)。 針對問題類型選取 [訂用帳戶管理]。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="when-to-call-azure-support"></a>連絡 Azure 支援的時機

您可以透過本文顯示的自助式作業，移動大部分資源。 使用自助式作業︰

* 移动 Resource Manager 资源。
* 根據[傳統部署限制](#classic-deployment-limitations)移動傳統資源。

當您需要進行下列作業時，請連絡[支援人員](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)︰

* 將資源移至新的 Azure 帳戶 (和 Azure Active Directory 租用戶)，而且需要上一節中指示的說明。
* 移動傳統資源，但有限制的問題。

## <a name="services-that-can-be-moved"></a>可以移動的服務

下列清單提供可移至新資源群組和訂用帳戶之 Azure 服務的一般摘要。 对于支持移动的资源类型列表，请参阅[支持移动操作的资源](move-support-resources.md)。

* Analysis Services
* API 管理
* App Service 應用程式 (Web 應用程式) - 請參閱 [App Service 限制](#app-service-limitations)
* App Service 憑證 - 請參閱 [App Service 憑證限制](#app-service-certificate-limitations)
* 自動化 - Runbook 必須存在於和自動化帳戶相同的資源群組中。
* Azure Active Directory B2C
* Azure Cache for Redis - 如果 Azure Cache for Redis 執行個體已設定虛擬網路，該執行個體便無法移至不同的訂用帳戶。 請參閱[虛擬網路限制](#virtual-networks-limitations)。
* Azure Cosmos DB
* Azure 資料總管
* 適用於 MariaDB 的 Azure 資料庫
* 適用於 MySQL 的 Azure 資料庫
* 適用於 PostgreSQL 的 Azure 資料庫
* Azure DevOps-請遵循步驟來[變更用於計費的 Azure 訂用帳戶](/azure/devops/organizations/billing/change-azure-subscription?view=azure-devops)。
* Azure 地圖服務
* Azure 監視器記錄
* Azure 轉送
* Azure Stack - 註冊
* Batch
* BizTalk 服務
* Bot 服務
* CDN
* 雲端服務 - 請參閱 [傳統部署限制](#classic-deployment-limitations)
* 認知服務
* Container Registry
* 內容仲裁
* 成本管理
* Customer Insights
* 資料目錄
* Data Factory
* Data Lake Analytics
* Data Lake Store
* DNS
* Event Grid
* 事件中樞
* Front Door
* HDInsight 叢集 - 請參閱 [HDInsight 限制](#hdinsight-limitations)
* IoT 中心
* IoT 中樞
* Key Vault - 用於磁碟加密的 Key Vault 無法移至相同訂用帳戶中或跨訂用帳戶的資源群組。
* 負載平衡器 - 可以移動基本 SKU 負載平衡器。 不能移動標準 SKU Load Balancer。
* Logic Apps
* Machine Learning：Machine Learning Studio Web 服務可以移至相同訂用帳戶 (而非不同的訂用帳戶) 中的資源群組。 其他 Machine Learning 資源可以在訂用帳戶之間移動。
* 受控磁碟-受控磁碟，在可用性區域中的無法移到不同的訂用帳戶
* 受控識別 - 使用者指派
* 媒體服務
* 監視 - 確定移至新的訂用帳戶不會超過[訂用帳戶配額](../azure-subscription-service-limits.md#monitor-limits)
* 通知中樞
* Operational Insights
* Operations Management
* 入口網站儀表板
* Power BI - Power BI Embedded 和 Power BI 工作區集合
* 公用 IP - 可以移動基本 SKU 公用 IP。 不能移動標準 SKU 公用 IP。
* 復原服務保存庫 - 註冊[預覽版](#recovery-services-limitations)。
* SAP HANA on Azure
* 排程器
* 搜尋 - 您無法在一個作業中移動不同區域中的數個搜尋資源， 而是要在不同作業中移動它們。
* 服務匯流排
* Service Fabric
* Service Fabric Mesh
* SignalR Service
* 儲存體 - 不同區域中的儲存體帳戶無法在相同的作業中移動。 請改為針對每個區域使用個別的作業。
* 儲存體 (傳統) - 請參閱 [傳統部署限制](#classic-deployment-limitations)
* 串流分析 - 無法移動執行中狀態的串流分析作業。
* SQL Database 伺服器 - 資料庫和伺服器必須位於相同的資源群組。 當您移動 SQL 伺服器時，其所有資料庫也會跟著移動。 此行為會套用至 Azure SQL Database 和 Azure SQL Data Warehouse 資料庫。
* 時間序列深入解析
* 流量管理員
* 虚拟机 - 请参阅[虚拟机限制](#virtual-machines-limitations)
* 虛擬機器 (傳統) - 請參閱 [傳統部署限制](#classic-deployment-limitations)
* 虛擬機器擴展集 - 請參閱[虛擬機器限制](#virtual-machines-limitations)
* 虛擬網路 - 請參閱[虛擬網路限制](#virtual-networks-limitations)
* VPN 閘道

### <a name="services-that-cannot-be-moved"></a>無法移動的服務

下列清單提供無法移至新資源群組和訂用帳戶之 Azure 服務的一般摘要。 如需更多詳細資料，請參閱[資源的移動作業支援](move-support-resources.md)。

* AD Domain Services
* AD 混合式健康狀態服務
* 应用程序网关
* Azure 資料庫移轉
* Azure Databricks
* Azure 防火牆
* Azure Kubernetes Service (AKS)
* Azure Migrate
* Azure NetApp Files
* 憑證 - App Service 憑證可以移動，但上傳的憑證則有其[限制](#app-service-limitations)。
* 傳統應用程式
* Container Instances
* 容器服務
* 資料箱
* Dev Spaces
* Dynamics LCS
* ExpressRoute
* 實驗室服務-教室實驗室無法移到新的資源群組或訂用帳戶。 DevTest Labs 可以移至新的資源群組相同的訂用帳戶，但不是會跨訂用帳戶中。
* 受控應用程式
* Microsoft Genomics
* 安全性
* Site Recovery
* StorSimple 裝置管理員
* 虛擬網路 (傳統) - 請參閱 [傳統部署限制](#classic-deployment-limitations)

## <a name="limitations"></a>限制

本節提供如何處理用以移動資源之複雜案例的說明。 限制如下：

* [虛擬機器限制](#virtual-machines-limitations)
* [虛擬網路限制](#virtual-networks-limitations)
* [App Service 限制](#app-service-limitations)
* [App Service 憑證限制](#app-service-certificate-limitations)
* [傳統部署限制](#classic-deployment-limitations)
* [復原服務限制](#recovery-services-limitations)
* [HDInsight 限制](#hdinsight-limitations)

### <a name="virtual-machines-limitations"></a>虛擬機器限制

可以移动包含托管磁盘、托管映像和托管快照的虚拟机，以及移动所含虚拟机使用托管磁盘的可用性集。 受控的磁碟位於可用性區域中無法移到不同的訂用帳戶中。

尚未支援下列案例：

* 憑證儲存在 Key Vault 中的虛擬機器可以移動至相同訂用帳戶中的新資源群組，但是無法跨訂用帳戶移動。
* 无法移动具有标准 SKU 负载均衡器或标准 SKU 公共 IP 的虚拟机规模集。
* 從 Marketplace 資源建立且附加方案的虛擬機器無法在資源群組或訂用帳戶之間移動。 在目前的訂用帳戶中取消佈建虛擬機器，然後於新訂用帳戶中再次部署。

若要移動使用 Azure 備份設定的虛擬機器，請使用下列因應措施：

* 尋找您虛擬機器的位置。
* 尋找具有下列命名模式的資源群組：`AzureBackupRG_<location of your VM>_1`，例如 AzureBackupRG_westus2_1
* 如果是在 Azure 入口網站中，則請選取 [顯示隱藏的類型]
* 如果是在 PowerShell 中，請使用 `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` Cmdlet
* 如果是在 CLI 中，請使用 `az resource list -g AzureBackupRG_<location of your VM>_1`
* 尋找類型為 `Microsoft.Compute/restorePointCollections` 且命名模式為 `AzureBackup_<name of your VM that you're trying to move>_###########` 的資源
* 刪除此資源。 此作業只會刪除立即復原點，而不會刪除保存庫中備份的資料。
* 完成刪除之後，您將能移動您的虛擬機器。 您可以將保存庫和虛擬機器移至目標訂用帳戶。 移動之後，您可以繼續備份而不會遺失資料。
* 如需移動復原服務保存庫以進行備份的相關資訊，請參閱[復原服務限制](#recovery-services-limitations)。

### <a name="virtual-networks-limitations"></a>虛擬網路限制

當您移動虛擬網路時，也必須移動其相依資源。 針對 VPN 閘道，您必須移動 IP 位址、虛擬網路閘道和所有相關聯的連線資源。 區域網路閘道可位於不同的資源群組。

若要移动带网络接口卡的虚拟机，必须移动所有依赖的资源。 必须移动与该网络接口卡对应的虚拟网络、该虚拟网络的所有其他网络接口卡，以及 VPN 网关。

若要移動對等虛擬網路，您必須先停用虛擬網路對等互連。 停用之後，您可以移動虛擬網路。 移動之後，重新啟用虛擬網路對等互連。

如果虛擬網路包含有資源導覽連結的子網路，則無法將虛擬網路移動到其他訂用帳戶。 例如，如果 Azure Cache for Redis 資源部署到子網路，該子網路具有資源導覽連結。

### <a name="app-service-limitations"></a>App Service 限制

根據您是要移動訂用帳戶內的資源還是將資源移到新的訂用帳戶，移動 App Service 資源的限制會有所不同。 如果您的 Web 應用程式使用 App Service 憑證，請參閱 [App Service 憑證限制](#app-service-certificate-limitations)

#### <a name="moving-within-the-same-subscription"></a>在相同的訂用帳戶內移動

在_相同訂用帳戶內_移動 Web 應用程式時，您無法移動第三方 SSL 憑證。 不過，您可以只將 Web 應用程式移至新的資源群組，而不移動第三方 SSL 憑證，而且您應用程式的 SSL 功能仍可正常運作。

如果您想在移動 Web 應用程式時一併移動 SSL 憑證，請遵循下列步驟：

1. 從 Web 應用程式中，刪除第三方憑證，但保留一份您的憑證
2. 移動 Web 應用程式。
3. 將第三方憑證上傳至移動的 Web 應用程式。

#### <a name="moving-across-subscriptions"></a>跨訂用帳戶移動

在_訂用帳戶之間_移動 Web 應用程式時，適用下列限制：

- 目的地資源群組中必須沒有任何已存在的 App Service。 App Service 資源包括：
    - Web Apps
    - App Service 方案
    - 已上傳或已匯的入 SSL 憑證
    - App Service 環境
- 資源群組中的所有 App Service 資源必須一起移動。
- 只能從其最初建立 App Service 資源的資源群組中移動 App Service 資源。 如果 App Service 資源已不存在於其原始的資源群組中，則必須將其移回原始資源群組，然後才可以在訂用帳戶間移動。

如果您不記得原始的資源群組，您可以透過診斷找到它。 Web 應用程式中，選取**診斷並解決問題**。 然後，選取**設定和管理**。

![選取診斷](./media/resource-group-move-resources/select-diagnostics.png)

選取 **移轉選項**。

![選取移轉選項](./media/resource-group-move-resources/select-migration.png)

選取建議的步驟，以移動 web 應用程式的選項。

![選取建議的步驟](./media/resource-group-move-resources/recommended-steps.png)

您會看到移動資源之前應採取的建議的動作。 這些資訊包括 web 應用程式的原始資源群組。

![建議](./media/resource-group-move-resources/recommendations.png)

### <a name="app-service-certificate-limitations"></a>App Service 憑證限制

您可以將 App Service 憑證移至新資源群組或訂用帳戶。 如果您的 App Service 憑證是繫結至 Web 應用程式，在將資源移動到新的訂用帳戶之前，還必須多採取幾個步驟。 請在移動資源前，先從 Web 應用程式刪除 SSL 繫結和私用憑證。 不需要刪除 App Service 憑證，只需要刪除 Web 應用程式中的私用憑證即可。

### <a name="classic-deployment-limitations"></a>傳統部署限制

透過傳統模型所部署的資源移動選項，會根據移動訂用帳戶內的資源還是將資源移到新的訂用帳戶而有所不同。

#### <a name="same-subscription"></a>相同訂用帳戶

將資源從一個資源群組移到相同訂用帳戶內的另一個資源群組時，適用下列限制︰

* 無法移動虛擬網路 (傳統)。
* 虛擬機器 (傳統) 必須與雲端服務一起移動。
* 只有當移動作業包含雲端服務的所有虛擬機器時，才能移動雲端服務。
* 一次只能移動一個雲端服務。
* 一次只能移動一個儲存體帳戶 (傳統)。
* 透過相同的作業，儲存體帳戶 (傳統) 不能與虛擬機器或雲端服務一起移動。

若要將傳統資源移到相同訂用帳戶內的新資源群組，請透過[入口網站](#use-portal)、Azure PowerShell、Azure CLI 或 REST API，使用標準移動作業。 當您移動 Resource Manager 資源時，您會使用相同的作業。

#### <a name="new-subscription"></a>新的訂用帳戶

將資源移到新訂用帳戶時，適用下列限制︰

* 必須透過相同的作業移動訂用帳戶中的所有傳統資源。
* 目標訂用帳戶不得有其他任何傳統資源。
* 只能透過適用於傳統移動的個別 REST API 來要求移動。 將傳統資源移到新的訂用帳戶時，標準 Resource Manager 移動命令無法運作。

若要將傳統資源移到新的訂用帳戶，請使用傳統資源特定的 REST 作業。 若要使用 REST，執行下列步驟：

1. 請檢查來源訂用帳戶是否可以參與跨訂用帳戶移動。 使用以下操作：

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     在请求正文中包括：

   ```json
   {
    "role": "source"
   }
   ```

     驗證作業的回應格式如下︰

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

2. 請檢查目的地訂用帳戶是否可以參與跨訂用帳戶移動。 請使用下列作業：

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     在要求本文中包含：

   ```json
   {
    "role": "target"
   }
   ```

     回應的格式與來源訂用帳戶驗證的格式相同。
3. 如果兩個訂用帳戶都通過驗證，使用下列作業將所有傳統資源從某個訂用帳戶移到另一個訂用帳戶︰

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    在要求本文中包含：

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

這項作業可能需要幾分鐘的時間執行。

### <a name="recovery-services-limitations"></a>復原服務限制

 若要移動「復原服務」保存庫，您必須註冊[有限公開預覽版](../backup/backup-azure-move-recovery-services-vault.md)。

目前每個區域一次可移動一個復原服務保存庫。 您無法移動負責備份 IaaS 虛擬機器中的 Azure 檔案服務、Azure 檔案同步或 SQL 的保存庫。

如果虛擬機器不隨著保存庫移動，目前的虛擬機器復原點都會保留在保存庫中，直到過期為止。 無論虛擬機器是否隨著保存庫移動，您都可以從保存庫中的備份記錄還原虛擬機器。

復原服務保存庫不支援跨訂用帳戶的備份。 如果您在訂用帳戶之間隨著虛擬機器備份資料移動保存庫，您必須將您的虛擬機器移至相同的訂用帳戶，並使用相同的目標資源群組，才可繼續執行備份。

保存庫移動之後，會保留替保存庫定義的備份原則。 在移動之後，必須再次對保存庫設定報告和監視作業。

將虛擬機器移至新的訂用帳戶，而不移動復原服務保存庫：

 1. 暫時停止備份
 1. [刪除還原點](#virtual-machines-limitations)。 此作業只會刪除立即復原點，而不會刪除保存庫中備份的資料。
 1. 將虛擬機器移至新的訂用帳戶
 1. 在該訂用帳戶中的新保存庫下，重新保護該虛擬機器

無法移動用來設定 Azure Site Recovery 相關災害復原的「儲存體」、「網路」或「計算」資源。 舉例來說，假設您已設定將內部部署機器複寫到某個儲存體帳戶 (Storage1)，而想要讓受保護的機器在容錯移轉到 Azure 之後，以連接到虛擬網路 (Network1) 的虛擬機器 (VM1) 身分上線。 您無法跨相同訂用帳戶內的資源群組或跨訂用帳戶來移動任何這些 Azure 資源 - Storage1、VM1 及 Network1。

### <a name="hdinsight-limitations"></a>HDInsight 限制

您可以將 HDInsight 叢集移至新的訂用帳戶或資源群組。 不過，您無法跨訂用帳戶來移動連結至 HDInsight 叢集的網路資源 (例如虛擬網路、NIC 或負載平衡器)。 此外，您無法將已連結至叢集虛擬機器的 NIC 移至新的資源群組。

將 HDInsight 叢集移至新的訂用帳戶時，請先移動其他資源 (例如儲存體帳戶)。 然後，移動 HDInsight 叢集本身。

## <a name="checklist-before-moving-resources"></a>移動資源前的檢查清單

在移動資源之前，有幾個重要步驟需要進行。 藉由驗證這些條件，您可以避免錯誤。

1. 來源和目的地訂用帳戶必須為作用中。 如果您在啟用已停用的帳戶時遇到問題，請[建立 Azure 支援要求](../azure-supportability/how-to-create-azure-support-request.md)。 針對問題類型選取 [訂用帳戶管理]。

1. 來源和目的地的訂用帳戶必須存在於相同的 [Azure Active Directory 租用戶](../active-directory/develop/quickstart-create-new-tenant.md)內。 若要檢查這兩個訂用帳戶都有相同的租用戶識別碼，請使用 Azure PowerShell 或 Azure CLI。

   如果是 Azure PowerShell，請使用：

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   對於 Azure CLI，請使用：

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   如果來源和目的地訂用帳戶的租用戶識別碼不相同，請使用下列方法來協調租用戶識別碼：

   * [將 Azure 訂用帳戶的擁有權轉移給另一個帳戶](../billing/billing-subscription-transfer.md)
   * [如何將 Azure 訂用帳戶關聯或新增至 Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. 必須針對要移動之資源的資源提供者註冊其目的地訂用帳戶。 否則，您會收到錯誤，指出 **未針對資源類型註冊訂用帳戶**。 將資源移至新的訂用帳戶時，可能會因為不曾以指定的資源類型使用過該訂用帳戶，因而出現此錯誤。

   針對 PowerShell，使用下列命令來取得註冊狀態：

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   若要註冊資源提供者，請使用：

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   針對 Azure CLI，使用下列命令來取得註冊狀態：

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   若要註冊資源提供者，請使用：

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. 移動資源的帳戶至少必須有下列權限：

   * 來源資源群組上的 **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action**。
   * 來源資源群組上的 **Microsoft.Resources/subscriptions/resourceGroups/write**。

1. 移動資源之前，請針對您要將資源移入的訂用帳戶，檢查其訂用帳戶配額。 如果移動資源表示訂用帳戶會超出限制，那麼您必須檢閱您是否可以要求增加配額。 如需限制清單和如何要求增加配額的資訊，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-subscription-service-limits.md)。

1. 可能的話，請將大型移動細分為個別的移動作業。 單一作業超過 800 個資源時，Resource Manager 會立即傳回錯誤。 不過，移動少於 800 個資源也可能因為逾時而失敗。

1. 服務必須啟用移動資源的功能。 若要判斷移動是否會成功，請[驗證您的移動要求](#validate-move)。 請參閱本文的以下小節，了解[哪些服務可移動資源](#services-that-can-be-moved)，以及[哪些服務無法移動資源](#services-that-cannot-be-moved)。

## <a name="validate-move"></a>驗證移動

[驗證移動作業](/rest/api/resources/resources/validatemoveresources)可讓您直接測試移動案例，而不需要實際移動資源。 您可以使用這項作業來判斷移動是否會成功。 若要執行這項作業，您需要：

* 來源資源群組的名稱
* 目標資源群組的資源識別碼
* 要移動的每個資源所具備的資源識別碼
* 帳戶的[存取權杖](/rest/api/azure/#acquire-an-access-token)

傳送下列要求：

```
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2018-02-01
Authorization: Bearer <access-token>
Content-type: application/json
```

使用要求本文：

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

如果要求的格式正確，此作業將會傳回：

```
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

202 狀態碼指出已接受驗證要求，但尚未判斷移動作業是否會成功。 `location` 值包含一個 URL，可讓您用來檢查長時間執行作業的狀態。  

若要檢查狀態，請傳送下列要求：

```
GET <location-url>
Authorization: Bearer <access-token>
```

當作業仍在執行時，您會持續收到 202 狀態碼。 請等候 `retry-after` 值中指出的秒數，再重新嘗試。 如果移動作業驗證成功，您會收到 204 狀態碼。 如果移動驗證失敗，則會收到錯誤訊息，例如：

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="move-resources"></a>移動資源

### <a name="a-nameuse-portal-by-using-azure-portal"></a><a name="use-portal" />使用 Azure 入口網站

若要移動資源，請選取具有這些資源的資源群組，然後選取 [移動] 按鈕。

![移動資源](./media/resource-group-move-resources/select-move.png)

選擇將資源移動到新的資源群組或新的訂用帳戶。

選取要移動的資源和目的地資源群組。 認可您需要更新這些資源的指令碼，然後選取 [確定] 。 如果您在上一個步驟中選取了 [編輯訂用帳戶] 圖示，則也必須選取目的地訂用帳戶。

![選取目的地](./media/resource-group-move-resources/select-destination.png)

在 [通知] 中，您會看到移動作業正在執行。

![顯示移動狀態](./media/resource-group-move-resources/show-status.png)

完成後，您會收到結果的通知。

![顯示移動結果](./media/resource-group-move-resources/show-result.png)

### <a name="by-using-azure-powershell"></a>使用 Azure PowerShell

若要將現有的資源移動到另一個資源群組或訂用帳戶，請使用 [Move-AzResource](/powershell/module/az.resources/move-azresource) 命令。 下列範例示範了如何將多個資源移動到新的資源群組。

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

若要移動到新的訂用帳戶，請包含 `DestinationSubscriptionId`參數的值。

### <a name="by-using-azure-cli"></a>使用 Azure CLI

若要將現有的資源移動到另一個資源群組或訂用帳戶，請使用 [az resource move](/cli/azure/resource?view=azure-cli-latest#az-resource-move) 命令。 提供要移動之資源的資源識別碼。 下列範例示範了如何將多個資源移動到新的資源群組。 請在 `--ids` 參數中，為要移動的資源識別碼提供以空格分隔的清單。

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

若要移動到新的訂用帳戶，請提供 `--destination-subscription-id` 參數。

### <a name="by-using-rest-api"></a>使用 REST API

若要將現有的資源移動到另一個資源群組或訂用帳戶，請執行：

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

在要求主體中，您可以指定目標資源群組以及要移動的資源。 如需有關 REST 移動作業的詳細資訊，請參閱 [移動資源](/rest/api/resources/Resources/MoveResources)。

## <a name="next-steps"></a>後續步驟

* 若要了解管理资源所需的 PowerShell cmdlet，请参阅[将 Azure PowerShell 与资源管理器配合使用](manage-resources-powershell.md)。
* 若要了解管理资源所需的 Azure CLI 命令，请参阅[将 Azure CLI 与资源管理器配合使用](manage-resources-cli.md)。
* 若要了解用於管理訂用帳戶的入口網站功能，請參閱 [使用 Azure 入口網站來管理資源](resource-group-portal.md)。
* 若要了解如何將邏輯組織套用到您的資源，請參閱 [使用標記來組織您的資源](resource-group-using-tags.md)。
