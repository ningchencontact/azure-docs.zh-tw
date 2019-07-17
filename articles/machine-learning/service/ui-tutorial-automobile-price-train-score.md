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
ms.openlocfilehash: 21f5a2d93b708e93f124bd44177bb7852dfbd86a
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720494"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>教學課程：透過視覺化介面預測汽車價格

在本教學課程中，您將進一步了解如何在 Azure Machine Learning 服務視覺化介面中開發預測解決方案。 本教學課程結束時，您將會有可根據您所傳送的技術規格對任何汽車預測價格的解決方案。

在教學課程的第一部分中，您將了解如何：

> [!div class="checklist"]
> * 匯入及清除資料
> * 訓練機器學習模型
> * 對模型進行評分和評估

在教學課程的[第二部分](ui-tutorial-automobile-price-deploy.md)中，您將了解如何將預測模型部署為 Azure Web 服務。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GY]

本教學課程完成後，將可作為範例實驗。

若要找到它，請從 [實驗頁面]  選取 [新增]  ，然後選取 [範例 1 - 迴歸：  汽車價格預測 (基本)] 實驗。

## <a name="create-a-workspace"></a>建立工作區

如果您有 Azure Machine Learning 服務工作區，請跳至[下一節](#open-the-visual-interface-webpage)。

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="open-the-visual-interface-webpage"></a>開啟視覺化介面網頁

1. 在 [Azure 入口網站](https://portal.azure.com/)中開啟工作區。

1. 在您的工作區中，選取 [視覺化介面]  。 然後，選取 [啟動視覺化介面]  。 

    ![Azure 入口網站的螢幕擷取畫面，顯示如何從機器學習服務工作區存取視覺化介面](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

## <a name="create-your-first-experiment"></a>建立您的第一個實驗

視覺化介面工具提供互動式、視覺化位置，以便建置預測分析模型。 將資料集和分析模組拖放到互動式畫布，將它們連接在一起以建立「實驗」  。

1. 選取視覺化介面視窗底部的 [+新增]  ，以建立新的實驗。

    ![新增實驗](./media/ui-tutorial-automobile-price-train-score/add-new.png)

1. 選取 [空白實驗]  。

1. 選取畫布頂端的預設實驗名稱「實驗建立時間」  ，並將其重新命名為有意義的名稱。 例如，**汽車價格預測**。 此名稱不必是唯一的。

## <a name="add-data"></a>加入資料

要執行機器學習，首先您必須要有資料。 此介面附有多個範例資料集供您使用。 您也可以從許多現有來源匯入資料。 在本教學課程中，使用範例資料集**汽車價格資料 (原始)** 。 

1. 實驗畫布左側是資料集和模組的調色盤。 選取 [儲存的資料集]  ，然後選取 [範例]  以檢視可用的範例資料集。

1. 選取資料集**汽車價格資料 (原始)** ，並將其拖曳到畫布上。

   ![將資料拖曳到畫布](./media/ui-tutorial-automobile-price-train-score/drag-data.png)

## <a name="select-columns"></a>Select columns

選取要使用的資料行。 首先，請設定要顯示所有可用資料行的模組。

> [!TIP]
> 如果您知道所需資料或模組的名稱，請使用選擇區頂端的搜尋列以快速尋找。 本教學課程的其餘部分將使用此捷徑。


1. 在 [搜尋] 方塊中輸入**選取**，以尋找**選取資料集中的資料行**模組。

1. 按住**選取資料集中的資料行**，並將其拖曳到畫布上。 將模組放在您先前新增的資料集下。

1. 若要將資料集連線至**選取資料集中的資料行**：按住資料集的輸出連接埠，並拖曳到**選取資料集中的資料行**的輸入連接埠，然後放開滑鼠按鈕。 即使您在畫布上移動資料集或模組，這兩者仍會保持連線。

    > [!TIP]
    > 資料集和模組以小圓圈代表輸入和輸出連接埠，輸入連接埠位在頂端，輸出連接埠在底端。 將某個模組的輸出連接埠連線至另一個模組的輸入連接埠時，您會透過實驗建立資料流程。
    >

    ![連線模組](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

    紅色驚嘆號標示表示您尚未設定該模組的屬性。

1. 選取**選取資料集中的資料行**模組。

1. 在畫布右側的 [屬性]  窗格中，選取 [編輯資料行]  。

    在 [選取資料行]  對話方塊中選取 [所有資料行]  ，並包含 [所有功能]  。 對話方塊應該會看起來如下：

     ![資料行選取器](./media/ui-tutorial-automobile-price-train-score/select-all.png)

1. 在右下方，選取 [確定]  按鈕，以關閉資料行選取器。

## <a name="run-the-experiment"></a>執行實驗

您隨時可以按一下資料集或模組的輸出連接埠，以查看資料於該時間點在資料流程中的型態。 如果 [視覺化]  選項已停用，則必須先執行實驗。

對計算目標執行實驗，此目標為連結至工作區的計算資源。 建立計算目標之後，您可以將其重複用於未來的執行。

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

有可用的計算目標之後，便會執行實驗。 執行完成時，每個模組上會出現綠色的核取記號。


## <a name="preview-the-data"></a>預覽資料

現在您已執行初始實驗，接下來可以將資料視覺化，以深入了解您必須處理的相關資料集。

1. 選取位於**選取資料集中的資料行**底部的輸出連接埠，然後選取 [視覺化]  。

1. 按一下 [資料] 視窗中的不同資料行，以檢視該資料行的相關資訊。

    在此資料集中，每個資料列分別代表一款汽車，而與每款汽車相關聯的變數會顯示為資料行。 此資料集中有 205 個資料列和 26 個資料行。

     每當您按一下資料的資料行時，該資料行的 [統計資料]  資訊和 [視覺效果]  影像就會出現在左側。 例如，當您按一下**車門數**時，您會看到它有 2 個唯一值和 2 個遺漏值。 向下捲動可查看其值：雙門和四門。

     ![預覽資料](./media/ui-tutorial-automobile-price-train-score/preview-data.gif)

1. 按一下每個資料行即可深入了解您的資料集，並判斷這些資料行是否有助於預測汽車的價格。

## <a name="prepare-data"></a>準備資料

資料集通常必須先經過某些前置處理，才能進行分析。 您在視覺化資料集時，可能已注意到某些遺漏值。 必須清除這些遺漏的值，讓模型才能正確地分析資料。 您將移除含有遺漏值的所有資料列。 此外，**自負虧損**資料行含有比例很高的遺漏值，因此您會將該資料行從模型中完全排除。

> [!TIP]
> 在使用大部分的模組時，都必須從輸入資料中清除遺漏值。

### <a name="remove-column"></a>移除資料行

首先，完全移除**自負虧損**資料行。

1. 選取**選取資料集中的資料行**模組。

1. 在畫布右側的 [屬性]  窗格中，選取 [編輯資料行]  。

    * 保留 [套用規則]  和 [所有資料行]  的選取狀態。

    * 在下拉式清單中，選取 [排除]  和 [資料行名稱]  ，然後按一下文字方塊內部。 輸入**自負虧損**。

    * 在右下方，選取 [確定]  按鈕，以關閉資料行選取器。

    ![排除資料行](./media/ui-tutorial-automobile-price-train-score/exclude-column.png)
        
    現在，[選取資料集中的資料行] 的屬性窗格指出它會傳遞資料集中的所有資料行，但 [自負虧損]  除外。
        
    [屬性] 窗格顯示 [自負虧損]  資料行已排除。
        
    ![屬性窗格](./media/ui-tutorial-automobile-price-train-score/property-pane.png)
        
    您可以按兩下模組並輸入文字，為模組新增註解。 這有助於您快速檢視模組在您實驗中的執行情況。 

1. 按兩下**選取資料集中的資料行**模組，然後輸入註解「排除自負虧損」。 
    
    輸入註解後，請按一下該模組外部。 此時會出現向下箭號，以顯示該模組包含註解。

1. 按一下向下箭號以顯示註解。

    模組此時會顯示向上箭號以隱藏註解。
        
    ![註解](./media/ui-tutorial-automobile-price-train-score/comments.png)

### <a name="clean-missing-data"></a>清除遺漏的資料

當您定型模型時，您必須對遺漏的資料採取某些動作。 在此案例中，您將新增模組來移除任何含有遺漏資料的其餘資料列。

1. 在搜尋方塊中輸入**清除**，以尋找**清除遺漏的資料**模組。

1. 將**清除遺漏的資料**模組拖曳到實驗畫布，然後將其連線至**選取資料集中的資料行**模組。 

1. 在 [屬性] 窗格中，選取 [清除模式]  下方的 [移除整個資料列]  。

    這些選項會指示**清除遺漏的資料**藉由移除含任何遺漏值的資料列來清除資料。

1. 按兩下模組，並輸入註解「移除遺漏值資料列」。
 
    ![移除資料列](./media/ui-tutorial-automobile-price-train-score/remove-rows.png)

    您的實驗目前看起來如下：
    
    ![select-column](./media/ui-tutorial-automobile-price-train-score/experiment-clean.png)

## <a name="visualize-the-results"></a>將結果視覺化

由於您對實驗中的模組進行了變更，狀態已變更為 [草稿]。  若要將全新的資料視覺化，您必須先再次執行實驗。

1. 選取底部的 [執行]  以執行實驗。

    這次您可以重複使用先前建立的計算目標。

1. 在對話方塊中選取 [執行]  。

   ![執行實驗](./media/ui-tutorial-automobile-price-train-score/select-compute.png)

1. 執行完成時，以滑鼠右鍵按一下**清除遺漏的資料**模組，將全新的資料視覺化。

    ![將全新的資料視覺化](./media/ui-tutorial-automobile-price-train-score/visualize-cleaned.png)

1. 按一下 [已清除的資料] 視窗中的不同資料行，以查看資料變更的情形。

    ![將全新的資料視覺化](media/ui-tutorial-automobile-price-train-score/visualize-result.png)

    現在有 193 個資料列和 25 個資料行。

    當您按一下**車門數**時，您會看到現在仍有 2 個唯一值，但已沒有遺漏值。 按一下其餘資料行，確認沒有任何遺漏值留在資料集中。 

## <a name="train-the-model"></a>訓練模型

現在資料已就緒，接下來可以建構預測模型。 您將使用您的資料來訓練模型。 然後，您將測試模型以確認它預測價格的精準度。

**分類**和**迴歸**是兩種受監督的機器學習服務演算法。 **分類**可從一組已定義的類別預測答案，例如色彩 (紅色、藍色或綠色)。 **迴歸**可用來預測數字。

因為要預測價格，也就是一個數字，因此您將使用迴歸演算法。 在此範例中，您將使用線性迴歸模型。

您將藉由提供一組包含價格的資料來訓練模型。 模型會掃描的資料，然後尋找汽車性能與價格之間的關聯性。 然後，藉由為模型提供一組它所熟悉的汽車性能，並確認模型預測已知價格的精準度，來測試模型。

請將資料分割成個別的訓練和測試資料集，用來訓練和測試模型。

1. 在搜尋方塊中輸入**分割資料**以尋找**分割資料**模組，並將其連線至**清除遺漏的資料**模組的左側連接埠。

1. 選取您剛剛連線的**分割資料**模組。 在 [屬性] 窗格中，將第一個輸出資料集中的資料列比例設為 0.7。 如此，我們將使用百分之 70 的資料來訓練模型，並保留百分之 30 供測試之用。

    ![此螢幕擷取畫面顯示屬性窗格的正確組態。 [分割資料] 的值應為「分割資料列」、0.7、「隨機分割」、0、False。](./media/ui-tutorial-automobile-price-train-score/split-data.png)

1. 按兩下 [分割資料]  ，然後輸入註解「將資料集分割為訓練集 (0.7) 和測試集 (0.3)」

1. 若要選取學習演算法，請清除您的模組選擇區搜尋方塊。

1. 依序展開 [機器學習]  和 [初始化模型]  。 這會顯示數個可用來初始化機器學習演算法的模組類別。

1. 在此實驗中，請選取 [迴歸]   > [線性迴歸]  ，然後將其拖曳到實驗畫布。

    ![此螢幕擷取畫面顯示屬性窗格的正確組態。 [分割資料] 的值應為「分割資料列」、0.7、「隨機分割」、0、False。](./media/ui-tutorial-automobile-price-train-score/linear-regression-module.png)

1. 找出**訓練模型**模組，並將其拖曳到實驗畫布。 將「線性迴歸」模組的輸出連線至「訓練模型」模組的左側輸入，並將**分割資料**模組的訓練資料輸出 (左側連接埠) 連線至**訓練模型**模組的右側輸入。

    ![此螢幕擷取畫面顯示「訓練模型」模組的正確組態。 「線性迴歸」模組連線至「訓練模型」模組的左側連接埠，「分割資料」模組連線至「訓練模型」的右側連接埠](./media/ui-tutorial-automobile-price-train-score/train-model.png)

1. 選取 [訓練模型]  模組。 在 [屬性] 窗格中選取 [啟動資料行選取器]，然後在 [包含資料行名稱]  旁邊輸入**價格**。 「價格」是我們的模型所將預測的值

    ![此螢幕擷取畫面顯示資料行選取器的正確組態。 套用規則 > 包含資料行名稱 >「價格」](./media/ui-tutorial-automobile-price-train-score/select-price.png)

    實驗看起來如下：

    ![此螢幕擷取畫面顯示實驗在新增「訓練模型」模組之後的正確組態。](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

### <a name="run-the-training-experiment"></a>執行訓練實驗

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

## <a name="score-and-evaluate-the-model"></a>對模型進行評分和評估

現在您已完成使用百分之 70 資料模型的訓練，而可用它來為其他百分之 30 的資料評分，以了解模型的運作是否理想。

1. 在搜尋方塊中輸入**評分模型**以尋找**評分模型**模組，並將其拖曳到實驗畫布。 將**訓練模型**模組的輸出連線至**評分模型**的左側輸入連接埠。 將**分割資料**模組的測試資料輸出 (右側連接埠) 連線至**評分模型**的右側輸入連接埠。

1. 在搜尋方塊中輸入**評估**以尋找 [評估模型]  ，並將模組拖曳到實驗畫布。 將**評分模型**模組的輸出連線至**評估模型**的左側輸入。 實驗最終應呈現如下：

    ![此螢幕擷取畫面顯示實驗的正確組態。](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. 使用先前使用的相同計算目標來執行實驗。

1. 選取**評分模型**的輸出連接埠並選取 [視覺化]  ，以檢視**評分模型**模組的輸出。 輸出會顯示價格的預測值，以及來自測試資料的已知值。

    ![此螢幕擷取畫面將「評分標籤」資料行醒目提示的輸出視覺效果](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. 若要檢視「評估模型」模組的輸出，請選取輸出連接埠，然後選取 [視覺化]。

    ![此螢幕擷取畫面顯示最終實驗的評估結果。](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

您的模型會顯示下列統計資料：

* **平均絕對誤差 (MAE)** ：絕對誤差的平均值 (「誤差」是指預測值與實際值之間的差異)。
* **均方根誤差 (RMSE)** ：對測試資料集所做之預測的平方誤差的評分根平均值。
* **相對絕對誤差**：相對於實際值與所有實際值之平均值之間的絕對差異的絕對誤差平均值。
* **相對平方誤差**：相對於實際值與所有實際值之平均值之間的平方差異的平方誤差平均值。
* **決定係數**：也稱為 R 平方值，這是一個統計計量，可指出模型對於資料的適用程度。

針對每個誤差統計資料，越小越好。 值越小，表示預測越接近實際值。 就 [決定係數] 而言，其值愈接近一 (1.0)，預測就愈精準。

## <a name="manage-experiments-in-azure-machine-learning-service-workspace"></a>在 Azure Machine Learning 服務工作區中管理實驗

您可以透過 Azure Machine Learning 服務工作區來管理在視覺化介面中建立的實驗。 您可以使用工作區查看更多詳細資訊，例如個人實驗執行、診斷記錄、執行圖形等等。

1. 在 [Azure 入口網站](https://portal.azure.com/)中開啟工作區。  

1. 在您的工作區中，選取 [實驗]  。 然後，選取您建立的實驗。

    ![顯示如何在 Azure 入口網站中瀏覽至實驗的螢幕擷取畫面](./media/ui-tutorial-automobile-price-train-score/portal-experiments.png)

    在此頁面上，您會看到實驗的概觀及其最新的執行。

    ![此螢幕擷取畫面顯示實驗統計資料在 Azure 入口網站中的概觀](./media/ui-tutorial-automobile-price-train-score/experiment-overview.png)

1. 選取執行號碼以查看特定執行的相關詳細資料。

    ![詳細執行報告的螢幕擷取畫面](./media/ui-tutorial-automobile-price-train-score/run-details.png)

    執行報告會即時更新。 如果您在實驗中使用**執行 Python 指令碼**模組，您可以在 [記錄]  索引標籤中指定要輸出的指令碼記錄。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程的第一部分中，您已完成下列步驟：

* 建立實驗
* 準備資料
* 訓練模型
* 對模型進行評分和評估

在第二部分中，您將了解如何將模型部署為 Azure Web 服務。

> [!div class="nextstepaction"]
> [繼續部署模型](ui-tutorial-automobile-price-deploy.md)
