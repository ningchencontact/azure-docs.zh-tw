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
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028511"
---
在計算目標，附加至您的工作區的計算資源上，執行實驗。  一旦您建立計算目標時，您可以重複用於未來的執行。

1. 選取 **執行**底部來執行實驗。

     ![執行實驗](./media/aml-ui-create-training-compute/run-experiment.png)

1. 當**設定計算目標**對話方塊隨即出現，如果您的工作區中已經有的計算資源，您可以現在選取它。  否則，請選取**新建**。

    > [!NOTE]
    > 視覺化介面只可以在 Machine Learning 計算目標上執行實驗。 不會顯示其他計算目標。

1. 提供計算資源的名稱。

1. 選取 [執行]。

    ![設定計算目標](./media/aml-ui-create-training-compute/set-compute.png)

    現在將建立的計算資源。 在右上角，實驗的檢視狀態。 

    > [!NOTE]
    > 需要大約 5 分鐘的時間建立計算資源。 建立資源之後，您可以重複使用它，並略過這個等候時間，以便之後執行。
    >
    > 計算資源來節省成本閒置時，將會為 0 個節點的自動調整。  當您使用一次在延遲之後時，您可能會再次遇到大約 5 分鐘的等候時間時它會依據上一步。
