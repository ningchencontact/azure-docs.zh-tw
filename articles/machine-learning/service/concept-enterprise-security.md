---
title: 企業安全性
titleSuffix: Azure Machine Learning service
description: '安全地使用 Azure Machine Learning 服務: 驗證、授權、網路安全性、資料加密和監視。'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/10/2019
ms.openlocfilehash: f0fb6f0d2b2579679ee8a6ec43b3241377701d48
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780909"
---
# <a name="enterprise-security-for-azure-machine-learning-service"></a>Azure Machine Learning 服務的企業安全性

在本文中, 您將瞭解 Azure Machine learning 服務所提供的安全性功能。

使用雲端服務時, 最好的作法是限制只有需要它的使用者才能存取。 這一開始會先瞭解服務所使用的驗證和授權模型。 您可能也會想要限制網路存取, 或安全地將內部部署網路中的資源與雲端中的資源聯結。 資料加密也很重要, 不論是待用, 還是資料在服務之間移動。 最後, 您必須能夠監視服務, 並產生所有活動的審核記錄。

## <a name="authentication"></a>驗證

如果 Azure Active Directory (Azure AD) 設定為相同的, 則支援多重要素驗證。

* 用戶端登入 Azure AD 並取得 Azure Resource Manager token。  完全支援使用者和服務主體。
* 用戶端會將權杖提供給 Azure Resource Manager & 所有 Azure Machine Learning 服務
* Azure Machine Learning 服務提供使用者計算的 Azure Machine Learning token。 例如, Machine Learning Compute。 使用者計算在執行完成之後, 會使用此 Azure Machine Learning token 回呼 Azure Machine Learning 服務 (將範圍限制為工作區)。

![顯示驗證如何在 Azure Machine Learning 服務中運作的螢幕擷取畫面](./media/enterprise-readiness/authentication.png)

### <a name="authentication-for-web-service-deployment"></a>Web 服務部署的驗證

Azure Machine Learning 針對 web 服務、金鑰和權杖支援兩種驗證形式。 每個 webservice 一次只能啟用一種形式的驗證。

|驗證方法|ACI|AKS|
|---|---|---|
|Key|預設為停用| 預設為啟用|
|權杖| 無| 預設為停用 |

#### <a name="authentication-with-keys"></a>使用金鑰進行驗證

當您啟用部署的金鑰驗證時, 會自動建立驗證金鑰。

* 在部署到 Azure Kubernetes Service 時，預設會啟用驗證。
* 在部署到 Azure Container Instances 時，預設會停用驗證。

若要啟用金鑰驗證, 請`auth_enabled`在建立或更新部署時使用參數。

