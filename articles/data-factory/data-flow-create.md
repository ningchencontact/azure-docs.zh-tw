---
title: 建立 Azure Data Factory 對應資料流程
description: 如何建立 Azure Data Factory 對應資料流程
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 31528c0ac7796aafbde74a3814d971138ec4ba2e
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387249"
---
# <a name="create-azure-data-factory-data-flow"></a>建立 Azure Data Factory 資料流程



對應 ADF 中的資料流程可以大規模轉換資料，完全不需要撰寫任何程式碼。 您可以建構一系列轉換，以便在資料流設計工具中設計資料轉換作業。 先進行不限次數的來源轉換，然後進行資料轉換步驟。 接著，使用接收完成資料流程，以便將結果置於目的地中。

首先從 Azure 入口網站建立新的 V2 Data Factory 來開始著手。 建立新的中心之後，按一下「作者與監視器」磚以啟動 Data Factory 使用者介面。

![資料流程選項](media/data-flow/v2portal.png "資料流程建立")

您在 Data Factory UI 中時，可以使用範例資料流程。 可以從 ADF 範本資源庫中取得這些範例。 在 ADF 中，建立「範本管線」，並從範本資源庫中選取資料流程類別。

![資料流程選項](media/data-flow/template.png "資料流程建立")

系統會提示您輸入 Azure Blob 儲存體帳戶資訊。

![資料流程選項](media/data-flow/template2.png "資料流程建立2")

[這些範例所使用的資料可以在這裡找到](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata)。 下載範例資料，並將檔案儲存在您的 Azure Blob 儲存體帳戶上，以便您執行範例。

## <a name="create-new-data-flow"></a>建立新的資料流程

使用 ADF UI 中的 [建立資源] [加號] 按鈕來建立資料流程。

![資料流程選項](media/data-flow/newresource.png "新增資源")

## <a name="next-steps"></a>後續步驟

使用[來源轉換](data-flow-source.md)開始建立您的資料轉換。
