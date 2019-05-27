---
title: 教學課程：透過視覺化介面預測汽車價格
titleSuffix: Azure Machine Learning service
description: 了解如何使用拖放視覺化介面進行機器學習模型的訓練、評分和部署。 本教學課程是使用線性迴歸預測汽車價格相關系列的第一部分 (共兩個部分)。
author: peterclu
ms.author: peterclu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: 52b5291e4b56b5065b9dddd5b8908ade0c1a8387
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2019
ms.locfileid: "66019831"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>教學課程：透過視覺化介面預測汽車價格

在本教學課程中，您將進一步了解如何在 Azure Machine Learning 服務視覺化介面中開發預測解決方案。 本教學課程結束時，您將會有可根據您所傳送的技術規格對任何汽車預測價格的解決方案。

本教學課程[是快速入門的延續](ui-quickstart-run-experiment.md)，並且是**兩部分教學課程系列的第一部分**。 不過，您無須完成快速入門即可開始進行本教學課程。

在教學課程系列的第一部分中，您將了解如何：

> [!div class="checklist"]
> * 匯入及清除資料 (步驟與快速入門相同)
> * 訓練機器學習模型
> * 對模型進行評分和評估

在教學課程系列的[第二部分](ui-tutorial-automobile-price-deploy.md)中，您將了解如何將預測模型部署為 Azure Web 服務。

> [!NOTE]
> 本教學課程完成後，將可作為範例實驗。
> 從 實驗 頁面中，移至 新增 > **範例 1 - 迴歸：汽車價格預測 (基本)**


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GY]

## <a name="create-a-workspace"></a>建立工作區

