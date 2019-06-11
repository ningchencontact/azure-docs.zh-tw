---
title: 包含檔案
description: 包含檔案
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/06/2019
ms.openlocfilehash: cf35651f7dd839e8792029851b9bfe278036624c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66397365"
---
對計算目標執行實驗，此目標為連結至工作區的計算資源。  建立計算目標之後，您可以將其重複用於未來的執行。

1. 選取底部的 [執行]  以執行實驗。

     ![執行實驗](./media/aml-ui-create-training-compute/run-experiment.png)

1. 當 [設定計算目標]  對話方塊出現時，如果您的工作區中已有計算資源，您即可加以選取。  否則，請選取 [新建]  。

    > [!NOTE]
    > 視覺化介面只能對 Machine Learning Compute 目標執行實驗。 其他計算目標將不會顯示。

1. 提供計算資源的名稱。

1. 選取 [執行]  。

    ![設定計算目標](./media/aml-ui-create-training-compute/set-compute.png)

    此時會建立計算資源。 在實驗的右上角檢視狀態。 

    > [!NOTE]
    > 建立計算資源大約需要 5 分鐘。 資源建立後，您可以在未來執行時加以重複使用，而略過這段等候時間。
    >
    > 計算資源在閒置時會自動調整為 0 個節點，以節省成本。  當您在一段時間後再次加以使用時，它在重新相應增加時可能又會再出現約 5 分鐘的等候時間。
