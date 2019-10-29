---
title: 在 Azure 時間序列深入解析預覽總管中將資料視覺化 | Microsoft Docs
description: 本文描述 Azure 時間序列深入解析預覽總管 Web 應用程式中的各種功能和可用選項。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/07/2019
ms.custom: seodec18
ms.openlocfilehash: 3f6c03ace13b9b6cb1bda004799502e1f382051d
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989961"
---
# <a name="visualize-data-in-the-explorer-preview"></a>在總管中將資料視覺化 (預覽)

本檔說明 Azure 時間序列深入解析 Preview[示範 web 應用程式](https://insights.timeseries.azure.com/preview/demo)的使用者介面和使用者體驗功能和介面。 具體而言，它會討論託管範例的配置、介面自訂選項，以及透過提供的示範進行導覽。

## <a name="prerequisites"></a>必要條件

若要開始使用 Azure 時間序列深入解析 Preview explorer，您必須：

* 建立時間序列深入解析環境。 若要深入瞭解如何布建實例，請嘗試[Azure 時間序列深入解析 Preview](./time-series-insights-update-create-environment.md)教學課程。
* 提供您為帳戶所建立時間序列深入解析環境的[資料存取權](./time-series-insights-data-access.md)。 您可以將存取權提供給其他人和您自己。
* 將事件來源新增至時間序列深入解析環境，以將資料推送至環境：
  * 瞭解[如何連接到事件中樞](./time-series-insights-how-to-add-an-event-source-eventhub.md)。
  * 瞭解[如何連接到 IoT 中樞](./time-series-insights-how-to-add-an-event-source-iothub.md)。

## <a name="learn-about-the-preview-explorer"></a>瞭解預覽瀏覽器

Azure 時間序列深入解析預覽總管包含下列元素：

[![Explorer 視圖](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

- <a href="#environment-drop-down-list">環境面板</a>：顯示您的 Azure 時間序列深入解析環境。
- <a href="#navigation-menu">導覽功能表</a>：用來在 [**分析**] 和 [**模型**] 頁面之間切換。
- 階層<a href="#hierarchy-tree">樹狀結構</a>：用來選取要繪製成圖表的特定模型和資料元素。
- <a href="#preview-well">時間序列良好</a>：以色彩編碼顯示您目前選取的資料表格式資料元素。
- <a href="#preview-chart">圖表面板</a>：顯示您目前的工作圖表。
- <a href="#time-editor-panel">時間軸</a>：用來修改您的工作時間範圍。
- <a href="#navigation-panel">應用程式行</a>：包含您的使用者管理選項，例如目前的租使用者。 您可以使用它來變更主題和語言設定。

## <a name="environment-drop-down-list"></a>[環境] 下拉式清單

[環境] 下拉式清單會顯示您有權存取的所有時間序列深入解析環境。 此清單包括隨用隨付環境，例如時間序列深入解析預覽。 此清單也包含已正式運作的 S1/S2 環境。

1. 選取顯示的環境旁邊的下拉箭號。

   [![控制台](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. 然後，選取您想要的環境。

## <a name="navigation-menu"></a>巡覽功能表

  [![導覽功能表](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

使用導覽功能表，在兩個不同的視圖之間進行選取：

* **分析**：用來繪製模型化或未模型化時間序列資料，並對其執行豐富的分析。
* **Model**：用來將新的時間序列深入解析預覽類型、階層和實例推送至您的時間序列深入解析模型。

## <a name="hierarchy-tree"></a>階層樹狀結構

階層樹狀結構會顯示選取的資料元素，其中包含您裝置上的模型、特定裝置和感應器。

### <a name="model-search-panel"></a>模型搜尋面板

您可以使用 [模型搜尋] 面板，輕鬆地搜尋及流覽您的時間序列模型階層，以尋找您想要在圖表上顯示的特定時間序列實例。 當您選取實例之後，它們就會加入至目前的圖表和資料。

  [![模型搜尋面板](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>模型撰寫

Azure 時間序列深入解析 Preview 支援在您的時間序列模型上進行完整的建立、讀取、更新和刪除（CRUD）作業。

* **時間序列模型類型**：您可以使用時間序列深入解析類型來定義用於執行計算的變數或公式。 它們會與指定的時間序列深入解析實例相關聯。 一個類型可以有一或多個變數。
* **時間序列模型**階層：階層是您的資料的系統化組織。 階層會描述時間序列深入解析資料中不同實體之間的關係。
* **時間序列模型實例**：實例是時間序列本身。 在大部分情況下，它們是**DeviceID**或**AssetID**，也就是環境中資產的唯一識別碼。

若要深入了解時間序列模型，請參閱[時間序列模型](./time-series-insights-update-tsm.md)。

## <a name="preview-well"></a>預覽良好

這會顯示實例欄位，以及與所選時間序列深入解析實例相關聯的其他中繼資料。 藉由選取右側的核取方塊，您可以隱藏或顯示目前圖表中的特定實例。 您也可以選取專案左側的紅色 [**刪除**] （垃圾桶）控制項，從目前的資料中移除特定的資料元素。

  [![預覽的效果](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

若要重新設定 [**分析**圖表] 頁面的版面配置，請選取右上角的省略號圖示：

  [![遙測版面配置選項](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> 如果您看到下列訊息，則在選取的時間範圍內，實例沒有任何資料。 若要解決此問題，請增加時間範圍，或確認實例正在推送資料。
>
> ![沒有資料通知](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>預覽圖表

在圖表中，您可以將時間序列深入解析實例顯示為線條。 您可以選取 web 控制項，讓圖表變大，以折迭 [環境] 面板、[資料模型] 和 [時間範圍] 控制台。

  [![預覽圖表總覽](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

- **選取的日期範圍**：控制哪些資料元素可供視覺效果使用。

- **內部日期範圍滑杆工具**：您可以在所需的時間範圍內拖曳兩個端點控制項，以使用此專案。

- **時間範圍**折迭控制項：折迭和展開時間範圍面板編輯器。

- **Y 軸格式控制項**：迴圈顯示可用的 Y 軸視圖選項：

    * `Default`：每一行都有一個個別的 y 軸。
    * `Stacked`：用來在相同的 y 軸上堆疊多行，並根據選取的線條變更 y 軸資料。
    * `Shared`：顯示在一起的所有 y 軸資料。

- 目前的**資料元素**：目前選取的資料元素及其相關聯的詳細資料。

若要進一步切入到特定的資料配量，請以滑鼠左鍵按一下目前圖表上的資料點，然後將選取的區域拖曳至您選擇的端點。 以滑鼠右鍵按一下選取的灰色區域，然後選取 [**縮放**]，如下圖所示：

  [![預覽圖表縮放比例](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

執行 [**縮放**] 動作之後，您會看到選取的資料集。 選取 y 軸格式控制項，即可迴圈顯示時間序列深入解析資料的三個 y 軸標記法。

  [![預覽圖表 y 軸](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

在這裡，您可以看到共用 Y 軸的範例：

  [![預覽共用 Y 軸](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>時間編輯器面板

使用時間序列深入解析預覽時，必須先選取一個時間範圍。 選取的時間範圍會控制可使用時間序列深入解析預覽 widget 來操作的資料集。 時間序列深入解析 Preview 提供下列 web 控制項來選取您的工作時間範圍：

  [![時間選取面板](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **內部日期範圍滑杆工具**：您可以在所需的時間範圍內拖曳兩個端點控制項，以使用此專案。 這個內部日期範圍受限於外部日期範圍滑杆控制項。

1. **增加和減少日期範圍按鈕**：在您想要的間隔中選取任一按鈕，以增加或減少時間範圍。

1. **時間範圍**折迭控制項：這個 web 控制項可讓您隱藏內部日期範圍滑杆工具以外的所有控制項。

1. **外部日期範圍滑杆控制項**：使用端點控制項選取外部日期範圍，這將可供您的內部日期範圍控制項使用。

1. **快速次數日期範圍下拉式清單**：用來在預設的時間範圍選項之間快速切換，例如過去**30 分鐘**、**過去12小時**或**自訂範圍**。 變更此值時，也會變更間隔大小滑桿工具中所討論的可用間隔範圍。

1. **間隔大小滑杆工具**：用來放大和縮小相同時間範圍內的間隔。 這個動作可在大型時間配量之間提供更精確的移動控制。 它會將平滑的趨勢顯示為小至毫秒的磁區。 您可以使用它來查看細微、高解析度的資料剪下。 滑桿的預設起始點會從您所選取項目中設定為資料的最佳檢視；平衡解析度、查詢速度與細微性。

1. **Web 控制項的日期範圍**：您可以使用這個 web 控制項，輕鬆地選取您想要的日期和時間範圍。 您也可以使用控制項切換不同的時區。 將變更套用至目前的工作區之後，請選取 [**儲存**]。

   [從選取專案面板![（& a）](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>導覽面板

時間序列深入解析預覽 導覽面板會出現在時間序列深入解析應用程式的頂端。 它提供下列功能：

### <a name="current-session-share-link-control"></a>目前工作階段共用連結控制項

  [![共用圖示](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

選取 [新增**共用**] 圖示，與您的小組共用 URL 連結。

  [![共用您的實例 URL](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>租用戶區段

  [![租使用者選取](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* 顯示目前時間序列深入解析登入帳戶資訊。
* 使用它在可用的時間序列深入解析主題之間切換。
* 使用它來觀看預覽[示範 web 應用程式](https://insights.timeseries.azure.com/preview/demo)。

### <a name="theme-selection"></a>佈景主題選取項目

若要選取新的主題，請選取位於右上角的設定檔圖示。 然後，選取 [**變更主題**]。

  [![主題選取範圍](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> 選取您的設定檔圖示也可以選取 [語言]。

Azure 時間序列深入解析預覽支援兩種佈景主題：

* **淺色主題**：這份檔中顯示的預設主題。
* **深色主題**：呈現 explorer，如下所示：

  [![選取的深色主題](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>S1/S2 環境控制項

### <a name="preview-terms-panel"></a>預覽條款面板

此區段僅適用於嘗試在更新後的 UI 中使用總管的現有 S1/S2 環境。 您可能想要使用正式推出的產品和預覽組合。 我們已透過現有的 UI 對更新後的總管新增了一些功能，但您可以在現有的時間序列深入解析總管中，體驗 S1/S2 環境的完整 UI。 

您會看到 [時間序列深入解析詞彙] 面板，而不是階層，您可以在環境中定義查詢。 使用它根據述詞來篩選資料。

  [![查詢面板](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

時間序列深入解析預覽字詞編輯器使用以下參數：

**Where**：使用 where 子句，藉由使用下表所列的運算元集合來快速篩選事件。 如果透過選取運算元進行搜尋，述詞會依據該搜尋自動更新。 支援的運算元類型包括下列各項：

| 作業 | 支援的類型   | 注意 |
| --- | --- | --- |
| `<`、`>`、`<=`、`>=` | Double、DateTime、TimeSpan | |
| `=`、`!=`, `<>` | String、Bool、Double、DateTime、TimeSpan、NULL |
| `IN` | String、Bool、Double、DateTime、TimeSpan、NULL | 所有的運算元都應該是相同的類型或是 NULL 常數。 |
| `HAS` | String | 右側只允許常數位串常值。 不允許空字串和 Null。 |

若要深入瞭解支援的查詢作業和資料類型，請參閱[時間序列運算式（TSX）](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx)。

### <a name="examples-of-where-clauses"></a>Where 子句範例

  [![Where 子句範例](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**量值**：顯示所有數值資料行（**雙精度**浮點數）的下拉式清單，您可以用來做為目前圖表的元素。

**分割依據**：此下拉式清單會顯示您模型中的所有可用類別資料行（字串），您可以將其分組。 您最多可以在同一個 X 軸上加入五個詞彙。 輸入您想要的參數，然後選取 [**新增**] 以新增新的字詞。

  [![查詢和篩選的視圖一](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

您可以藉由選取可見圖示來顯示和隱藏 [圖表] 面板中的元素，如下圖所示。 若要完全移除查詢，請選取紅色**X**。

  [![查詢和篩選的視圖二](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>後續步驟

- 瞭解 Azure 時間序列深入解析 Preview 中的[儲存體和](./time-series-insights-update-storage-ingress.md)輸入。
- 閱讀有關[資料模型](./time-series-insights-update-tsm.md)化的時間序列深入解析預覽檔。
- 瞭解[如何診斷和疑難排解](./time-series-insights-update-how-to-troubleshoot.md)您的時間序列深入解析實例。
