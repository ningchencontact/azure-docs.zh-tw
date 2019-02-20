---
title: Azure Data Factory 對應資料流程參考節點
description: 「Data Factory 資料流程」會新增用於聯結、查詢、聯集的參考節點
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 641375c2b848957ffc0f5ad092a28460d91b6690
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56212406"
---
# <a name="azure-data-factory-mapping-data-flow-reference-node"></a>Azure Data Factory 對應資料流程參考節點

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![參考節點](media/data-flow/referencenode.png "參考節點")

參考節點會自動新增至畫布，以表示它所連結的節點參考畫布上另一個現有的節點。 請將參考節點想成是另一個資料流程轉換的指標或參考。

例如︰當您將多個資料流「聯結」或「聯集」時，「資料流程」畫布可能會新增反映非主要傳入資料流之名稱和設定的參考節點。

您無法移動或刪除此參考節點。 不過，您可以按一下節點來進入其中，以修改原始的轉換設定。

控制「資料流程」新增參考節點之時機的 UI 規則，依據的是資料列間可用的空間和垂直間距。
