---
title: 包含檔案
description: 包含檔案
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 11/06/2019
ms.openlocfilehash: 66f5c72fcabb62e21f0110cb981b7271244c0648
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2019
ms.locfileid: "73799725"
---
>[!IMPORTANT]
>您可以使用您所建立的資源，作為其他 Azure Machine Learning 教學課程和操作說明文章的先決條件。

### <a name="delete-everything"></a>刪除所有內容

如果您不打算使用您所建立的任何資源，請刪除整個資源群組，以免產生任何費用：

1. 在 Azure 入口網站中，於視窗左側選取 [資源群組]  。
 
   ![在 Azure 入口網站中刪除資源群組](./media/aml-ui-cleanup/delete-resources.png)

1. 在清單中，選取您所建立的資源群組。

1. 選取 [刪除資源群組]  。

刪除資源群組同時會刪除您在設計工具中建立的所有資源。  

### <a name="delete-individual-assets"></a>刪除個別資產

在建立實驗的設計工具中，藉由選取個別資產，再選取 [刪除]  按鈕，即可刪除個別資產。

您在這裡建立的計算目標會在不使用時*自動調整*為零個節點。 這是為了盡量降低費用。 如果您想要刪除計算目標，請採取下列步驟：

![刪除資產](./media/aml-ui-cleanup/delete-asset.png)

您可以選取每個資料集並選取 **Unregister** ，從工作區中將資料集取消註冊。

![取消註冊資料集](./media/aml-ui-cleanup/unregister-dataset.png)

若要刪除資料集，請使用 Azure 入口網站或儲存體總管瀏覽至儲存體帳戶，並手動刪除這些資產。


