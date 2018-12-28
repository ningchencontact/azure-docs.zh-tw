---
title: 已知問題和疑難排解
titleSuffix: Azure Machine Learning service
description: 取得 Azure Machine Learning 服務的已知問題、因應措施與疑難排解清單。
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 4a4f1691162ab9c9fbd5bc8802ecf7ebc4894d74
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2018
ms.locfileid: "53193666"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>已知問題與針對 Azure Machine Learning 服務進行疑難排解
 
此文章可協助您尋找並修正使用 Azure Machine Learning 服務時遇到的錯誤與失敗。 

## <a name="sdk-installation-issues"></a>SDK 安裝問題

**錯誤訊息：無法解除安裝 'PyYAML'** 

適用於 Python 的 Azure Machine Learning SDK：PyYAML 是已安裝 distutils 的專案。 因此，我們無法在部分解決安裝的事件中，精確地判斷哪些檔案屬於它。 若要繼續安裝 SDK，但略過此錯誤，請使用：
```Python 
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="trouble-creating-azure-machine-learning-compute"></a>無法建立 Azure Machine Learning Compute
在極少數的情況下，一些使用者在 GA 發行之前就從 Azure 入口網站建立 Azure Machine Learning 工作區，可能會導致無法在該工作區上建立 Azure Machine Learning Compute。 您可以對該服務提出支援要求，或透過入口網站或 SDK 來建立新的工作區，以立即自行解除鎖定。 

## <a name="image-building-failure"></a>映像建置失敗

部署 Web 服務時，映像建置失敗。 因應措施是將 "pynacl==1.2.1" 當成 pip 相依性新增到 Conda 檔案，以用於映像設定。  

## <a name="fpgas"></a>FPGA
您將無法在 FPGA 上部署模型，直到您已針對 FPGA 配額提出要求並已獲得核准。 若要要求存取權，請填妥配額要求表單： https://aka.ms/aml-real-time-ai

## <a name="databricks"></a>Databricks

Databricks 與 Azure Machine Learning 問題。

1. Databricks 叢集建議：
   
   使用 Python 3 將 Azure Databricks 叢集建立為 v4.x。 建議使用高並行存取叢集。
 
2. 已安裝更多套件時，在 Databricks 上安裝 AML SDK 會失敗。

   有些套件 (例如 `psutil`) 會導致發生衝突。 若要避免發生安裝錯誤，請透過凍結 lib 版本來安裝套件。 此問題與 Databricks 有關，和 Azure ML SDK 無關 - 您可能也會在其他程式庫遇到相同的問題。 範例：
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
   ```
   或者，如果您持續遇到 Python 程式庫的安裝問題，可以使用初始化指令碼。 這個方法不是正式的支援方法。 您可以參考[這份文件](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)。

3. 在 Databricks 上使用自動化機器學習時，如果看到 `Import error: numpy.core.multiarray failed to import`

   因應措施：使用「在[安裝和附加](https://docs.databricks.com/user-guide/libraries.html#create-a-library)上建立程式庫」，將 Python 程式庫 `numpy==1.14.5` 匯入到您的 Databricks 叢集中。

## <a name="azure-portal"></a>Azure 入口網站
如果您從 SDK 或入口網站的共用連結直接檢視工作區，將無法在延伸模組中檢視包含訂用帳戶資訊的一般 [概觀] 頁面。 您也無法切換至另一個工作區。 如果要檢視另一個工作區，因應措施是直接前往 [Azure 入口網站](https://portal.azure.com)並搜尋工作區名稱。

## <a name="diagnostic-logs"></a>診斷記錄檔
當您在尋求協助時，如果能夠提供診斷資訊，有時可能會相當有幫助。 以下是記錄檔的所在位置：

## <a name="resource-quotas"></a>資源配額

深入了解使用 Azure Machine Learning 時可能會遇到的[資源配額](how-to-manage-quotas.md)。

## <a name="get-more-support"></a>取得更多支援

您可以提交支援要求，並從支援小組、論壇等來源獲得協助。 [深入了解...](support-for-aml-services.md)
