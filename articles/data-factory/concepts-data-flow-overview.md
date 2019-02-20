---
title: Azure Data Factory 對應資料流程概觀
description: Azure Data Factory 中對應資料流程的概觀說明
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: ac07c0207f9463107b0c354bcd690cd9cc3981f4
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56212465"
---
# <a name="what-are-mapping-data-flows-in-azure-data-factory"></a>Azure Data Factory 中的對應資料流程是什麼？

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

對應資料流程可讓工程師在無須撰寫程式碼的情況下開發圖形化的轉換邏輯。 結果的資料流程會以活動的形式在 Azure Data Factory [管線] 內執行，並使用擴充的 Azure Databricks 叢集。

Azure Data Factory 資料流程的目的，是為了在無須撰寫程式碼的情況下，提供完全視覺化的體驗。 您的資料流程將會在您自己的執行叢集上執行，以取得擴充的資料處理。 Azure Data Factory 會處理所有的程式碼轉譯、路徑最佳化，以及您資料流程作業的執行。

您可以先在偵錯模式中建立資料流程，以透過互動方式驗證您的轉換邏輯。 接下來，將資料流程活動新增到您的管線以在管線偵錯中執行並測試您的資料流程，或是在管線中使用 [立即觸發] 來從管線活動測試您的資料流程。

您接著便可以使用會執行資料流程活動的 Azure Data Factory 管線，對該資料流程活動進行排程和監視。

偵錯模式開關能開啟資料流程設計介面，來允許以互動方式建置資料轉換。 偵錯模式能提供資料準備環境以進行資料流程建構。
