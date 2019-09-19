---
title: 企業安全性
titleSuffix: Azure Machine Learning
description: 安全地使用 Azure Machine Learning：驗證、授權、網路安全性、資料加密和監視。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/07/2019
ms.openlocfilehash: 309cef6ec058d8192bc7a6341b49a59c0000a305
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2019
ms.locfileid: "71035567"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Azure Machine Learning 的企業安全性

在本文中，您將瞭解 Azure Machine Learning 可用的安全性功能。

當您使用雲端服務時，最佳做法是限制只有需要它的使用者才能存取。 一開始先瞭解服務所使用的驗證和授權模型。 您也可能想要限制網路存取，或安全地將內部部署網路中的資源與雲端聯結。 資料加密也很重要，不論是待用，還是資料在服務之間移動。 最後，您必須能夠監視服務，並產生所有活動的審核記錄。

## <a name="authentication"></a>驗證

如果 Azure Active Directory （Azure AD）設定為使用多重要素驗證，則支援。 以下是驗證程式：

1. 用戶端登入以 Azure AD 並取得 Azure Resource Manager token。  完全支援使用者和服務主體。
1. 用戶端會呈現權杖，以 Azure Resource Manager 和所有 Azure Machine Learning。
1. Machine Learning 服務會為使用者計算目標提供 Machine Learning 的服務權杖（例如，Machine Learning Compute）。 在執行完成之後，使用者計算目標會使用此權杖來回呼 Machine Learning 服務。 範圍僅限於工作區。

[![Azure Machine Learning 中的驗證](./media/enterprise-readiness/authentication.png)](./media/enterprise-readiness/authentication-expanded.png)

### <a name="authentication-for-web-service-deployment"></a>Web 服務部署的驗證

Azure Machine Learning 針對 web 服務支援兩種形式的驗證：金鑰和權杖。 每個 web 服務一次只能啟用一種形式的驗證。

|驗證方法|Azure Container Instances|AKS|
|---|---|---|
|Key|預設為停用| 預設為啟用|
|權杖| 無| 預設為停用 |

#### <a name="authentication-with-keys"></a>使用金鑰進行驗證

當您啟用部署的金鑰驗證時，會自動建立驗證金鑰。

* 當您部署至 Azure Kubernetes Service （AKS）時，預設會啟用驗證。
* 當您部署至 Azure 容器實例時，預設會停用驗證。

若要啟用金鑰驗證，請`auth_enabled`在建立或更新部署時使用參數。

如果已啟用金鑰驗證，您可以使用`get_keys`方法來取出主要和次要驗證金鑰：

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> 如果您需要重新產生金鑰，請使用 [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)。

#### <a name="authentication-with-tokens"></a>使用權杖進行驗證

當您啟用 web 服務的權杖驗證時，使用者必須向 web 服務出示 Azure Machine Learning 的 JSON Web 權杖才能存取它。

* 當您部署到 Azure Kubernetes Service 時，預設會停用權杖驗證。
* 當您部署至 Azure 容器實例時，不支援權杖驗證。

若要控制權杖驗證，請`token_auth_enabled`在建立或更新部署時使用參數。

如果已啟用權杖驗證，您可以使用`get_token`方法來取出 JSON Web token （JWT）和該權杖的到期時間：

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> 您必須在權杖的`refresh_by`時間之後要求新的權杖。
>
> 強烈建議您在與 Azure Kubernetes Service 叢集相同的區域中建立您的 Azure Machine Learning 工作區。 
>
> 若要使用權杖進行驗證，web 服務會呼叫您的 Azure Machine Learning 工作區建立所在的區域。 如果您的工作區區域無法使用，您將無法提取 web 服務的權杖，即使您的叢集與工作區位於不同的區域也一樣。 結果是，除非您的工作區區域再次可供使用，否則 Azure AD 驗證無法使用。 
>
> 此外，您的叢集區域和工作區區域之間的距離愈大，提取權杖所需的時間就越長。

## <a name="authorization"></a>Authorization

您可以建立多個工作區，而且每個工作區都可由多人共用。 當您共用工作區時，您可以藉由將這些角色指派給使用者來控制其存取權：

* 擁有者
* 參與者
* 讀者

下表列出一些主要 Azure Machine Learning 作業，以及可以執行它們的角色：

