---
title: 加入資料行：模組參考
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure Machine Learning 服務中的新增資料行 」 模組來串連兩個資料集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f1e087e97007c6ba271651a9791c7c3b38a9b9b7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029351"
---
# <a name="add-columns-module"></a>新增資料行 模組

本文說明 Azure Machine Learning 服務的視覺化介面 （預覽） 的模組。

您可以使用此模組來串連兩個資料集。 您將從您指定為輸入來建立單一資料集的兩個資料集的所有資料行。 如果您要串連兩個以上的資料集，請使用數個執行個體**加入資料行**。



## <a name="how-to-configure-add-columns"></a>如何設定新增資料行
1. 新增**加入資料行**模組至您的實驗。

2. 連接您想要串連兩個資料集。 如果您想要結合兩個以上的資料集，您可以鏈結在一起的數個組合**加入資料行**。

    - 可以結合兩個具有不同數目的資料列的資料行。 輸出資料集填補遺漏值的較小的來源資料行中每個資料列。

    - 您無法選擇加入個別資料行。 當您使用串連每個資料集的所有資料行**加入資料行**。 因此，如果您想要新增資料行的子集，使用資料集中選取的資料行建立資料集與您想要的資料行中。

3. 執行實驗。

### <a name="results"></a>結果
執行實驗之後有：

- 若要查看新的資料集的第一個資料列，以滑鼠右鍵按一下的輸出**加入資料行**，然後選取視覺化。

在新的資料集中的資料行數目等於兩個輸入資料集的資料行的總和。

如果有兩個具有相同的名稱，輸入資料集中的資料行，數值後置詞會加入資料行的名稱。 例如，如果有兩個執行個體的資料行名為 TargetOutcome，左側資料行重新命名為 TargetOutcome_1 和右邊的資料行可能是重新命名 TargetOutcome_2。

## <a name="next-steps"></a>後續步驟

請參閱[可用的模組集](module-reference.md)Azure Machine Learning 服務。 