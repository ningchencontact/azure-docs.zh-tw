---
title: 建置分類器 - 自訂視覺服務
titlesuffix: Azure Cognitive Services
description: 了解如何使用自訂視覺網站建立影像分類模型。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: anroth
ms.openlocfilehash: d0f0f3b120187a7538989f219876a8c10569a98e
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2019
ms.locfileid: "59051460"
---
# <a name="how-to-build-a-classifier-with-custom-vision"></a>如何使用自訂視覺建置分類器

若要對於影像分類使用自訂視覺服務，您必須先建置分類器模型。 在本指南中，您將了解如何透過自訂視覺網站建置分類器。

## <a name="prerequisites"></a>必要條件

- 有效的 Azure 訂用帳戶。 免費[建立帳戶](https://azure.microsoft.com/free/)。
- 一組用來定型分類的影像。 如需選擇影像的秘訣，請參閱下文。


## <a name="create-custom-vision-resources-in-the-azure-portal"></a>在 Azure 入口網站中建立自訂視覺資源
若要使用自訂視覺服務，您必須在 [Azure 入口網站](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision) 中建立自訂視覺定型和預測資源。 這會建立定型和預測資源。 

## <a name="create-a-new-project"></a>建立新專案

在網頁瀏覽器中，瀏覽到 [自訂視覺網頁](https://customvision.ai)，並選取 [登入]。 使用您用來登入 Azure 入口網站的相同帳戶登入。

![[登入] 頁面的影像](./media/browser-home.png)


1. 若要建立第一個專案，請選取 [新增專案]。 [建立新專案] 對話方塊隨即出現。

    ![[新增專案] 對話方塊提供名稱、描述和領域的欄位。](./media/getting-started-build-a-classifier/new-project.png)

1. 輸入專案的名稱和描述。 然後選取一個 [資源群組]。 如果您登入的帳戶與 Azure 帳戶相關聯，[資源群組] 下拉式清單會顯示所有的 Azure 資源群組，包括自訂視覺服務資源。 

   > [!NOTE]
   > 如果沒有資源群組可用，請確認您已登入[customvision.ai](https://customvision.ai)使用相同的帳戶與您用來登入[Azure 入口網站](https://portal.azure.com/)。 此外，請確認您所選取的自訂視覺入口網站「目錄」，與 Azure 入口網站中的目錄相同，也就是您自訂視覺資源的位置。 在這兩個網站中，您可以從畫面右上角的下拉式帳戶功能表中選取您的目錄。 

1. 選取 [專案類型] 下的 [分類]。 然後，在 [分類類型] 下，端視您的使用案例而定，選擇 [多標籤] 或 [多類別]。 多標籤分類會將任意數目的標記套用至影像 (零或多個)，而多類別分類會將影像排序成單一類別 (您提交的每個影像將排序到最可能的標記)。 如果您想要，可以稍後變更分類類型。

1. 然後，選取其中一個可用領域。 每個領域都會針對特定類型的影像來將分類器最佳化，如下表所述。 如果您想要，可以稍後變更網域。

    |網域|目的|
    |---|---|
    |__泛型__| 已針對廣泛的影像分類工作進行最佳化。 如果沒有其他適用的領域，或您不確定要選擇哪一個領域，請選取「泛型」領域。 |
    |__Food__|已針對菜餚相片進行最佳化，如同您在餐廳菜單上看見的一樣。 如果您想要將個別水果或蔬菜的相片分類，請使用「食物」領域。|
    |__特徵點__|已針對可辨識的地標 (包括自然和人工) 進行最佳化。 地標在相片中清楚顯示時，此領域的效果最佳。 即使地標前面的人稍微阻擋到該地標，此領域還是能夠運作。|
    |__零售__|已針對在購物目錄或購物網站上找到的影像進行最佳化。 如果您想在連衣裙、褲子和襯衫之間進行高精確度的分類，請使用此領域。|
    |__Compact 的網域__| 已針對行動裝置上的即時分類條件約束進行最佳化。 精簡領域所產生的模型可以匯出到本機執行。|

1. 最後，選取 [建立專案]。

## <a name="choose-training-images"></a>選擇定型影像

建議在初始定型集的每個標記使用至少 30 個影像。 您也會想要收集一些額外的影像，來測試已定型的模型。

若要有效地定型您的模型，可使用有不同視覺效果的影像。 選取有下列各種變化的影像：
* 攝影機角度
* 光源
* 背景資訊
* 視覺效果樣式
* 單一/群組對象
* size
* type

此外，請確定所有的訓練映像符合下列準則：
* .jpg、.png 或 .bmp 格式
* 不大於 6MB (預測影像為 4MB)
* 最短邊緣不小於 256 像素；任何超過此上限的影像會由自訂視覺服務自動相應增加

## <a name="upload-and-tag-images"></a>上傳和標記影像

在本節中，您將上傳並手動標記影像，以便定型分類器。 

1. 若要新增影像，請按一下 [新增影像] 按鈕，然後選取 [瀏覽本機檔案]。 選取 [開啟] 以移至標記。 標記選取將套用到您選取要上傳的整組影像，因此根據所需的標記以個別的群組上傳影像較容易。 上傳個別影像之後，您也可以變更影像的標記。

    ![[新增影像] 控制項會顯示於左上方，並在正下方顯示為按鈕。](./media/getting-started-build-a-classifier/add-images01.png)


1. 若要建立標記，請在 [我的標記] 欄位中輸入文字，然後按下 [Enter]。 如果標籤已經存在，會出現在下拉式功能表中。 在多標籤的專案中，您可以將多個標記新增到影像，但是，在多類別專案中，只能新增一個標記。 若要完成上傳影像，請使用 [上傳 [number] 個檔案] 按鈕。 

    ![標記和上傳頁面的影像](./media/getting-started-build-a-classifier/add-images03.png)

1. 一旦上傳影像之後，請選取 [完成]。

    ![進度列會顯示所有工作都已完成。](./media/getting-started-build-a-classifier/add-images04.png)

若要上傳另一組影像，請返回本小節的最上端並重複進行步驟。

## <a name="train-the-classifier"></a>為分類器定型

若要為分類器定型，請選取 [定型] 按鈕。 分類器會使用所有目前的影像建立模型，以識別每個標籤的視覺品質。

![網頁標題工具列右上角的 [定型] 按鈕](./media/getting-started-build-a-classifier/train01.png)

此定型程序應該只需要幾分鐘的時間。 在此期間，[效能] 索引標籤會顯示定型程序的相關資訊。

![定型對話方塊出現在主要區段的瀏覽器視窗](./media/getting-started-build-a-classifier/train02.png)

## <a name="evaluate-the-classifier"></a>評估分類器

定型完成之後，將預估並顯示模型的效能。 自訂視覺服務會利用名為 [K 次交叉驗證](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) \(英文\) 的程序，使用您送出來定型的影像計算精確度和回收。 精確度和回收是衡量分類器效率的兩個不同標準：

- **精確度**表示識別的正確分類所得到的分數。 例如，如果模型識別 100 張影像為狗，而實際上有 99 張為狗，則精確度為 99%。
- **回收**表示正確識別實際分類所得到的分數。 例如，如果實際上有 100 張影像為蘋果，而模型識別 80 張為蘋果，則回收為 80%。

![定型結果會顯示整體的精確度與記憶度，以及分類器中每個標記的精確度與記憶度。](./media/getting-started-build-a-classifier/train03.png)

### <a name="probability-threshold"></a>機率閾值

請注意 [效能] 索引標籤的左窗格上出現的 [機率閾值] 滑桿。這是計算精確度與回收時被視為正確的預測機率閾值。

解譯高機率閾值的預測呼叫通常會在犧牲回收的情況下傳回高精確度的結果 (找到的分類正確，但是許多找不到)；低機率閾值則會執行相反的動作 (找到最多的實際分類，但是該集合內有誤判)。 記住這一點，您應該根據專案的特定需求設定機率閾值。 然後，在用戶端，您應該使用相同的機率閾值做為從模型收到預測結果時的篩選條件。

## <a name="manage-training-iterations"></a>管理定型反覆項目

每次您定型分類器時，都會使用更新的效能度量建立新_反覆項目_。 您可以在 [效能] 索引標籤的左窗格中檢視所有的反覆項目。在左窗格中，您也可以找到 [刪除] 按鈕，可用來刪除已經過時的反覆項目。 您刪除反覆項目時，會刪除唯一與它相關聯的任何影像。

## <a name="next-steps"></a>後續步驟

在本指南中，您已了解如何使用自訂視覺網站建立並定型影像分類模型。 接下來，深入瞭解改進模型的反覆程序。

[測試和重新定型模型](test-your-model.md)