| Azure Machine Learning 作業 | 擁有者 | 參與者 | 讀者 |
| ---- |:----:|:----:|:----:|
| 建立工作區 | ✓ | ✓ | |
| 共用工作區 | ✓ | |  |
| 建立計算目標 | ✓ | ✓ | |
| 附加計算目標 | ✓ | ✓ | |
| 附加資料存放區 | ✓ | ✓ | |
| 執行實驗 | ✓ | ✓ | |
| 視圖執行/計量 | ✓ | ✓ | ✓ |
| 註冊模型 | ✓ | ✓ | |
| 建立映像 | ✓ | ✓ | |
| 部署 Web 服務 | ✓ | ✓ | |
| 視圖模型/影像 | ✓ | ✓ | ✓ |
| 呼叫 web 服務 | ✓ | ✓ | ✓ |

如果內建角色不符合您的需求，您可以建立自訂角色。 只有工作區上的作業和 Machine Learning Compute 支援自訂角色。 自訂角色可以具有工作區的讀取、寫入或刪除許可權，以及該工作區中的計算資源。 您可以在特定工作區層級、特定的資源群組層級或特定的訂用帳戶層級上，讓角色可供使用。 如需詳細資訊，請參閱[管理 Azure Machine Learning 工作區中的使用者和角色](how-to-assign-roles.md)。

### <a name="securing-compute-targets-and-data"></a>保護計算目標和資料

擁有者和參與者可以使用附加至工作區的所有計算目標和資料存放區。  

每個工作區也有相關聯的系統指派受控識別，其名稱與工作區相同。 受控識別在工作區中使用的附加資源上具有下列許可權。

如需受控識別的詳細資訊，請參閱[適用于 Azure 資源的受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)識別。

| Resource | Permissions |
| ----- | ----- |
| 工作區 | 參與者 |
| 儲存體帳戶 | 儲存體 Blob 資料參與者 |
| 金鑰保存庫 | 存取所有金鑰、秘密、憑證 |
| Azure 容器登錄 | 參與者 |
| 包含工作區的資源群組 | 參與者 |
| 包含金鑰保存庫的資源群組（如果與包含工作區的不同） | 參與者 |

我們不建議系統管理員撤銷上表所述資源的受控識別存取權。 您可以使用 [重新同步金鑰] 作業來還原存取權。

Azure Machine Learning 會針對每個工作區區域，在`aml-`您`Microsoft-AzureML-Support-App-`的訂用帳戶中建立額外的應用程式（名稱開頭為或）。 例如，如果您在美國東部有一個工作區，而在同一個訂用帳戶中有一個位於北歐的另一個工作區，您會看到這兩個應用程式。 這些應用程式可讓 Azure Machine Learning 協助您管理計算資源。

## <a name="network-security"></a>網路安全性

Azure Machine Learning 依賴其他 Azure 服務來計算資源。 計算資源 (計算目標) 用於定型和部署模型。 您可以在虛擬網路中建立這些計算目標。 例如，您可以使用 Azure 資料科學虛擬機器來定型模型，然後將模型部署至 AKS。  

如需詳細資訊，請參閱[如何在虛擬網路中執行實驗和推斷](how-to-enable-virtual-network.md)。

## <a name="data-encryption"></a>資料加密

### <a name="encryption-at-rest"></a>待用加密

#### <a name="azure-blob-storage"></a>Azure Blob 儲存體

Azure Machine Learning 會將快照、輸出和記錄儲存在與 Azure Machine Learning 工作區和您的訂用帳戶相關聯的 Azure Blob 儲存體帳戶中。 Azure Blob 儲存體中儲存的所有資料都會使用 Microsoft 管理的金鑰進行待用加密。

如需如何針對儲存在 Azure Blob 儲存體中的資料使用您自己的金鑰的相關資訊，請參閱[Azure Key Vault 中的 Azure 儲存體使用客戶管理的金鑰加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys)。

定型資料通常也會儲存在 Azure Blob 儲存體中，以供定型計算目標。 此儲存體不受 Azure Machine Learning 管理，而是裝載至計算目標做為遠端檔案系統。

如需有關為您的工作區所使用的 Azure 儲存體帳戶重新產生存取金鑰的資訊，請參閱[重新產生儲存體存取金鑰](how-to-change-storage-access-key.md)。

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning 會在與 Azure Machine Learning 所管理之 Microsoft 訂用帳戶相關聯的 Azure Cosmos DB 實例中儲存計量和中繼資料。 Azure Cosmos DB 中儲存的所有資料都會使用 Microsoft 管理的金鑰進行待用加密。

#### <a name="azure-container-registry"></a>Azure 容器登錄

登錄（Azure Container Registry）中的所有容器映射都會在待用時加密。 Azure 會在儲存映射前自動將其加密，並在 Azure Machine Learning 提取映射時立即將其解密。

#### <a name="machine-learning-compute"></a>Machine Learning Compute

