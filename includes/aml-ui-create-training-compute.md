---
title: 包含檔案
description: 包含檔案
services: machine-learning
author: peterclu
ms.service: machine-learning
ms.author: peterlu
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 10/09/2019
ms.openlocfilehash: 1ee90e0c99234497b072bbee0b92d76129baea48
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929672"
---
對計算目標執行管線，此目標為連結至工作區的計算資源。 建立計算目標之後，您可以將其重複用於未來的執行。

1. 在畫布頂端選取 [執行]  以執行管線。

1. 當 [設定]  窗格出現時，請選取 [選取計算目標]  。

    如果您已經有可用的計算目標，您可以選取該目標來執行此管線。

    > [!NOTE]
    > 設計工具只能對 Azure Machine Learning Compute 目標執行實驗。 其他計算目標將不會顯示。

1. 輸入計算資源的名稱。

1. 選取 [儲存]  。

    ![設定計算目標](./media/aml-ui-create-training-compute/set-compute.png)

1. 選取 [執行]  。

1. 在 [設定管線執行]  對話方塊中，針對 [實驗]  選取 [+ 新增實驗]  。

    > [!NOTE]
    > 實驗群組的類似管線會一起執行。 如果您多次執行某個管線，您可以選取相同的實驗進行後續執行。

    1. 輸入**實驗名稱**的描述性名稱。

    1. 選取 [執行]  。
    
    您可以在畫布右上方檢視執行狀態和詳細資料。

    > [!NOTE]
    > 建立計算資源大約需要五分鐘。 資源建立後，您可以在未來執行時加以重複使用，而略過這段等候時間。
    >
    > 計算資源在閒置時會自動調整為零個節點，以節省成本。 當您在一段時間後再次加以使用時，它在相應增加時可能又會再出現約五分鐘的等候時間。
