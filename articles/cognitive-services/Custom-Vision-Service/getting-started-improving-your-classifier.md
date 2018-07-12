---
title: 使用自訂視覺服務改善分類器 - Azure 認知服務 | Microsoft Docs
description: 了解如何改善自訂視覺服務分類器的品質。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 65b1424f259066c7d5bd6b2b508d2a4052ff0527
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368835"
---
# <a name="how-to-improve-your-classifier"></a>如何改善分類器

了解如何改善自訂視覺服務分類器的品質。 分類器品質取決於您提供給它且已標示的資料品質。 

## <a name="train-more-varied-images"></a>為更多樣化的影像定型

提供具有不同角度、背景、物件大小、相片群組及其他變化的已標記影像，可以改善分類器。 具有前後景的相片會比位於中性背景前面的相片更好。 包含代表在正常使用期間將送出到分類器的影像。

如需新增影像的詳細資訊，請參閱[建置分類器](getting-started-build-a-classifier.md)文件。

> [!IMPORTANT]
> 記得要在新增影像之後為分類器定型。

## <a name="use-images-submitted-for-prediction"></a>使用送出來進行預測的影像

自訂視覺服務會儲存送出到預測端點的影像。 若要使用這些影像來改善分類器，請使用下列步驟：

1. 若要檢視送出到分類器的影像，開啟[自訂視覺網頁](https://customvision.ai) \(英文\)，然後選取 [預測] 索引標籤。

    ![[預測] 索引標籤的影像](./media/getting-started-improving-your-classifier/predictions-tab.png)

    > [!TIP]
    > 預設檢視會顯示來自目前反覆項目的影像。 您可以使用 [反覆項目] 下拉式清單欄位，來檢視在先前反覆項目期間送出的影像。

2. 將滑鼠停留在影像上方，以查看由分類器所預測的標記。

    > [!TIP]
    > 影像會設定順位，如此一來，可為分類器帶來最大獲益的影像就會位於最上方。 若要選取不同的排序，使用 [排序] 區段。

    若要將影像新增到定型資料，請選取影像、選取標記，然後選取 [儲存並關閉]。 從 [預測] 移除影像並新增到定型影像。 您可以選取 [定型影像] 索引標籤加以檢視。

    ![標記頁面的影像](./media/getting-started-improving-your-classifier/tag-image.png)

3. 使用 [定型] 按鈕來重塑分類器。

## <a name="visually-inspect-predictions"></a>以視覺化方式檢查預測

若要檢查影像預測，選取 [定型影像] 索引標籤，然後選取 [反覆項目記錄]。 錯誤預測的影像具有紅色方塊外框。

![反覆項目記錄的影像](./media/getting-started-improving-your-classifier/iteration-history.png)

有時，視覺檢查可以識別出您接著可藉由新增其他定型資料來更正的模式。 例如，玫瑰與雛菊的分類器可能會將所有白色玫瑰錯誤標示為雛菊。 您可以讓定型資料包含已標記白色玫瑰的影像，然後新增並提供此定型資料，藉以更正此問題。

## <a name="unexpected-classification"></a>未預期的分類

有時，分類器會學習影像所具備的共同特性。 例如，您想要建立玫瑰與鬱金香的分類器。 您提供了田野中的鬱金香影像，以及在藍色牆面前紅色花瓶中的玫瑰影像。 根據此資料，分類器可能會針對田野以及牆面加上花瓶來定型，而不是針對玫瑰與鬱金香來定型。

若要更正此問題，請使用利用更多樣化的影像來定型的指引：提供具有不同角度、背景、物件大小、群組及其他變化的影像。

## <a name="negative-image-handling"></a>負片處理

自訂視覺服務支援某些自動處理負片的功能。 如果您正在建置貓與狗的分類器，且送出了一支鞋子的影像進行預測，則分類器應該會針對貓與狗，將該影像評分為接近 0%。 

> [!WARNING]
> 自動化方法適用於非常清楚的負片。 如果負片只是定型中所使用的影像變化，則此方法可能不會運作得很好。 
>
> 例如，如果有一個哈士奇與柯基犬的分類器，但您饋送了博美狗的影像，它可能會將博美狗評分為哈士奇。 如果您的負片屬於此性質，請建立新的標記 (例如「其他」)，並將它套用到負片的定型影像。

## <a name="next-steps"></a>後續步驟

[使用預測 API](use-prediction-api.md)