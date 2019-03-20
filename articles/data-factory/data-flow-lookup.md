---
title: Azure Data Factory 對應資料流程查閱轉換
description: Azure Data Factory 對應資料流程查閱轉換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: b156917a9987b023a9bf94e51c0cc14aebb133c7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/23/2019
ms.locfileid: "56738380"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure Data Factory 對應資料流程查閱轉換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

使用查閱以將另一個來源的參考資料新增至資料流程。 查閱轉換需要定義的來源，其指向您的參考資料表且會比對索引鍵欄位。

![查閱轉換](media/data-flow/lookup1.png "查閱")

選取您想要在內送資料流欄位與參考來源欄位之間比對的索引鍵欄位。 您必須先在資料流程設計畫布上建立新的來源，以作為查閱的右端。

當找到相符項目時，來自參考來源的結果資料列和資料行將會新增到資料流程中。 您可以選擇有興趣且希望包含在資料流結尾接收 (Sink) 中的欄位。
