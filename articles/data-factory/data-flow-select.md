---
title: Azure Data Factory 對應資料流程選取轉換
description: Azure Data Factory 對應資料流程選取轉換
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 15c74637a2dc42ec44f582878b5505d94637cd7b
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314199"
---
# <a name="azure-data-factory-mapping-data-flow-select-transformation"></a>Azure Data Factory 對應資料流程選取轉換
[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

將此轉換用於資料行選擇性 (減少資料行數目)、別名資料行和資料流程名稱, 以及重新排序資料行。

## <a name="how-to-use-select-transformation"></a>如何使用選取轉換
選取的轉換可讓您設定整個資料流的別名，或在該資料流中的資料行指派不同的名稱 (別名)，之後可在資料流程中參考這些新名稱。 這項轉換可用於自我聯結的案例。 在 ADF 資料流程中實作自我聯結的方式是選取資料流、使用「新分支」設定資料流的分支，然後立即加入「選取」轉換。 該資料流現在會有新的名稱可供您聯結回原始的資料流，以便建立自我聯結：

![自我聯結](media/data-flow/selfjoin.png "自我聯結")

在上圖中，選取轉換位於最上層。 這是原始資料流的別名「OrigSourceBatting」。 在下方反白顯示的聯結轉換中, 您可以看到我們使用這個選取別名資料流程做為右手聯結, 讓我們能夠在內部聯結的左側 & 右邊參考相同的索引鍵。

選取也可以用來取消選取資料流程中的資料行。 例如，如果在接收中定義 6 個資料行，但是您只想要挑選其中 3 個進行轉換，然後流向接收，您可以使用選取轉換僅選取 3 個。

> [!NOTE]
> 您必須切換「全部選取」才能僅挑選特定的資料行

![選取轉換](media/data-flow/select001.png "選取別名")

## <a name="options"></a>選項。
* 「選取」的預設設定是包含所有內送的資料行，並保留這些原始名稱。 您可以設定 Select 轉換的名稱，為資料流設定別名。
* 若要設定個別資料行的別名，請取消選取「全部選取」並使用下方的資料行對應。
* 選擇 [略過重複專案], 以排除輸入或輸出中繼資料的重復資料行。

![略過重複專案](media/data-flow/select-skip-dup.png "略過重複專案")

## <a name="next-steps"></a>後續步驟
* 使用 [選取] 來重新命名、重新排列和建立別名資料行之後, 請使用 [[接收] 轉換](data-flow-sink.md), 將您的資料放入資料存放區中。
