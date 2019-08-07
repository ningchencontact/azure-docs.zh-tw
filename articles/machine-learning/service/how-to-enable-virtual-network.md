---
title: 在虛擬網路中執行實驗和推斷
titleSuffix: Azure Machine Learning service
description: 在 Azure 虛擬網路中安全地執行機器學習實驗和推斷。 了解如何為模型定型建立計算目標，和如何在虛擬網路中推斷。 了解受保護虛擬網路的需求，例如需要輸入和輸出連接埠。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 08/05/2019
ms.openlocfilehash: 7c4c4ff611b35cac9aa8be1a9697a0d11bc4dc8b
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815956"
---
# <a name="securely-run-experiments-and-inference-inside-an-azure-virtual-network"></a>在 Azure 虛擬網路內安全地執行實驗和推斷

在本文中, 您將瞭解如何在虛擬網路內執行實驗和推斷。 虛擬網路充當安全邊界，將 Azure 資源與公用網際網路隔離。 您也可以將 Azure 虛擬網路加入到您的內部部署網路， 它可讓您安全地定型模型, 並存取已部署的模型以進行推斷。 推斷 (或模型計分) 是已部署的模型用於預測的階段, 最常見的是生產資料。

Azure Machine Learning 服務依賴其他 Azure 服務來處理計算資源。 計算資源 (計算目標) 用於定型和部署模型。 這些計算目標可建立在虛擬網路內部。 例如，您可以使用 Microsoft 資料科學虛擬機器來定型模型，然後將模型部署到 Azure Kubernetes Service (AKS)。 如需虛擬網路的詳細資訊，請參閱 [Azure 虛擬網路概觀](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。

本文提供有關「高級」**安全性設定**的詳細資訊, 並不是基本或實驗性使用案例的必要專案。 本文中的各節提供不同案例的設定資訊, 但不需要依序或完整完成。

## <a name="prerequisites"></a>先決條件

如果您還沒有 Azure Machine Learning 服務[工作區](setup-create-workspace.md), 請建立一個。 本檔假設您已熟悉 Azure 虛擬網路以及一般的 IP 網路功能。 本檔也假設您已建立要與計算資源搭配使用的虛擬網路和子網。 如果您不熟悉 Azure 虛擬網路, 請閱讀下列文章以瞭解服務:

* [IP 定址](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)
* [安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview)
* [快速入門：建立虛擬網路](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)
* [篩選網路流量](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="storage-account-for-your-workspace"></a>您工作區的儲存體帳戶

若要在虛擬網路中使用工作區的預設 Azure 儲存體帳戶, 請使用下列步驟:

1. 建立實驗計算, 例如 Machine Learning Compute 虛擬網路背後, 或將實驗計算附加至工作區, 例如 HDInsight 叢集或虛擬機器。 如需詳細資訊, 請參閱本檔中的[使用 Machine Learning Compute](#use-machine-learning-compute)和[使用虛擬機器或 HDInsight](#use-a-virtual-machine-or-hdinsight-cluster)叢集章節
2. 移至附加至工作區的儲存體。 ![顯示附加至 Azure Machine Learning 服務工作區 Azure 儲存體的 Azure 入口網站影像](./media/how-to-enable-virtual-network/workspace-storage.png)
3. 在 [Azure 儲存體] 頁面上, 選取 [__防火牆和虛擬網路__]。 ![[Azure 儲存體] 頁面上顯示 [防火牆和虛擬網路] 區段之 Azure 入口網站的影像](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)
4. 在 [__防火牆和虛擬網路__] 頁面上, 選取下列專案:
    - 選取 [選取的網路]。
    - 在 [__虛擬網路__] 底下, 選取 [__新增現有的虛擬網路__], 以新增您的實驗計算所在的虛擬網路。 (請參閱步驟 1)。
    - 選取 [__允許信任的 Microsoft 服務存取此儲存體帳戶__]。
![Azure 入口網站顯示 [防火牆和虛擬網路] 頁面的影像 Azure 儲存體](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)

5. 在執行實驗時, 請在您的實驗程式碼中, 將回合設定變更為使用 blob 儲存體:
    ```python
    run_config.source_directory_data_store = "workspaceblobstore"
    ```

> [!IMPORTANT]
> __只有在進行實驗時__, 您 Azure Machine Learning 服務的__預設儲存體帳戶__才可以放在虛擬網路中。
>
> 針對__實驗的非預設儲存體帳戶__, 或如果您使用儲存體帳戶進行__推斷__, 您必須具有__儲存體帳戶的不受限制存取權__。
>
> 如果您不確定是否修改了這些設定，請參閱[設定 Azure 儲存體防火牆和虛擬網路](https://docs.microsoft.com/azure/storage/common/storage-network-security)中的__預設網路存取規則__。 使用步驟, 允許在推斷或模型評分期間從所有網路存取。

## <a name="key-vault-for-your-workspace"></a>工作區的金鑰保存庫

Azure Machine Learning 服務使用與工作區相關聯的 Key Vault 實例, 來儲存各種類型的認證:
* 相關聯的儲存體帳戶連接字串
* Azure 容器存放庫實例的密碼
* 資料存放區的連接字串。

若要搭配虛擬網路背後的 Key Vault 使用 Azure Machine Learning 測試功能, 請使用下列步驟:
1. 移至與工作區相關聯的 Key Vault。 ![Azure 入口網站的影像, 顯示與 Azure Machine Learning 服務工作區相關聯 Key Vault](./media/how-to-enable-virtual-network/workspace-key-vault.png)
2. 在 [Key Vault] 頁面上, 選取 [__防火牆和虛擬網路__] 區段。 ![[Key Vault] 頁面上顯示 [防火牆和虛擬網路] 區段之 Azure 入口網站的影像](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)
3. 在 [__防火牆和虛擬網路__] 頁面上, 選取下列專案:
    - 選取 [選取的網路]。
    - 在 [__虛擬網路__] 底下, 選取 [__新增現有的虛擬網路__], 以新增您的實驗計算所在的虛擬網路。
    - 選取 [__允許信任的 Microsoft 服務略過此防火牆__]。
![Azure 入口網站顯示 [防火牆和虛擬網路] 頁面的影像 Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)


## <a name="use-machine-learning-compute"></a>使用 Machine Learning Compute

若要在虛擬網路中使用 Azure Machine Learning 計算, 請考慮下列有關網路需求的資訊:

- 虛擬網路必須在與 Azure Machine Learning 服務工作區相同的訂用帳戶和區域中。

- 為計算叢集指定的子網必須有足夠的未指派 IP 位址, 以容納叢集目標的 Vm 數目。 如果子網路沒有足夠的未指派 IP 位址，則將局部配置叢集。

- 如果您打算透過限制流量來保護虛擬網路, 請讓計算服務的一些埠保持開啟。 如需詳細資訊，請參閱[必要連接埠](#mlcports)。

- 確認您對虛擬網路其訂用帳戶或資源群組的安全性原則或鎖定是否限制了管理虛擬網路的權限。

- 如果您要將多個計算叢集放在一個虛擬網路中, 您可能需要要求增加一或多個資源的配額。

    Azure Machine Learning 計算會自動在包含虛擬網路的資源群組中配置額外的網路資源。 針對每個計算叢集, 服務會配置下列資源:

    - 一個網路安全性群組

    - 一個公用 IP 位址

    - 一個負載平衡器

  這些資源會被訂用帳戶的[資源配額](https://docs.microsoft.com/azure/azure-subscription-service-limits)所限制。

### <a id="mlcports"></a> 所需連接埠

Machine Learning Compute 目前使用 Azure Batch 服務將 VM 佈建在指定的虛擬網路中。 子網路必須允許來自 Batch 服務的輸入通訊。 此通訊用於排程在 Machine Learning Compute 節點上的執行，並與 Azure 儲存體和其他資源進行通訊。 Batch 會在連接至 Vm 的網路介面 (**NIC**) 層級新增網路安全性群組 (**NSG**)。 這些 NSG 會自動設定輸入和輸出規則，以允許下列流量：

- 埠29876和29877上的輸入 TCP 流量, 來自__BatchNodeManagement__的__服務標記__。

    ![Azure 入口網站的影像, 顯示使用 BatchNodeManagement 服務標籤的輸入規則](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- 選擇性埠22上的輸入 TCP 流量, 以允許遠端存取。 只有當您想要在公用 IP 上使用 SSH 連線時, 才需要此埠。

- 任何連接埠上傳至虛擬網路的輸出流量。

- 任何連接埠上傳至網際網路的輸出流量。

如果您要在 Batch 設定的 NSG 中修改或新增輸入/輸出規則，請謹慎操作。 如果 NSG 封鎖與計算節點的通訊, 則計算服務會將計算節點的狀態設定為 [無法使用]。

您不需要在子網層級指定 Nsg, 因為 Azure Batch 服務會設定自己的 Nsg。 不過，如果指定的子網路有相關聯的 NSG 和 (或) 防火牆，請設定輸入和輸出安全性規則，如上所述。

下列螢幕擷取畫面顯示 NSG 規則設定在 Azure 入口網站中的外觀:

![Machine Learning Compute 的輸入 NSG 規則螢幕擷取畫面](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![Machine Learning Compute 的輸出 NSG 規則螢幕擷取畫面](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a>限制來自虛擬網路的輸出連線能力

如果您不想要使用預設輸出規則, 而且想要限制虛擬網路的輸出存取, 請使用下列步驟:

- 使用 NSG 規則拒絕連出網際網路連線

- 將輸出流量限制為 Azure 儲存體 (使用儲存體的__服務__標籤 __。 Region_Name__例如 EastUS)、Azure Container Registry (使用 AzureContainerRegistry 的__服務標記__ __. Region_Name__ ex AzureContainerRegistry. EastUS) 和 Azure Machine Learning 服務 (使用__AzureMachineLearning__的__服務__標籤)

下列螢幕擷取畫面顯示 NSG 規則設定在 Azure 入口網站中的外觀:

![Machine Learning Compute 的輸出 NSG 規則螢幕擷取畫面](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)

### <a name="user-defined-routes-for-forced-tunneling"></a>強制通道的使用者定義路由

如果您使用強制通道搭配 Azure Machine Learning 計算, 則必須將[使用者定義的路由 (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)新增至包含計算資源的子網。

* 您必須在資源所在的區域中, 為 Azure Batch 服務所使用的每個 IP 位址建立使用者定義的路由。 這些 Udr 可讓 Batch 服務與計算節點進行通訊, 以進行工作排程。 若要取得 Batch 服務的 IP 位址清單, 請聯絡 Azure 支援。

* 要 Azure 儲存體的輸出流量 (具體而言, 您的`<account>.table.core.windows.net`內部`<account>.queue.core.windows.net`部署網路`<account>.blob.core.windows.net`應用裝置不得封鎖表單、和的 url)。

當您新增使用者定義的路由時, 請定義每個相關批次 IP 位址首碼的路由, 並將 __[下一個躍點類型]__ 設定為 [__網際網路__]。 下圖顯示此 UDR 在 Azure 入口網站中的範例:

![位址首碼的使用者定義路由範例](./media/how-to-enable-virtual-network/user-defined-route.png)

如需詳細資訊, 請參閱在[虛擬網路中建立 Azure Batch 集區一](../../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)文。

### <a name="create-machine-learning-compute-in-a-virtual-network"></a>在虛擬網路中建立 Machine Learning Compute

若要使用 Azure 入口網站建立 Azure Machine Learning 計算叢集, 請使用下列步驟:

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 Azure Machine Learning 服務工作區。

1. 在 [應用程式] 區段中，選取 [計算]。 然後，選取 [新增計算]。

1. 若要設定此計算資源以使用虛擬網路，使用下列選項：

    - __網路組態__：選取 [進階]。

    - __资源组__：選取包含虛擬網路的資源群組。

    - __虛擬網路__：選取包含子網路的虛擬網路。

    - __子網路__：選取要使用的子網路。

   ![顯示機器學習計算其虛擬網路設定的螢幕擷取畫面](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

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

    # Wait for the cluster to complete, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

當建立程式完成時, 您可以在實驗中使用叢集來定型模型。 如需詳細資訊，請參閱[選取與使用定型的計算目標](how-to-set-up-training-targets.md)。

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>使用虛擬機器或 HDInsight 叢集

若要在虛擬網路中使用虛擬機器或 Azure HDInsight 叢集與您的工作區, 請使用下列步驟:

1. 透過使用 Azure 入口網站 或 Azure CLI 建立 VM 或 HDInsight 叢集，並將它放在 Azure 虛擬網路中。 如需詳細資訊，請參閱下列文件：
    * [建立和管理適用於 Linux VM 的 Azure 虛擬網路](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [使用 Azure 虛擬網路延伸 HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. 若要允許 Azure Machine Learning 服務與 VM 或叢集上的 SSH 埠通訊, 您必須設定網路安全性群組的來源專案。 SSH 連接埠通常是連接埠 22。 請使用下列步驟來允許來自此來源的流量:

    * __來源__：選取 [服務標記]。

    * __來源服務標記__：選取 [AzureMachineLearning]。

    * __來源連接埠範圍__:選取 __[*]__ 。

    * __目的地__:選取 [任何]。

    * __目的地連接埠範圍__：選取 [22]。

    * __通訊協定__：選取 [任何]。

    * __動作__：選取 [允許]。

   ![用於對虛擬網路內的 VM 或 HDInsight 叢集進行實驗的輸入規則螢幕擷取畫面](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    保留網路安全性群組的預設輸出規則。 如需詳細資訊，請參閱[安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)中的預設安全性規則一節。

    如果您不想要使用預設輸出規則, 而且想要限制虛擬網路的輸出存取, 請參閱[限制來自虛擬網路的輸出連線能力](#limiting-outbound-from-vnet)

1. 將 VM 或 HDInsight 叢集附加至 Azure Machine Learning 服務工作區。 如需詳細資訊，請參閱[為模型定型設定計算目標](how-to-set-up-training-targets.md)。

> [!IMPORTANT]
> Azure Machine Learning 服務僅支援執行 Ubuntu 的虛擬機器。

## <a name="use-azure-kubernetes-service"></a>使用 Azure Kubernetes Service

若要將虛擬網路中的 Azure Kubernetes Service 新增至您的工作區, 請遵循 Azure 入口網站中的下列步驟:

1. 請確定控制虛擬網路的網路安全性群組 (NSG) 已為使用__AzureMachineLearning__做為**來源**的 Azure Machine Learning 服務啟用輸入規則。

    ![如何在 Azure Machine Learning 服務中新增計算](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 Azure Machine Learning 服務工作區。

1. 在 [應用程式] 區段中，選取 [計算]。 然後，選取 [新增計算]。

1. 若要設定此計算資源以使用虛擬網路，使用下列選項：

    - __網路組態__：選取 [進階]。

    - __资源组__：選取包含虛擬網路的資源群組。

    - __虛擬網路__：選取包含子網路的虛擬網路。

    - __子網路__：選取子網路。

    - __Kubernetes 服務位址範圍__：選取 Kubernetes Service 位址範圍。 此位址範圍使用 CIDR 標記法 IP 範圍，來定義可用於叢集的 IP 位址。 此範圍不得與任何子網路 IP 範圍重疊。 例如: 10.0.0.0/16。

    - __Kubernetes DNS 服務 IP 位址__：選取 Kubernetes DNS 服務 IP 位址。 此 IP 位址會指派給 Kubernetes DNS 服務。 它必須在 Kubernetes Service 位址範圍內。 例如: 10.0.0.10.

    - __Docker 橋接器位址__：選取 Docker 橋接器位址。 此 IP 位址會指派給 Docker 橋接器， 不得位於任何子網路 IP 範圍或 Kubernetes Service 位址範圍中。 例如: 172.17.0.1/16。

   ![Azure Machine Learning 服務：Machine Learning Compute 虛擬網路設定](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. 請確定控制虛擬網路的 NSG 群組具有針對評分端點啟用的輸入安全性規則, 以便可從虛擬網路外部呼叫。

    ![如何在 Azure Machine Learning 服務中新增計算](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)

    > [!TIP]
    > 如果您在虛擬網路中已經有 AKS 叢集，您可以將它附加到工作區。 如需詳細資訊，請參閱[部署至 AKS 的方式](how-to-deploy-to-aks.md)。

> [!IMPORTANT]
> 請先檢查必要條件並規劃叢集的 IP 位址, 再繼續進行上述步驟。 如需詳細資訊，請參閱[在 Azure Kubernetes Service 中設定進階網路](https://docs.microsoft.com/azure/aks/configure-advanced-networking)。
>
>
> 為 NSG 保留預設輸出規則。 如需詳細資訊，請參閱[安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)中的預設安全性規則一節。
>
> Azure Kubernetes Service 與 Azure 虛擬網路應在相同的區域中。

您在虛擬網路中也可以使用 **Azure Machine Learning SDK** 新增 Azure Kubernetes 服務。 下列程式碼會在名為 `mynetwork` 的虛擬網路其 `default` 子網路中建立新的 Azure Kubernetes Service 執行個體：

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

當建立程式完成時, 您可以在虛擬網路背後的 AKS 叢集上進行推斷/評分。 如需詳細資訊，請參閱[部署至 AKS 的方式](how-to-deploy-to-aks.md)。

## <a name="next-steps"></a>後續步驟

* [設定定型環境](how-to-set-up-training-targets.md)
* [部署模型的位置](how-to-deploy-and-where.md)
* [使用 SSL 安全地部署模型](how-to-secure-web-service.md)

