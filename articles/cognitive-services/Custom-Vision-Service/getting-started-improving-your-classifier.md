---
title: 改善您的分類器 - 自訂視覺服務
titlesuffix: Azure Cognitive Services
description: 了解如何改善分類器的品質。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: 13c0346324ae8e3cf3485985a9014f9999230630
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351434"
---
# <a name="how-to-improve-your-classifier"></a>如何改善分類器

在本指南中，您將了解如何改善自訂視覺服務分類器的品質。 分類器品質取決於您提供給它的已標示資料數量、品質及種類，以及整體資料集的平衡程度。 良好的分類器具有平衡的定型資料集，而該資料集將送出到分類器的內容具有代表性。 建置這類分類器的程序為反覆程序，通常要進行幾個回合的定型，才會達到預期的結果。

以下是可協助您建置更精確分類器的一般模式：

1. 第一回合定型
1. 新增更多影像並平衡資料；重新定型
1. 新增背景、光源、物件大小、攝影機角度及樣式各異的影像；重新定型
1. 使用新影像來測試預測
1. 根據預測結果修改現有的定型資料

## <a name="prevent-overfitting"></a>防止過度配適

有時候，分類器會學習如何根據您影像共同的任意特性進行預測。 例如，如果您建立蘋果與柑橘的分類器，且已使用手拿蘋果與白盤裝柑橘的影像，分類器可能過度重視手與白盤來，而不是蘋果與柑橘。

![未預期的分類影像](./media/getting-started-improving-your-classifier/unexpected.png)

若要更正此問題，請使用以下指引，利用更多樣化的影像來定型：提供具有不同角度、背景、物件大小、群組及其他變化的影像。

## <a name="data-quantity"></a>資料品質

定型影像數目是最重要的因素。 我們建議使用每個標籤至少 50 個映像做為起點。 使用較少影像，過度學習的風險比較高，雖然您的效能數字可能暗示品質良好，但您的模型可能在與真實世界的資料奮戰。 

## <a name="data-balance"></a>資料平衡

此外，務必考慮定型資料的相對數量。 例如，對一個標籤使用 500 個影像且對另一個標籤使用 50 個影像，會讓定型資料集不平衡。 這會導致模型在預測某個標籤會比預測另一個標籤更精確。 如果影像最少的標籤與影像最多的標籤之間可至少維持 1:2 的比例，可能會看到較佳的結果。 例如，如果具有最多影像的標籤有 500 個影像，那麼最少影像的標籤應該至少有 250 個影像用於定型。

## <a name="data-variety"></a>資料多樣性

務必使用代表在正常使用期間將送出到分類器的影像。 否則，分類器可以學習如何根據您影像共同的任意特性進行預測。 例如，如果您建立蘋果與柑橘的分類器，且已使用手拿蘋果與白盤裝柑橘的影像，分類器可能過度重視手與白盤，而不是蘋果與柑橘。

![未預期的分類影像](./media/getting-started-improving-your-classifier/unexpected.png)

若要更正這個問題，請包含各種影像，以確保分類器可以妥善歸納。 以下一些方式可讓您的定型集更為多元：

* __背景：__ 提供物件放在不同背景前方的影像。 自然內容中的相片會將更多資訊提供給分類器，所以比放在中性背景前方的相片更好。

    ![背景範例影像](./media/getting-started-improving-your-classifier/background.png)

* __光源：__ 提供多樣化光源的影像 (也就是，以閃光燈、高曝光等方式拍攝等)，當用於預測的影像有不同的光源時特別有用。 對於使用具有各種飽和度、色調、亮度的影像也很有幫助。

    ![光源範例影像](./media/getting-started-improving-your-classifier/lighting.png)

* __物件大小：__ 提供內含物件大小和數目不同的影像 (例如，多串香蕉的相片和單根香蕉的特寫)。 不同的大小可協助分類器更適當地一般化。

    ![大小範例影像](./media/getting-started-improving-your-classifier/size.png)

* __攝影機角度：__ 提供以不同攝影機角度拍攝的影像。 或者，如果所有相片都必須以固定式攝影機 (例如，監視攝影機) 拍攝，請務必對每個定期發生的物件指派不同的標籤，以避免過度學習 &mdash; 將不相關的物件 (例如，路燈柱) 解釋為主要特徵。

    ![角度範例影像](./media/getting-started-improving-your-classifier/angle.png)

* __樣式：__ 提供類別相同但樣式不同的影像 (例如，各種同類水果)。 不過，如果您有樣式迥異的物件 (例如米老鼠與現實中的老鼠)，建議您分別標示為不同類別，更清楚地表示不同的特徵。

    ![樣式範例影像](./media/getting-started-improving-your-classifier/style.png)

## <a name="use-prediction-images-for-further-training"></a>使用預測影像以供進一步定型

當您藉由將影像送出到預測端點來使用或測試影像分類器時，自訂視覺服務會儲存這些影像。 您可以接著使用它們來改善模型。

1. 若要檢視送出到分類器的影像，開啟[自訂視覺網頁](https://customvision.ai) \(英文\)，前往您的專案，然後選取 [預測] 索引標籤。預設檢視會顯示來自目前反覆項目的影像。 您可以使用 [反覆項目] 下拉式功能表欄位，來檢視在先前反覆項目期間送出的影像。

    ![預測索引標籤的螢幕擷取畫面 (檢視中有影像)](./media/getting-started-improving-your-classifier/predictions.png)

2. 將滑鼠停留在影像上方，以查看由分類器所預測的標記。 影像會排序，如此一來，可為分類器帶來最大改善的影像就會列在最上方。 若要使用不同的排序方法，請在 [排序] 區段中進行選取。 

    若要將影像新增到現有定型資料，請選取影像、設定正確的標記，然後按一下 [儲存並關閉]。 影像將會從 [預測] 中移除並新增到定型影像集。 您可以選取 [定型影像] 索引標籤加以檢視。

    ![標記頁面的影像](./media/getting-started-improving-your-classifier/tag.png)

3. 然後使用 [定型] 按鈕來重塑分類器。

## <a name="visually-inspect-predictions"></a>以視覺化方式檢查預測

若要檢查影像預測，請移至 [定型影像] 索引標籤，在 [反覆項目] 下拉式功能表中選取您先前的定型反覆項目，，然後檢查 [標記] 區段下的一或多個標記。 檢視現在應該在模型無法正確預測指定標記的每個影像周圍顯示紅色方塊。

![反覆項目記錄的影像](./media/getting-started-improving-your-classifier/iteration.png)

有時，視覺檢查可以識別出您接著可藉由新增更多定型資料，或修改現有定型資料來更正的模式。 例如，蘋果與萊姆的分類器錯誤地將所有青蘋果加上萊姆的標籤。 您可接著讓定型資料包含已標記青蘋果的影像，然後新增並提供此定型資料，藉以更正此問題。

## <a name="next-steps"></a>後續步驟

在本指南中，您已了解數種讓自訂影像分類模型更加精確的技術。 接下來，了解如何藉由將影像送出到預測 API，以程式設計方式測試它們。

> [!div class="nextstepaction"]
> [使用預測 API](use-prediction-api.md)
