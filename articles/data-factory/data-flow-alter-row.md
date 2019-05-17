---
title: 對應資料流程 Alter 資料列轉換的 azure Data Factory
description: 如何更新使用 Azure Data Factory 對應資料流程 Alter 資料列轉換的資料庫目標
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: f0ac5bb36079983b10e4d86cc776bd4e5ee6817d
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2019
ms.locfileid: "65520153"
---
# <a name="azure-data-factory-alter-row-transformation"></a>Azure Data Factory Alter 資料列轉換

您可以使用 Alter 資料列轉換來設定的資料列的 insert、 delete、 update 和更新插入的原則。 您可以加入一個對多條件，做為運算式。 這些情況可能會導致資料列 （或資料列） 插入、 更新、 刪除或更新插入。 Alter 資料列可能會產生 DDL 與 DML 動作，對您的資料庫。

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![修改資料列設定](media/data-flow/alter-row1.png "Alter 資料列設定")

> [!NOTE]
> Alter 資料列轉換只會對資料庫在資料流程中的接收器。 在偵錯工作階段期間，不會發生的動作，您將指派給資料列 （insert、 update、 delete、 upsert）。 您必須執行資料流程工作新增至管線，並使用管線偵錯] 或 [觸發程序制定您的資料庫資料表的 alter 資料列原則。

## <a name="view-policies"></a>檢視原則

上切換資料流程偵錯模式，然後在 [資料預覽] 窗格中檢視的 alter 資料列原則結果。 在資料工作流程進行偵錯模式中執行 alter 資料列不會產生 DDL 或 DML 動作，對您的目標。 若要有這些動作發生，請執行資料流程管線中執行資料流程的活動內的資料。

![變更資料列的原則](media/data-flow/alter-row3.png "Alter 資料列的原則")

這可讓您驗證並檢視您的條件所依據的每個資料列的狀態。 有圖示代表每個插入、 更新、 刪除和更新插入會發生在資料流程中指出哪些動作的動作會發生，當您執行資料流程管線中的資料。

## <a name="sink-settings"></a>接收設定

您必須擁有資料庫，接收要運作的 Alter 資料列的類型。 在 接收設定中，您必須設定允許每個動作。

![Alter 資料列接收器](media/data-flow/alter-row2.png "Alter 資料列接收")

資料庫接收 ADF 資料流程中的預設行為是插入資料列。 如果您想要允許更新、 更新插入，以及刪除，您也必須檢查兩個方塊中的接收器，以允許執行的動作。

> [!NOTE]
> 如果您的插入、 更新或 upsert 修改接收之目標資料表的結構描述，您的資料流程將會失敗。 若要修改目標結構描述，您的資料庫中，您必須選擇 「 重新建立資料表 」 選項，接收中。 這會卸除並重新建立您的資料表與新的結構描述定義。

## <a name="next-steps"></a>後續步驟

Alter 資料列轉換之後，您可能想要[接收資料至目的地資料存放區](data-flow-sink.md)。
