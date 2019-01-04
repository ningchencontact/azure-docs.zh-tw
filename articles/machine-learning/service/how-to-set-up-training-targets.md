---
title: 計算目標進行模型定型
titleSuffix: Azure Machine Learning service
description: 設定機器學習服務模型定型的定型環境 (計算目標)。 您可以輕鬆切換定型環境。 在本機開始定型作業，並在需要相應放大時切換至雲端式計算目標。 Databricks
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: larryfr
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 664d56daf3e70e2e5699d0c07331c466c60e06c5
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2018
ms.locfileid: "53338853"
---
# <a name="set-up-compute-targets-for-model-training"></a>設定計算目標進行模型定型

使用 Azure Machine Learning 服務時，您可以對不同的計算資源進行模型定型。 這些計算資源 (稱為「計算目標」) 可位於本機或雲端。 在此文件中，您將會了解支援的計算目標類型有哪些，以及如何使用它們。

計算目標是執行定型指令碼的資源，或部署成為 Web 服務時裝載您的模型所用的資源。 您可以使用 Azure Machine Learning SDK、Azure 入口網站或 Azure CLI 建立和管理計算目標。 如果您有透過其他服務 (例如 HDInsight 叢集) 建立的計算目標，可以將它們附加至 Azure Machine Learning 服務工作區來使用這些計算目標。

Azure Machine Learning 支援三大類計算目標：

* __本機__：本機電腦，或做為開發/測試環境的雲端式虛擬機器。 

* __受控運算__：Azure Machine Learning Compute 是由 Azure Machine Learning 服務所管理的計算供應項目。 這可讓您輕鬆建立單一或多重節點計算進行定型、測試和批次推斷。

* __附加計算__：您也可以進行您自己的 Azure 雲端計算，並附加到 Azure Machine Learning。 深入了解下列支援的計算類型以及如何使用這些計算類型。


## <a name="supported-compute-targets"></a>支援的計算目標

Azure Machine Learning 服務在各種計算目標上提供不同的支援。 一般模型開發生命週期會先開始開發/測試少量的資料。 在這個階段，我們建議使用本機環境。 例如，您的本機電腦或雲端式虛擬機器。 您對於較大的資料集相應增加您的定型或進行分散式定型時，建議使用 Azure Machine Learning Compute 建立單一或多重節點叢集，在每次提交執行時自動調整。 您也可以附加您自己的計算資源，不過支援的各種情節可能會有所不同，詳述如下：

