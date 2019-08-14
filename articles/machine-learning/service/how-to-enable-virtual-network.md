---
title: 在虛擬網路中執行實驗和推斷
titleSuffix: Azure Machine Learning service
description: 在 Azure 虛擬網路內執行機器學習實驗和推斷的安全。 瞭解如何為模型定型建立計算目標, 以及如何在虛擬網路內執行推斷。 瞭解受保護虛擬網路的需求, 例如需要輸入和輸出埠。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 08/05/2019
ms.openlocfilehash: bd70957671c11137465225aa3bbb046b12a2c650
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966909"
---
# <a name="run-experiments-and-inference-securely-within-an-azure-virtual-network"></a>在 Azure 虛擬網路內安全地執行實驗和推斷

在本文中, 您將瞭解如何在虛擬網路內執行實驗和推斷, 或模型評分。 虛擬網路充當安全邊界，將 Azure 資源與公用網際網路隔離。 您也可以將 Azure 虛擬網路加入到您的內部部署網路， 藉由加入網路, 您可以安全地定型模型, 並存取已部署的模型以進行推斷。 推斷 (或模型計分) 是將部署的模型用於預測的階段, 最常見的是生產資料。

Azure Machine Learning 服務依賴其他 Azure 服務來處理計算資源。 計算資源或計算目標是用來定型和部署模型。 目標可以在虛擬網路內建立。 例如, 您可以使用 Microsoft 資料科學虛擬機器來定型模型, 然後將模型部署至 Azure Kubernetes Service (AKS)。 如需虛擬網路的詳細資訊, 請參閱[Azure 虛擬網路總覽](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。

本文提供有關「基本」或「實驗性」使用案例的「*高級安全性設定*」、「不需要」的詳細資訊。 本文的某些章節會提供各種案例的設定資訊。 您不需要依序或完整完成指示。

## <a name="prerequisites"></a>先決條件

如果您還沒有 Azure Machine Learning 服務[工作區](how-to-manage-workspace.md), 請建立一個。 本文假設您已熟悉 Azure 虛擬網路服務和 IP 網路的一般功能。 本文也假設您已建立要與您的計算資源搭配使用的虛擬網路和子網。 如果您不熟悉 Azure 虛擬網路服務, 可以在下列文章中深入瞭解:

* [IP 定址](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)
* [安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview)
* [快速入門：建立虛擬網路](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)
* [篩選網路流量](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="use-a-storage-account-for-your-workspace"></a>針對您的工作區使用儲存體帳戶

若要在虛擬網路中使用 Azure 儲存體帳戶作為工作區, 請執行下列動作:

1. 建立虛擬網路背後的實驗運算實例 (例如 Machine Learning Compute 實例), 或將實驗運算實例附加至工作區 (例如, HDInsight 叢集或虛擬機器)。 

   如需詳細資訊, 請參閱本文中的「使用 Machine Learning Compute 實例」和「使用虛擬機器或 HDInsight 叢集」章節。

1. 在 Azure 入口網站中, 移至附加至工作區的儲存體。 

   ![附加至 Azure Machine Learning 服務工作區的儲存體](./media/how-to-enable-virtual-network/workspace-storage.png)

1. 在 [ **Azure 儲存體**] 頁面上, 選取 [__防火牆和虛擬網路__]。 

   ![[Azure 儲存體] 頁面上的 [防火牆和虛擬網路] 區域 Azure 入口網站](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. 在 [__防火牆和虛擬網路__] 頁面上, 執行下列動作:
    - 選取 [選取的網路]。
    - 在 [__虛擬網路__] 底下, 選取 [__新增現有的虛擬網路__] 連結。 此動作會新增您的實驗計算實例所在的虛擬網路 (請參閱步驟 1)。
    - 選取 [__允許受信任的 Microsoft 服務存取此儲存體帳戶__] 核取方塊。

   ![Azure 入口網站中的 [防火牆和虛擬網路] 窗格](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)

1. 當您執行實驗時, 請在您的實驗程式碼中, 將回合設定變更為使用 Azure Blob 儲存體:

    ```python
    run_config.source_directory_data_store = "workspaceblobstore"
    ```

> [!IMPORTANT]
> 您可以將 Azure Machine Learning 服務的_預設儲存體帳戶_放在虛擬網路中 _, 僅供實驗_之用。
>
> 您可以將_非預設儲存體帳戶_放在虛擬網路中 _, 僅供實驗_之用。
>
> 用於_推斷_的預設和非預設儲存體帳戶, 都必須具有_儲存體帳戶不受限制的存取權_。
>
> 如果您不確定是否已修改這些設定, 請參閱[設定 Azure 儲存體防火牆和虛擬網路](https://docs.microsoft.com/azure/storage/common/storage-network-security)中的「變更預設網路存取規則」一節。 請遵循指示, 以允許在推斷期間從所有網路存取, 或模型計分。

## <a name="use-a-key-vault-instance-with-your-workspace"></a>搭配您的工作區使用 key vault 實例

與工作區相關聯的金鑰保存庫實例會由 Azure Machine Learning 服務用來儲存下列認證:
* 相關聯的儲存體帳戶連接字串
* Azure 容器存放庫實例的密碼
* 資料存放區的連接字串

若要在虛擬網路背後的 Azure Key Vault 使用 Azure Machine Learning 測試功能, 請執行下列動作:
1. 移至與工作區相關聯的金鑰保存庫。 

   ![與 Azure Machine Learning 服務工作區相關聯的金鑰保存庫](./media/how-to-enable-virtual-network/workspace-key-vault.png)

1. 在 [ **Key Vault** ] 頁面上, 選取左窗格中的 [__防火牆和虛擬網路__]。 

   ![[Key Vault] 窗格中的 [防火牆和虛擬網路] 區段](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. 在 [__防火牆和虛擬網路__] 頁面上, 執行下列動作:
    - 在 [允許存取來源] 之下，選取 [選取的網路]。
    - 在 [__虛擬網路__] 底下, 選取 [__新增現有的虛擬網路__], 以新增您的實驗計算實例所在的虛擬網路。
    - 在 [__允許信任的 Microsoft 服務略過此防火牆__] 底下, 選取 __[是]__ 。

   ![[Key Vault] 窗格中的 [防火牆和虛擬網路] 區段](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)

## <a name="use-a-machine-learning-compute-instance"></a>使用 Machine Learning Compute 實例

若要在虛擬網路中使用 Azure Machine Learning 計算實例, 請考慮下列網路需求:

- 虛擬網路必須在與 Azure Machine Learning 服務工作區相同的訂用帳戶和區域中。

- 為計算叢集指定的子網必須有足夠的未指派 IP 位址, 以容納叢集目標的 Vm 數目。 如果子網路沒有足夠的未指派 IP 位址，則將局部配置叢集。

- 如果您打算透過限制流量來保護虛擬網路, 請讓計算服務的一些埠保持開啟。 如需詳細資訊，請參閱[必要連接埠](#mlcports)一節。

- 查看虛擬網路的訂用帳戶或資源群組的安全性原則或鎖定, 是否限制管理虛擬網路的許可權。

- 如果您要將多個計算叢集放在一個虛擬網路中, 您可能需要要求增加一或多個資源的配額。

    Machine Learning Compute 實例會自動在包含虛擬網路的資源群組中配置額外的網路資源。 針對每個計算叢集, 服務會配置下列資源:

    - 一個網路安全性群組

    - 一個公用 IP 位址

    - 一個負載平衡器

  這些資源會被訂用帳戶的[資源配額](https://docs.microsoft.com/azure/azure-subscription-service-limits)所限制。

### <a id="mlcports"></a> 所需連接埠

Machine Learning Compute 目前使用 Azure Batch 服務將 VM 佈建在指定的虛擬網路中。 子網路必須允許來自 Batch 服務的輸入通訊。 您可以使用此通訊來排程 Machine Learning Compute 節點上的執行, 以及與 Azure 儲存體和其他資源進行通訊。 Batch 服務會在連接至 Vm 的網路介面 (Nic) 層級新增網路安全性群組 (Nsg)。 這些 NSG 會自動設定輸入和輸出規則，以允許下列流量：

- 埠29876和29877上的輸入 TCP 流量, 來自__BatchNodeManagement__的__服務標記__。

    ![使用 BatchNodeManagement 服務標記的輸入規則](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- 選擇性埠22上的輸入 TCP 流量, 以允許遠端存取。 只有當您想要在公用 IP 上使用 SSH 進行連接時, 才使用此埠。

- 任何連接埠上傳至虛擬網路的輸出流量。

- 任何連接埠上傳至網際網路的輸出流量。

如果您要在 Batch 設定的 NSG 中修改或新增輸入或輸出規則，請謹慎操作。 如果 NSG 封鎖與計算節點的通訊, 則計算服務會將計算節點的狀態設定為 [無法使用]。

您不需要在子網層級指定 Nsg, 因為 Azure Batch 服務會設定自己的 Nsg。 不過, 如果指定的子網有相關聯的 Nsg 或防火牆, 請設定前面所述的輸入和輸出安全性規則。

下列影像顯示 Azure 入口網站中的 NSG 規則設定:

![Machine Learning Compute 的輸入 NSG 規則](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![Machine Learning Compute 的輸出 NSG 規則](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a>限制來自虛擬網路的輸出連線能力

如果您不想要使用預設輸出規則, 而您想要限制虛擬網路的輸出存取, 請執行下列動作:

- 使用 NSG 規則拒絕連出網際網路連線。

- 將輸出流量限制為下列各項:
   - Azure 儲存體, 方法是使用儲存體的__服務__標籤 Region_Name (例如 EastUS) __。__
   - Azure Container Registry, 方法是使用__AzureContainerRegistry. Region_Name__的__服務標記__(例如 AzureContainerRegistry. EastUS)
   - Azure Machine Learning 服務, 方法是使用__AzureMachineLearning__的__服務標記__

下圖顯示 Azure 入口網站中的 NSG 規則設定:

![Machine Learning Compute 的輸出 NSG 規則](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)

### <a name="user-defined-routes-for-forced-tunneling"></a>強制通道的使用者定義路由

如果您搭配 Machine Learning Compute 使用強制通道, 請將[使用者定義的路由 (udr)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)新增至包含計算資源的子網。

* 在資源所在的區域中, 為 Azure Batch 服務所使用的每個 IP 位址建立一個 UDR。 這些 Udr 可讓 Batch 服務與計算節點進行通訊, 以進行工作排程。 若要取得 Batch 服務的 IP 位址清單, 請聯絡 Azure 支援。

* 您的內部部署網路應用裝置不得封鎖對 Azure 儲存體的輸出流量。 具體而言, url 的格式`<account>.table.core.windows.net`為、 `<account>.queue.core.windows.net`和`<account>.blob.core.windows.net`。

當您新增 Udr 時, 請定義每個相關批次 IP 位址首碼的路由, 並將 __[下一個躍點類型]__ 設定為 [__網際網路__]。 下圖顯示此 UDR 在 Azure 入口網站中的範例:

![位址首碼的 UDR 範例](./media/how-to-enable-virtual-network/user-defined-route.png)

如需詳細資訊, 請參閱[在虛擬網路中建立 Azure Batch 集](../../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)區。

### <a name="create-a-machine-learning-compute-cluster-in-a-virtual-network"></a>在虛擬網路中建立 Machine Learning Compute 叢集

若要建立 Machine Learning Compute 叢集, 請執行下列動作:

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 Azure Machine Learning 服務工作區。

1. 在 [__應用程式__] 區段中, 選取 [__計算__], 然後選取 [__新增計算__]。

1. 若要將此計算資源設定為使用虛擬網路, 請執行下列動作:

    a. 針對 [__網路__設定], 選取 [ __Advanced__]。

    b. 在 [__資源群組__] 下拉式清單中, 選取包含虛擬網路的資源群組。

    c. 在 [__虛擬網路__] 下拉式清單中, 選取包含子網的虛擬網路。

    d. 在 [__子網__] 下拉式清單中, 選取要使用的子網。

   ![Machine Learning Compute 的虛擬網路設定](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

您也可以透過使用 Azure Machine Learning SDK 建立 Machine Learning Compute 叢集。 下列程式碼會在名為 `mynetwork` 的虛擬網路其 `default` 子網路中建立新的 Machine Learning Compute 叢集：

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")

    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

當建立程式完成時, 您可以在實驗中使用叢集來定型模型。 如需詳細資訊，請參閱[選取與使用定型的計算目標](how-to-set-up-training-targets.md)。

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>使用虛擬機器或 HDInsight 叢集

若要在虛擬網路中使用虛擬機器或 Azure HDInsight 叢集與您的工作區, 請執行下列動作:

1. 使用 Azure 入口網站或 Azure CLI 建立 VM 或 HDInsight 叢集, 並將叢集放在 Azure 虛擬網路中。 如需詳細資訊，請參閱下列文章：
    * [建立和管理適用於 Linux VM 的 Azure 虛擬網路](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [使用 Azure 虛擬網路延伸 HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. 若要允許 Azure Machine Learning 服務與 VM 或叢集上的 SSH 埠通訊, 請設定網路安全性群組的來源專案。 SSH 連接埠通常是連接埠 22。 若要允許來自此來源的流量, 請執行下列動作:

    * 在 [__來源__] 下拉式清單中, 選取 [__服務標記__]。

    * 在 [__來源服務標記__] 下拉式清單中, 選取 [ __AzureMachineLearning__]。

    * 在 [__來源埠範圍__] 下拉式清單中, 選取 __*__ []。

    * 在 [__目的地__] 下拉式清單中, 選取 [__任何__]。

    * 在 [__目的地埠範圍__] 下拉式清單中, 選取 [ __22__]。

    * 在 [__通訊協定__] 底下, 選取 [__任何__]。

    * 在 [__動作__] 底下, 選取 [__允許__]。

   ![在虛擬網路內的 VM 或 HDInsight 叢集上進行實驗的輸入規則](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    保留網路安全性群組的預設輸出規則。 如需詳細資訊，請參閱[安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)中的預設安全性規則一節。

    如果您不想要使用預設輸出規則, 而想要限制虛擬網路的輸出存取, 請參閱[限制來自虛擬網路的輸出](#limiting-outbound-from-vnet)連線一節。

1. 將 VM 或 HDInsight 叢集附加至 Azure Machine Learning 服務工作區。 如需詳細資訊，請參閱[為模型定型設定計算目標](how-to-set-up-training-targets.md)。

> [!IMPORTANT]
> Azure Machine Learning 服務僅支援執行 Ubuntu 的虛擬機器。

## <a name="use-azure-kubernetes-service-aks"></a>使用 Azure Kubernetes Service (AKS)

若要將虛擬網路中的 AKS 新增至您的工作區, 請執行下列動作:

> [!IMPORTANT]
> 開始下列程式之前, 請先檢查必要條件並規劃叢集的 IP 位址。 如需詳細資訊，請參閱[在 Azure Kubernetes Service (AKS) 中設定進階網路](https://docs.microsoft.com/azure/aks/configure-advanced-networking)。
>
> 為 NSG 保留預設輸出規則。 如需詳細資訊，請參閱[安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)中的預設安全性規則一節。
>
> AKS 實例和 Azure 虛擬網路應位於相同的區域。

1. 在[Azure 入口網站](https://portal.azure.com)中, 請確定控制虛擬網路的 NSG 具有使用__AzureMachineLearning__做為**來源**而 Azure Machine Learning 服務啟用的輸入規則。

    ![Azure Machine Learning 服務新增計算窗格](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)

1. 選取您的 Azure Machine Learning 服務工作區。

1. 在 [__應用程式__] 區段中, 選取 [__計算__], 然後選取 [__新增計算__]。

1. 若要將此計算資源設定為使用虛擬網路, 請執行下列動作:

    - 針對 [__網路__設定], 選取 [ __Advanced__]。

    - 在 [__資源群組__] 下拉式清單中, 選取包含虛擬網路的資源群組。

    - 在 [__虛擬網路__] 下拉式清單中, 選取包含子網的虛擬網路。

    - 在 [__子網__] 下拉式清單中, 選取 [子網]。

    - 在 [ __Kubernetes 服務位址範圍__] 方塊中, 輸入 Kubernetes 服務位址範圍。 此位址範圍會使用無類別網域間路由 (CIDR) 標記法 IP 範圍來定義可用於叢集的 IP 位址。 它不得與任何子網 IP 範圍重迭 (例如 10.0.0.0/16)。

    - 在 [ __KUBERNETES dns 服務 ip 位址__] 方塊中, 輸入 Kubernetes DNS 服務的 ip 位址。 此 IP 位址會指派給 Kubernetes DNS 服務。 它必須在 Kubernetes 服務位址範圍內 (例如 10.0.0.10)。

    - 在 [ __docker 橋接器位址__] 方塊中, 輸入 docker 橋接器位址。 此 IP 位址會指派給 Docker 橋接器， 不得位於任何子網 IP 範圍或 Kubernetes 服務位址範圍 (例如, 172.17.0.1/16)。

   ![Azure Machine Learning 服務：Machine Learning Compute 虛擬網路設定](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. 請確定控制虛擬網路的 NSG 群組具有針對評分端點啟用的輸入安全性規則, 以便可從虛擬網路外部呼叫。

    ![輸入安全性規則](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)

    > [!TIP]
    > 如果您在虛擬網路中已經有 AKS 叢集，您可以將它附加到工作區。 如需詳細資訊，請參閱[部署至 AKS 的方式](how-to-deploy-to-aks.md)。

您也可以使用 Azure Machine Learning SDK, 在虛擬網路中新增 AKS。 下列程式碼會在名為`default` `mynetwork`的虛擬網路子網中建立新的 AKS 實例:

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

當建立程式完成時, 您可以在虛擬網路背後的 AKS 叢集上執行推斷或模型評分。 如需詳細資訊，請參閱[部署至 AKS 的方式](how-to-deploy-to-aks.md)。

## <a name="next-steps"></a>後續步驟

* [設定定型環境](how-to-set-up-training-targets.md)
* [部署模型的位置](how-to-deploy-and-where.md)
* [使用 SSL 安全地部署模型](how-to-secure-web-service.md)

