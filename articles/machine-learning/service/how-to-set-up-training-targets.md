---
title: 計算目標進行模型定型
titleSuffix: Azure Machine Learning service
description: 設定機器學習服務模型定型的定型環境 (計算目標)。 您可以輕鬆地在定型環境之間切換。 在本機開始定型作業。 如果您需要相應放大，請切換至雲端式計算目標。
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
ms.openlocfilehash: 9037f6d7602f186bc30e55acbc050280bca134ee
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/27/2018
ms.locfileid: "53794459"
---
# <a name="set-up-compute-targets-for-model-training"></a>設定計算目標進行模型定型

使用 Azure Machine Learning 服務時，您可以對不同的計算資源進行模型定型。 這些計算資源 (稱為「計算目標」) 可位於本機或雲端。 在本文中，您會了解支援的計算目標類型有哪些，以及如何使用它們。

計算目標是執行定型指令碼的資源，或部署成為 Web 服務時裝載您的模型所用的資源。 您可以使用 Azure Machine Learning SDK、Azure 入口網站或 Azure CLI 建立和管理計算目標。 如果您已透過其他服務 (例如 Azure HDInsight 叢集) 建立計算目標，可以將它們附加至 Azure Machine Learning 服務工作區來使用這些目標。

Azure Machine Learning 支援三大類計算目標：

* __本機__：本機電腦，或當作開發和測試環境使用的雲端式虛擬機器 (VM)。 
* __受控運算__：Azure Machine Learning Compute 是由 Azure Machine Learning 服務所管理的計算供應項目。 此供應項目可讓您輕鬆建立單一或多重節點計算以進行定型、測試和批次推斷。
* __附加計算__：您也可以進行您自己的 Azure 雲端計算，並附加到 Azure Machine Learning。 深入了解支援的計算類型，以及如何在下列各節中使用它們。


## <a name="supported-compute-targets"></a>支援的計算目標

Azure Machine Learning 服務在各種計算目標上提供不同的支援。 一般模型開發生命週期會先開始開發和測試少量的資料。 在這個階段，我們建議您使用本機環境，例如您的本機電腦或雲端式 VM。 您對於較大的資料集相應增加您的定型或進行分散式定型時，請使用 Azure Machine Learning 計算環境建立單一或多重節點叢集，在每次提交執行時自動調整。 您也可以附加自己的計算資源，但是支援的各種情節可能會有所不同，如下表所述：