如果已啟用金鑰驗證, 您可以使用`get_keys`方法來取出主要和次要驗證金鑰:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> 如果您需要重新產生金鑰, 請使用[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

#### <a name="authentication-with-tokens"></a>使用權杖進行驗證

當您啟用 web 服務的權杖驗證時, 使用者必須提供 Azure Machine Learning 的 JSON Web 權杖給 web 服務, 才能存取它。

* 當您部署到 Azure Kubernetes Service 時, 預設會停用權杖驗證。
* 當您部署至 Azure 容器實例時, 不支援權杖驗證。

若要控制權杖驗證, 請`token_auth_enabled`在建立或更新部署時使用參數。

如果已啟用權杖驗證, 您可以使用`get_token`方法來取出 JWT 權杖和該權杖的到期時間:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> 您將需要在權杖的`refresh_by`時間之後要求新的權杖。
>
> 我們強烈建議您在與 Azure Kubernetes Service 叢集相同的區域中建立您的 Azure Machine Learning 工作區。 若要使用權杖進行驗證, web 服務會呼叫您的 Azure Machine Learning 工作區建立所在的區域。 如果您的工作區區域無法使用, 則即使您的叢集與工作區位於不同的區域, 您也無法為 webservice 提取權杖。 這實際上會導致 Azure AD 驗證無法使用, 直到您的工作區區域再次可用為止。 此外, 您的叢集區域和工作區區域之間的距離愈大, 提取權杖所需的時間就越長。

## <a name="authorization"></a>Authorization

您可以建立多個工作區，而且每個工作區都可由多人共用。 共用工作區時，您可以透過指派下列角色給使用者來控制對工作區的存取：

* 擁有者
* 參與者
* 讀者

下表列出一些主要 Azure Machine Learning 服務作業, 以及可以執行它們的角色:

| Azure Machine Learning 服務作業 | 擁有者 | 參與者 | 讀者 |
| ---- |:----:|:----:|:----:|
| 建立工作區 | ✓ | ✓ | |
| 共用工作區 | ✓ | |  |
| 建立計算 | ✓ | ✓ | |
| 附加計算 | ✓ | ✓ | |
| 附加資料存放區 | ✓ | ✓ | |
| 執行實驗 | ✓ | ✓ | |
| 視圖執行/計量 | ✓ | ✓ | ✓ |
| 註冊模型 | ✓ | ✓ | |
| 建立映像 | ✓ | ✓ | |
| 部署 Web 服務 | ✓ | ✓ | |
| 視圖模型/影像 | ✓ | ✓ | ✓ |
| 呼叫 web 服務 | ✓ | ✓ | ✓ |

如果內建角色不足以滿足您的需求, 您也可以建立自訂角色。 我們支援的自訂角色僅適用于工作區上的作業和 Machine Learning Compute。 自訂角色可能具有工作區的讀取、寫入或刪除許可權, 以及該工作區中的計算資源。 您可以在特定工作區層級、特定的資源群組層級或特定的訂用帳戶層級上, 讓角色可供使用。 如需詳細資訊, 請參閱[管理 Azure Machine Learning 工作區中的使用者和角色](how-to-assign-roles.md)

### <a name="securing-compute-and-data"></a>保護計算和資料

擁有者和參與者可以使用附加至工作區的所有計算目標和資料存放區。  
每個工作區也有相關聯的系統指派受控識別 (與工作區同名), 並在工作區中使用的附加資源上具有下列許可權:

如需受控識別的詳細資訊, 請參閱[適用于 Azure 資源的受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)識別

| Resource | Permissions |
| ----- | ----- |
| 工作區 | 參與者 |
| 儲存體帳戶 | 儲存體 Blob 資料參與者 |
| Key Vault | 存取所有金鑰、秘密、憑證 |
| Azure 容器登錄 | 參與者 |
| 包含工作區的資源群組 | 參與者 |
| 包含 Key Vault 的資源群組 (如果與包含工作區的不同) | 參與者 |

建議系統管理員不要撤銷對上述資源的受控識別存取權。 您可以使用重新同步金鑰作業來還原存取權。

Azure Machine Learning 服務會在您的訂用帳戶中`aml-`, 為每個工作區區域建立額外的應用程式 (名稱開頭為) 和參與者層級存取權。 針對 ex 如果您在美國東部和北歐的另一個工作區中有工作區, 則您會看到兩個這類應用程式。 這是必要的, 因此 Azure Machine Learning 服務可以協助管理計算資源。

## <a name="network-security"></a>網路安全性

Azure Machine Learning 服務依賴其他 Azure 服務來處理計算資源。 計算資源 (計算目標) 用於定型和部署模型。 這些計算目標可建立在虛擬網路內部。 例如，您可以使用 Microsoft 資料科學虛擬機器來定型模型，然後將模型部署到 Azure Kubernetes Service (AKS)。  

如需詳細資訊, 請參閱[如何在虛擬網路中執行實驗和推斷](how-to-enable-virtual-network.md)。

## <a name="data-encryption"></a>資料加密

### <a name="encryption-at-rest"></a>待用加密

#### <a name="azure-blob-storage"></a>Azure Blob 儲存體

Azure Machine Learning 服務會將快照、輸出和記錄儲存在與 Azure Machine Learning 服務工作區系結的 Azure Blob 儲存體帳戶中, 並存在於使用者的訂閱中。 儲存在 Azure Blob 儲存體中的所有資料都會使用 Microsoft 管理的金鑰進行待用加密。

如需如何將您自己的金鑰帶入 Azure Blob 儲存體中儲存之資料的詳細資訊, 請參閱[在 Azure Key Vault 中使用客戶管理的金鑰儲存體服務加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys)。

定型資料通常也會儲存在 Azure Blob 儲存體中, 以供訓練計算之用。 此儲存體不是由 Azure Machine Learning 管理, 而是裝載至計算作為遠端檔案系統。

如需針對您的工作區所使用的 Azure 儲存體帳戶重新產生存取金鑰的詳細資訊, 請參閱[重新產生儲存體存取金鑰](how-to-change-storage-access-key.md)一文。

#### <a name="cosmos-db"></a>Cosmos DB

Azure Machine Learning 服務會將計量和中繼資料儲存到位於 Azure Machine Learning 服務所管理之 Microsoft 訂用帳戶中的 Cosmos DB。 儲存在 Cosmos DB 中的所有資料都會使用 Microsoft 管理的金鑰進行待用加密。

#### <a name="azure-container-registry-acr"></a>Azure Container Registry (ACR)

登錄 (ACR) 中的所有容器映射都會在待用時加密。 Azure 會在儲存映射之前自動將其加密, 並在 Azure Machine Learning 服務提取映射時, 即時將它解密。

#### <a name="machine-learning-compute"></a>Machine Learning Compute

每個計算節點的 OS 磁片會儲存在 Azure 儲存體使用 Azure Machine Learning 服務儲存體帳戶中的 Microsoft 管理金鑰進行加密。 這是暫時性的計算, 而叢集通常會在沒有任何執行排入佇列時縮小。 基礎虛擬機器已解除布建, 且作業系統磁片已刪除。 OS 磁片不支援 Azure 磁片加密。
每部虛擬機器也會有本機暫存磁片供 OS 作業使用。 此磁片也可以選擇性地用來暫存定型資料。 此磁片未加密。
如需 Azure 中待用加密運作方式的詳細資訊, 請參閱[Azure 資料待用加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)。

### <a name="encryption-in-transit"></a>傳輸中加密

各種 Azure Machine Learning 微服務之間的內部通訊, 以及呼叫評分端點的外部通訊, 都是使用 SSL 來支援。 所有 Azure 儲存體存取也會透過安全通道來進行。
如需詳細資訊, 請參閱[使用 SSL 保護 Azure Machine Learning web 服務](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service)。

### <a name="using-azure-key-vault"></a>編輯 Azure Key Vault

Azure Machine Learning 服務使用與工作區相關聯的 Key Vault 實例, 來儲存各種類型的認證:

* 相關聯的儲存體帳戶連接字串
* Azure 容器存放庫實例的密碼
* 資料存放區的連接字串。

HDI HDInsight 和 VM 等計算目標的 SSH 密碼和金鑰會儲存在與 Microsoft 訂用帳戶相關聯的個別 Key Vault 中。 Azure Machine Learning 服務會儲存使用者所提供的任何密碼或金鑰, 而不會產生、授權及儲存自己的 SSH 金鑰, 以便連線至 VM/HDInsight 以執行實驗。
每個工作區都有相關聯的系統指派受控識別 (具有與工作區相同的名稱), 可存取 Key Vault 中的所有金鑰、秘密和憑證。

## <a name="monitoring"></a>監視

使用者可以查看工作區下的活動記錄, 以查看工作區上執行的各種作業, 並取得如作業名稱、所起始的事件、時間戳記等等的基本資訊。

下列螢幕擷取畫面顯示工作區的活動記錄:

![螢幕擷取畫面, 顯示工作區下的活動記錄](./media/enterprise-readiness/workspace-activity-log.png)

評分要求詳細資料會儲存在 AppInsights 中, 這會在建立工作區時于使用者的訂用帳戶中建立。 這包括像是 HTTPMethod、UserAgent、ComputeType、RequestUrl、StatusCode、RequestId、Duration 等欄位。

## <a name="data-flow-diagram"></a>資料流程圖

### <a name="create-workspace"></a>建立工作區

下圖顯示 [建立工作區工作流程]。
使用者從任何支援的 Azure Machine Learning 服務用戶端 (CLI、Python SDK、Azure 入口網站) 登入 Azure AD, 並要求適當的 Azure Resource Manager token。 然後, 使用者會呼叫 Azure Resource Manager 來建立工作區。  Azure Resource Manager 聯絡 Azure Machine Learning 服務資源提供者以布建工作區。  建立工作區時, 會在客戶的訂用帳戶中建立額外的資源:

* KeyVault (用來儲存秘密)
* Azure 儲存體帳戶 (包括 Blob & 檔案共用)
* Azure Container Registry (儲存 docker 映射以進行推斷/評分和實驗)
* Application Insights (用來儲存遙測資料)

客戶也可以視需要布建附加至工作區的其他計算 (Azure Kubernetes Service、VM 等)。

![顯示建立工作區工作流程的螢幕擷取畫面](./media/enterprise-readiness/create-workspace.png)

### <a name="save-source-code-training-scripts"></a>儲存原始碼 (訓練腳本)

下圖顯示程式碼快照集工作流程。
與 Azure Machine Learning 服務工作區相關聯的是目錄 (實驗), 其中包含原始程式碼 (訓練腳本)。  這些會儲存在客戶的本機電腦和雲端 (在客戶的訂用帳戶下的 Azure Blob 儲存體中)。 這些程式碼快照集會用來執行或檢查歷程記錄的審核。

![顯示建立工作區工作流程的螢幕擷取畫面](./media/enterprise-readiness/code-snapshot.png)

### <a name="training"></a>訓練

下圖顯示訓練工作流程。

* 使用上面所儲存之程式碼快照集的快照集識別碼呼叫 Azure Machine Learning 服務
* Azure Machine Learning 服務會建立 & Azure Machine Learning 服務權杖的執行識別碼 (選擇性), 稍後由計算目標 (例如 Machine Learning Compute/VM) 用來重新討論 Azure Machine Learning 服務
* 您可以選擇受控計算 (例如 Machine Learning Compute) 或未受管理的計算 (例如 VM) 來執行您的訓練作業。 下列兩個案例都會說明資料流程:
* (VM/HDInsight –使用 Microsoft 訂用帳戶中 Key Vault 的 SSH 認證來存取)Azure Machine Learning 服務會在計算目標上執行管理程式碼, 其:

   1. 準備環境。 (請注意, Docker 也是 VM 和本機的選項。 如需瞭解如何在 docker 容器上執行實驗的運作方式 Machine Learning Compute, 請參閱下列步驟。)
   1. 下載程式代碼。
   1. 設定環境變數和配置。
   1. 執行使用者腳本 (上面所述的程式碼快照集)。

* (Machine Learning Compute –使用工作區受控識別存取)請注意, 因為 Machine Learning Compute 是受管理的計算, 所以它是由 Microsoft 管理, 因此它會在 Microsoft 訂用帳戶下執行。

   1. 如有需要, 會啟動遠端 Docker 結構。
   1. 將管理程式碼寫入使用者 Azure 檔案共用。
   1. 使用初始命令啟動容器, 也就是上一個步驟中所述的管理程式碼。

#### <a name="querying-runs-and-metrics"></a>查詢執行和計量

此步驟會顯示在流程中, 其中定型運算會將*執行計量*寫回 Azure Machine Learning 服務, 從該處儲存于 Cosmos DB 中。 用戶端可以呼叫 Azure Machine Learning 服務, 然後再從 Cosmos DB 提取計量, 然後傳回給用戶端。

![顯示建立工作區工作流程的螢幕擷取畫面](./media/enterprise-readiness/training-and-metrics.png)

### <a name="creating-web-services"></a>建立 web 服務

下圖顯示推斷工作流程。 推斷 (或模型計分) 是已部署的模型用於預測的階段, 最常見的是生產資料。
請參閱下列詳細資料:

* 使用者使用 Azure ML SDK 之類的用戶端註冊模型
* 使用者使用模型、分數檔案和其他模型相依性建立映射
* Docker 映射會建立並儲存在 ACR 中
* Webservice 會使用上面建立的映射部署到計算目標 (ACI/AKS)
* 評分要求詳細資料會儲存在 AppInsights 中, 也就是使用者的訂用帳戶
* 遙測也會推送至 Microsoft/Azure 訂用帳戶

![顯示建立工作區工作流程的螢幕擷取畫面](./media/enterprise-readiness/inferencing.png)

## <a name="next-steps"></a>後續步驟

* [使用 SSL 保護 Azure Machine Learning Web 服務](how-to-secure-web-service.md)
* [取用部署為 Web 服務的 ML 模型](how-to-consume-web-service.md)
* [如何執行批次預測](how-to-run-batch-predictions.md)
* [使用 Application Insights 監視您的 Azure Machine Learning 模型](how-to-enable-app-insights.md)
* [在生產環境中收集模型資料](how-to-enable-data-collection.md)
* [Azure Machine Learning 服務 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [透過 Azure 虛擬網路使用 Azure Machine Learning 服務](how-to-enable-virtual-network.md)
* [建置建議系統的最佳作法](https://github.com/Microsoft/Recommenders)
* [在 Azure 上建置即時建議 API](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
