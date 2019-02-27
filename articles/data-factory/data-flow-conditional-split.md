---
title: Azure Data Factory 對應資料流程條件式分割轉換
description: Azure Data Factory 資料流程條件式分割轉換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 9fe650465160ab837d8c8c191887d0f952976682
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2019
ms.locfileid: "56270972"
---
# <a name="azure-data-factory-mapping-data-flow-conditional-split-transformation"></a>Azure Data Factory 對應資料流程條件式分割轉換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

條件式分割轉換可以視資料內容而定，將資料列路由傳送到不同的資料流。 條件式分割轉換的實作類似程式設計語言中的 CASE 決策結構。 轉換會評估運算式，並根據結果將資料列導向特定資料流。 此轉換也提供預設輸出，因此若某個資料列不符合任何運算式，它會被導向到預設輸出。

![條件式分割](media/data-flow/cd1.png "條件式分割")

若要新增額外的條件，請選取設定窗格底部的 [加入資料流]，然後按一下 [運算式產生器] 文字方塊以建置您的運算式。