如果您有 Azure Machine Learning 服務工作區，請跳至[下一節](#open-the-visual-interface-webpage)。 否則，請建立此工作區。

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="open-the-visual-interface-webpage"></a>開啟視覺化介面網頁

1. 在 [Azure 入口網站](https://portal.azure.com/)中開啟工作區。  

1. 在您的工作區中，選取 [視覺化介面]。  然後，選取 [啟動視覺化介面]。  

    ![Azure 入口網站的螢幕擷取畫面，顯示如何從機器學習服務工作區存取視覺化介面](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

    介面網頁會在新的瀏覽器頁面中開啟。  

## <a name="import-and-clean-your-data"></a>匯入及清除資料

首先，您需要全新的資料。 如果您已完成快速入門，在此您可以重複使用資料準備實驗。 如果您尚未完成快速入門，請略過下一節並[從新的實驗開始](#start-from-a-new-experiment)。

### <a name="reuse-the-quickstart-experiment"></a>重複使用快速入門實驗

1. 開啟您的快速入門實驗。

1. 選取視窗底部的 [另存新檔]。

1. 在顯示的快顯對話方塊中，指定實驗的新名稱。

    ![顯示如何將實驗重新命名為「教學課程 - 預測汽車價格」的螢幕擷取畫面](./media/ui-tutorial-automobile-price-train-score/save-a-copy.png)

1. 實驗目前看起來如下：

    ![此螢幕擷取畫面顯示實驗的預期狀態。 汽車資料集連線至與「清除遺漏的資料」連線的「選取資料行」模組](./media/ui-tutorial-automobile-price-train-score/save-copy-result.png)

如果您成功地重複使用快速入門實驗，請略過開始下一節，並開始進行[訓練模型](#train-the-model)。

### <a name="start-from-a-new-experiment"></a>從新的實驗開始

如果您未完成快速入門，請依照下列步驟快速建立新的實驗，以匯入並清除汽車資料集。

1. 選取視覺化介面視窗底部的 [+新增]，以建立新的實驗。

1. 選取 [實驗] >  [空白實驗]。

1. 選取畫布頂端的預設實驗名稱「實驗建立時間」，並將其重新命名為有意義的名稱。 例如，**汽車價格預測**。 此名稱不必是唯一的。

1. 實驗畫布左側是資料集和模組的調色盤。 若要尋找模組，請使用位於模組選擇區頂端的搜尋方塊。 在搜尋方塊中輸入**汽車**，以尋找標示為**汽車價格資料 (原始)** 的資料集。 將此資料集拖曳到實驗畫布。

    ![此螢幕擷取畫面顯示如何尋找汽車價格資料集](./media/ui-tutorial-automobile-price-train-score/automobile-dataset.png)

    現在您已有資料，接下來可以新增一個完全移除 [自負虧損] 資料行的模組。 然後，再新增一個將任何含有遺漏資料的資料列移除的模組。

1. 在搜尋方塊中輸入**選取資料行**，以尋找**選取資料集中的資料行**模組。 然後，將其拖曳到實驗畫布上。 此模組可讓您選取要將哪些資料行包含在模型中，或是從模型中排除。

1. 將**汽車價格資料 (原始)** 資料集的輸出連接埠連線至「選取資料集中的資料行」的輸入連接埠。

    ![此動畫 gif 顯示如何將「汽車價格資料」模組連線至「選取資料行」模組](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

1. 選取「選取資料集中的資料行」模組，然後選取 [屬性] 窗格中的 [啟動資料行選取器]。

   1. 在左側選取 [套用規則]

   1. 在 [開始於] 旁邊，選取 [所有資料行]。 這些規則會指示**選取資料集中的資料行**傳遞所有資料行 (但我們將排除的資料行除外)。

   1. 在下拉式清單中，選取 [排除] 和 [資料行名稱]，然後在文字方塊內輸入**自負盈虧**。

   1. 按一下 [確定] 按鈕，以關閉資料行選取器 (位於右下方)。

     現在，[選取資料集中的資料行] 的屬性窗格指出它會傳遞資料集中的所有資料行，但 [自負虧損] 除外。

1. 按兩下**選取資料集中的資料行**模組，並輸入「排除自負虧損」，以將註解新增至該模組。 這有助於您快速檢視模組在您實驗中的執行情況。

    ![此螢幕擷取畫面顯示「選取資料行」模組的正確組態](./media/ui-tutorial-automobile-price-train-score/select-columns.png)

1. 在搜尋方塊中輸入**清除**，以尋找**清除遺漏的資料**模組。 將**清除遺漏的資料**模組拖曳到實驗畫布，然後將其連線至**選取資料集中的資料行**模組。

1. 在 [屬性] 窗格中，選取 [清除模式] 底下的 [移除整個資料列]。 這些選項會指示**清除遺漏的資料**藉由移除含任何遺漏值的資料列來清除資料。 按兩下模組，並輸入註解「移除遺漏值資料列」。

![此螢幕擷取畫面顯示「清除遺漏的資料」模組的正確組態](./media/ui-tutorial-automobile-price-train-score/clean-missing-data.png)

## <a name="train-the-model"></a>訓練模型

現在資料已就緒，接下來可以建構預測模型。 您將使用您的資料來訓練模型。 然後，您將測試模型以確認它預測價格的精準度。

**分類**和**迴歸**是兩種受監督的機器學習服務演算法。 **分類**可從一組已定義的類別預測答案，例如色彩 (紅色、藍色或綠色)。 **迴歸**可用來預測數字。

因為要預測價格，也就是一個數字，因此您將使用迴歸演算法。 在此範例中，您將使用線性迴歸模型。

您將藉由提供一組包含價格的資料來訓練模型。 模型會掃描的資料，然後尋找汽車性能與價格之間的關聯性。 然後，藉由為模型提供一組它所熟悉的汽車性能，並確認模型預測已知價格的精準度，來測試模型。

請將資料分割成個別的訓練和測試資料集，用來訓練和測試模型。

1. 在搜尋方塊中輸入**分割資料**以尋找**分割資料**模組，並將其連線至**清除遺漏的資料**模組的左側連接埠。

1. 選取您剛剛連線的**分割資料**模組。 在 [屬性] 窗格中，將第一個輸出資料集中的資料列比例設為 0.7。 如此，我們將使用百分之 70 的資料來訓練模型，並保留百分之 30 供測試之用。

    ![此螢幕擷取畫面顯示屬性窗格的正確組態。 [分割資料] 的值應為「分割資料列」、0.7、「隨機分割」、0、False。](./media/ui-tutorial-automobile-price-train-score/split-data.png)

1. 按兩下 [分割資料]，然後輸入註解「將資料集分割為訓練集 (0.7) 和測試集 (0.3)」

1. 若要選取學習演算法，請清除您的模組選擇區搜尋方塊。

1. 依序展開 [機器學習] 和 [初始化模型]。 這會顯示數個可用來初始化機器學習演算法的模組類別。

1. 在此實驗中，請選取 [迴歸] > [線性迴歸]，然後將其拖曳到實驗畫布。

    ![此螢幕擷取畫面顯示屬性窗格的正確組態。 [分割資料] 的值應為「分割資料列」、0.7、「隨機分割」、0、False。](./media/ui-tutorial-automobile-price-train-score/linear-regression-module.png)

1. 找出**訓練模型**模組，並將其拖曳到實驗畫布。 將「線性迴歸」模組的輸出連線至「訓練模型」模組的左側輸入，並將**分割資料**模組的訓練資料輸出 (左側連接埠) 連線至**訓練模型**模組的右側輸入。

    ![此螢幕擷取畫面顯示「訓練模型」模組的正確組態。 「線性迴歸」模組連線至「訓練模型」模組的左側連接埠，「分割資料」模組連線至「訓練模型」的右側連接埠](./media/ui-tutorial-automobile-price-train-score/train-model.png)

1. 選取 [訓練模型] 模組。 在 [屬性] 窗格中選取 [啟動資料行選取器]，然後在 [包含資料行名稱] 旁邊輸入**價格**。 「價格」是我們的模型所將預測的值

    ![此螢幕擷取畫面顯示資料行選取器的正確組態。 套用規則 > 包含資料行名稱 >「價格」](./media/ui-tutorial-automobile-price-train-score/select-price.png)

    此時實驗應呈現如下。
    ![此螢幕擷取畫面顯示實驗在新增「訓練模型」模組之後的正確組態。](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

### <a name="run-the-training-experiment"></a>執行訓練實驗

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

## <a name="score-and-evaluate-the-model"></a>對模型進行評分和評估

現在您已完成使用百分之 70 資料模型的訓練，而可用它來為其他百分之 30 的資料評分，以了解模型的運作是否理想。

1. 在搜尋方塊中輸入**評分模型**以尋找**評分模型**模組，並將其拖曳到實驗畫布。 將**訓練模型**模組的輸出連線至**評分模型**的左側輸入連接埠。 將**分割資料**模組的測試資料輸出 (右側連接埠) 連線至**評分模型**的右側輸入連接埠。

1. 在搜尋方塊中輸入**評估**以尋找**評估模型**，並將其拖曳到實驗畫布。 將**評分模型**模組的輸出連線至**評估模型**的左側輸入。 實驗最終應呈現如下：

    ![此螢幕擷取畫面顯示實驗的正確組態。](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. 使用先前使用的相同計算目標來執行實驗。

1. 選取**評分模型**的輸出連接埠並選取 [視覺化]，以檢視**評分模型**模組的輸出。 輸出會顯示價格的預測值，以及來自測試資料的已知值。

    ![此螢幕擷取畫面將「評分標籤」資料行醒目提示的輸出視覺效果](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. 若要檢視「評估模型」模組的輸出，請選取輸出連接埠，然後選取 [視覺化]。

    ![此螢幕擷取畫面顯示最終實驗的評估結果。](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

您的模型會顯示下列統計資料：

* **平均絕對誤差 (MAE)**：絕對誤差的平均值 (「誤差」是指預測值與實際值之間的差異)。
* **均方根誤差 (RMSE)**：對測試資料集所做之預測的平方誤差的評分根平均值。
* **相對絕對誤差**：相對於實際值與所有實際值之平均值之間的絕對差異的絕對誤差平均值。
* **相對平方誤差**：相對於實際值與所有實際值之平均值之間的平方差異的平方誤差平均值。
* **決定係數**：也稱為 R 平方值，這是一個統計計量，可指出模型對於資料的適用程度。

針對每個誤差統計資料，越小越好。 值越小，表示預測越接近實際值。 就 [決定係數] 而言，其值愈接近一 (1.0)，預測就愈精準。

## <a name="manage-experiments-in-azure-machine-learning-service-workspace"></a>在 Azure Machine Learning 服務工作區中管理實驗

您可以透過 Azure Machine Learning 服務工作區來管理在視覺化介面中建立的實驗。 您可以使用工作區查看更多詳細資訊，例如個人實驗執行、診斷記錄、執行圖形等等。

1. 在 [Azure 入口網站](https://portal.azure.com/)中開啟工作區。  

1. 在您的工作區中，選取 [實驗]。 然後，選取您建立的實驗。

    ![顯示如何在 Azure 入口網站中瀏覽至實驗的螢幕擷取畫面](./media/ui-tutorial-automobile-price-train-score/portal-experiments.png)

    在此頁面上，您會看到實驗的概觀及其最新的執行。

    ![此螢幕擷取畫面顯示實驗統計資料在 Azure 入口網站中的概觀](./media/ui-tutorial-automobile-price-train-score/experiment-overview.png)

1. 選取執行號碼以查看特定執行的相關詳細資料。

    ![詳細執行報告的螢幕擷取畫面](./media/ui-tutorial-automobile-price-train-score/run-details.png)

    執行報告會即時更新。 如果您在實驗中使用**執行 Python 指令碼**模組，您可以在 [記錄] 索引標籤中指定要輸出的指令碼記錄。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程的第一部分中，您已完成下列步驟：

* 重複使用在快速入門中建立的實驗
* 準備資料
* 訓練模型
* 對模型進行評分和評估

在第二部分中，您將了解如何將模型部署為 Azure Web 服務。

> [!div class="nextstepaction"]
> [繼續部署模型](ui-tutorial-automobile-price-deploy.md)
