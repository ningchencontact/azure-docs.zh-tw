---
title: Azure Data Factory 對應資料流程 [最佳化] 索引標籤
description: 使用 [最佳化] 索引標籤資料分割設定來最佳化 Azure Data Factory 對應資料流程
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 3802a8475d8a39a2f275dbc7fcf21ce69892a117
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728772"
---
# <a name="mapping-data-flow-transformation-optimize-tab"></a>對應資料流程轉換 [最佳化] 索引標籤

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

每個資料流程轉換都有 [最佳化] 索引標籤。[最佳化] 索引標籤包含選擇性設定，可用來設定資料流程的資料分割配置。

<img src="media/data-flow/opt001.png" width="800">

預設設定是 [使用目前的資料分割]。 [目前的資料分割] 會指示 Azure Data Factory 使用在 Azure Databricks 中 Spark 上執行之資料流程的原生資料分割配置。 這通常是建議的方法。

不過在某些情況下，您可能會想要調整資料分割。 例如，如果您想要將轉換輸出為 Lake 中的單一檔案，請在 [最佳化] 索引標籤上選擇 [單一分割區]，以在接收轉換中進行資料分割。

此外，您也可能想要控制用於資料轉換的資料分割配置以提升效能。 調整資料的資料分割可讓您對資料在計算節點上的散發和資料位置最佳化取得一定程度的控制，這可能會對整體資料流程效能產生正面與負面的影響。

如果您想要變更任何轉換上的資料分割，請按一下 [最佳化] 索引標籤，並選取 [設定資料分割] 選項按鈕。 系統將會為您顯示一系列資料分割選項。 應實作的最佳資料分割方法，會根據您的資料量、候選索引鍵、Null 值和多重性而有所不同。 最佳做法是從預設資料分割開始，然後嘗試不同的資料分割選項。 您可以使用 [管線] 中的 [偵錯] 執行來進行測試，然後從 [監視] 檢視中檢視每個轉換群組所花費的時間，以及分割區的使用情況。

<img src="media/data-flow/opt002.png" width="600">

### <a name="round-robin"></a>循環配置資源

[循環配置資源] 是會自動將資料平均散發到所有分割區上的簡易資料分割。 當您沒有良好的索引鍵候選以實作穩固、智慧的資料分割策略時，請使用 [循環配置資源]。 您可以設定實體分割區的數目。

### <a name="hash"></a>雜湊

Azure Data Factory 會產生資料行雜湊以產生統一的分割區，使具有相似值的資料列會落在相同的分割區中。 使用 [雜湊] 選項時，請測試潛在的分割扭曲。 您可以設定實體分割區的數目。

### <a name="dynamic-range"></a>動態範圍

[動態範圍] 會使用 Spark 動態範圍，其會以您提供的資料行或運算式為基礎。 您可以設定實體分割區的數目。 

### <a name="fixed-range"></a>固定範圍

您必須建置能針對已分割之資料行內的值提供固定範圍的運算式。 在使用此選項之前，您應該先充分了解自己的資料，以避免分割扭曲。 針對運算式所輸入的值將會被用來作為資料分割函數的一部分。 您可以設定實體分割區的數目。

### <a name="key"></a>Key

如果您已充分了解自己資料的多重性，索引鍵資料分割可能會是個良好的分割策略。 索引鍵資料分割將會針對資料行中的每個唯一值建立分割區。 您無法設定分割區的數目，因為該數目是以資料中的唯一值作為基礎。
