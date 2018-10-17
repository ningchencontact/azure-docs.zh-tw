---
title: 快速入門：在 Azure 入口網站中建立機器學習服務工作區 - Azure Machine Learning
description: 使用 Azure 入口網站來建立 Azure Machine Learning 服務工作區。 此工作區是雲端中使用 Azure Machine Learning services，來實驗、定型及部署機器學習服務模型的基礎區塊。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: rastala
ms.author: roastala
ms.date: 09/24/2018
ms.openlocfilehash: b6f0201a36a676e7647b9f5e60bc2df3415b9594
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2018
ms.locfileid: "48831325"
---
# <a name="quickstart-use-the-azure-portal-to-get-started-with-azure-machine-learning-service"></a>快速入門：利用 Azure 入口網站開始使用 Azure Machine Learning 服務

在本快速入門中，您將使用 Azure 入口網站來建立 Azure Machine Learning 服務工作區。 此工作區是雲端中使用 Azure Machine Learning services，來實驗、定型及部署機器學習服務模型的基礎區塊。 

在本教學課程中，您將：

* 在您的 Azure 訂用帳戶中建立工作區
* 試著在 Azure Notebook 中使用 Python 並記錄多個反覆項目中的值
* 在工作區中檢視記錄的值

為了方便起見，當下列的 Azure 資源可在區域內取得時，會自動加入至您的工作區：[容器登錄](https://azure.microsoft.com/services/container-registry/)、[儲存體](https://azure.microsoft.com/services/storage/)、[Application Insights](https://azure.microsoft.com/services/application-insights/) 和 [Key Vault](https://azure.microsoft.com/services/key-vault/)。

您所建立的資源可用來作為其他 Azure Machine Learning 服務教學課程和操作說明文章的必要條件。 如同使用其他 Azure 服務，對於特定資源有一些限制 (例如， BatchAI 叢集大小)，這類資源均與 Azure Machine Learning services 相關聯。 請閱讀[這篇](how-to-manage-quotas.md)文章，以了解預設限制及如何要求更多配額。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。


## <a name="create-a-workspace"></a>建立工作區 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

在工作區頁面上，按一下 `Explore your Azure Machine Learning service workspace`

 ![探索工作區](./media/quickstart-get-started/explore_aml.png)


## <a name="use-the-workspace"></a>使用工作區

現在會看到工作區如何協助您管理機器學習服務指令碼。 在本節中，您可以：

* 在 Azure Notebooks 中開啟 Notebook
* 執行程式碼來建立一些記錄的值
* 在工作區中檢視記錄的值

這個範例示範工作區如何協助您追蹤指令碼中所產生的資訊。 

### <a name="open-a-notebook"></a>開啟 Notebook 

Azure Notebooks 會為 Jupyter Notebook 提供免費的雲端平台，其會利用您執行 Azure Machine Learning services 所需的一切來預先設定。  

按一下 `Open Azure Notebooks` 按鈕以試著您的第一個實驗。

 ![啟動 Azure Notebook](./media/quickstart-get-started/explore_ws.png)

登入之後，即會開啟新索引標籤並出現 `Clone Library` 提示。  按一下 `Clone`


### <a name="run-the-notebook"></a>執行 Notebook

除了兩個 Notebook，您還將看見 `config.json` 檔案。  此設定檔包含您剛建立的工作區相關資訊。  

按一下 `01.run-experiment.ipynb` 來開啟 Notebook。

您可以使用 `Shift`+`Enter`，一次執行一個儲存格。  或者，使用功能表 `Cells` > `Run All` 來執行整個 Notebook。  當您在資料格旁邊看到 [*] 時，表示它正在執行中。  該資料格的程式碼完成後，會出現一個數字。

系統會提示您登入。  複製訊息中的程式碼，然後按一下連結，並將程式碼貼到新的視窗。  請確定不會複製程式碼前後的空格。  請使用您在 Azure 入口網站中使用的相同帳戶登入。

 ![登入](./media/quickstart-get-started/login.png)

在 Notebook 中，第二個資料格會從 `config.json` 讀取以連線到您的工作區。
```
ws = Workspace.from_config()
```

程式碼的第三個資料格會開始進行名為 "my-first-experiment" 的實驗。  您將使用此名稱來查看要返回您工作區之執行的相關資訊。

```
experiment = Experiment(workspace_object=ws, name = "my-first-experiment")
```

請注意，在 Notebook 的最後一個儲存格中，會顯示要寫入到記錄檔的值。

```
# Log final results
run.log("Final estimate: ",pi_estimate)
run.log("Final error: ",math.pi-pi_estimate)
```

執行程式碼之後，您可以在工作區中檢視這些值。

## <a name="view-logged-values"></a>檢視記錄的值

完成 Notebook 中的所有儲存格之後，請回到入口網站頁面。  

按一下 `View Experiments`。

![檢視實驗](./media/quickstart-get-started/view_exp.png)

關閉 `Reports` 快顯。

按一下 `my-first-experiment`。

查看您剛執行的執行相關資訊。  向下捲動頁面以找到執行的資料表，然後按一下執行號碼連結。

 ![執行歷程記錄連結](./media/quickstart-get-started/report.png)

您會看到已自動建立所記錄之值的繪圖：

   ![檢視歷程記錄](./media/quickstart-get-started/plots.png)

## <a name="clean-up-resources"></a>清除資源 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

您也可以保留資源群組，但可藉由顯示工作區屬性並選取 [刪除] 按鈕來刪除單一工作區。

## <a name="next-steps"></a>後續步驟

您現在已經建立了開始實驗和部署模型所需的資源。 您也在 Notebook 中執行了一些程式碼，並在雲端的工作區中探索了該程式碼的執行歷程記錄。

如需深入的工作流程體驗，請按照 Azure Machine Learning 教學課程來定型和部署模型。  

> [!div class="nextstepaction"]
> [教學課程：將影像分類模型定型](tutorial-train-models-with-aml.md)
