---
title: 包含檔案
description: 包含檔案
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 05/06/2019
ms.openlocfilehash: 623e993dfbe6bbb3297fa6470865ab1a04f55b37
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028586"
---
>[!IMPORTANT]
>您可以使用您所建立的資源做為其他 Azure Machine Learning 服務教學課程和 how-to 文章的必要條件。


### <a name="delete-everything"></a>刪除所有項目

如果您不打算使用您所建立的任何項目，刪除整個資源群組，因此您不會產生任何費用：

1. 在 Azure 入口網站中，選取**資源群組**視窗的左邊。
 
   ![在 Azure 入口網站中刪除資源群組](./media/aml-ui-cleanup/delete-resources.png)

1. 在清單中，選取您所建立的資源群組。

1. 在視窗的右側，選取省略符號按鈕 (**...**).

1. 選取 [刪除資源群組]。

刪除資源群組時，也會刪除您建立視覺介面中的所有資源。  

### <a name="delete-only-the-compute-target"></a>刪除計算目標

您在這裡建立的計算目標*自動相應*零時它未使用的節點。 這是為了將費用降至最低。 如果您想要刪除的計算目標，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)中，開啟您的工作區。

    ![刪除計算目標](./media/aml-ui-cleanup/delete-compute-target.png)

1. 在 **計算**區段的 工作區中，選取 資源。

1. 選取 [刪除] 。

### <a name="delete-individual-assets"></a>刪除個別資產

在您用來建立您的實驗的視覺化介面，刪除個別資產選取它們，然後選取**刪除** 按鈕。

![刪除實驗](./media/aml-ui-cleanup/delete-experiment.png)
