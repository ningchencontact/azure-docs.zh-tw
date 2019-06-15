---
title: 企業安全性
titleSuffix: Azure Machine Learning service
description: 安全地使用 Azure Machine Learning 服務： 驗證、 授權、 網路安全性、 資料加密和監視。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/10/2019
ms.openlocfilehash: e9002b96467d6fa3a5c4fb03fb20bde4e1bf87a1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67059347"
---
# <a name="enterprise-security-for-azure-machine-learning-service"></a>Azure Machine Learning 服務的企業安全性

在本文中，您將搭配 Azure 機器學習服務來了解可用的安全性功能。

使用雲端服務時，最好先存取只限於需要它的使用者。 這一開始會了解服務所使用的驗證和授權模型。 您可能也想要限制的網路存取或安全地參與您的內部部署網路與雲端中的資源。 資料加密也是很重要，在 rest 與服務之間的資料移動時。 最後，您需要能夠監視服務，並產生稽核記錄檔的所有活動。

## <a name="authentication"></a>Authentication
如果 Azure Active Directory (Azure AD) 設定為相同，支援多重要素驗證。
* 用戶端登入 Azure AD，並取得 Azure Resource Manager 的權杖。  完全支援使用者和服務主體。
* 用戶端出示權杖至 Azure Resource Manager 和所有的 Azure Machine Learning 服務
* Azure Machine Learning 服務提供的使用者電腦的 Azure Machine Learning 權杖。 例如，Machine Learning 計算。 此權杖由使用者的 Azure Machine Learning 計算來回呼至 Azure Machine Learning 服務 （限制工作區的範圍） 執行之後完成。

![螢幕擷取畫面顯示 Azure Machine Learning 服務中驗證的運作方式](./media/enterprise-readiness/authentication.png)

### <a name="authentication-keys-for-web-service-deployment"></a>部署 Web 服務的驗證金鑰

當您為部署啟用驗證時，您會自動建立驗證金鑰。

* 在部署到 Azure Kubernetes Service 時，預設會啟用驗證。
* 在部署到 Azure Container Instances 時，預設會停用驗證。

若要控制驗證，請在建立或更新部署時使用 `auth_enabled` 參數。

如果啟用驗證，則可以使用 `get_keys` 方法擷取主要和次要驗證金鑰：

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> 如果您需要重新產生金鑰，使用 [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)


## <a name="authorization"></a>授權

您可以建立多個工作區，而且每個工作區都可由多人共用。 共用工作區時，您可以透過指派下列角色給使用者來控制對工作區的存取：
* 擁有者
* 參與者
* 讀取者
    
下表列出一些主要的 Azure Machine Learning 服務作業和可執行的角色：

| Azure Machine Learning 服務作業 | 擁有者 | 參與者 | 讀取者 |
| ---- |:----:|:----:|:----:|
| 建立工作區 | ✓ | ✓ | |
| 共用工作區 | ✓ | |  |
| 建立計算 | ✓ | ✓ | |
| 附加計算 | ✓ | ✓ | |
| 附加資料存放區 | ✓ | ✓ | |
| 執行實驗 | ✓ | ✓ | |
| 檢視執行/計量 | ✓ | ✓ | ✓ |
| 註冊模型 | ✓ | ✓ | |
| 建立映像 | ✓ | ✓ | |
| 部署 Web 服務 | ✓ | ✓ | |
| 檢視模型/映像 | ✓ | ✓ | ✓ |
| 呼叫 web 服務 | ✓ | ✓ | ✓ |

如果內建的角色不足以執行您的需求，您也可以建立自訂角色。 我們支援只會將自訂角色會對工作區和 Machine Learning 計算作業。 自訂的角色可能會具有讀取、 寫入或刪除工作區和該工作區中的計算資源的權限。 您可以提供角色在特定的工作區層級、 特定的資源群組層級或特定的訂用帳戶層級。 如需詳細資訊，請參閱[管理使用者和 Azure Machine Learning 工作區中的角色](how-to-assign-roles.md)

### <a name="securing-compute-and-data"></a>計算和資料保護
擁有者和參與者可以使用所有計算目標和資料存放區連結至工作區。  
每個工作區也會有下列權限使用的工作區中的附加資源的相關聯的系統指派給受控身分識別 （使用工作區名稱相同）：