|計算目標| GPU 加速 | 自動化<br/> 超參數微調 | 自動化</br> 機器學習服務 | 易記管線|
|----|:----:|:----:|:----:|:----:|
|[本機電腦](#local)| 可能 | &nbsp; | ✓ | &nbsp; |
|[Azure Machine Learning Compute](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[遠端虛擬機器](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](#databricks)| &nbsp; | &nbsp; | ✓ | ✓[*](#pipeline-only) |
|[Azure Data Lake Analytics](#adla)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

> [!IMPORTANT]
> <a id="pipeline-only"></a>__*__ _Azure Databricks 和 Azure Data Lake Analytics 只能在管線中使用。_<br/>
> 如需管線的詳細資訊，請參閱 [Azure Machine Learning 中的管線](concept-ml-pipelines.md)。
>
> 您必須從工作區中建立 Azure Machine Learning 計算環境。 您無法將現有的執行個體附加至工作區。
>
> 必須在外部 Azure Machine Learning 之外建立其他計算目標，然後連接到您的工作區。
>
> 部分計算目標在定型模型時，需要 Docker 容器映像。 只能對 Microsoft Azure 服務使用 GPU 基礎映像。 對於模型定型，這些服務包括：
> * Azure Machine Learning Compute
> * Azure Kubernetes Service
> * Windows 資料科學虛擬機器 (DSVM)

## <a name="workflow"></a>工作流程

使用 Azure Machine Learning 開發及部署模型的工作流程包含下列步驟：

1. 使用 Python 開發機器學習服務定型指令碼。
1. 建立及設定計算目標，或附加現有的計算目標。
1. 將定型指令碼提交至計算目標。
1. 檢查結果以找出最佳模型。
1. 在模型登錄中註冊模型。
1. 部署模型。

> [!NOTE]
> 定型指令碼並未繫結到特定計算目標。 您可以先在本機電腦開始定型，然後切換計算目標，不需要重新撰寫定型指令碼。
> 
> 每當您建立受控計算或附加現有的計算來產生計算目標與工作區的關聯時，請提供計算的名稱。 此名稱的長度應該介於 2 到 16 個字元之間。

若要從某個計算目標切換到另一個計算目標，您需要有[回合組態](concept-azure-machine-learning-architecture.md#run-configuration)。 回合組態會定義如何在計算目標上執行指令碼。

## <a name="training-scripts"></a>將指令碼定型

當您啟動定型回合時，會針對包含定型指令碼的目錄建立快照集，並傳送到計算目標。 如需詳細資訊，請參閱[快照集](concept-azure-machine-learning-architecture.md#snapshot)。

## <a id="local"></a>本機電腦

在本機定型時，可以使用 SDK 提交定型作業。 您可以使用使用者管理或系統管理的環境執行定型作業。

### <a name="user-managed-environment"></a>使用者管理的環境

在使用者管理的環境中，請確定所有必要的套件都可以在執行指令碼所在的 Python 環境中使用。 下列程式碼片段為如何針對使用者管理的環境設定定型的範例：

```python
from azureml.core.runconfig import RunConfiguration

# Edit a run configuration property on the fly.
run_config_user_managed = RunConfiguration()

run_config_user_managed.environment.python.user_managed_dependencies = True

# Choose a specific Python environment by pointing to a Python path. For example:
# run_config.environment.python.interpreter_path = '/home/ninghai/miniconda3/envs/sdk2/bin/python'
```

  
### <a name="system-managed-environment"></a>系統管理的環境

系統管理的環境依賴 Conda 來管理相依性。 Conda 會建立一個名為 **conda_dependencies.yml** 的檔案，其中包含相依性清單。 接著，您可以要求系統建置新的 Conda 環境，並在其中執行您的指令碼。 只要 conda_dependencies.yml 檔案維持不變，之後就能重複使用系統管理的環境。 

根據所需相依性的大小，新環境的初始設定可能需要幾分鐘來完成。 下列程式碼片段示範如何建立取決於 scikit-learn 的系統管理環境：

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config_system_managed = RunConfiguration()

run_config_system_managed.environment.python.user_managed_dependencies = False
run_config_system_managed.auto_prepare_environment = True

# Specify the conda dependencies with scikit-learn

run_config_system_managed.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

## <a id="amlcompute"></a>Azure Machine Learning Compute

Azure Machine Learning Compute 是一種受控的計算基礎結構，可讓使用者輕鬆建立單一或多重節點計算。 計算會在工作區的區域內建立為能夠與工作區中的其他使用者共用的資源。 計算會在提交作業時自動相應增加，而且可以放在 Azure 虛擬網路中。 計算會在容器化環境中執行，並在 Docker 容器中封裝模型的相依性。

您可以使用 Azure Machine Learning Compute 在雲端中的 CPU 或 GPU 計算節點叢集散發定型程序。 如需包含 GPU 的 VM 大小有關的詳細資訊，請參閱 [GPU 最佳化虛擬機器大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)。

> [!NOTE]
> Azure Machine Learning Compute 有預設限制，例如可配置的核心數目。 如需詳細資訊，請參閱[管理和要求 Azure 資源的配額](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas)。

當您排定回合時，可以隨需建立 Azure Machine Learning 計算環境，也可以建立 Azure Machine Learning 計算環境作為持續性資源。

### <a name="run-based-creation"></a>執行式建立

您可以建立 Azure Machine Learning 計算環境作為執行階段的計算目標。 計算會自動為您的回合建立，並相應增加至您在回合組態中指定的 **max_nodes** 數目。回合完成之後，就會自動刪除計算。

> [!IMPORTANT]
> Azure Machine Learning 計算環境的回合式建立目前為預覽狀態。 如果您使用自動化超參數調整或自動化機器學習，請勿使用回合式建立。 若要使用超參數調整或自動化機器學習，請先建立 Azure Machine Learning 計算環境，然後再提交回合。

```python
from azureml.core.compute import ComputeTarget, AmlCompute

# First, list the supported VM families for Azure Machine Learning Compute
AmlCompute.supported_vmsizes()

from azureml.core.runconfig import RunConfiguration

# Create a new runconfig object
run_config = RunConfiguration()

# Signal that you want to use AmlCompute to execute the script
run_config.target = "amlcompute"

# AmlCompute is created in the same region as your workspace
# Set the VM size for AmlCompute from the list of supported_vmsizes
run_config.amlcompute.vm_size = 'STANDARD_D2_V2'

```

### <a name="persistent-compute-basic"></a>持續性計算：基本

持續性 Azure Machine Learning 計算環境可以跨作業重複使用。 計算可以與工作區中的其他使用者共用，並且在作業之間保留。

若要建立持續性 Azure Machine Learning 計算環境資源，您可以指定 **vm_size** 和 **max_nodes** 屬性。 Azure Machine Learning 接著會對於其他屬性使用智慧型預設值。 計算會在不使用時自動向下調整為零個節點，並且建立專用 VM 視需要執行您的作業。 

* **vm_size**：Azure Machine Learning Compute 建立的節點虛擬機器系列。
* **max_nodes**：對於 Azure Machine Learning Compute 執行作業時自動向上調整的最大節點數。

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that the cluster doesn't already exist
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```

### <a name="persistent-compute-advanced"></a>持續性計算：進階

建立 Azure Machine Learning 計算環境時，您也可以設定數個進階屬性。 這些屬性可讓您建立固定大小的持續性叢集，也可以在您訂用帳戶中現有的 Azure 虛擬網路內建立。

除了 **vm_size** 和 **max_nodes** 屬性之外，您也可以使用下列屬性：

* **min_nodes**：對於 Azure Machine Learning 計算環境執行作業時向下調整的最小節點數。 預設最小值為零 (0) 個節點。
* **vm_priority**：建立 Azure Machine Learning 計算環境資源時所使用的 VM 類型。 選擇 **dedicated** (預設) 或 **lowpriority**。 低優先順序的 VM 在 Azure 中使用過多的容量。 這些 Vm 的成本較低，但使用這些 VM 時，可以預先清空回合。
* **idle_seconds_before_scaledown**：回合完成之後，以及向上自動調整到 **min_nodes** 的數目之前等待的閒置時間。 預設的閒置時間是 120 秒。
* **vnet_resourcegroup_name**：__現有__虛擬網路的資源群組。 Azure Machine Learning 計算環境是在虛擬網路內建立的。
* **vnet_name**：虛擬網路的名稱。 虛擬網路必須位於與您的 Azure Machine Learning 工作區所在的同一個區域。
* **subnet_name**：虛擬網路內的子網路名稱。 Azure Machine Learning 計算環境資源將被指派這個子網路範圍中的 IP 位址。

> [!TIP]
> 當您建立持續性 Azure Machine Learning 計算環境資源時，可以更新屬性，例如 **min_nodes** 或 **max_nodes** 的數目。 若要更新屬性，請針對屬性呼叫 `update()` 函式。

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that the cluster doesn't already exist 
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

Azure Machine Learning 也支援提供您自己的計算資源，並將其附加到您的工作區。 其中一種資源類型是任意遠端 VM，只要可以從 Azure Machine Learning 服務存取即可。 此資源可以是 Azure VM，也可以是在您組織或內部部署的遠端伺服器。 具體來說，只要有 IP 位址和認證 (使用者名稱和密碼或 SSH 金鑰)，即可對於遠端執行使用任何可存取的 VM。
您可以使用系統建置的 Conda 環境、現有的 Python 環境或 Docker 容器。 當您使用 Docker 容器執行時，需要在 VM 上執行的 Docker 引擎。 當您需要有比本機電腦更有彈性的雲端式開發和測試環境時，遠端 VM 功能特別有用。

> [!TIP]
> 在此案例中，使用 DSVM 作為選擇的 Azure VM。 此 VM 是 Azure 中預先設定的資料科學和 AI 開發環境。VM 會針對整個生命週期的機器學習開發，提供精心選擇的工具和架構。 如需有關如何使用 DSVM 搭配 Azure Machine Learning 的詳細資訊，請參閱[設定開發環境](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm)。

> [!WARNING]
> Azure Machine Learning 只支援執行 Ubuntu 的虛擬機器。 當您建立 VM 或選擇現有的 VM 時，您必須選取使用 Ubuntu 的 VM。

下列步驟會使用 SDK 將 DSVM 設定為定型目標：

1. 若要附加現有的虛擬機器作為計算目標，您必須提供虛擬機器的完整網域名稱 (FQDN)、使用者名稱與密碼。 在範例中，請以 VM 的公用 FQDN 或公用 IP 位址取代 \<fqdn>。 將 \<username> 和 \<password> 取代為 VM 的 SSH 使用者名稱和密碼。

    ```python
    from azureml.core.compute import RemoteCompute, ComputeTarget
    
    # Create the compute config
    attach_config = RemoteCompute.attach_configuration(address = "ipaddress",
                                                       ssh_port=22,
                                                       username='<username>',
                                                       password="<password>")

    # If you use SSH instead of a password, use this code:
    #                                                  ssh_port=22,
    #                                                  username='<username>',
    #                                                  password=None,
    #                                                  private_key_file="path-to-file",
    #                                                  private_key_passphrase="passphrase")

    # Attach the compute target
    compute = ComputeTarget.attach(ws, "attach-dsvm", attach_config)

    compute.wait_for_completion(show_output=True)
    ```

1. 建立 DSVM 計算目標的組態。 Docker 和 Conda 用來建立和設定 DSVM 上的定型環境。

    ```python
    from azureml.core.runconfig import RunConfiguration
    from azureml.core.conda_dependencies import CondaDependencies

    # Load into memory the cpu-dsvm.runconfig file created in the previous attach operation
    run_config = RunConfiguration(framework = "python")

    # Set the compute target to the Linux DSVM
    run_config.target = compute_target_name

    # Use Docker in the remote VM
    run_config.environment.docker.enabled = True

    # Use the CPU base image
    # To use GPU in DSVM, you must also use the GPU base Docker image "azureml.core.runconfig.DEFAULT_GPU_IMAGE"
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask the system to provision a new conda environment based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when they're used for the first time
    run_config.prepare_environment = True

    # Specify the CondaDependencies object
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

## <a id="databricks"></a>Azure Databricks

Azure Databricks 是 Azure 雲端中的 Apache Spark 型環境。 使用 Azure Machine Learning 管線將模型定型時，此環境可以當作計算目標使用。

> [!IMPORTANT]
> Azure Databricks 計算目標只能在 Machine Learning 管線中使用。
>
> 您必須先建立 Azure Databricks 工作區，才能使用它來定型模型。 若要建立這些資源，請參閱[在 Azure Databricks 上執行 Spark 作業](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)。

若要連結 Azure Databricks 來作為計算目標，您必須使用 Azure Machine Learning SDK，並提供下列資訊：

* __計算名稱__：要指派給這個計算資源的名稱。
* __Databricks 工作區名稱__：Azure Databricks 工作區的名稱。
* __存取權杖__：用來向 Azure Databricks 進行驗證的存取權杖。 若要產生存取權杖，請參閱[驗證](https://docs.azuredatabricks.net/api/latest/authentication.html)。

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

    # Create the attach config
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

Azure Data Lake Analytics 是 Azure 雲端中的巨量資料分析平台。 使用 Azure Machine Learning 管線將模型定型時，此平台可以當作計算目標使用。

> [!IMPORTANT]
> Azure Data Lake Analytics 計算目標只能在 Machine Learning 管線中使用。
>
> 您必須先建立 Azure Data Lake Analytics 帳戶，才能使用它來定型模型。 若要建立此資源，請參閱[開始使用 Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal)。

若要連結 Data Lake Analytics 來作為計算目標，您必須使用 Azure Machine Learning SDK，並提供下列資訊：

* __計算名稱__：要指派給這個計算資源的名稱。
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
    
    # Create the attach config
    attach_config = AdlaCompute.attach_configuration(resource_group = adla_resource_group,
                                                     account_name = adla_account_name)
    # Attach the ADLA
    adla_compute = ComputeTarget.attach(
             ws,
             adla_compute_name,
             attach_config
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Azure Machine Learning 管線只能使用 Data Lake Analytics 帳戶的預設資料存放區中所儲存的資料來運作。 如果您需要的資料位於非預設存放區中，您可以先使用 [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) 作業複製資料，然後再定型模型。

## <a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight 是巨量資料分析的常用平台。 此平台會提供 Apache Spark，可用來將您的模型定型。

> [!IMPORTANT]
> 您必須先建立 HDInsight 叢集，才能使用它將您的模型定型。 若要在 HDInsight 叢集上建立 Spark，請參閱[在 HDInsight 中建立 Spark 叢集](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql)。
>
> 建立叢集時，必須指定 SSH 使用者名稱與密碼。 請記下這些值，因為使用 HDInsight 作為計算目標時會需要它們。
>
> 建立叢集之後，其 FQDN 為 \<clustername>.azurehdinsight.net，其中 \<clustername > 是您提供叢集的名稱。 您需要 FQDN 位址 (或叢集的公用 IP 位址) 才能使用該叢集作為計算目標。

若要將 HDInsight 設定為計算目標，必須提供 HDInsight 叢集的 FQDN、使用者名稱與密碼。 下列範例會使用 SDK 將叢集附加到您的工作區。 在範例中，請以叢集的公用 FQDN 或公用 IP 位址取代 \<fqdn>。 將 \<username> 和 \<password> 取代為叢集的 SSH 使用者名稱和密碼。

> [!NOTE]
> 若要尋找您叢集的 FQDN，請前往 Azure 入口網站並選取您的 HDInsight 叢集。 在 [概觀] 底下，您可以在 __URL__ 項目中看到 FQDN。 若要取得 FQDN，請移除項目開頭的 https:\// 前置詞。

![在 Azure 入口網站中，取得 HDInsight 叢集的 FQDN](./media/how-to-set-up-training-targets/hdinsight-overview.png)

```python
from azureml.core.compute import HDInsightCompute, ComputeTarget

try:
    # Attach an HDInsight cluster as a compute target
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

# Configure the HDInsight run
# Load the runconfig object from the myhdi.runconfig file generated in the previous attach operation
run_config = RunConfiguration.load(project_object = project, run_config_name = 'myhdi')

# Ask the system to prepare the conda environment automatically when it's used for the first time
run_config.auto_prepare_environment = True
```

## <a name="submit-training-runs"></a>提交定型回合

訓練執行的提交方式有兩種：

* 使用 `ScriptRunConfig` 物件提交定型回合。
* 使用 `Pipeline` 物件提交定型回合。

> [!IMPORTANT]
> Azure Databricks 和 Azure Data Lake Analytics 計算目標只能在管線中使用。
>
> 本機計算目標無法在管線中使用。

### <a name="scriptrunconfig-object"></a>ScriptRunConfig 物件

使用 `ScriptRunConfig` 物件提交定型回合的程式碼模式適用於所有類型的計算目標：

1. 使用計算目標的回合組態建立 `ScriptRunConfig` 物件。
1. 提交回合。
1. 等待回合完成。

下列範例使用稍早建立的系統管理本機計算目標設定：

```python
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```


### <a name="pipeline-object"></a>Pipeline 物件

使用 `Pipeline` 物件提交定型回合的程式碼模式適用於所有類型的計算目標：

1. 在計算資源的 `Pipeline` 物件中新增步驟。
1. 使用管線提交回合。
1. 等待回合完成。

下列範例會使用稍早建立的 Azure Databricks 計算目標：

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

# List of steps to run
steps = [dbStep]
pipeline = Pipeline(workspace=ws, steps=steps)
pipeline_run = Experiment(ws, 'Demo_experiment').submit(pipeline)
pipeline_run.wait_for_completion()
```

如需有關機器學習管線的詳細資訊，請參閱[管線和 Azure Machine Learning](concept-ml-pipelines.md)。

如需可示範如何使用管線定型模型的 Jupyter Notebook 範例，請參閱 [https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline)。

## <a name="access-computes-in-the-azure-portal"></a>在 Azure 入口網站中存取計算

您可以在 Azure 入口網站中，存取與您工作區相關聯的計算目標。 

### <a name="view-compute-targets"></a>檢視計算目標

若要查看您工作區的計算目標，請使用下列步驟：

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)，然後開啟您的工作區。
1. 在 [應用程式] 底下，選取 [計算]。

    ![檢視計算目標](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a name="create-a-compute-target"></a>建立計算目標

依照上述步驟來檢視計算目標的清單。 接著，使用下列步驟建立計算目標：

1. 按一下加號 (+) 以新增計算目標。

    ![新增計算目標](./media/how-to-set-up-training-targets/add-compute-target.png)

1. 輸入計算目標的名稱。
1. 選取 [Machine Learning Compute] 作為要用於 [定型] 的計算類型。

    > [!IMPORTANT]
    > 您只能建立 Azure Machine Learning 計算環境，作為要定型的受控計算資源。

1. 填寫表單。 針對必要的屬性 (特別是 **VM 系列**) 和**節點上限**提供值，以便向上微調計算。 
1. 選取 [建立] 。
1. 從清單選取計算目標以檢視建立作業的狀態：

    ![選取計算目標來檢視建立作業狀態](./media/how-to-set-up-training-targets/View_list.png)

1. 接著，您會看到該計算目標的詳細資料：

    ![檢視電腦目標的詳細資料](./media/how-to-set-up-training-targets/compute-target-details.png)

現在您可以針對電腦目標提交回合，如先前所述。


### <a name="reuse-existing-compute-targets"></a>重複使用現有的計算目標

依照稍早所述的步驟來檢視計算目標的清單。 接著，使用下列步驟重複使用計算目標：

1. 按一下加號 (+) 以新增計算目標。
1. 輸入計算目標的名稱。
1. 選取要附加以進行__定型__的計算類型：

    > [!IMPORTANT]
    > 並非所有計算類型都可以從 Azure 入口網站附加。
    > 目前可以附加以進行定型的目標類型包括：
    >
    > * 遠端 VM
    > * Azure Databricks
    > * Azure Data Lake Analytics
    > * Azure HDInsight

1. 填寫表單，並提供必要屬性的值。

    > [!NOTE]
    > Microsoft 建議使用 SSH 金鑰，因為它們比密碼更安全。 密碼很容易遭受暴力密碼破解攻擊。 SSH 金鑰需要密碼編譯簽章。 如需有關如何建立 SSH 金鑰以搭配 Azure 虛擬機器使用的資訊，請參閱下列文件：
    >
    > * [在 Linux 或 macOS 上建立及使用 SSH 金鑰](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [在 Windows 上建立及使用 SSH 金鑰](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. 選取 [附加]。
1. 從清單選取計算目標以檢視附加作業的狀態。

現在您可以針對這些計算目標提交回合，如先前所述。

## <a name="notebook-examples"></a>筆記本範例

如需範例，請參閱下列位置的筆記本：

* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>後續步驟

* [Azure Machine Learning SDK 參照](https://aka.ms/aml-sdk)
* [教學課程：將模型定型](tutorial-train-models-with-aml.md)
* [部署模型的位置](how-to-deploy-and-where.md)
* [使用 Azure Machine Learning 服務建置機器學習服務管線](concept-ml-pipelines.md)
