---
title: 什麼是 Azure 自訂視覺服務？
titlesuffix: Azure Cognitive Services
description: 了解如何使用自訂視覺服務在 Azure 雲端中建置自訂影像分類器。
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: overview
ms.date: 10/26/2018
ms.author: anroth
ms.openlocfilehash: 5e8e675b32bfd4c741b82b1ab341a80adbb0529d
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741635"
---
# <a name="what-is-azure-custom-vision"></a>什麼是 Azure 自訂視覺服務？

Azure 自訂視覺服務 API 是一項認知服務，可讓您建置、部署和改善自訂影像分類器。 影像分類器是一項 AI 服務，可根據特定的特性將影像按類別 (標記) 排序。 不同於[電腦視覺](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)服務，自訂視覺服務可讓您建立自己的分類。

## <a name="what-it-does"></a>作用

自訂視覺服務會使用機器學習演算法來分類影像。 身為開發人員的您必須提交具備和缺少相關分類的影像群組。 您必須在提交時指定正確的影像標記。 然後，演算法會針對這項資料進行訓練，並藉由對該項資料進行自我測試，計算其本身的精確度。 模型經過訓練後，您可以測試、重新訓練，最終根據您的應用程式需求用它來分類新的影像。 您也可以匯出模型本身以供離線使用。

### <a name="classification-and-object-detection"></a>分類和物件偵測

自訂視覺功能可以細分成兩項功能。 **影像分類**會將分類的分布情形指派給每個影像。 **物件偵測**也相類似，但它也會傳回影像中已套用的標記所在位置的座標。

### <a name="optimization"></a>最佳化

一般而言，自訂視覺服務所使用的方法不容易受到差異的影響，所以只要少量資料就可以開始建立原型。 一般而言，每個標記 50 個影像是不錯的起點。 但這表示服務並未具備偵測影像中些微差異的最佳效能 (例如，在品保案例中偵測出微小的裂縫或凹痕)。

此外，您可以從數種不同針對特定題材內容進行最佳化的自訂視覺演算法中擇一使用&mdash;例如，地標或零售項目。 如需其詳細資訊，請參閱[建置分類器](getting-started-build-a-classifier.md)指南。

## <a name="what-it-includes"></a>包含內容
自訂視覺服務可作為一組的原生 SDK，也可透過[自訂視覺服務首頁](https://customvision.ai/)上的 Web 介面來使用。 您可以透過上述一或兩種介面來建立、測試和訓練模型。

![Chrome 瀏覽器視窗中的自訂視覺服務首頁](media/browser-home.png)

## <a name="next-steps"></a>後續步驟

請依照[建置分類器](getting-started-build-a-classifier.md)指南在 Web 上開始使用自訂視覺服務，或完成[影像分類教學課程](csharp-tutorial.md)以使用程式碼實作案例。
