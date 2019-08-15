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
ms.date: 08/09/2019
ms.custom: seodec18
ms.openlocfilehash: 74d345249e1cbaeb45a1a35d3c3d2f61a4c0b9cf
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2019
ms.locfileid: "69032969"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>已知問題與針對 Azure Machine Learning 服務進行疑難排解

此文章可協助您尋找並修正使用 Azure Machine Learning 服務時遇到的錯誤與失敗。

## <a name="visual-interface-issues"></a>視覺介面問題

機器學習服務問題的視覺化介面。

### <a name="long-compute-preparation-time"></a>長計算準備時間

建立新的計算或呼叫讓計算需要一些時間, 可能需要幾分鐘或更久。 小組正致力於優化。


### <a name="cannot-run-an-experiment-only-contains-dataset"></a>無法執行實驗僅包含資料集 

您可能想要執行實驗只包含資料集, 以將資料集視覺化。 不過, 不允許執行實驗目前只包含資料集。 我們正積極修正此問題。
 
在修正之前, 您可以將資料集連接到任何資料轉換模組 (選取資料集中的資料行、編輯中繼資料、分割資料等) 並執行實驗。 然後您可以將資料集視覺化。 

下圖顯示如何: ![visulize-資料](./media/resource-known-issues/aml-visualize-data.png)

## <a name="sdk-installation-issues"></a>SDK 安裝問題

**錯誤訊息：無法解除安裝 'PyYAML'**

適用於 Python 的 Azure Machine Learning SDK：PyYAML 是已安裝 distutils 的專案。 因此，在有部分解決安裝的情況下，我們無法精確判斷哪些檔案屬於它。 若要繼續安裝 SDK，但略過此錯誤，請使用：

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

**錯誤訊息:`ERROR: No matching distribution found for azureml-dataprep-native`**

