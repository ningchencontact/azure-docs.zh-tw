---
title: 包含檔案
description: 包含檔案
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 10/22/2019
ms.openlocfilehash: 5a66212122745d0f4426e48e9487e9d674cec53f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489931"
---
>[!IMPORTANT]
>您可以使用您所建立的資源，作為其他 Azure Machine Learning 教學課程和操作說明文章的先決條件。

### <a name="delete-everything"></a>刪除所有內容

如果您不打算使用您所建立的任何資源，請刪除整個資源群組，以免產生任何費用：

1. 在 Azure 入口網站中，於視窗左側選取 [資源群組]  。
 
   ![在 Azure 入口網站中刪除資源群組](./media/aml-ui-cleanup/delete-resources.png)

1. 在清單中，選取您所建立的資源群組。

1. 在視窗的右側，選取省略符號按鈕 ( **...** )。

1. 選取 [刪除資源群組]  。

刪除資源群組同時會刪除您在設計工具中建立的所有資源。  

### <a name="delete-only-the-compute-target"></a>僅刪除計算目標

您在這裡建立的計算目標會在不使用時*自動調整*為零個節點。 這是為了盡量降低費用。 如果您想要刪除計算目標，請採取下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)中，開啟您的工作區。

    ![刪除計算目標](./media/aml-ui-cleanup/delete-compute-target.png)

1. 在工作區的 [計算]  區段中選取資源。

1. 選取 [刪除]  。

### <a name="delete-individual-assets"></a>刪除個別資產

在建立實驗的設計工具中，藉由選取個別資產，再選取 [刪除]  按鈕，即可刪除個別資產。

![刪除資產](./media/aml-ui-cleanup/delete-asset.png)

您可以選取每個資料集並選取 **Unregister** ，從工作區中將資料集取消註冊。

![取消註冊資料集](./media/aml-ui-cleanup/unregister-dataset.png)


