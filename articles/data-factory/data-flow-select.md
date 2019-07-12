---
title: Azure Data Factory 對應資料流程選取轉換
description: Azure Data Factory 對應資料流程選取轉換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: bc83b41067d587adce41658a2c4b3d68969750ba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "61364392"
---
# <a name="azure-data-factory-mapping-data-flow-select-transformation"></a>Azure Data Factory 對應資料流程選取轉換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

對於資料行選擇性 (減少資料行數目) 或別名資料行和資料流名稱，使用此轉換。

選取的轉換可讓您設定整個資料流的別名，或在該資料流中的資料行指派不同的名稱 (別名)，之後可在資料流程中參考這些新名稱。 這項轉換可用於自我聯結的案例。 在 ADF 資料流程中實作自我聯結的方式是選取資料流、使用「新分支」設定資料流的分支，然後立即加入「選取」轉換。 該資料流現在會有新的名稱可供您聯結回原始的資料流，以便建立自我聯結：

![自我聯結](media/data-flow/selfjoin.png "自我聯結")

在上圖中，選取轉換位於最上層。 這是原始資料流的別名「OrigSourceBatting」。 在其下反白顯示的聯結轉換中，您可以看到，我們使用這個選取別名資料流做為右邊的聯結，以便我們參考內部聯結左右兩邊的同一個索引鍵。

選取也可以用來取消選取資料流程中的資料行。 例如，如果在接收中定義 6 個資料行，但是您只想要挑選其中 3 個進行轉換，然後流向接收，您可以使用選取轉換僅選取 3 個。

> [!NOTE]
> 您必須切換「全部選取」才能僅挑選特定的資料行

選項。

「選取」的預設設定是包含所有內送的資料行，並保留這些原始名稱。 您可以設定 Select 轉換的名稱，為資料流設定別名。

若要設定個別資料行的別名，請取消選取「全部選取」並使用下方的資料行對應。

![選取轉換](media/data-flow/select001.png "選取別名")