Anaconda 的 Python 3.7.4 散發有一個錯誤, 會破壞 azureml sdk 安裝。 此[GitHub 問題](https://github.com/ContinuumIO/anaconda-issues/issues/11195)會討論此問題, 方法是使用此命令來建立新的 Conda 環境:
```bash
conda create -n <env-name> python=3.7.3
```
這會使用 Python 3.7.3 建立 Conda 環境, 而不會在3.7.4 中出現安裝問題。

## <a name="trouble-creating-azure-machine-learning-compute"></a>無法建立 Azure Machine Learning Compute

在極少數的情況下，一些使用者在 GA 發行之前就從 Azure 入口網站建立 Azure Machine Learning 工作區，可能會導致無法在該工作區上建立 Azure Machine Learning Compute。 您可以對該服務提出支援要求，或透過入口網站或 SDK 來建立新的工作區，以立即自行解除鎖定。

## <a name="image-building-failure"></a>映像建置失敗

部署 Web 服務時，映像建置失敗。 因應措施是將 "pynacl==1.2.1" 當成 pip 相依性新增到 Conda 檔案，以用於映像設定。

## <a name="deployment-failure"></a>部署失敗

如果您發現 `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`，請將部署中所使用 VM 的 SKU 變更為具有更多記憶體的 SKU。

## <a name="fpgas"></a>FPGA

您將無法在 FPGA 上部署模型，直到您已針對 FPGA 配額提出要求並已獲得核准。 若要要求存取權，請填妥配額要求表單： https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>自動化機器學習

張量 Flow 自動化機器學習目前不支援張量流程版本1.13。 安裝此版本將導致封裝相依性停止運作。 我們正致力於在未來的版本中修正此問題。 

### <a name="experiment-charts"></a>實驗圖表

自動化 ML 實驗反復專案中顯示的二元分類圖表 (精確度召回、ROC、增益曲線等) 在4/12 之後無法在使用者介面中正確轉譯。 圖表繪圖目前顯示的是反向結果, 其中較佳的執行模型會以較低的結果顯示。 解決方案正在進行調查。

## <a name="databricks"></a>Databricks

Databricks 與 Azure Machine Learning 問題。

### <a name="failure-when-installing-packages"></a>安裝封裝失敗

安裝更多套件時, Azure Databricks 上的 Azure Machine Learning SDK 安裝會失敗。 有些套件 (例如 `psutil`) 會導致發生衝突。 若要避免安裝錯誤, 請藉由凍結程式庫版本來安裝套件。 此問題與 Databricks 有關, 而不是 Azure Machine Learning 服務 SDK。 您也可能會遇到其他程式庫的這個問題。 範例:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

或者, 如果您持續遇到 Python 程式庫的安裝問題, 則可以使用 init 腳本。 這種方法並不正式支援。 如需詳細資訊, 請參閱叢集[範圍的初始化腳本](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)。

### <a name="cancel-an-automated-machine-learning-run"></a>取消自動化機器學習執行

當您在 Azure Databricks 上使用自動化機器學習功能時, 若要取消執行並開始新的實驗執行, 請重新開機您的 Azure Databricks 叢集。

### <a name="10-iterations-for-automated-machine-learning"></a>> 自動化機器學習服務的10次反復專案

在自動化機器學習設定中, 如果您有10個以上的反復`show_output`專案`False` , 當您提交執行時, 請將設定為。

### <a name="widget-for-the-azure-machine-learning-sdkautomated-machine-learning"></a>Azure Machine Learning SDK/自動化機器學習的 Widget

Databricks 筆記本中不支援 Azure Machine Learning SDK widget, 因為筆記本無法剖析 HTML 小工具。 您可以在入口網站中使用此 Python 程式碼, 在您的 Azure Databricks 筆記本資料格中查看 widget:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>匯入錯誤:沒有名為 ' pandas ' 的模組

如果您在使用自動化機器學習時看到此錯誤:

1. 執行此命令以在您的 Azure Databricks 叢集中安裝兩個套件: 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. 卸離叢集, 然後將叢集重新附加至您的筆記本。 

如果這些步驟無法解決問題, 請嘗試重新開機叢集。

### <a name="failtosendfeather"></a>FailToSendFeather

如果您在 Azure Databricks `FailToSendFeather`叢集上讀取資料時看到錯誤, 請參閱下列解決方案:

* 將`azureml-sdk[automl_databricks]`套件升級至最新版本。
* 新增`azure-dataprep` 1.1.8 或更新版本。
* 新增`pyarrow` 0.11 版或更新版本。

## <a name="azure-portal"></a>Azure 入口網站

如果您從 SDK 或入口網站的共用連結直接檢視工作區，將無法在延伸模組中檢視包含訂用帳戶資訊的一般 [概觀] 頁面。 您也無法切換至另一個工作區。 如果要檢視另一個工作區，因應措施是直接前往 [Azure 入口網站](https://portal.azure.com)並搜尋工作區名稱。

## <a name="diagnostic-logs"></a>診斷記錄

當您在尋求協助時，如果能夠提供診斷資訊，有時可能會相當有幫助。 若要查看一些記錄, 請造訪[Azure 入口網站](https://portal.azure.com)並移至您的工作區, 然後選取 [**工作區 > 實驗] > [執行 > 記錄**]。

> [!NOTE]
> Azure Machine Learning 服務會在定型期間記錄各種來源的資訊, 例如 AutoML 或執行定型作業的 Docker 容器。 其中有許多記錄檔並未記載。 如果您遇到問題並聯系 Microsoft 支援服務, 他們可能會在進行疑難排解時使用這些記錄。

## <a name="activity-logs"></a>活動記錄

Azure Machine Learning 工作區中的某些動作並不會將資訊記錄到__活動記錄__中。 例如, 啟動定型執行或註冊模型。

其中有些動作會出現在工作區的 [__活動__] 區域中, 但不會指出起始活動的人員。

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

## <a name="overloaded-azurefile-storage"></a>多載的 AzureFile 儲存體

如果您收到錯誤`Unable to upload project files to working directory in AzureFile because the storage is overloaded`, 請套用下列因應措施。

如果您使用檔案共用來進行其他工作負載 (例如資料傳輸), 建議使用 blob, 讓檔案共用可免費用於提交執行。 您也可以在兩個不同的工作區之間分割工作負載。