如需有關受管理的身分識別的詳細資訊，請參閱[管理適用於 Azure 資源的身分識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

| 資源 | 權限 |
| ----- | ----- |
| 工作區 | 參與者 | 
| 儲存體帳戶 | 儲存體 Blob 資料參與者 | 
| Key Vault | 所有的金鑰，祕密，憑證的存取權 | 
| Azure Container Registry | 參與者 | 
| 包含的工作區的資源群組 | 參與者 | 
| （如果不同於包含工作區），包含金鑰保存庫的資源群組 | 參與者 | 

建議系統管理員不會撤銷的受管理的身分識別以上面所提到的資源存取權。 重新同步金鑰作業，就可以還原存取權。

Azure Machine Learning 服務會建立其他的應用程式 (名稱開頭為`aml-`) 與參與者層級存取您的訂用帳戶中的每個工作區的區域。 Ex。 如果您有工作區，在美國東部和北歐地區另一個工作區相同的訂用帳戶中，您會看到兩個這類應用程式。 這需要使服務可以幫助您管理 Azure Machine Learning 計算資源。


## <a name="network-security"></a>網路安全性

Azure Machine Learning 服務依賴其他 Azure 服務來處理計算資源。 計算資源 (計算目標) 用於定型和部署模型。 這些計算目標可建立在虛擬網路內部。 例如，您可以使用 Microsoft 資料科學虛擬機器來定型模型，然後將模型部署到 Azure Kubernetes Service (AKS)。  

如需詳細資訊，請參閱 <<c0> [ 如何在虛擬網路中執行實驗和推斷](how-to-enable-virtual-network.md)。

## <a name="data-encryption"></a>資料加密

### <a name="encryption-at-rest"></a>待用加密
#### <a name="azure-blob-storage"></a>Azure Blob 儲存體
Azure Machine Learning 服務會繫結至 Azure 機器學習服務工作區，並且住在使用者的訂用帳戶的 Azure Blob 儲存體帳戶中儲存的快照集、 輸出和記錄檔。 Azure Blob 儲存體中的所有資料在待用期間使用 Microsoft-Managed 金鑰都加密。

如需有關如何將您自己的金鑰儲存在 Azure Blob 儲存體中資料的詳細資訊，請參閱[在 Azure Key Vault 中使用客戶管理金鑰的儲存體服務加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys)。

定型資料通常也會儲存在 Azure Blob 儲存體，如此就能夠訓練計算。 此儲存體不是受 Azure Machine Learning，而掛接到計算做為遠端檔案系統。

如需有關重新產生存取金鑰與您的工作區搭配使用 Azure 儲存體帳戶，請參閱[重新產生儲存體存取金鑰](how-to-change-storage-access-key.md)文章。

#### <a name="cosmos-db"></a>Cosmos DB
Azure Machine Learning 服務會將計量和到所在 Cosmos DB 的中繼資料儲存在 Azure Machine Learning 服務所管理的 Microsoft 訂用帳戶。 在待用期間使用 Microsoft 受控金鑰加密儲存在 Cosmos DB 中的所有資料。

#### <a name="azure-container-registry-acr"></a>Azure Container Registry (ACR)
在您的登錄 (ACR) 中的所有容器映像皆在待用都加密。 Azure 會自動加密再將其儲存的映像，並將它解密上即時，Azure Machine Learning 服務提取映像時。

#### <a name="machine-learning-compute"></a>Machine Learning 計算
OS 磁碟的每個計算節點會儲存在 Azure 儲存體已加密您在 Azure Machine Learning 服務的儲存體帳戶中使用 Microsoft 管理的金鑰。 此計算是暫時的並沒有執行時通常叢集相應減少已排入佇列。 解除佈建基礎的虛擬機器，OS 磁碟刪除。 針對 OS 磁碟不支援 azure 磁碟加密。
每個虛擬機器也有作業系統作業的本機暫存磁碟。 此磁碟也選擇性可用階段定型資料。 此磁碟不會加密。 如需有關如何在 Azure 中的待用加密運作方式的詳細資訊，請參閱 < [Azure 資料加密待用](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)。 

### <a name="encryption-in-transit"></a>傳輸中加密
使用 SSL，可支援各種不同的 Azure Machine Learning 的微服務和呼叫的評分端點的外部通訊之間的這兩個內部通訊。 所有的 Azure 儲存體存取也是透過安全通道。 如需詳細資訊，請參閱 <<c0> [ 保護的 Azure Machine Learning web 服務使用 SSL](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service)。

### <a name="using-azure-key-vault"></a>編輯 Azure Key Vault
工作區相關聯的 Key Vault 執行個體由 Azure Machine Learning 服務來儲存各種類型的認證：
* 相關聯的儲存體帳戶連接字串
* Azure 容器存放庫執行個體的密碼
* 連接字串至資料存放區。 

SSH 密碼和計算目標，例如 HDI HDInsight 和 VM 的金鑰會儲存在個別的金鑰保存庫與 Microsoft 訂用帳戶相關聯。 Azure Machine Learning 服務未儲存任何密碼或金鑰提供的使用者而是會產生、 授權，以及儲存它自己的 SSH 金鑰才能連接到 VM/HDInsight 來執行實驗。 每個工作區都有相關聯的系統指派給受控身分識別 （使用工作區名稱相同） 可存取所有的金鑰、 祕密和憑證金鑰保存庫中。

 
## <a name="monitoring"></a>監視

使用者可以看到活動記錄檔，請參閱工作區上執行的各種作業，並取得作業名稱、 事件起始者、 時間戳記等等的基本資訊的工作區下。

下列螢幕擷取畫面顯示工作區的活動記錄檔：

![工作區下的螢幕擷取畫面顯示活動記錄](./media/enterprise-readiness/workspace-activity-log.png)


評分要求詳細資料會儲存在 AppInsights 中，這是在使用者的訂用帳戶時建立工作區。 這包括如 HTTPMethod UserAgent、 ComputeType、 RequestUrl、 StatusCode、 RequestId、 持續時間等欄位。


## <a name="data-flow-diagram"></a>資料流程圖

### <a name="create-workspace"></a>建立工作區
下圖顯示建立工作區的工作流程。
使用者登入 Azure AD 從任何支援的 Azure Machine Learning 服務用戶端 （CLI 中，Python SDK 中，Azure 入口網站），並要求適當的 Azure Resource Manager 權杖。  使用者接著會呼叫 Azure Resource Manager 建立工作區。  Azure Resource Manager 連絡人 Azure Machine Learning 服務來佈建工作區的資源提供者。  在工作區建立期間的客戶的訂用帳戶中建立其他資源：
* 金鑰保存庫 （以儲存祕密）
* Azure 儲存體帳戶 （包括 Blob 和檔案共用）
* Azure Container Registry （以儲存 docker 映像，來推斷/評分和測試）
* Application Insights （以儲存遙測）

視其他附加至工作區 （Azure Kubernetes 服務、 VM 等等） 的計算也可提供客戶。 

![建立工作區的工作流程的螢幕擷取畫面顯示](./media/enterprise-readiness/create-workspace.png)

### <a name="save-source-code-training-scripts"></a>儲存原始碼 （訓練指令碼）
下圖顯示程式碼的快照集工作流程。
相關聯的 Azure Machine Learning 服務工作區是目錄 （實驗），其中包含原始碼 （訓練指令碼）。  這些儲存在客戶的本機電腦上部署和雲端 (Azure Blob 儲存體中客戶的訂用帳戶下）。 這些程式碼的快照集用來執行或的稽核歷程記錄檢查。

![建立工作區的工作流程的螢幕擷取畫面顯示](./media/enterprise-readiness/code-snapshot.png)

### <a name="training"></a>訓練
下圖顯示的訓練工作流程。
* Azure Machine Learning 服務儲存上述程式碼快照集時，稱為快照集識別碼
* 服務會建立 azure Machine Learning 執行識別碼 （選擇性） 和 Azure Machine Learning 服務權杖，稍後用來向 Azure Machine Learning 服務的計算目標例如機器學習計算/VM
* 您可以選擇其中一個受控的計算 （例如。 Machine Learning 計算) 或非受控的計算 （例如。 虛擬機器） 執行定型工作。 資料流程會說明這兩個案例下：
* （VM/HDInsight-使用金鑰保存庫中的 SSH 認證，Microsoft 訂用帳戶中存取）Azure Machine Learning 服務管理程式碼上執行的計算目標的：
    1.  準備環境 (請注意：Docker 是可選擇的 VM/本機以及。 若要了解 docker 容器運作的方式執行實驗 Machine Learning 計算下，請參閱步驟）
    2.  下載程式碼
    3.  設定環境變數設定
    4.  執行使用者指令碼 （上述程式碼快照集）
* （machine Learning 計算 – 使用受管理的工作區的身分識別存取）請注意，因為 Machine Learning 計算是受控的計算也就是它由 Microsoft 管理，因此 Microsoft 訂用帳戶下執行。
    1.  如有需要開始遠端 Docker 建構
    2.  管理程式碼寫入使用者 Azure 檔案共用
    3.  初始啟動容器命令也就是上述的步驟中的管理程式碼


#### <a name="querying-runs--metrics"></a>查詢執行與計量
此步驟中會顯示在訓練其中計算寫入流程*執行計量*回到 Azure Machine Learning 服務，從它取得儲存在 Cosmos DB 中。 用戶端可以呼叫，接著從 Cosmos DB 提取計量並將它傳回至用戶端時將 Azure Machine Learning 服務。

![建立工作區的工作流程的螢幕擷取畫面顯示](./media/enterprise-readiness/training-and-metrics.png)

### <a name="creating-web-services"></a>建立 web 服務
下圖顯示推斷工作流程。 推斷，或模型計分，是階段已部署的模型用於預測，最常在實際執行資料的位置。
請參閱下面的詳細資料：
* 使用者註冊使用 Azure ML SDK 等用戶端的模型
* 使用者會建立使用模型、 評分檔案和其他模型相依性的映像
* 建立並儲存在 ACR 中的 Docker 映像
* Web 服務會部署到計算目標 (ACI/AKS) 使用上述所建立的映像
* 評分要求詳細資料會儲存在 AppInsights 中，也就是使用者的訂用帳戶中
* 遙測也會推送至 Microsoft/Azure 訂用帳戶

![建立工作區的工作流程的螢幕擷取畫面顯示](./media/enterprise-readiness/inferencing.png)

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
