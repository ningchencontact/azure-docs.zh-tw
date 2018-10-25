---
title: Azure Machine Learning 服務的已知問題與疑難排解
description: 取得已知問題、因應措施與疑難排解清單
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 02cee5a3e088c919ec94aee6f46ef6f428b9bb48
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249412"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>已知問題與針對 Azure Machine Learning 服務進行疑難排解
 
此文章可協助您尋找並修正使用 Azure Machine Learning 服務時遇到的錯誤與失敗。 

## <a name="sdk-installation-issues"></a>SDK 安裝問題

**錯誤訊息：無法解除安裝 'PyYAML'** 

PyYAML 是已安裝 distutils 的專案。 因此，我們無法在部分解決安裝的事件中，精確地判斷哪些檔案屬於它。 若要繼續安裝 SDK，但略過此錯誤，請使用：
```Python 
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="image-building-failure"></a>映像建置失敗

部署 Web 服務時，映像建置失敗。 因應措施是將 "pynacl==1.2.1" 當成 pip 相依性新增到 Conda 檔案，以用於映像設定。  

## <a name="pipelines"></a>管線
在資料列中多次呼叫 PythonScriptStep，而未變更指令碼或參數時，就會發生錯誤。 因應措施是重建 PipelineData 物件。

## <a name="fpgas"></a>FPGA
您將無法在 FPGA 上部署模型，直到您已針對 FPGA 配額提出要求並已獲得核准。 若要要求存取權，請填妥配額要求表單： https://aka.ms/aml-real-time-ai

## <a name="databricks"></a>Databricks

Databricks 與 Azure Machine Learning 問題。

1. Databricks 叢集建議：
   
   使用 Python 3 將 Azure Databricks 叢集建立為 v4.x。 建議使用高並行存取叢集。
 
1. 已安裝更多套件時，在 Databricks 上安裝 AML SDK 會失敗。

   有些套件 (例如 `psutil upgrade libs`) 會導致發生衝突。 若要避免發生安裝錯誤，請透過凍結 lib 版本來安裝套件。 這個問題與 Databricks 有關，而與 AML SDK 無關。 範例：
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython=2.2.0
   ```

## <a name="diagnostic-logs"></a>診斷記錄檔
當您在尋求協助時，如果能夠提供診斷資訊，有時可能會相當有幫助。 以下是記錄檔的所在位置：

## <a name="resource-quotas"></a>資源配額

深入了解使用 Azure Machine Learning 時可能會遇到的[資源配額](how-to-manage-quotas.md)。

## <a name="get-more-support"></a>取得更多支援

您可以提交支援要求，並從支援小組、論壇等來源獲得協助。 [深入了解...](support-for-aml-services.md)
