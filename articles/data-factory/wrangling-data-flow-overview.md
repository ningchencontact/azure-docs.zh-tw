---
title: 在 Azure Data Factory 中整頓資料流程 |Microsoft Docs
description: Azure Data Factory 中的整頓資料流程總覽
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 05119adfa5b254297fd656854a027ede05c5ef26
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518576"
---
# <a name="what-are-wrangling-data-flows"></a>什麼是整頓的資料流程？

組織需要進行資料準備和整頓，以精確分析每日持續成長的複雜資料。 需要進行資料準備，讓組織可以使用各種商務程式中的資料，並縮短價值的時間。

Azure Data Factory 中的整頓資料流程可讓您以雲端規模反復執行無程式碼資料的準備工作。 整頓資料流程會與[Power Query Online](https://docs.microsoft.com/power-query/)整合，讓 data factory 使用者可以使用 Power Query M 功能。

整頓資料流程會將 Power Query 線上混合編輯器產生的 M 轉譯為 spark 程式碼，以進行雲端規模的執行。

整頓資料流程特別適用于資料工程師或「公民資料整合者」。

## <a name="use-cases"></a>使用案例

### <a name="fast-interactive-data-exploration-and-preparation"></a>快速的互動式資料探索和準備

多個資料工程師和公民資料整合者可以互動方式探索及準備雲端規模的資料集。 隨著資料 lake 中資料量、各種和速度的增加，使用者需要有效的方式來探索和準備資料集。 例如，您可能需要建立一個資料集，其中包含自2017起，新客戶的所有客戶人口統計資訊。 您未對應到已知的目標。 您正在探索、整頓和準備資料集，以符合在 lake 中發佈之前的需求。 整頓的資料流程通常用於較不正式的分析案例。 備妥資料集可用來進行轉換，並使用下游的機器學習作業。

### <a name="code-free-agile-data-preparation"></a>無程式碼的 agile 資料準備

公民資料整合者會花費超過60% 的時間來尋找和準備資料。 他們想要以程式碼的免費方式執行，以提升營運生產力。 讓公民資料整合者能夠以可擴充的方式，使用已知的 Power Query 工具來豐富、塑造及發佈資料，以大幅提升其生產力。 Azure Data Factory 中的整頓資料流程可讓您熟悉的 Power Query 線上混合式編輯器，讓公民資料整合者能夠快速修正錯誤、將資料標準化，以及產生高品質的資料，以支援商務決策。

### <a name="data-validation"></a>資料驗證

以無程式碼的方式流覽資料，以移除任何極端值、異常狀況，並將其符合用於快速分析的圖形。

## <a name="next-steps"></a>後續步驟

瞭解如何[建立整頓資料流程](wrangling-data-flow-tutorial.md)。