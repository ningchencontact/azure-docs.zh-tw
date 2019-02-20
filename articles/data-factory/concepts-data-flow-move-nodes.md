---
title: Azure Data Factory 資料流程移動節點
description: 如何移動 Azure Data Factory 資料流程圖表中的節點
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 4328a090478747e9acbfb25cca45b330cd4a300b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56212382"
---
#<a name="data-factory-data-flow-move-nodes"></a>Data Factory 資料流程移動節點

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![彙總轉換選項](media/data-flow/agghead.png "彙總工具標頭")

Azure Data Factory 資料流程設計介面是「建構」介面，其中您要由上至下、由左至右建立資料流程。 每個轉換皆附有工具箱 (加號 (+) 符號)。 專注在商務邏輯，而非透過自由格式 DAG 環境中的邊緣連接節點。

因此，它們「移動」轉換節點的方式是變更傳入資料流，而不使用拖放架構。 您將改為變更「傳入資料流」來四處移動轉換。

在 Azure Data Factory 資料流程中，資料流代表資料的流程。 在轉換設定窗格中，您將會看到 [輸入資料流] 欄位。 這會告訴您提供轉換的是哪一個資料流。 您可以按一下傳入資料流名稱，然後選擇另一個資料流，變更圖表上轉換節點的實體位置。 目前的轉換以及該資料流上的所有後續轉換，將會接著移至新位置。

如果您在之後要移動具有一或多個轉換的轉換，則資料流程中的新位置會透過新的分支聯結。

如果您在選擇節點之後沒有後續的轉換，則只有該轉換會移至新的位置。
