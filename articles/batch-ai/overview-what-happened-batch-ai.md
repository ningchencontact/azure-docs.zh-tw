---
title: Azure Batch AI 的現況為何？ | Microsoft Docs
description: 了解 Azure Batch AI 的現況及 Azure Machine Learning 服務計算選項。
ms.service: batch-ai
services: batch-ai
ms.topic: overview
ms.author: jmartens
author: j-martens
ms.date: 2/28/2019
ms.openlocfilehash: edd6a7e5f385d766d51631d77f5889233af2469a
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194497"
---
# <a name="whats-happening-to-azure-batch-ai"></a>Azure Batch AI 的現況為何？

**Azure Batch AI 服務將在 3 月淘汰。** Batch AI 的大規模定型與計分功能目前已在 [Azure Machine Learning 服務](../machine-learning/service/overview-what-is-azure-ml.md)中提供，其已於 2018 年 12 月 4 日正式推出。

除了許多其他機器學習功能，Azure Machine Learning 服務還包括雲端式受控計算目標來對機器學習服務模型進行定型、部署和評分。 此計算目標稱為 [Azure Machine Learning Compute](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute)。 [立即開始移轉並使用它](#migrate)。 您可以透過其 [Python SDK](../machine-learning/service/quickstart-create-workspace-with-python.md)、命令列介面及 [Azure 入口網站](../machine-learning/service/quickstart-get-started.md)來與 Azure Machine Learning 服務進行互動。

## <a name="support-timeline"></a>支援時間表

此時，您可以和以前一樣使用現有的 Azure Batch AI 訂用帳戶。 不過，您無法建立**新的訂用帳戶**，且我們不會進行任何新的投資。

從 2019 年 3 月 31 日起&nbsp;&nbsp;，未使用的 Batch AI 訂用帳戶將無法運作。

## <a name="compare-to-azure-machine-learning"></a>相較於 Azure Machine Learning
這是用來訓練、部署、自動化及管理機器學習模型的雲端服務，而這一切都在雲端所提供的廣泛規模下進行。 透過[此概觀中的 Azure Machine Learning 服務](../machine-learning/service/overview-what-is-azure-ml.md)取得大致的了解。
 

一般的模型開發生命週期包括資料準備、訓練與測試和部署階段。 此端對端週期可使用機器學習管線來協調。

![流程圖](./media/overview-what-happened-batch-ai/lifecycle.png)


[深入了解服務的運作方式及其主要概念](../machine-learning/service/concept-azure-machine-learning-architecture.md)。 模型訓練工作流程中有許多概念都與 Batch AI 中現有的概念類似。 

具體而言，以下是兩者之間的對應關係：
 
|Batch AI 服務|  Azure Machine Learning 服務|
|:--:|:---:|
|工作區|工作區|
|叢集|   `AmlCompute` 類型的計算|
|檔案伺服器|資料存放區|
|實驗|實驗|
|工作|執行 (允許巢狀執行)|
 
以下是相同資料表的另一個檢視，可協助您進一步檢視這兩項服務：
 
### <a name="batch-ai-hierarchy"></a>Batch AI 階層
![流程圖](./media/overview-what-happened-batch-ai/batchai-heirarchy.png) 
 
### <a name="azure-machine-learning-service-hierarchy"></a>Azure Machine Learning 服務階層
![流程圖](./media/overview-what-happened-batch-ai/azure-machine-learning-service-heirarchy.png) 

## <a name="platform-capabilities"></a>平台功能
Azure Machine Learning 服務導入了一組絕佳的新功能，包括從訓練到部署的端對端堆疊，可讓您直接用於 AI 開發，而不需要管理任何 Azure 資源。 下表比較兩項服務之間的訓練功能支援。

|功能|Batch AI 服務|Azure Machine Learning 服務|
|-------|:-------:|:-------:|
|VM 大小選擇 |CPU/GPU    |CPU/GPU。 也支援用於推斷的 FPGA|
|AI Ready 叢集 (驅動程式、Docker 等等)|  yes |yes|
|節點準備| yes|    否|
|作業系統系列選擇   |部分    |否|
|專用和 LowPriority VM  |yes    |yes|
|自動調整   |yes    |是 (依照預設)|
|自動調整的等待時間  |否 |yes|
|SSH    |yes|   yes|
|叢集層級掛接 |是 (FileShare、Blob、NFS、自訂)   |是 (掛接或下載您的資料存放區)|
|分散式訓練|  yes |yes|
|作業執行模式|    VM 或容器|    容器|
|自訂容器映像|    yes |yes|
|任何工具組    |yes    |是 (執行 Python 指令碼)|
|JobPreparation|    yes |尚未提供|
|作業層級掛接 |是 (FileShare、Blob、NFS、自訂)   |是 (FileShare、Blob)|
|作業監視     |透過 GetJob|    透過執行歷程記錄 (更豐富的資訊、以自訂執行階段推送更多計量)|
|擷取作業記錄和檔案/模型 |   透過 ListFile 和儲存體 API  |透過成品服務|
 |Tensorboard 的支援   |否|    yes|
|VM 系列層級配額 |yes    |是 (您先前的容量可轉移使用)|
 
除了上表所列項目外，Azure Machine Learning 服務中還有有一些 BatchAI 過去未支援的功能。

|功能|Batch AI 服務|Azure Machine Learning 服務|
|-------|:-------:|:-------:|
|環境準備    |否 |是 (Conda 準備和上傳至 ACR)|
|超參數調整  |否|    yes|
|模型管理   |否 |yes|
|運算化/部署| 否  |透過 AKS 和 ACI|
|資料準備   |否 |yes|
|計算目標    |Azure VM  |本機、BatchAI (作為 AmlCompute)、DataBricks、HDInsight|
|自動化 Machine Learning |否|    yes|
|管線  |否 |yes|
|Batch 評分  |yes    |yes|
|入口網站/CLI 支援|    yes |yes|


## <a name="programming-interfaces"></a>程式設計介面

下表列出每項服務可用的各種程式設計介面。
    
|功能|BatchAI 服務|Azure Machine Learning 服務|
|-------|:-------:|:-------:|
|SDK    |Java、C#、Python、Nodejs   |Python (兩項服務針對一般架構均執行以設定為基礎和以估算器為基礎的 SDK)|
|CLI    |yes    |尚未提供|
|Azure 入口網站   |yes    |是 (作業提交除外)|
|REST API   |yes    |在，分散於微服務之間|


從預覽 BatchAI 升級至正式運作的 Azure Machine Learning 服務，可透過估算器和資料存放區等更容易使用的概念讓您獲得更好的體驗。 此外，也可保證 GA 層級的 Azure 服務 SLA 和客戶支援。

Azure Machine Learning 服務也導入了像是自動化機器學習、超參數調整和 ML 管線等新功能，可運用在大部分的大規模 AI 工作負載中。 無須切換至不同服務即可部署定型模型的能力，有助於完成從資料準備 (使用資料準備 SDK) 到運算化和模型監視的資料科學迴圈。

<a name="migrate"></a>
## <a name="migrate"></a>移轉

在[遷移至 Azure Machine Learning 服務](how-to-migrate.md)一文中，了解如何遷移，以及您使用的程式碼如何對應至 Azure Machine Learning 服務中的程式碼。

## <a name="get-support"></a>取得支援

BatchAI 預定在 3 月 31 日淘汰，且目前已封鎖對服務註冊新的訂用帳戶，除非透過支援申請例外處理而列於允許清單中。  如有任何疑問，或是在移轉到 Azure Machine Learning 服務的過程中有任何意見，請透過 [Azure Batch AI 訓練預覽版](mailto:AzureBatchAITrainingPreview@service.microsoft.com)與我們連絡。

Azure Machine Learning 服務已是正式運作的服務。 這表示它附有經認可的 SLA 和各種支援方案可供選擇。

透過 BatchAI 服務或透過 Azure Machine Learning 服務使用 Azure 基礎結構的定價應該是相同的，因為在這兩種情況下，我們都只收取基礎計算的費用。 如需詳細資訊，請參閱[定價計算機](https://azure.microsoft.com/pricing/details/machine-learning-service/)。

請在 [Azure 入口網站](https://azure.microsoft.com/global-infrastructure/services/?products=batch-ai,machine-learning-service&regions=all)上檢視這兩項服務的可用區域。


## <a name="next-steps"></a>後續步驟

+ 了解[如何遷移](how-to-migrate.md)，以及您使用的程式碼現在如何對應至 Azure Machine Learning 服務中的程式碼。

+ 請參閱 [Azure Machine Learning 服務概觀](../machine-learning/service/overview-what-is-azure-ml.md)。

+ 使用 Azure Machine Learning 服務[為模型定型設定計算目標](../machine-learning/service/how-to-set-up-training-targets.md)。

+ 檢閱 [Azure 藍圖](https://azure.microsoft.com/updates/)以了解其他 Azure 服務更新。
