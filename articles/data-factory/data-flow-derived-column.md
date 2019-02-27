---
title: Azure Data Factory 資料流程衍生的資料行轉換
description: Azure Data Factory 資料流程衍生的資料行轉換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: aee005aed52563e214e24148af2563fd7869de76
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2019
ms.locfileid: "56270974"
---
# <a name="azure-data-factory-data-flow-derived-column-transformation"></a>Azure Data Factory 資料流程衍生的資料行轉換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

使用「衍生的資料行」轉換可在資料流程中產生新的資料行或修改現有欄位。

![衍生資料行](media/data-flow/dc1.png "衍生的資料行")

您可以在單一「衍生的資料行」轉換中執行多個衍生的資料行動作。 按一下 [新增資料行] 以在單一轉換步驟中轉換多個資料行。

在 [資料行] 欄位中，選取要使用新的衍生值覆寫現有的資料行，或按一下 [建立新資料行] 以使用衍生的新值來產生新的資料行。

[運算式] 文字方塊中將開啟運算式產生器，您可以使用運算式函式為衍生資料行建置運算式。
