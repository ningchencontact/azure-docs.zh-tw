---
title: Azure Data Factory 對應資料流程條件式分割轉換
description: Azure Data Factory 資料流程條件式分割轉換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: a4ea79e05165dfae4f79aa6473a07151ba7c00fc
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/23/2019
ms.locfileid: "56727803"
---
# <a name="azure-data-factory-mapping-data-flow-conditional-split-transformation"></a>Azure Data Factory 對應資料流程條件式分割轉換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

條件式分割轉換可以視資料內容而定，將資料列路由傳送到不同的資料流。 條件式分割轉換的實作類似程式設計語言中的 CASE 決策結構。 轉換會評估運算式，並根據結果將資料列導向特定資料流。 此轉換也提供預設輸出，因此若某個資料列不符合任何運算式，它會被導向到預設輸出。

![條件式分割](media/data-flow/cd1.png "條件式分割")

若要新增額外的條件，請選取設定窗格底部的 [加入資料流]，然後按一下 [運算式產生器] 文字方塊以建置您的運算式。