儲存在 Azure 儲存體中的每個計算節點的 OS 磁片都會使用 Azure Machine Learning 儲存體帳戶中 Microsoft 管理的金鑰進行加密。 此計算目標是暫時的，而且叢集通常會在沒有任何執行排入佇列時相應減少。 基礎虛擬機器已解除布建，且作業系統磁片已刪除。 OS 磁片不支援 Azure 磁碟加密。

每部虛擬機器也會有本機暫存磁片供 OS 作業使用。 如果您想要的話，可以使用磁片來暫存定型資料。 磁片未加密。
如需 Azure 中待用加密運作方式的詳細資訊，請參閱[azure 待用資料加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)。

### <a name="encryption-in-transit"></a>傳輸中加密

您可以使用 SSL 來保護 Azure Machine Learning 微服務之間的內部通訊，以及保護對評分端點的外部呼叫。 所有的 Azure 儲存體存取也會透過安全通道進行。

如需詳細資訊，請參閱[使用 SSL 透過 Azure Machine Learning 保護 web 服務](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service)。

### <a name="using-azure-key-vault"></a>編輯 Azure Key Vault

Azure Machine Learning 使用與工作區相關聯的 Azure Key Vault 實例來儲存各種類型的認證：

* 相關聯的儲存體帳戶連接字串
* Azure 容器存放庫實例的密碼
* 資料存放區的連接字串

SSH 密碼和計算目標（例如 Azure HDInsight 和 Vm）的金鑰會儲存在與 Microsoft 訂用帳戶相關聯的個別金鑰保存庫中。 Azure Machine Learning 不會儲存使用者所提供的任何密碼或金鑰。 相反地，它會產生、授權及儲存自己的 SSH 金鑰，以連線到 Vm 和 HDInsight 來執行實驗。

每個工作區都有相關聯的系統指派受控識別，其名稱與工作區相同。 此受控識別可存取金鑰保存庫中的所有金鑰、秘密和憑證。

## <a name="monitoring"></a>監視

### <a name="metrics"></a>計量

