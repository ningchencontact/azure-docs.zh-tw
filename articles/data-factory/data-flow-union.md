---
title: Azure Data Factory 對應資料流程新增分支轉換
description: Azure Data Factory 對應資料流程新增分支轉換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: af2225d749283c7124f89d5a7cd735b2f6bfd121
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792611"
---
# <a name="mapping-data-flow-union-transformation"></a>對應資料流程聯集轉換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

集合聯集會將多個資料流結合在一起，並使那些資料流的 SQL 集合聯集成為來自集合聯集轉換的新輸出。 所有的每個輸入資料流中的結構描述會結合內資料流程中，而不需要有聯結索引鍵。

您可以結合 n 個資料流，在 [設定] 資料表中，選取 [+] 圖示旁邊每個設定的資料列，包括在資料流程中的來源資料以及從現有的轉換的資料流。

![聯集的轉換](media/data-flow/union.png "聯集")

在此情況下，您可以結合不同的中繼資料 （在此範例中，三個不同的來源檔案） 的多個來源，並將它們合併成單一資料流：

![聯集轉換概觀](media/data-flow/union111.png "聯集 1")

若要這麼做，請新增額外的資料列聯集的設定中包含您想要新增的所有來源。 就不需要針對相同的查詢或聯結索引鍵：

![聯集轉換設定](media/data-flow/unionsettings.png "聯集的設定")

如果您將選取的轉換後等位時，您將能夠重新命名重疊的欄位或欄位不是名為 headerless 來源。 按一下 「 檢查 」，以查看具有 132 的總資料行，在此範例中三個不同來源的合併中繼資料：

![最終的聯集轉換](media/data-flow/union333.png "聯集 3")

## <a name="name-and-position"></a>名稱和位置

當您選擇 「 依名稱的聯集 」 時，每個資料行值將拖放至對應的資料行，從每個來源，使用新的串連的中繼資料結構描述中。

如果您選擇 「 依位置的聯集 」 時，每個資料行值會放置到每個對應的來源，在新合併的資料流其中每個來源的資料會加入到相同的資料流中產生的原始位置中：

![聯集的輸出](media/data-flow/unionoutput.png "聯集的輸出")

## <a name="next-steps"></a>後續步驟

瀏覽類似的轉換，包括[聯結](data-flow-join.md)並[Exists](data-flow-exists.md)。
