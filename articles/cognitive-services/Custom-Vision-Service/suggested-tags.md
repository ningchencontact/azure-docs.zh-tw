---
title: 使用建議的標籤更快地標記影像
titleSuffix: Azure Cognitive Services
description: 在本指南中，您將瞭解如何使用建議的標籤，在定型自訂視覺模型時，更快速地標記大量影像。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: pafarley
ms.openlocfilehash: 06735240729fb2bfd21b87f592e143e9ceabb390
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2019
ms.locfileid: "72753492"
---
# <a name="label-images-faster-with-suggested-tags"></a>使用建議的標籤更快地標記影像

在本指南中，您將瞭解如何使用建議的標記功能，在定型自訂視覺模型時，更快速地標記大量影像。 

當您為自訂視覺模型標記影像時，服務會使用模型的最新定型反復專案來預測未標記影像的標籤。 接著，它會根據選取的信賴閾值和預測不確定性，將這些預測顯示為建議的標記。 接著，您可以確認或變更建議，加速手動標記影像以進行定型的程式。

## <a name="when-to-use-suggested-tags"></a>使用建議標記的時機

請注意下列限制：

* 您應該只針對內容已定型一次的影像要求建議的標記。 針對您剛開始訓練的新標籤，請不要取得建議。

> [!IMPORTANT]
> 建議的標記功能會使用與一般預測相同的[計價模式](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)。 當您第一次針對一組影像觸發建議的標籤時，您的費用會與預測呼叫相同。 之後，服務會在資料庫中儲存所選影像的結果30天，而您可以在該期間內免費存取。 30天后，如果您再次要求其建議的標籤，就會向您收取費用。

## <a name="suggested-tags-workflow"></a>建議的標記工作流程

下列步驟示範如何使用建議的標記功能：

1. 將所有訓練影像上傳到您的自訂視覺專案。
1. 標記部分資料集，為每個標記選擇相同的影像數目。
    > [!TIP]
    > 請務必使用您稍後想要提出建議的所有標記。
1. 開始定型程式。
1. 訓練完成時，流覽至未標記**的視圖**，然後選取左窗格上的 [**取得建議的標記**] 按鈕。
    > [!div class="mx-imgBorder"]
    > [未標記的影像] 索引標籤底下會顯示 ![The 建議的標籤按鈕。 ](./media/suggested-tags/suggested-tags-button.png)
1. 選取您想要建議的映射集合。 您應該只取得部分未標記影像的初始標籤建議。 當您逐一查看此程式時，您將會得到更好的標記建議。
1. 請確認建議的標籤，並修正任何不正確的標記。
    > [!TIP]
    > 具有建議標記的影像會依其預測不確定性排序（較低的值表示較高的信賴度）。 您可以使用 [**依不確定性排序**] 選項來變更排序次序。 如果您將順序設定為「**高**」，您可以先更正高確定性預測，然後快速確認不確定性的預測。
    * 在 [影像分類專案] 中，您可以選取並確認批次中的標記。 依指定的建議標記篩選視圖、取消選取標記錯誤的影像，然後在批次中確認其餘部分。
        > [!div class="mx-imgBorder"]
        > 具有篩選器的 IC 會以批次模式顯示 ![Suggested 標記。](./media/suggested-tags/ic-batch-mode.png)

        您也可以從資源庫中選取影像，以在個別影像模式中使用建議的標記。

        ![建議的標籤會顯示在 IC 的個別影像模式中。](./media/suggested-tags/ic-individual-image-mode.png)
    * 在物件偵測專案中，不支援批次確認，但您仍然可以依建議的標記進行篩選和排序，以獲得更有條理的標籤體驗。 未標記影像的縮圖會顯示周框方塊的重迭，指出建議標記的位置。 如果您未選取建議的標記篩選，則所有未標記的影像都會顯示，而不會覆迭周框方塊。
        > [!div class="mx-imgBorder"]
        > ![Suggested 標記會以批次模式顯示，並使用篩選器進行 OD。](./media/suggested-tags/od-batch-mode.png)

        若要確認物件偵測標記，您必須將它們套用至資源庫中的每個個別映射。

        ![建議的標記會以個別影像模式顯示為 OD。](./media/suggested-tags/od-individual-image-mode.png)
1. 再次開始訓練程式。
1. 重複上述步驟，直到您滿意建議品質為止。

## <a name="next-steps"></a>後續步驟

遵循快速入門，開始建立和訓練自訂視覺專案。

* [建立分類器](getting-started-build-a-classifier.md)
* [建立物件偵測器](get-started-build-detector.md)