您可以使用 Azure 監視器計量來查看和監視 Azure Machine Learning 工作區的計量。 在 [Azure 入口網站](https://portal.azure.com)中，選取您的工作區，然後選取 [**計量**]：

[![顯示工作區範例計量的螢幕擷取畫面](./media/enterprise-readiness/workspace-metrics.png)](./media/enterprise-readiness/workspace-metrics-expanded.png)

計量包含有關執行、部署和註冊的資訊。

如需詳細資訊，請參閱[Azure 監視器中的計量](/azure/azure-monitor/platform/data-platform-metrics)。

### <a name="activity-log"></a>活動記錄

您可以查看工作區的活動記錄，以查看工作區上執行的各種作業。 此記錄檔包含基本資訊，例如作業名稱、事件起始端和時間戳記。

此螢幕擷取畫面顯示工作區的活動記錄：

[![螢幕擷取畫面，顯示工作區的活動記錄](./media/enterprise-readiness/workspace-activity-log.png)](./media/enterprise-readiness/workspace-activity-log-expanded.png)

評分要求詳細資料會儲存在 Application Insights 中。 當您建立工作區時，會在您的訂用帳戶中建立 Application Insights。 記錄的資訊包括 HTTPMethod、UserAgent、ComputeType、RequestUrl、StatusCode、RequestId 和 Duration 等欄位。

> [!IMPORTANT]
> Azure Machine Learning 工作區中的某些動作不會將資訊記錄到活動記錄中。 例如，不會記錄定型執行的開始和模型的註冊。
>
> 其中有些動作會出現在工作區的 [**活動**] 區域中，但這些通知不會指出起始活動的人員。

## <a name="data-flow-diagrams"></a>資料流程圖

### <a name="create-workspace"></a>建立工作區

下圖顯示 [建立工作區工作流程]。

* 使用者從其中一個支援的 Azure Machine Learning 用戶端（Azure CLI、Python SDK、Azure 入口網站）登入 Azure AD，並要求適當的 Azure Resource Manager token。
* 使用者會呼叫 Azure Resource Manager 來建立工作區。 
* Azure Resource Manager 聯絡 Azure Machine Learning 資源提供者以布建工作區。

建立工作區時，會在使用者的訂用帳戶中建立額外的資源：

* Key Vault （用來儲存秘密）
* Azure 儲存體帳戶（包括 blob 和檔案共用）
* Azure Container Registry （儲存 Docker 映射以進行推斷/評分和實驗）
* Application Insights （用來儲存遙測資料）

使用者也可以視需要布建附加至工作區（例如 Azure Kubernetes Service 或 Vm）的其他計算目標。

[![建立工作區工作流程](./media/enterprise-readiness/create-workspace.png)](./media/enterprise-readiness/create-workspace-expanded.png)

### <a name="save-source-code-training-scripts"></a>儲存原始碼（訓練腳本）

下圖顯示程式碼快照集工作流程。

與 Azure Machine Learning 工作區相關聯的目錄（實驗）包含原始程式碼（訓練腳本）。 這些腳本會儲存在您的本機電腦和雲端（在您的訂用帳戶的 Azure Blob 儲存體中）。 程式碼快照集會用來執行或檢查歷程記錄。

[![程式碼快照集工作流程](./media/enterprise-readiness/code-snapshot.png)](./media/enterprise-readiness/code-snapshot-expanded.png)

### <a name="training"></a>訓練

下圖顯示訓練工作流程。

* 會使用上一節中所儲存之程式碼快照集的快照集識別碼來呼叫 Azure Machine Learning。
* Azure Machine Learning 會建立執行識別碼（選擇性）和 Machine Learning 服務權杖，稍後由計算目標（例如 Machine Learning Compute/Vm）用來與 Machine Learning 服務進行通訊。
* 您可以選擇受控計算目標（例如 Machine Learning Compute）或非受控計算目標（例如 Vm）來執行定型作業。 以下是這兩種案例的資料流程：
   * 由 Microsoft 訂用帳戶中的金鑰保存庫中的 SSH 認證存取的 Vm/HDInsight。 Azure Machine Learning 會在計算目標上執行管理程式碼，其：

   1. 準備環境。 （Docker 是 Vm 和本機電腦的選項。 如需瞭解如何在 Docker 容器上執行實驗的運作方式 Machine Learning Compute，請參閱下列步驟。）
   1. 下載程式代碼。
   1. 設定環境變數和配置。
   1. 執行使用者腳本（上一節中所述的程式碼快照集）。

   * Machine Learning Compute，透過工作區管理的身分識別來存取。
因為 Machine Learning Compute 是受管理的計算目標（也就是由 Microsoft 管理），所以它會在您的 Microsoft 訂用帳戶下執行。

   1. 如有需要，會啟動遠端 Docker 結構。
   1. 管理程式碼會寫入使用者的 Azure 檔案儲存體共用。
   1. 使用初始命令啟動容器。 也就是上一個步驟中所述的管理程式碼。

#### <a name="querying-runs-and-metrics"></a>查詢執行和計量

在下面的流程圖中，當定型計算目標將執行計量從 Cosmos DB 資料庫中的儲存體寫回 Azure Machine Learning 時，就會發生此步驟。 用戶端可以呼叫 Azure Machine Learning。 Machine Learning 接著會從 Cosmos DB 資料庫中提取計量，並將其傳回用戶端。

[![訓練工作流程](./media/enterprise-readiness/training-and-metrics.png)](./media/enterprise-readiness/training-and-metrics-expanded.png)

### <a name="creating-web-services"></a>建立 web 服務

下圖顯示推斷工作流程。 推斷或模型計分，是部署的模型用於預測的階段，最常見的是生產資料。

詳細資料如下：

* 使用者會使用 Azure Machine Learning SDK 之類的用戶端來註冊模型。
* 使用者會使用模型、分數檔案和其他模型相依性來建立影像。
* Docker 映射會建立並儲存在 Azure Container Registry 中。
* Web 服務會使用在上一個步驟中建立的映射，部署到計算目標（容器實例/AKS）。
* 評分要求詳細資料會儲存在使用者訂用帳戶中的 Application Insights。
* 遙測也會推送至 Microsoft/Azure 訂用帳戶。

[![推斷工作流程](./media/enterprise-readiness/inferencing.png)](./media/enterprise-readiness/inferencing-expanded.png)

## <a name="next-steps"></a>後續步驟

* [使用 SSL 保護 Azure Machine Learning Web 服務](how-to-secure-web-service.md)
* [使用部署為 web 服務的 Machine Learning 模型](how-to-consume-web-service.md)
* [如何執行批次預測](how-to-run-batch-predictions.md)
* [使用 Application Insights 監視您的 Azure Machine Learning 模型](how-to-enable-app-insights.md)
* [在生產環境中收集模型資料](how-to-enable-data-collection.md)
* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [搭配 Azure 虛擬網路使用 Azure Machine Learning](how-to-enable-virtual-network.md)
* [建置建議系統的最佳作法](https://github.com/Microsoft/Recommenders)
* [在 Azure 上建置即時建議 API](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
