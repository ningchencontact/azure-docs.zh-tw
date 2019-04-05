---
title: 建立及使用計算目標進行模型定型
titleSuffix: Azure Machine Learning service
description: 設定機器學習服務模型定型的定型環境 (計算目標)。 您可以輕鬆地在定型環境之間切換。 在本機開始定型作業。 如果您需要相應放大，請切換至雲端式計算目標。
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/07/2019
ms.custom: seodec18
ms.openlocfilehash: d75deaca7ce052d40274f1f57a8f6603a3ecdfd2
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2019
ms.locfileid: "59046150"
---
# <a name="set-up-compute-targets-for-model-training"></a>設定計算目標進行模型定型

使用 Azure Machine Learning 服務，您可以在各種資源或環境中定型您的模型，統稱為[__計算目標__](concept-azure-machine-learning-architecture.md#compute-target)。 計算目標可以是本機電腦或雲端資源，例如 Azure Machine Learning Compute、Azure HDInsight 或遠端虛擬機器。  您也可以建立用於部署模型的計算目標，如[模型的部署位置和方法](how-to-deploy-and-where.md)中所述。

您可以使用 Azure Machine Learning SDK、Azure 入口網站或 Azure CLI 建立和管理計算目標。 如果您有透過其他服務 (例如 HDInsight 叢集) 建立的計算目標，可以將它們附加至 Azure Machine Learning 服務工作區來使用這些計算目標。
 
在本文中，您將了解如何使用各種計算目標來訓練模型。  所有計算目標的步驟皆遵循相同的工作流程：
1. 如果您沒有計算目標，請__建立__一個。
2. 將計算目標__連結__至您的工作區。
3. __設定__計算目標，以便其包含您的指令碼所需的 Python 環境和封裝相依性。


>[!NOTE]
> 本文中的程式碼已使用 Azure Machine Learning SDK 1.0.6 版進行測試。

## <a name="compute-targets-for-training"></a>訓練用的計算目標

Azure Machine Learning 服務在不同計算目標上提供不同的支援。 一般模型開發生命週期會先開始開發/測試少量的資料。 在這個階段，我們建議使用本機環境。 例如，您的本機電腦或雲端式虛擬機器。 您對於較大的資料集相應增加您的定型或進行分散式定型時，建議使用 Azure Machine Learning Compute 建立單一或多重節點叢集，在每次提交執行時自動調整。 您也可以附加您自己的計算資源，不過支援的各種情節可能會有所不同，詳述如下：


|訓練用的計算目標| GPU 加速 | 自動化<br/> 超參數微調 | 自動化</br> 機器學習服務 | Azure Machine Learning 管線 |
|----|:----:|:----:|:----:|:----:|
|[本機電腦](#local)| 可能 | &nbsp; | ✓ | &nbsp; |
|[Azure Machine Learning Compute](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[遠端虛擬機器](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](how-to-create-your-first-pipeline.md#databricks)| &nbsp; | &nbsp; | ✓ | ✓ |
|[Azure Data Lake Analytics](how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | &nbsp; | ✓ |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |
|[Azure Batch](#azbatch)| &nbsp; | &nbsp; | &nbsp; | ✓ |

**所有計算目標皆可用於多個定型作業**。 例如，將遠端 VM 連結至您的工作區之後，您可以將它重複用於多個作業。

> [!NOTE]
> 您可以將 Azure Machine Learning Compute 建立成持續性資源，也可以在要求回合時才以動態方式建立。 回合型建立會在定型回合完成後移除計算目標，因此您無法重複使用以此方式建立的計算目標。

## <a name="whats-a-run-configuration"></a>什麼是回合組態？

定型時，通常在本機電腦上啟動，然後在不同的計算目標上執行該定型指令碼。 使用 Azure Machine Learning 服務時，您無須變更指令碼，即可在各種計算目標上執行指令碼。 

您需要做的是使用**回合組態**，為每個計算目標定義環境。  然後，當您想要在不同的計算目標上執行定型實驗時，為該計算指定回合組態。 

深入了解本文末尾的[提交實驗](#submit)。

### <a name="manage-environment-and-dependencies"></a>管理環境和相依性

當您建立回合組態時，需要決定如何管理環境和計算目標上的相依性。 

#### <a name="system-managed-environment"></a>系統管理的環境

當您希望 [Conda](https://conda.io/docs/) 為您管理 Python 環境和指令碼相依性時，請使用系統管理的環境。 預設情況下，系統管理的環境是最常見的選擇。 它對遠端計算目標很有用，尤其是當您無法設定該目標時。 

您只需要使用 [CondaDependency 類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py)，指定每個封裝相依性，然後 Conda 會在您工作區的 **aml_config** 目錄中，建立名為 **conda_dependencies.yml** 的檔案，其中包含封裝相依性的清單，並在您提交定型實驗時，設定 Python 環境。 

視所需相依性的大小而定，新環境的初始設定可能會需要數分鐘的時間。 只要套件清單保持不變，設定時間就只會發生一次。
  
下列程式碼顯示需要 scikit-learn 之系統管理環境的範例：
    
[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/runconfig.py?name=run_system_managed)]

#### <a name="user-managed-environment"></a>使用者管理的環境

針對使用者管理的環境，您需負責設定環境，並在計算目標上安裝定型指令碼所需的每個套件。 如果已經設定好定型環境 (例如在本機電腦上)，您便可以將 `user_managed_dependencies` 設定為 True 來略過設定步驟。 Conda 不會檢查您的環境，或為您安裝任何項目。

下列程式碼顯示為使用者管理的環境設定定型回合的範例：

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/runconfig.py?name=run_user_managed)]
  
## <a name="set-up-compute-targets-with-python"></a>使用 Python 設定計算目標

使用各節來設定這些計算目標：

* [本機電腦](#local)
* [Azure Machine Learning Compute](#amlcompute)
* [遠端虛擬機器](#vm)
* [Azure HDInsight](#hdinsight)


### <a id="local"></a>本機電腦

1. **建立並連結**：無須建立或連結計算目標，即可將本機電腦作為定型環境。  

1. **設定**：當您使用本機電腦作為計算目標時，會在您的[開發環境](how-to-configure-environment.md)中執行定型程式碼。  如果該環境已經有您需要的 Python 封裝，請使用使用者管理的環境。

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

現在您已連結計算並設定執行，下一步是[提交定型回合](#submit)。

### <a id="amlcompute"></a>Azure Machine Learning Compute

Azure Machine Learning Compute 是一種受控的計算基礎結構，可讓使用者輕鬆建立單一或多重節點計算。 計算會在工作區的區域內建立為能夠與工作區中的其他使用者共用的資源。 計算會在提交作業時自動相應增加，而且可以放在 Azure 虛擬網路中。 計算會在容器化環境中執行，並在 [Docker 容器](https://www.docker.com/why-docker)中封裝模型的相依性。

您可以使用 Azure Machine Learning Compute 在雲端中的 CPU 或 GPU 計算節點叢集散發定型程序。 如需包含 GPU 的 VM 大小有關的詳細資訊，請參閱 [GPU 最佳化虛擬機器大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)。

Azure Machine Learning Compute 有預設限制，例如可配置的核心數目。 如需詳細資訊，請參閱[管理和要求 Azure 資源的配額](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas)。


當您排定回合時，可以隨需建立 Azure Machine Learning 計算環境，也可以建立 Azure Machine Learning 計算環境作為持續性資源。

#### <a name="run-based-creation"></a>執行式建立

您可以建立 Azure Machine Learning Compute 做為執行階段的計算目標。 系統會自動為您的執行建立計算。 執行完成之後，就會自動刪除計算。 

> [!NOTE]
> 若要指定要使用的節點數目上限，您會通常設定`node_count`的節點數目。 目前沒有 (04/04/2019) 的錯誤會讓這無法運作。 因應措施，使用`amlcompute._cluster_max_node_count`執行組態的屬性。 例如： `run_config.amlcompute._cluster_max_node_count = 5`。

> [!IMPORTANT]
> Azure Machine Learning Compute 的執行式建立目前為「預覽」狀態。 如果您使用自動化超參數調整或自動化機器學習，請勿使用回合式建立。 若要使用超參數微調或自動化機器學習，請改為建立[持續性計算](#persistent)目標。

1.  **建立、連結及設定**：執行式建立會使用回合組態執行建立、連結和設定計算目標的所有必要步驟。  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


現在您已連結計算並設定執行，下一步是[提交定型回合](#submit)。

#### <a id="persistent"></a>持續性計算

持續性 Azure Machine Learning Compute 可以跨作業重複使用。 計算可以與工作區中的其他使用者共用，並且在作業之間保留。

1. **建立並連結**：若要使用 Python 建立持續性 Azure Machine Learning Compute 資源，請指定 **vm_size** 和 **max_nodes** 屬性。 Azure Machine Learning 接著會對於其他屬性使用智慧型預設值。 未使用時，計算會自動向下調整為零節點。   視需要建立專用的虛擬機器以執行您的作業。
    
    * **vm_size**：Azure Machine Learning Compute 建立的節點虛擬機器系列。
    * **max_nodes**：在 Azure Machine Learning Compute 上執行作業時，自動向上調整的最大節點數。
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   建立 Azure Machine Learning Compute 時，您也可以設定多個進階屬性。 這些屬性可讓您建立固定大小的持續性叢集，也可以在您訂用帳戶中現有的 Azure 虛擬網路內建立。  如需詳細資料，請參閱 [AmlCompute 類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    )。
    
   或者，您可以[在 Azure 入口網站中](#portal-create)建立並連結持續性 Azure Machine Learning Compute 資源。

1. **設定**：為持續性計算目標建立回合組態。

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

現在您已連結計算並設定執行，下一步是[提交定型回合](#submit)。


### <a id="vm"></a>遠端虛擬機器

Azure Machine Learning 也支援提供您自己的計算資源，並將其附加到您的工作區。 其中一種資源類型是任意遠端 VM，只要可以從 Azure Machine Learning 服務存取即可。 此資源可以是 Azure VM，也可以是在您組織或內部部署的遠端伺服器。 具體來說，只要有 IP 位址和認證 (使用者名稱和密碼或 SSH 金鑰)，即可對於遠端執行使用任何可存取的 VM。

您可以使用系統建立的 Conda 環境、現有的 Python 環境或 Docker 容器。 若要在 Docker 容器上執行，您必須在虛擬機器上執行的 Docker 引擎。 您想要比本機電腦的更有彈性、以雲端為基礎的開發/測試環境時，這項功能特別有用。

使用 Azure 資料科學虛擬機器 (DSVM) 作為對於此案例選擇的 Azure 虛擬機器。 此虛擬機器是 Azure 中預先設定的資料科學和 AI 開發環境。 VM 會針對整個生命週期的機器學習開發，提供精心選擇的工具和架構。 如需有關如何使用 DSVM 搭配 Azure Machine Learning 的詳細資訊，請參閱[設定開發環境](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm)。

1. **建立**：先建立 DSVM，才能使用它定型模型。 若要建立此資源，請參閱[佈建適用於 Linux (Ubuntu) 的資料科學虛擬機器](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)。

    > [!WARNING]
    > Azure Machine Learning 只支援執行 Ubuntu 的虛擬機器。 當您建立 VM 或選擇現有的 VM 時，您必須選取使用 Ubuntu 的 VM。

1. **連結**：若要附加現有的虛擬機器作為計算目標，您必須提供虛擬機器的完整網域名稱 (FQDN)、使用者名稱與密碼。 在範例中，請以 VM 的公用 FQDN 或公用 IP 位址取代 \<fqdn>。 將 \<username> 和 \<password> 取代為 VM 的 SSH 使用者名稱和密碼。

   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   attach_config = RemoteCompute.attach_configuration(address = "<fqdn>",
                                                    ssh_port=22,
                                                    username='<username>',
                                                    password="<password>")

   # If you authenticate with SSH keys instead, use this code:
   #                                                  ssh_port=22,
   #                                                  username='<username>',
   #                                                  password=None,
   #                                                  private_key_file="<path-to-file>",
   #                                                  private_key_passphrase="<passphrase>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   或者，您可以[使用 Azure 入口網站](#portal-reuse)，將 DSVM 連結到工作區。

1. **設定**：為 DSVM 計算目標建立回合組態。 Docker 和 Conda 用來建立和設定 DSVM 上的定型環境。

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


現在您已連結計算並設定執行，下一步是[提交定型回合](#submit)。

### <a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight 是巨量資料分析的常用平台。 此平台會提供 Apache Spark，可用來將您的模型定型。

1. **建立**：先建立 HDInsight 叢集，才能使用它定型模型。 若要在 HDInsight 叢集上建立 Spark，請參閱[在 HDInsight 中建立 Spark 叢集](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql)。 

    建立叢集時，必須指定 SSH 使用者名稱與密碼。 請記下這些值，因為使用 HDInsight 作為計算目標時會需要它們。
    
    建立叢集之後，使用下列主機名稱連結至叢集：\<clustername>-ssh.azurehdinsight.net，其中 \<clustername> 是您提供的叢集名稱。 

1. **連結**：若要連結 HDInsight 叢集作為計算目標，必須提供 HDInsight 叢集的主機名稱、使用者名稱與密碼。 下列範例會使用 SDK 將叢集附加到您的工作區。 在此範例中，將 \<clustername> 取代為叢集的名稱。 將 \<username> 和 \<password> 取代為叢集的 SSH 使用者名稱和密碼。

   ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase
    attach_config = HDInsightCompute.attach_configuration(address='<clustername>-ssh.azureinsight.net', 
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   或者，您可以[使用 Azure 入口網站](#portal-reuse)，將 HDInsight 叢集連結到工作區。

1. **設定**：為 HDI 計算目標建立回合組態。 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


現在您已連結計算並設定執行，下一步是[提交定型回合](#submit)。


### <a id="azbatch"></a>Azure Batch 

Azure 批次用來在雲端有效地執行大規模平行和高效能運算 (HPC) 應用程式。 AzureBatchStep 可以使用 Azure 機器學習服務管線中，將作業提交至機器的 Azure Batch 集區。

若要附加做為計算目標的 Azure Batch，您必須使用 Azure 機器學習服務 SDK，並提供下列資訊：

-   **Azure Batch 計算名稱**:好記的名稱，用於在工作區中計算
-   **Azure Batch 帳戶名稱**:Azure Batch 帳戶的名稱
-   **資源群組**：包含 Azure Batch 帳戶的資源群組。

下列程式碼示範如何附加做為計算目標的 Azure Batch:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

batch_compute_name = 'mybatchcompute' # Name to associate with new compute in workspace

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>" # Name of the Batch account
batch_resource_group = "<batch_resource_group>" # Name of the resource group which contains this account

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

## <a name="set-up-compute-in-the-azure-portal"></a>在 Azure 入口網站中設定計算

您可以在 Azure 入口網站中，存取與您工作區相關聯的計算目標。  您可以使用入口網站以執行下列操作：

* [檢視連結至工作區的計算目標](#portal-view)
* 在工作區中[建立計算目標](#portal-create)
* [連結已在工作區外建立的計算目標](#portal-reuse)

建立目標並連結至工作區之後，您會搭配 `ComputeTarget` 物件，將目標用於回合組態中： 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a id="portal-view"></a>檢視計算目標


若要查看您工作區的計算目標，請使用下列步驟：

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)，然後開啟您的工作區。 
1. 在 [應用程式] 底下，選取 [計算]。

    ![[檢視計算] 索引標籤](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a id="portal-create"></a>建立計算目標

依照上述步驟來檢視計算目標的清單。 接著，使用下列步驟建立計算目標： 

1. 選取加號 (+) 以新增計算目標。

    ![新增計算目標](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. 輸入計算目標的名稱。 

1. 選取 [Machine Learning Compute] 作為要用於 [定型] 的計算類型。 

    >[!NOTE]
    >Azure Machine Learning Compute 是您可以在 Azure 入口網站中建立的唯一受控計算資源。  建立之後，即可連結所有其他計算資源。

1. 填寫表單。 針對必要的屬性 (特別是 **VM 系列**) 和**節點上限**提供值，以便向上微調計算。  

    ![填妥表單](./media/how-to-set-up-training-targets/add-compute-form.png) 

1. 選取 [建立] 。


1. 從清單選取計算目標以檢視建立作業的狀態：

    ![選取計算目標來檢視建立作業狀態](./media/how-to-set-up-training-targets/View_list.png)

1. 接著，您會看到該計算目標的詳細資料： 

    ![檢視電腦目標的詳細資料](./media/how-to-set-up-training-targets/compute-target-details.png) 



### <a id="portal-reuse"></a>連結計算目標

若要使用在 Azure Machine Learning 服務外建立的計算目標，您必須連結它們。 連結計算目標可讓您的工作區使用它們。

依照稍早所述的步驟來檢視計算目標的清單。 然後使用下列步驟來連結計算目標： 

1. 選取加號 (+) 以新增計算目標。 
1. 輸入計算目標的名稱。 
1. 選取要附加以進行__定型__的計算類型：

    > [!IMPORTANT]
    > 並非所有計算類型都可以從 Azure 入口網站附加。 目前可以附加以進行定型的目標類型包括：
    >
    > * 遠端 VM
    > * Azure Databricks (適用於機器學習管線)
    > * Azure Data Lake Analytics (適用於機器學習管線)
    > * Azure HDInsight

1. 填寫表單，並提供必要屬性的值。

    > [!NOTE]
    > Microsoft 建議使用 SSH 金鑰，因為它們比密碼更安全。 密碼很容易遭受暴力密碼破解攻擊。 SSH 金鑰需要密碼編譯簽章。 如需有關如何建立 SSH 金鑰以搭配 Azure 虛擬機器使用的資訊，請參閱下列文件：
    >
    > * [建立和使用 Linux 或 macOS 上的 SSH 金鑰](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [建立和使用在 Windows 上的 SSH 金鑰](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. 選取 [附加]。 
1. 從清單選取計算目標以檢視附加作業的狀態。

## <a name="set-up-compute-with-the-cli"></a>使用 CLI 設定計算

您可以為 Azure Machine Learning 服務使用 [CLI 擴充功能](reference-azure-machine-learning-cli.md)存取與您工作區相關聯的計算目標。  您可以使用 CLI 執行下列操作：

* 建立受控計算目標
* 更新受控計算目標
* 連結非受控計算目標

如需詳細資訊，請參閱[資源管理](reference-azure-machine-learning-cli.md#resource-management)。


## <a id="submit"></a>提交定型回合

建立回合組態之後，會使用該回合組態以執行您的實驗。  提交定型回合的程式碼模式對於所有類型的計算目標皆相同：

1. 建立要執行的實驗
1. 提交回合。
1. 等待回合完成。

### <a name="create-an-experiment"></a>建立實驗

首先，在您的工作區中建立實驗。

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>提交實驗

使用 `ScriptRunConfig` 物件來提交實驗。  此物件包含：

* **source_directory**：包含定型指令碼的來源目錄
* **指令碼**：識別定型指令碼
* **run_config**：回合組態，可依序定義定型的發生位置。

當您提交定型回合時，會對於包含定型指令碼的目錄建立快照集，並傳送到計算目標。 如需詳細資訊，請參閱[快照集](concept-azure-machine-learning-architecture.md#snapshot)。

例如，若要使用[本機目標](#local)組態：

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

藉由使用不同的回合組態 (例如 [amlcompute 目標](#amlcompute))，切換相同實驗以在不同的計算目標中執行：

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

或者您可以：

* 使用 `Estimator` 物件提交實驗，如[使用估算程式將 ML 模型定型](how-to-train-ml-models.md)中所示。 
* [使用 CLI 擴充功能](reference-azure-machine-learning-cli.md#experiments)提交實驗。

## <a name="notebook-examples"></a>筆記本範例

如需具有各種計算目標的定型範例，請參閱下列筆記：
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>後續步驟

* [教學課程：定型模型](tutorial-train-models-with-aml.md)會使用受控計算目標來定型模型。
* 擁有定型的模型之後，請了解[部署模型的方式和位置](how-to-deploy-and-where.md)。
* 檢視 [RunConfiguration 類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) SDK 參考。
* [使用 Azure Machine Learning 服務與 Azure 虛擬網路](how-to-enable-virtual-network.md)
