---
title: Azure Data Factory 對應的資料流程 Alter Row 轉換
description: 如何使用 Azure Data Factory 對應的資料流程改變數據列轉換來更新資料庫目標
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: fc497837792075501bcd92f6ee07ad9ee4fe2dfa
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027016"
---
# <a name="azure-data-factory-alter-row-transformation"></a>Azure Data Factory Alter Row 轉換

使用 Alter Row 轉換來設定資料列的 insert、delete、update 和 upsert 原則。 您可以新增一到多個條件做為運算式。 這些條件應依照優先順序來指定，因為每個資料列會以對應至第一個相符運算式的原則來標示。 每一個條件都會導致插入、更新、刪除或 upserted 資料列（或資料列）。 Alter Row 可以針對您的資料庫產生 DDL & DML 動作。



![改變數據列設定](media/data-flow/alter-row1.png "改變數據列設定")

> [!NOTE]
> Alter Row 轉換只會在資料流程中的資料庫接收上運作。 您指派給資料列（insert、update、delete、upsert）的動作不會在 debug 會話期間發生。 您必須將「執行資料流程」工作加入至管線，並使用管線 debug 或觸發程式來制定資料庫資料表的 alter row 原則。

## <a name="indicate-a-default-row-policy"></a>指出預設的資料列原則

建立 Alter Row 轉換，並指定條件為 `true()` 的資料列原則。 不符合任何先前定義之運算式的每個資料列都會標示為指定的資料列原則。 根據預設，不符合任何條件運算式的每個資料列都會標示為 `Insert`。

![改變數據列一個原則](media/data-flow/alter-row4.png "改變數據列一項原則")

> [!NOTE]
> 若要使用一個原則標記所有資料列，您可以建立該原則的條件，並將條件指定為 `true()`。

## <a name="view-policies"></a>檢視原則

在 [資料預覽] 窗格中開啟 [資料流程] 偵錯工具，以查看您的 alter row 原則結果。 在資料流程的「資料流程」（Debug）模式中執行 alter row 不會針對您的目標產生 DDL 或 DML 動作。 若要執行這些動作，請在管線內執行「資料流程」活動內的資料流程。

![改變數據列原則](media/data-flow/alter-row3.png "改變數據列原則")

這可讓您根據條件來驗證和查看每個資料列的狀態。 您的資料流程中會有每個插入、更新、刪除和 upsert 動作的圖示代表，這表示當您在管線內執行資料流程時，將會發生哪一個動作。

## <a name="sink-settings"></a>接收設定

您必須有資料庫接收類型，Alter Row 才能正常執行。 在 [接收設定] 中，您應該將每個對應于您 Alter Row 條件的動作設定為 [允許]。

![改變數據列接收](media/data-flow/alter-row2.png "改變數據列接收")

ADF 資料流程中具有資料庫接收的預設行為是插入資料列。 如果您也想要允許更新、更新插入和刪除，您也必須在接收中檢查這些方塊以允許動作。

> [!NOTE]
> 如果您的插入、更新或更新插入修改了接收中目標資料表的架構，資料流程將會失敗。 若要修改資料庫中的目標架構，您必須選擇接收中的 [重新建立資料表] 選項。 這會卸載並重新建立您的資料表，並使用新的架構定義。

## <a name="next-steps"></a>後續步驟

在 Alter Row 轉換之後，您可能會想要將[資料接收到目的地資料存放區](data-flow-sink.md)。
