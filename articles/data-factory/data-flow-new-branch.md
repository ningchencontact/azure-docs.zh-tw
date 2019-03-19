---
title: Azure Data Factory 對應資料流程新增分支轉換
description: Azure Data Factory 對應資料流程新增分支轉換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 9fac78f21f2f128ccb040e176891c33d39bf2820
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732784"
---
# <a name="azure-data-factory-mapping-data-flow-new-branch-transformation"></a>Azure Data Factory 對應資料流程新增分支轉換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![分支選項](media/data-flow/menu.png "功能表")

分支會取用資料流程中的目前資料流，並將它複寫至另一個資料流。 使用「新增分支」針對同一個資料流執行多組作業和轉換。

範例：在資料流程中有來源轉換，包含一組選取的資料行和資料類型轉換。 接下來在該來源後放置衍生的資料行。 在「衍生的資料行」中，您已建立新的欄位，一個結合名字和姓氏而成的新「全名」欄位。

您可以使用一組轉換和一個資料列的接收處理新的資料流，並使用「新增分支」建立一份相同的資料流，以便使用不同組的轉換來轉換該資料。 在另外的分支轉換複製的資料，後續即可將該資料接收到不同的位置。

> [!NOTE]
> 在嘗試設定分支的目前位置有後續的轉換時，「新增分支」只會顯示「+ 轉換」功能表上的動作。 換句話說，直到在「選取」之後新增另一個轉換時，您才會看到此處結尾出現「新增分支」選項

![分支](media/data-flow/branch2.png "分支 2")
