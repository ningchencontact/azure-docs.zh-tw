---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: ee2c1fd1fc1cad07b14a2c99318be20be30db9c5
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423558"
---
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