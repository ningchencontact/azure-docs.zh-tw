---
title: 已知問題和疑難排解
titleSuffix: Azure Machine Learning service
description: 取得 Azure Machine Learning 服務的已知問題、因應措施與疑難排解清單。
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/29/2019
ms.custom: seodec18
ms.openlocfilehash: 242b7cb45e9a73e78ae9f9b62e83311e110e07ee
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021098"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>已知問題與針對 Azure Machine Learning 服務進行疑難排解

此文章可協助您尋找並修正使用 Azure Machine Learning 服務時遇到的錯誤與失敗。

## <a name="visual-interface-issues"></a>視覺化介面問題

機器學習服務問題的視覺化介面。

### <a name="long-compute-preparation-time"></a>長時間計算的準備時間

建立新的計算或呼叫離開的運算需要時間，可能是幾分鐘的時間或更久時間。 小組正著手進行最佳化。


### <a name="cannot-run-an-experiment-only-contains-dataset"></a>無法執行實驗只包含資料集 

您可能想要執行實驗只包含以視覺化方式檢視資料集的資料集。 不過，它具有不允許執行實驗只包含資料集現在。 我們主動修正此問題。
 
之前修正程式中，您可以將資料集連接到任何資料轉換模組 （選取資料行中資料集 」、 「 編輯中繼資料 」 「 分割資料等），並執行實驗。 然後您可以將資料集視覺化。 

下圖顯示方式： ![visulize 資料](./media/resource-known-issues/aml-visualize-data.png)

## <a name="sdk-installation-issues"></a>SDK 安裝問題

**錯誤訊息：無法解除安裝 'PyYAML'**

適用於 Python 的 Azure Machine Learning SDK：PyYAML 是已安裝 distutils 的專案。 因此，在有部分解決安裝的情況下，我們無法精確判斷哪些檔案屬於它。 若要繼續安裝 SDK，但略過此錯誤，請使用：

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="trouble-creating-azure-machine-learning-compute"></a>無法建立 Azure Machine Learning Compute

在極少數的情況下，一些使用者在 GA 發行之前就從 Azure 入口網站建立 Azure Machine Learning 工作區，可能會導致無法在該工作區上建立 Azure Machine Learning Compute。 您可以對該服務提出支援要求，或透過入口網站或 SDK 來建立新的工作區，以立即自行解除鎖定。

## <a name="image-building-failure"></a>映像建置失敗

部署 Web 服務時，映像建置失敗。 因應措施是將 "pynacl==1.2.1" 當成 pip 相依性新增到 Conda 檔案，以用於映像設定。

## <a name="deployment-failure"></a>部署失敗

如果您發現 `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`，請將部署中所使用 VM 的 SKU 變更為具有更多記憶體的 SKU。

## <a name="fpgas"></a>FPGA

您將無法在 FPGA 上部署模型，直到您已針對 FPGA 配額提出要求並已獲得核准。 若要要求存取權，請填妥配額要求表單： https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>自動化機器學習

Tensor Flow 自動化機器學習服務目前不支援 tensor flow 版本 1.13。 安裝此版本將會停止運作的套件相依性。 我們正努力在未來版本中修正此問題。 


## <a name="databricks"></a>Databricks

Databricks 與 Azure Machine Learning 問題。

### <a name="failure-when-installing-packages"></a>安裝套件時的失敗

在 Azure Databricks 上的 azure 機器學習服務 SDK 安裝失敗，安裝多個套件時。 有些套件 (例如 `psutil`) 會導致發生衝突。 若要避免安裝錯誤，請透過凍結的程式庫版本中安裝套件。 此問題與 Databricks，不適用於 Azure Machine Learning 服務 SDK。 您也可能會遇到這個問題的其他程式庫。 範例：

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

或者，您可以使用 init 指令碼，如果您保留遇到安裝的 Python 程式庫的問題。 這個方法不受正式支援。 如需詳細資訊，請參閱 <<c0> [ 叢集為範圍的 init 指令碼](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)。

### <a name="cancel-an-automated-machine-learning-run"></a>取消自動化的機器學習服務執行

當您使用自動化的機器學習服務在 Azure Databricks 上的功能時，以取消執行，並開始新的實驗執行時，重新啟動您的 Azure Databricks 叢集。

### <a name="10-iterations-for-automated-machine-learning"></a>> 自動化的機器學習服務 10 個反覆項目

在 自動化的機器學習的設定，如果您有超過 10 個反覆項目，設定`show_output`至`False`當您提交的執行。

### <a name="widget-for-the-azure-machine-learning-sdkautomated-machine-learning"></a>Azure Machine Learning SDK/自動化機器學習服務的小工具

Azure 機器學習服務 SDK 的小工具不支援在 Databricks notebook 中，因為 notebook 無法剖析的 HTML widget。 您可以檢視入口網站中的小工具，在您的 Azure Databricks notebook 資料格中使用此 Python 程式碼：

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>匯入錯誤：沒有名為 'pandas.core.indexes' 的模組

如果您看到此錯誤，當您使用自動化機器學習服務：

1. 執行此命令以安裝在您的 Azure Databricks 叢集中的兩個套件： 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. 中斷連結並再重新附加至您的 notebook 叢集。 

如果上述步驟未能解決此問題，請嘗試重新啟動叢集。

## <a name="azure-portal"></a>Azure 入口網站

如果您從 SDK 或入口網站的共用連結直接檢視工作區，將無法在延伸模組中檢視包含訂用帳戶資訊的一般 [概觀] 頁面。 您也無法切換至另一個工作區。 如果要檢視另一個工作區，因應措施是直接前往 [Azure 入口網站](https://portal.azure.com)並搜尋工作區名稱。

## <a name="diagnostic-logs"></a>診斷記錄

當您在尋求協助時，如果能夠提供診斷資訊，有時可能會相當有幫助。 若要查看一些記錄檔，請瀏覽[Azure 入口網站](https://portal.azure.com)並移至您的工作區並選取**工作區 > 實驗 > 執行 > 記錄檔**。

## <a name="resource-quotas"></a>資源配額

深入了解使用 Azure Machine Learning 時可能會遇到的[資源配額](how-to-manage-quotas.md)。

## <a name="authentication-errors"></a>驗證錯誤

如果您從遠端工作執行計算目標的管理作業，您會收到下列錯誤的其中一個：

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

例如，如果您嘗試從 ML 管線建立或連結為遠端執行所提交的計算目標，您會收到錯誤。
