---
title: 在 Azure 時間序列深入解析預覽總管中將資料視覺化 | Microsoft Docs
description: 本文描述 Azure 時間序列深入解析預覽總管 Web 應用程式中的各種功能和可用選項。
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/03/2019
ms.custom: seodec18
ms.openlocfilehash: 862465a7611f1a2bc65dbb0c49c4de512bd239de
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442095"
---
# <a name="visualize-data-in-the-explorer-preview"></a>在總管中將資料視覺化 (預覽)

本文件說明 Azure 時間序列深入解析預覽的介面的 UI/UX 功能[示範 web 應用程式](https://insights.timeseries.azure.com/preview/demo)。 具體來說，它還會討論的配置裝載的範例，介面的自訂選項，以及瀏覽提供示範。

## <a name="prerequisites"></a>必要條件

若要開始使用 Azure 時間序列深入解析預覽版總管 中，您必須：

* 建立時間序列深入解析環境。 若要深入了解佈建執行個體請嘗試我們[Azure 時間序列深入解析預覽版](./time-series-insights-update-create-environment.md)教學課程。
* [提供資料存取](./time-series-insights-data-access.md)加入時間序列深入解析環境中，您建立帳戶。 您可以將存取權提供給其他人和您自己。
* 將事件來源新增至 Time Series Insights 環境，以將資料推送到環境中：
  * 了解[如何連接到事件中樞](./time-series-insights-how-to-add-an-event-source-eventhub.md)
  * 了解[如何連接到 IoT 中樞](./time-series-insights-how-to-add-an-event-source-iothub.md)

## <a name="learn-about-the-preview-explorer"></a>了解預覽總管

Azure 時間序列深入解析預覽總管包含下列元素：

[![[總管] 檢視](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

1. <a href="#environment-dropdown">**環境面板**</a>:顯示您的 Azure TSI 環境。
1. <a href="#navigation-menu">**瀏覽功能表**</a>:可讓您切換**分析**並**模型**頁面。
1. <a href="#hierarchy-tree">**階層樹狀結構**</a>:可讓您選取特定的模型和資料元素，若要繪製成圖表。
1. <a href="#preview-well">**時間序列也**</a>:具有色彩編碼的表格格式顯示您目前選取之資料的項目。
1. <a href="#preview-chart">**圖表窗格**</a>:可顯示您目前處理的圖表。
1. <a href="#time-editor-panel">**時間軸**</a>:可讓您修改自己的工作時間範圍。
1. <a href="#navigation-panel">**應用程式列**</a>:可包含您的使用者管理選項 (例如目前的租用戶)，並可讓您變更佈景主題和語言設定。

## <a name="environment-dropdown"></a>環境下拉式清單

[環境] 下拉式清單會顯示您具有存取權的所有時間序列深入解析環境。 此清單包括隨用隨付的環境 （預覽） 和 S1/S2 環境 （「 正式 」 或 「 GA 」）。 

1. 只要按一下下拉箭號旁顯示的環境：

   [![在控制台中](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. 然後，選取所需的環境。

## <a name="navigation-menu"></a>巡覽功能表

  [![在導覽功能表](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

在導覽功能表可讓您選取兩個檢視之間：

* **分析**：可讓您在已建立模型或未建立模型的時間序列資料上製作圖表或執行豐富的分析。
* **模型**：可讓您將新的時間序列深入解析預覽類型、階層和執行個體推送至您的時間序列深入解析模型。

## <a name="hierarchy-tree"></a>階層樹狀結構

階層樹狀結構會顯示選取的資料元素，包括模型、 特定的裝置，以及在您的裝置上的感應器。

### <a name="model-search-panel"></a>模型 [搜尋] 面板

模型搜尋面板可讓您輕鬆地搜尋及瀏覽您的時間序列模型階層，尋找想要在圖表上顯示的特定時間序列執行個體。 當您選取執行個體時，它們會同步新增至目前的圖表和資料井中。

  [![模型的 [搜尋] 面板](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>撰寫模型

Azure 時間序列深入解析預覽版支援您的時間序列模型上的完整建立、 讀取、 更新和刪除 (CRUD) 作業。  

* **時間序列模型類型**：時間序列深入解析類型可讓您定義變數或公式，以執行計算。 它們會與指定的時間序列深入解析執行個體相關聯。 一個類型可以有一或多個變數。
* **時間序列模型階層**：階層是資料的系統化組織。 階層會描述時間序列深入解析資料中不同實體之間的關係。
* **時間序列模型執行個體**：執行個體就是時間序列本身。 在大部分情況下，它們是**DeviceID**或是**AssetID**，這是在環境中的資產的唯一識別碼。

若要深入了解時間序列模型，請參閱[時間序列模型](./time-series-insights-update-tsm.md)。

## <a name="preview-well"></a>也預覽

也會顯示執行個體欄位和其他選取的 TSI 執行個體相關聯的中繼資料。 在右邊的核取方塊可讓您隱藏或顯示從目前圖表的特定執行個體。 您也可能會移除從您目前的資料特定的資料元素，也是藉由按一下紅色**刪除**（垃圾筒） 中之項目的左下方的控制項。

  [![和預覽](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

您也可以重新設定的版面配置您**分析**圖表頁面上，在右上角選取省略符號圖示：

  [![遙測的版面配置選項](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> 如果您看到以下訊息，表示執行個體在選取的時間範圍內沒有任何資料。 若要解決此問題，可以加大時間範圍，或確認執行個體正在推送資料。
>
> ![沒有資料的通知](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>預覽圖表

圖表，您可以顯示為線條的 TSI 執行個體。 您可以按一下 Web 控制項將圖表加大，以摺疊環境面板、資料模型和時間範圍控制面板。

  [![預覽圖表概觀](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

1. **選取的日期範圍**：可控制哪些資料元素可用於視覺效果。

1. **內部日期範圍滑桿工具**：使用兩個端點控制項，並透過拖曳它們來設定所需的日期範圍。

1. **時間範圍摺疊控制項**：可摺疊及展開時間範圍面板編輯器。

1. **Y 軸格式控制項**：可循環瀏覽可用的 Y 軸檢視選項：

    * `Default`:每個線條都有一個獨立的 Y 軸。
    * `Stacked`:可讓您在同一 Y 軸上堆疊多行線條，並依據選取的線條變更 Y 軸資料。
    * `Shared`:同時顯示所有 Y 軸資料。

1. **目前資料元素**：目前選取的資料元素與其相關資訊。

您可以在目前圖形上的某個資料點按一下滑鼠左鍵，然後將選取的區域拖曳到您選擇的端點，就能進一步向內切入到特定資料配量。 灰色、 選取區域中，按一下滑鼠右鍵，然後按一下 **縮放**此如下圖所示：

  [![預覽圖表縮放](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

當您執行之後**縮放**動作，您會看到您所選取的資料集。 按一下 Y 軸格式控制項，以循環瀏覽您時間序列深入解析資料的三個 Y 軸表示法。

  [![預覽圖表 y 軸](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

這裡提供一個共用 Y 軸的範例：

  [![預覽共用的 y 軸](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>時間 [編輯器] 面板

使用時間序列深入解析預覽時，必須先選取一個時間範圍。 選取的時間範圍會控制資料集，而這些資料集可用於和時間序列深入解析預覽小工具搭配操作。 時間序列深入解析預覽提供下列 Web 控制項，用於選取您的工作時間範圍。

  [![時間選取面板](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **內部日期範圍滑桿工具**：使用兩個端點控制項，並透過拖曳它們來設定所需的日期範圍。 此內部日期範圍會受到外部日期範圍滑桿控制項限制。

1. **加大及縮小日期範圍按鈕**：可選取兩個按鈕的任一個來加大或縮小時間範圍，設定所需的間隔。

1. **時間範圍摺疊控制項**：這個 Web 控制項可讓您隱藏內部日期範圍滑桿工具以外的所有控制項。

1. **外部日期範圍滑桿控制項**：使用此端點控制項選取外部日期範圍，這將可供內部日期範圍控制項使用。

1. **快速時間日期範圍下拉式**:可讓您快速切換預設的時間範圍選取項目，例如上次**30 分鐘**，則**過去 12 小時**，或有**自訂範圍**。 變更此值時，也會變更間隔大小滑桿工具中所討論的可用間隔範圍。

1. **間隔大小滑桿工具**：可讓您透過相同的時間範圍縮放間隔。 這個動作可在大型時間配量之間提供更精確的移動控制。 它會顯示向下配量最小至毫秒的平滑趨勢，讓您得以查看細微、高解析度的資料剪輯。 滑桿的預設起始點會從您所選取項目中設定為資料的最佳檢視；平衡解析度、查詢速度與細微性。

1. **日期範圍結束與開始日期 Web 控制項**：使用此 web 控制項，您可以輕鬆地按一下，然後選取您想要的日期和時間範圍。 您也可以使用控制項切換不同的時區。 進行變更之後，若要套用至您目前的工作區，請選取 [儲存]。

   [![與選取面板](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>瀏覽窗格

時間序列深入解析預覽瀏覽窗格中會出現在 TSI 應用程式頂端。 它提供下列功能。

### <a name="current-session-share-link-control"></a>目前工作階段共用連結控制項

  [![共用圖示](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

選取新**共用**圖示，以與小組共用的 URL 連結。

  [![共用您的執行個體 URL](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>租用戶區段

  [![租用戶選取項目](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* 可顯示您目前的時間序列深入解析登入帳戶資訊。
* 可讓您在可用的時間序列深入解析佈景主題之間切換。
* 可讓您檢視預覽版[示範 web 應用程式](https://insights.timeseries.azure.com/preview/demo)。

### <a name="theme-selection"></a>佈景主題選取項目

若要選取新的佈景主題，請按一下您右上角的設定檔圖示。 然後，選取**變更佈景主題**。

  [![佈景主題選取範圍](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> 語言選擇，也可以透過按一下設定檔圖示。

Azure 時間序列深入解析預覽支援兩種佈景主題：

* **淺色主題**：本文件中顯示的全部都是預設佈景主題。
* **深色主題**：可轉變總管，如下所示：

  [![選取 暗色調佈景主題](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>S1/S2 環境控制項

### <a name="preview-terms-panel"></a>預覽條款面板

此區段僅適用於嘗試在更新後的 UI 中使用總管的現有 S1/S2 環境。 您可能希望將 GA 產品和預覽結合使用。 我們已透過現有的 UI 對更新後的總管新增了一些功能，但您可以在現有的時間序列深入解析總管中，體驗 S1/S2 環境的完整 UI。  

您會看到時間序列深入解析字詞面板取代了階層，您可以在其中定義環境中的查詢。 它可以讓您根據述詞篩選資料。

  [![Where 查詢面板](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

時間序列深入解析預覽字詞編輯器使用以下參數：

**Where**：Where 子句可讓您使用下表所列的運算元集合快速篩選事件。 如果透過選取運算元進行搜尋，述詞會依據該搜尋自動更新。 支援的運算元類型包括：

| 作業 | 支援的類型   | 注意 |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double、DateTime、TimeSpan | |
| `=`, `!=`, `<>` | String、Bool、Double、DateTime、TimeSpan、NULL |
| `IN` | String、Bool、Double、DateTime、TimeSpan、NULL | 所有的運算元都應該是相同的類型或是 NULL 常數。 |
| `HAS` | 字串 | 右側只允許常數字串常值。 不允許空字串和 NULL。 |

請閱讀[時間序列運算式 (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx)，以深入了解支援的查詢作業和資料類型。

### <a name="examples-of-where-clauses"></a>Where 子句範例

  [![其中子句範例](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**量值**：顯示所有數值資料行下拉式清單 (**雙精度浮點數**) 可能會在您目前的圖表做為項目使用。

**分割依據**：此下拉式清單會顯示模型中所有可用的類別資料行 (字串)，您可以依據這些資料行將資料分組。 您可以新增最多五個要在相同 x 軸上檢視的字詞。 輸入您想要的參數，然後選取 [新增] 加入新字詞。

  [![查詢和篩選的檢視，其中一個](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

您可以透過選取如下圖所示的可見圖示，來顯示及隱藏圖表面板中的元素。 您可以按一下紅色，以完全移除查詢**X**。

  [![查詢和已篩選檢視兩個](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>後續步驟

- 深入了解[儲存體與輸入](./time-series-insights-update-storage-ingress.md)預覽版 Azure 時間序列深入解析。

- 讀取時間序列深入解析預覽文件上[資料模型化](./time-series-insights-update-tsm.md)。

- 了解[如何診斷和疑難排解](./time-series-insights-update-how-to-troubleshoot.md)您的 Time Series Insights 執行個體。