|計算目標| GPU 加速 | 自動化超參數調整 | 自動化機器學習 | 易記管線|
|----|:----:|:----:|:----:|:----:|
|[本機電腦](#local)| 可能 | &nbsp; | ✓ | &nbsp; |
|[Azure Machine Learning Compute](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[遠端虛擬機器](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](#databricks)| &nbsp; | &nbsp; | ✓ | ✓[*](#pipeline-only) |
|[Azure Data Lake Analytics](#adla)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

> [!IMPORTANT]
> <a id="pipeline-only"></a>__*__ Azure Databricks 和 Azure Data Lake Analytics __只能__在管線中使用。 如需管線的詳細資訊，請參閱 [Azure Machine Learning 中的管線](concept-ml-pipelines.md)文件。

> [!IMPORTANT]
> 必須從工作區中建立 Azure Machine Learning Compute。 您無法將現有的執行個體附加至工作區。
>
> 必須在外部 Azure Machine Learning 之外建立其他計算目標，然後連接到您的工作區。

> [!NOTE]
> 部分計算目標在定型模型時，需要 Docker 容器映像。 只能對 Microsoft Azure 服務使用 GPU 基礎映像。 對於模型定型，這些服務包括：
>
> * Azure Machine Learning Compute
> * Azure Kubernetes Service
> * 資料科學虛擬機器。

## <a name="workflow"></a>工作流程

使用 Azure Machine Learning 開發及部署模型的工作流程包含下列步驟：

1. 使用 Python 開發機器學習服務定型指令碼。
1. 建立及設定，或附加現有的計算目標。
1. 將定型指令碼提交至計算目標。
1. 檢查結果以找出最佳模型。
1. 在模型登錄中註冊模型。
1. 部署模型。

> [!IMPORTANT]
> 定型指令碼並未繫結到特定計算目標。 您可以先在本機電腦開始定型，然後切換計算目標，不需要重新撰寫定型指令碼。

> [!TIP]
> 每當您建立受控計算或連結現有的計算來關聯計算目標與您的工作區時，您都需要提供計算的名稱。 長度應該介於 2 到 16 個字元之間。

從某個計算目標切換到其他計算目標的作業牽涉到建立[回合組態](concept-azure-machine-learning-architecture.md#run-configuration)。 回合組態會定義如何在計算目標上執行指令碼。

## <a name="training-scripts"></a>將指令碼定型

當您啟動定型執行時，會對於包含定型指令碼的目錄建立快照集，並傳送到計算目標。 如需詳細資訊，請參閱[快照集](concept-azure-machine-learning-architecture.md#snapshot)。

## <a id="local"></a>本機電腦

在本機定型時，可以使用 SDK 提交定型作業。 您可以使用使用者管理或系統管理的環境執行定型作業。

### <a name="user-managed-environment"></a>使用者管理的環境

在使用者管理的環境中，您必須負責確保選擇用來執行指令碼的 Python 環境中有所有必要套件。 下列程式碼片段為針對使用者管理的環境設定定型的範例：

```python
from azureml.core.runconfig import RunConfiguration

# Editing a run configuration property on-fly.
run_config_user_managed = RunConfiguration()

run_config_user_managed.environment.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
#run_config.environment.python.interpreter_path = '/home/ninghai/miniconda3/envs/sdk2/bin/python'
```

  
### <a name="system-managed-environment"></a>系統管理的環境

系統管理的環境依賴 Conda 來管理相依性。 Conda 會建立名為 `conda_dependencies.yml` 的檔案，其中包含相依性清單。 接著，您可以要求系統建置新的 Conda 環境並在其中執行您的指令碼。 只要 `conda_dependencies.yml` 檔案維持不變，系統管理的環境之後就能重複使用。 

視所需相依性的大小而定，新環境的初始設定可能需要幾分鐘來完成。 下列程式碼片段示範如何建立取決於 scikit-learn 的系統管理環境：

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config_system_managed = RunConfiguration()

run_config_system_managed.environment.python.user_managed_dependencies = False
run_config_system_managed.auto_prepare_environment = True

# Specify conda dependencies with scikit-learn

run_config_system_managed.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

## <a id="amlcompute"></a>Azure Machine Learning Compute

Azure Machine Learning Compute 是受控的計算基礎結構，可讓使用者輕鬆建立單一到多重節點計算。 這會__在工作區的區域內__建立，而且是能夠與工作區中的其他使用者共用的資源。 這會在提交作業時自動相應增加，而且可放在 Azure 虛擬網路中。 這會在__容器化環境__中執行，在 Docker 容器中封裝模型的相依性。

您可以使用 Azure Machine Learning Compute 在雲端中的 CPU 或 GPU 計算節點叢集散發定型程序。 如需包含 GPU 的虛擬機器大小有關的詳細資訊，請參閱 [GPU 最佳化虛擬機器大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)文件。

> [!NOTE]
> Azure Machine Learning Compute 有預設限制，例如可配置的核心數目。 如需詳細資訊，請參閱[管理和要求 Azure 資源的配額](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas)文件。

您排定執行時，您可以隨需建立 Azure Machine Learning Compute，也可以建立 Azure Machine Learning Compute 做為持續性資源。

### <a name="run-based-creation"></a>執行式建立

您可以建立 Azure Machine Learning Compute 做為執行階段的計算目標。 在此情況下，會自動為您的執行建立計算、相應增加為您在執行組態中指定的 max_nodes，並且在執行完成後__自動刪除__。

> [!IMPORTANT]
> Azure Machine Learning Compute 的執行式建立目前為預覽狀態。 如果您使用超參數調整或自動化機器學習，請勿使用執行式建立。 如果您需要使用超參數調整或自動化機器學習，請先建立Azure Machine Learning Compute 再提交執行。

```python
from azureml.core.compute import ComputeTarget, AmlCompute

#Let us first list the supported VM families for Azure Machine Learning Compute
AmlCompute.supported_vmsizes()

from azureml.core.runconfig import RunConfiguration

# create a new runconfig object
run_config = RunConfiguration()

# signal that you want to use AmlCompute to execute script.
run_config.target = "amlcompute"

# AmlCompute will be created in the same region as workspace. Set vm size for AmlCompute from the list returned above
run_config.amlcompute.vm_size = 'STANDARD_D2_V2'

```

### <a name="persistent-compute-basic"></a>持續性計算 (基本)

永續性 Azure Machine Learning Compute 可以跨多項作業重複使用。 這可以與工作區中的其他使用者共用，並且在工作之間保留。

若要建立持續性 Azure Machine Learning Compute 資源，您可以指定 `vm_size` 和 `max_nodes` 參數。 Azure Machine Learning 接著會對於其餘的參數使用智慧型預設值。  例如，計算是設定為不使用時向下自動調整為零個節點，並且建立專用虛擬機器視需要執行您的作業。 

* **vm_size**：Azure Machine Learning Compute 建立的節點虛擬機器系列。
* **max_nodes**：對於 Azure Machine Learning Compute 執行作業時自動調整的最大節點數。

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```

### <a name="persistent-compute-advanced"></a>持續性計算 (進階)

建立 Azure Machine Learning Compute 時，您也可以設定多個進階屬性。  這些屬性可讓您建立固定大小的永續性叢集，也可以在您的訂用帳戶現有之中的 Azure 虛擬網路內建立。

除了 `vm_size` 和 `max_nodes` 之外，您也可以使用下列屬性：

* **min_nodes**：對於 Azure Machine Learning Compute 執行作業時縮小的最小節點數 (預設為 0 個節點)。
* **vm_priority**：建立 Azure Machine Learning Compute 時，請選擇「專用」(預設值) 或「lowpriority」虛擬機器。 低優先順序虛擬機器會使用 Azure 的多餘容量，因此成本較低，不過您的執行可能會被優先佔用。
* **idle_seconds_before_scaledown**：在自動調整為 min_nodes 之前，執行完成後所要等待的閒置時間 (預設值 120 秒)。
* **vnet_resourcegroup_name**：__現有__虛擬網路的資源群組。 會在此虛擬網路內建立 Azure Machine Learning Compute。
* **vnet_name**：虛擬網路的名稱。 虛擬網路必須位於與您的 Azure Machine Learning 工作區所在的同一個區域。
* **subnet_name**：虛擬網路內的子網路名稱。 Azure Machine Learning Compute 資源將被指派這個子網路範圍中的 IP 位址。

> [!TIP]
> 您建立持續性 Azure Machine Learning Compute 資源時，也能夠更新其屬性，例如 min_nodes 或 max_nodes。 只要為其呼叫 `update()` 函式即可。

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           vm_priority='lowpriority',
                                                           min_nodes=2,
                                                           max_nodes=4,
                                                           idle_seconds_before_scaledown='300',
                                                           vnet_resourcegroup_name='<my-resource-group>',
                                                           vnet_name='<my-vnet-name>',
                                                           subnet_name='<my-subnet-name>')
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```


## <a id="vm"></a>遠端虛擬機器

Azure Machine Learning 也支援提供您自己的計算資源，並將其附加到您的工作區。 其中一種資源類型是任意遠端虛擬機器，只要可以從 Azure Machine Learning 服務存取即可。 這可能是 Azure 虛擬機器，也可能是在您的組織或內部部署的遠端伺服器。 具體來說，只要有 IP 位址和認證 (使用者名稱/密碼或 SSH 金鑰)，即可對於遠端執行使用任何可存取的虛擬機器。
您可以使用系統建立的 Conda 環境、現有的 Python 環境或 Docker 容器。 使用 Docker 容器執行需要在虛擬機器上執行的 Docker 引擎。 您想要比本機電腦的更有彈性、以雲端為基礎的開發/測試環境時，這項功能特別有用。

> [!TIP]
> 我們建議使用資料科學虛擬機器做為對於此案例選擇的 Azure 虛擬機器。 這是 Azure 中預先設定的資料科學和 AI 開發環境，其中有對於 ML 開發的整個生命週期精心選擇的工具和架構。 如需有關如何對於 Azure Machine Learning ML 開發的資料科學虛擬機器的詳細資訊，請參閱 [設定開發環境](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm)文件。

> [!WARNING]
> Azure Machine Learning 只支援執行 Ubuntu 的虛擬機器。 建立新的虛擬機器或選取現有的虛擬機器時，必須選取使用 Ubuntu 的虛擬機器。

下列步驟會使用 SDK 將資料科學虛擬機器 (DSVM) 設定為定型目標：

1. 若要附加現有的虛擬機器作為計算目標，則必須提供虛擬機器的完整網域名稱、登入名稱與密碼。  在範例中，請以 VM 的公用完整網域名稱或公用 IP 位址取代 ```<fqdn>```。 以 VM 的 SSH 使用者名稱與密碼取代 ```<username>``` 與 ```<password>```：

    ```python
    from azureml.core.compute import RemoteCompute, ComputeTarget
    
    # Create compute config.
    attach_config = RemoteCompute.attach_configuration(address = "ipaddress",
                                                       ssh_port=22,
                                                       username='<username>',
                                                       password="<password>")
    # If using SSH instead of a password, use this:
    #                                                  ssh_port=22,
    #                                                   username='<username>',
    #                                                   password=None,
    #                                                   private_key_file="path-to-file",
    #                                                   private_key_passphrase="passphrase")

    # Attach the compute
    compute = ComputeTarget.attach(ws, "attach-dsvm", attach_config)

    compute.wait_for_completion(show_output=True)
    ```

1. 建立 DSVM 計算目標的組態。 Docker 和 conda 用來建立和設定 DSVM 上的定型環境：

    ```python
    from azureml.core.runconfig import RunConfiguration
    from azureml.core.conda_dependencies import CondaDependencies

    # Load the "cpu-dsvm.runconfig" file (created by the above attach operation) in memory
    run_config = RunConfiguration(framework = "python")

    # Set compute target to the Linux DSVM
    run_config.target = compute_target_name

    # Use Docker in the remote VM
    run_config.environment.docker.enabled = True

    # Use CPU base image
    # If you want to use GPU in DSVM, you must also use GPU base Docker image azureml.core.runconfig.DEFAULT_GPU_IMAGE
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask system to provision a new one based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when used the first time.
    run_config.prepare_environment = True

    # specify CondaDependencies obj
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

## <a id="databricks"></a>Azure Databricks

Azure Databricks 是 Azure 雲端中的 Apache Spark 型環境。 使用 Azure Machine Learning 管線訓練模型時，其可以作為計算目標。

> [!IMPORTANT]
> Azure Databricks 計算目標只能在 Machine Learning 管線中使用。
>
> 您必須先建立 Azure Databricks 工作區，才能使用它來訓練模型。 若要建立這些資源，請參閱[在 Azure Databricks 執行 Spark 作業](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)文件。

若要連結 Azure Databricks 來作為計算目標，您必須使用 Azure Machine Learning SDK，並提供下列資訊：

* __計算名稱__：您想要指派給這個計算資源的名稱。
* __Databricks 工作區名稱__：Azure Databricks 工作區的名稱。
* __存取權杖__：用來向 Azure Databricks 進行驗證的存取權杖。 若要產生存取權杖，請參閱[驗證](https://docs.azuredatabricks.net/api/latest/authentication.html)文件。

下列程式碼示範如何連結 Azure Databricks 來作為計算目標：

```python
databricks_compute_name = os.environ.get("AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get("AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get("AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get("AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group = databricks_resource_group,
                                                           workspace_name = databricks_workspace_name,
                                                           access_token = databricks_access_token)
    databricks_compute = ComputeTarget.attach(
             ws,
             databricks_compute_name,
             attach_config
         )
    
    databricks_compute.wait_for_completion(True)
```

## <a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics 是 Azure 雲端中的巨量資料分析平台。 使用 Azure Machine Learning 管線訓練模型時，其可以作為計算目標。

> [!IMPORTANT]
> Azure Data Lake Analytics 計算目標只能在 Machine Learning 管線中使用。
>
> 您必須先建立 Azure Data Lake Analytics 帳戶，才能使用它來訓練模型。 若要建立此資源，請參閱[開始使用 Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) 文件。

若要連結 Data Lake Analytics 來作為計算目標，您必須使用 Azure Machine Learning SDK，並提供下列資訊：

* __計算名稱__：您想要指派給這個計算資源的名稱。
* __資源群組__：包含 Data Lake Analytics 帳戶的資源群組。
* __帳戶名稱__：Data Lake Analytics 帳戶名稱。

下列程式碼示範如何連結 Data Lake Analytics 來作為計算目標：

```python
adla_compute_name = os.environ.get("AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get("AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get("AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group = adla_resource_group,
                                                     account_name = adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
             ws,
             adla_compute_name,
             attach_config
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Azure Machine Learning 管線只能使用 Data Lake Analytics 帳戶的預設資料存放區中所儲存的資料來運作。 如果您運作所需的資料位於非預設的存放區，則可以先使用 [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) 複製資料再進行訓練。

## <a id="hdinsight"></a>Azure HDInsight 

HDInsight 是巨量資料分析的熱門平台。 它提供了 Apache Spark，可用來將您的模型定型。

> [!IMPORTANT]
> 您必須先建立 HDInsight 叢集，才能使用它將您的模型定型。 若要在 HDInsight 叢集上建立 Spark，請參閱[在 HDInsight 中建立 Spark 叢集](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql)文件。
>
> 建立叢集時，必須指定 SSH 使用者名稱與密碼。 請記下這些值，因為使用 HDInsight 作為計算目標時會需要它們。
>
> 建立叢集之後，它會有完整網域名稱 (FQDN) `<clustername>.azurehdinsight.net`，其中 `<clustername>` 是您提供的叢集名稱。 您需要此位址 (或叢集的公用 IP 位址) 才能使用它作為計算目標

若要將 HDInsight 設定為計算目標，必須提供 HDInsight 叢集的完整網域名稱、叢集登入名稱與密碼。 下列範例會使用 SDK 將叢集附加到您的工作區。 在範例中，請以叢集的公用完整網域名稱或公用 IP 位址來取代 `<fqdn>`。 以叢集的 SSH 使用者名稱與密碼來取代 `<username>` 與 `<password>`：

> [!NOTE]
> 若要尋找您叢集的 FQDN，請瀏覽 Azure 入口網站並選取您的 HDInsight 叢集。 FQDN 位於 [概觀] 區段的 __URL__ 項目中。 請將 `https://` 從值的開頭移除。
>
> ![URL 項目已反白顯示之 HDInsight 叢集概觀的螢幕擷取畫面](./media/how-to-set-up-training-targets/hdinsight-overview.png)

```python
from azureml.core.compute import HDInsightCompute, ComputeTarget

try:
    # Attaches a HDInsight cluster as a compute target.
    attach_config = HDInsightCompute.attach_configuration(address = "fqdn-or-ipaddress",
                                                          ssh_port = 22,
                                                          username = "username",
                                                          password = None, #if using ssh key
                                                          private_key_file = "path-to-key-file",
                                                          private_key_phrase = "key-phrase")
    compute = ComputeTarget.attach(ws, "myhdi", attach_config)
except UserErrorException as e:
    print("Caught = {}".format(e.message))
    print("Compute config already attached.")

# Configure HDInsight run
# load the runconfig object from the "myhdi.runconfig" file generated by the attach operaton above.
run_config = RunConfiguration.load(project_object = project, run_config_name = 'myhdi')

# ask system to prepare the conda environment automatically when used for the first time
run_config.auto_prepare_environment = True
```

## <a name="submit-training-run"></a>提交定型回合

訓練執行的提交方式有兩種：

* 提交 `ScriptRunConfig` 物件。
* 提交 `Pipeline` 物件。

> [!IMPORTANT]
> Azure Databricks 和 Azure Data Lake Analytics 計算目標只能在管線中使用。
> 本機計算目標則無法在管線中使用。

### <a name="submit-using-scriptrunconfig"></a>使用 `ScriptRunConfig` 來提交

無論計算目標為何，使用 `ScriptRunConfig` 來提交訓練執行的程式碼模式都相同：

* 使用計算目標的回合組態建立 `ScriptRunConfig` 物件。
* 提交回合。
* 等待回合完成。

下列範例使用此文件中稍早建立的系統管理本機計算目標設定：

```python
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```


### <a name="submit-using-a-pipeline"></a>使用管線來提交

無論計算目標為何，使用管線來提交訓練執行的程式碼模式都相同：

* 在計算資源的管線中新增步驟。
* 使用管線來提交執行。
* 等待回合完成。

下列範例會使用本文件稍早所建立的 Azure Databricks 計算目標：

```python
dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    databricks_compute=databricks_compute,
    allow_reuse=False
)
# list of steps to run
steps = [dbStep]
pipeline = Pipeline(workspace=ws, steps=steps)
pipeline_run = Experiment(ws, 'Demo_experiment').submit(pipeline)
pipeline_run.wait_for_completion()
```

如需機器學習管線的詳細資訊，請參閱[管線和 Azure Machine Learning](concept-ml-pipelines.md) 文件。

如需會示範如何使用管線來進行訓練的 Jupyter Notebook 範例，請參閱 [https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline)。

## <a name="view-and-set-up-compute-using-the-azure-portal"></a>使用 Azure 入口網站來檢視及設定計算

您可以從 Azure 入口網站檢視有哪些計算目標與您的工作區關聯。 若要取得清單，請使用下列步驟：

1. 瀏覽 [Azure 入口網站](https://portal.azure.com)，然後瀏覽到您的工作區。
2. 按一下 [應用程式] 區段底下的 [計算] 連結。

    ![[檢視計算] 索引標籤](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a name="create-a-compute-target"></a>建立計算目標

依照上述步驟檢視計算目標清單，然後使用下列步驟建立計算目標：

1. 按一下 __+__ 號以新增計算目標。

    ![新增計算 ](./media/how-to-set-up-training-targets/add-compute-target.png)

1. 輸入計算目標的名稱
1. 選取 [Machine Learning Compute] 做為要用於__定型__的計算類型

    > [!IMPORTANT]
    > 您只能建立 Azure Machine Learning Compute 做為訓練的受控計算

1. 填寫必要的表單，特別是 VM 系列，以及加速計算的節點數上限 
1. 選取 [建立] 
1. 您可以從清單選取計算目標以檢視狀態建立作業

    ![檢視計算清單](./media/how-to-set-up-training-targets/View_list.png)

1. 您接著會看到該計算目標的詳細資料。

    ![檢視詳細資料](./media/how-to-set-up-training-targets/compute-target-details.png)

1. 現在您可以依照上述詳細說明，針對這些目標提交回合


### <a name="reuse-existing-compute-in-your-workspace"></a>重複使用工作區中現有的計算

請依照上述步驟檢視計算目標清單，然後使用下列步驟重複使用計算目標：

1. 按一下 **+** 號以新增計算目標
2. 輸入計算目標的名稱
3. 選取要附加以進行__定型__的計算類型

    > [!IMPORTANT]
    > 並非所有計算類型都可以使用入口網站來連結。
    > 目前可建立來進行連結的類型為：
    > 
    > * 遠端虛擬機器
    > * Databricks
    > * Data Lake Analytics
    > * HDInsight

1. 填寫必要的項表單

    > [!NOTE]
    > Microsoft 建議使用 SSH 金鑰，因為它們比密碼更安全。 密碼很容易遭受暴力密碼破解攻擊，而 SSH 金鑰則是仰賴密碼編譯簽章。 如需建立 SSH 金鑰以搭配 Azure 虛擬機器使用的資訊，請參閱下列文件：
    >
    > * [在 Linux 或 macOS 上建立及使用 SSH 金鑰]( https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [在 Windows 上建立及使用 SSH 金鑰]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. 選取 [附加]
1. 您可以從清單選取計算目標以檢視狀態附加
1. 現在您可以依照上述詳細說明，針對這些目標提交回合

## <a name="examples"></a>範例
請參閱下列位置的筆記本：
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)

* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>後續步驟

* [Azure Machine Learning SDK 參照](https://aka.ms/aml-sdk)
* [教學課程：將模型定型](tutorial-train-models-with-aml.md)
* [部署模型的位置](how-to-deploy-and-where.md)
* [使用 Azure Machine Learning 服務建置機器學習服務管線](concept-ml-pipelines.md)
