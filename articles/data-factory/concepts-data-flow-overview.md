---
title: 對應 Azure Data Factory 中的資料流程 |Microsoft Docs
description: 在 Azure Data Factory 中對應資料流程的總覽
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/7/2019
ms.openlocfilehash: 8d0ad794caee8a06c8d403a981037d6560fb3f43
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030093"
---
# <a name="what-are-mapping-data-flows"></a>什麼是對應資料流程？

對應資料流程會以視覺化方式設計 Azure Data Factory 中的資料轉換。 資料流程可讓資料工程師開發圖形化資料轉換邏輯，而不需要撰寫程式碼。 產生的資料流程會使用相應放大的 Spark 叢集，以 Azure Data Factory 管線內的活動執行。 您可以透過現有的 Data Factory 排程、控制、流程和監視功能來運作資料流程活動。

對應資料流程可提供完全視覺化的體驗，而不需要撰寫任何程式碼。 您的資料流程將會在您自己的執行叢集上執行，以進行相應放大的資料處理。 Azure Data Factory 會處理所有的程式碼轉譯、路徑最佳化，以及您資料流程作業的執行。

## <a name="getting-started"></a>使用者入門

若要建立資料流程，請按一下 [Factory 資源] 下的加號。 

![新]的資料流程(media/data-flow/newdataflow2.png "新")資料流程

這會帶您前往 [資料流程] 畫布，您可以在其中建立轉換邏輯。 按一下 [新增來源] 方塊，開始設定您的來源轉換。 如需詳細資訊，請參閱[來源轉換](data-flow-source.md)。

## <a name="data-flow-canvas"></a>資料流程畫布

資料流程畫布分成三個部分：頂端列、圖形和設定面板。 

![Canvas](media/data-flow/canvas1.png "Canvas")

### <a name="graph"></a>圖形

圖形會顯示轉換資料流程。 它會顯示來源資料流入一或多個接收時的歷程。 若要新增來源，請按一下 [新增來源] 方塊。 若要加入新的轉換，請按一下現有轉換右下方的加號。

![Canvas](media/data-flow/canvas2.png "Canvas")

### <a name="configuration-panel"></a>設定面板

[設定] 面板會顯示目前選取之轉換的特定設定，或者，如果未選取任何轉換，則為 [資料流程]。 在整體資料流程設定中，您可以編輯 [**一般**] 索引標籤底下的 [名稱] 和 [描述]，或透過 [**參數**] 索引標籤新增參數。如需詳細資訊，請參閱[對應資料流程參數](parameters-data-flow.md)。

每個轉換至少有四個設定索引標籤：

#### <a name="transformation-settings"></a>轉換設定

每個轉換的 [設定] 窗格中的第一個索引標籤包含該轉換的特定設定。 如需詳細資訊，請參閱該轉換的檔頁面。

![來源設定 索引標籤](media/data-flow/source1.png "來源設定索 引標籤")

#### <a name="optimize"></a>最佳化

[_優化_] 索引標籤包含用於設定資料分割配置的設定。

![優化](media/data-flow/optimize1.png "優化")

預設設定為 [使用目前的資料分割]，這會指示 Azure Data Factory 使用在 Spark 上執行之資料流程的原生資料分割配置。 在大部分的情況下，這項設定是建議的方法。

在某些情況下，您可能會想要調整資料分割。 例如，如果您想要將轉換輸出到 lake 中的單一檔案，請在接收轉換中選擇 [單一分割區]。

另一個您可能想要控制資料分割配置的情況，就是將效能優化。 調整資料分割可讓您控制跨計算節點和資料位置的優化，對整體資料流程效能有正面和負面影響。 如需詳細資訊，請參閱[資料流程效能指南](concepts-data-flow-performance.md)。

若要變更任何轉換的資料分割，請按一下 [優化] 索引標籤，然後選取 [設定分割] 選項按鈕。 您接著會看到一系列的資料分割選項。 分割的最佳方法會根據您的資料磁片區、候選索引鍵、null 值和基數而有所不同。 最佳做法是從預設分割開始，然後嘗試不同的資料分割選項。 您可以從 [監視] 視圖，在每個轉換群組中使用管線的「執行時間」和「資料分割使用」來進行測試。 如需詳細資訊，請參閱[監視資料流程](concepts-data-flow-monitoring.md)。

以下是可用的資料分割選項。

##### <a name="round-robin"></a>循環配置資源 

[循環配置資源] 是會自動將資料平均散發到所有分割區上的簡易資料分割。 當您沒有良好的金鑰候選項目來實行穩固的智慧型資料分割策略時，請使用迴圈配置資源。 您可以設定實體分割區的數目。

##### <a name="hash"></a>雜湊

Azure Data Factory 會產生資料行雜湊以產生統一的分割區，使具有相似值的資料列會落在相同的分割區中。 使用 [雜湊] 選項時，請測試潛在的分割扭曲。 您可以設定實體分割區的數目。

##### <a name="dynamic-range"></a>動態範圍

[動態範圍] 會使用 Spark 動態範圍，其會以您提供的資料行或運算式為基礎。 您可以設定實體分割區的數目。 

##### <a name="fixed-range"></a>固定範圍

建立運算式，為分割資料行內的值提供固定範圍。 在使用此選項來避免分割區扭曲之前，您應該先充分瞭解您的資料。 您為運算式輸入的值將會用來做為資料分割函數的一部分。 您可以設定實體分割區的數目。

##### <a name="key"></a>Key

如果您已充分了解自己資料的多重性，索引鍵資料分割可能會是個良好的分割策略。 索引鍵資料分割將會針對資料行中的每個唯一值建立分割區。 您無法設定分割區數目，因為此數目會以資料中的唯一值為基礎。

#### <a name="inspect"></a>檢驗

[_檢查_] 索引標籤可讓您查看要轉換之資料流程的中繼資料。 您可以看到資料行計數、變更的資料行、加入的資料行、資料類型、資料行順序和資料行參考。 [檢查] 是您的中繼資料的唯讀視圖。 您不需要啟用 [偵測] 模式，即可在 [檢查] 窗格中查看中繼資料。

![檢查](media/data-flow/inspect1.png "檢查")

當您透過轉換來變更資料的圖形時，您會看到中繼資料變更流經 [檢查] 窗格。 如果您的來源轉換中沒有已定義的架構，則中繼資料將不會顯示在 [檢查] 窗格中。 結構描述漂移案例經常會發生缺乏中繼資料的情況。

#### <a name="data-preview"></a>資料預覽

如果開啟 [偵錯工具] 模式，[_資料預覽_] 索引標籤會提供您每個轉換之資料的互動式快照集。 如需詳細資訊，請參閱[在偵錯工具模式中的資料預覽](concepts-data-flow-debug-mode.md#data-preview)。

### <a name="top-bar"></a>頂端列

頂端列包含會影響整個資料流程的動作，例如儲存和驗證。 您也可以使用 [**顯示圖形**] 和 [**隱藏圖形]** 按鈕，在圖表和設定模式之間切換。

![隱藏圖形](media/data-flow/hideg.png "隱藏圖形")

如果您隱藏圖形，可以透過 [**上一個** **] 和 [下一步]** 按鈕，橫向流覽轉換節點。

![流覽](media/data-flow/showhide.png "流覽")

## <a name="next-steps"></a>後續步驟

* 瞭解如何建立[來源轉換](data-flow-source.md)
* 瞭解如何以[Debug 模式](concepts-data-flow-debug-mode.md)建立資料流程
