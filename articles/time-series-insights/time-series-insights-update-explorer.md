---
title: 在 Azure 時間序列深入解析預覽總管中將資料視覺化 | Microsoft Docs
description: 本文描述 Azure 時間序列深入解析預覽總管 Web 應用程式中的各種功能和可用選項。
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/03/2019
ms.custom: seodec18
ms.openlocfilehash: c4f3053063ce33d2777387da2c53effd61b05f1a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399866"
---
# <a name="visualize-data-in-the-explorer-preview"></a>在總管中將資料視覺化 (預覽)

本文件說明的使用者介面和使用者體驗功能和介面 Azure 時間序列深入解析預覽版[示範 web 應用程式](https://insights.timeseries.azure.com/preview/demo)。 具體來說，它還會討論的配置裝載的範例，介面的自訂選項，以及瀏覽提供示範。

## <a name="prerequisites"></a>必要條件

若要開始使用 Azure 時間序列深入解析預覽版總管 中，您必須：

* 建立時間序列深入解析環境。 若要深入了解佈建執行個體，請嘗試[Azure 時間序列深入解析預覽版](./time-series-insights-update-create-environment.md)教學課程。
* [提供資料存取](./time-series-insights-data-access.md)加入時間序列深入解析環境中，您建立帳戶。 您可以將存取權提供給其他人和您自己。
* 將事件來源新增至 Time Series Insights 環境，以將資料推送到環境中：
  * 了解[如何連接到事件中樞](./time-series-insights-how-to-add-an-event-source-eventhub.md)。
  * 了解[如何連接到 IoT 中樞](./time-series-insights-how-to-add-an-event-source-iothub.md)。

## <a name="learn-about-the-preview-explorer"></a>了解預覽總管

Azure 時間序列深入解析預覽總管包含下列元素：

[![[總管] 檢視](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

- <a href="#environment-drop-down-list">環境面板</a>:顯示您的 Azure Time Series Insights 環境。
- <a href="#navigation-menu">瀏覽功能表</a>:用來切換**分析**並**模型**頁面。
- <a href="#hierarchy-tree">階層樹狀結構</a>：您可以使用它來選取特定模型和資料的項目要繪製成圖表。
- <a href="#preview-well">時間序列井</a>：具有色彩編碼的表格格式顯示您目前選取之資料的項目。
- <a href="#preview-chart">圖表面板</a>：可顯示您目前處理的圖表。
- <a href="#time-editor-panel">時間軸</a>：您可以使用它來修改您的工作時間範圍。
- <a href="#navigation-panel">應用程式列</a>：包含您的使用者管理選項，例如目前的租用戶。 您可以用它來變更佈景主題和語言設定。

## <a name="environment-drop-down-list"></a>環境下拉式清單

[環境] 下拉式清單會顯示您具有存取權的所有時間序列深入解析環境。 此清單包括隨用隨付的環境，例如時間序列深入解析預覽。 此清單也包含 S1/S2 環境中，也就是正式推出。

1. 選取下拉式清單旁的箭號顯示的環境。

   [![在控制台中](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. 然後，選取您想要的環境。

## <a name="navigation-menu"></a>巡覽功能表

  [![在導覽功能表](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

使用 [瀏覽] 功能表來選取兩個檢視之間：

* **分析**：您可以使用它來執行您的模型或不支援模型的時間序列資料的豐富的分析和圖表。
* **模型**：您可以使用它來將新的時間序列深入解析預覽類型、 階層和執行個體推送至您的 Time Series Insights 模型。

## <a name="hierarchy-tree"></a>階層樹狀結構

階層樹狀結構會顯示選取的資料元素，包括模型、 特定的裝置，以及在您的裝置上的感應器。

### <a name="model-search-panel"></a>模型 [搜尋] 面板

您可以使用模型的 [搜尋] 面板，輕鬆地搜尋及瀏覽您的時間序列模型階層架構，以尋找您想要顯示在圖表上的特定時間系列執行個體。 選取您的執行個體之後，它們是也新增至目前的圖表和資料。

  [![模型的 [搜尋] 面板](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>撰寫模型

完整的 Azure 時間序列深入解析預覽版支援建立、 讀取、 更新和刪除 (CRUD) 作業，您的時間序列模型上。

* **時間序列模型類型**：您可以使用時間序列深入解析類型來定義變數或公式進行計算。 它們與指定的 Time Series Insights 執行個體相關聯。 一個類型可以有一或多個變數。
* **時間序列模型階層**：階層是資料的系統化組織。 階層會描述時間序列深入解析資料中不同實體之間的關係。
* **時間序列模型執行個體**：執行個體就是時間序列本身。 在大部分情況下，它們**DeviceID**或是**AssetID**，這是在環境中的資產的唯一識別碼。

若要深入了解時間序列模型，請參閱[時間序列模型](./time-series-insights-update-tsm.md)。

## <a name="preview-well"></a>也預覽

也會顯示執行個體欄位和其他選取的 Time Series Insights 執行個體相關聯的中繼資料。 選取右邊的核取方塊，您可以隱藏或顯示從目前圖表的特定執行個體。 您也可以移除從您目前的資料特定的資料元素，也是藉由選取紅色**刪除**（垃圾筒） 上之項目的左下方的控制項。

  [![和預覽](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

若要重新設定的版面配置您**分析**圖表頁面、 在右上角選取省略符號圖示：

  [![遙測的版面配置選項](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> 如果您看到下列訊息，執行個體沒有任何資料在選取時間範圍內。 若要解決此問題，增加的時間範圍，或確認執行個體正在推送的資料。
>
> ![沒有資料的通知](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>預覽圖表

圖表，您可以顯示為線條的 Time Series Insights 執行個體。 您可以藉由選取，讓圖表更大的 web 控制項來摺疊的環境面板、 資料模型和時間範圍的控制台中。

  [![預覽圖表概觀](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

- **選取日期範圍**:可控制哪些資料元素可用於視覺效果。

- **內部的日期範圍滑桿工具**:使用兩個端點控制項拖放您想要將時間範圍內。

- **時間範圍摺疊控制項**：可摺疊及展開時間範圍面板編輯器。

- **Y 軸格式控制項**：循環使用的 y 軸檢視選項：

    * `Default`:每一行都有個別的 y 軸。
    * `Stacked`:您可以使用它來堆疊多行上相同的 y 軸，y 軸的資料，變更根據選取的行。
    * `Shared`:所有的 y 軸資料一起顯示。

- **目前資料元素**：目前選取的資料元素與其相關資訊。

若要向下鑽研進一步到特定的資料配量，以滑鼠左鍵按一下 在目前的圖形上的資料點，並再將選取的區域拖曳到您選擇的端點。 選取灰色的區域，以滑鼠右鍵按一下並選取**縮放**此如下圖所示：

  [![預覽圖表縮放](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

當您執行之後**縮放**動作，您會看到您選取的資料集。 選取 y 軸的格式控制項，以循環瀏覽您的 Time Series Insights 資料的三個 y 軸表示。

  [![預覽圖表 y 軸](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

在這裡，您可以看到共用的 Y 軸的範例：

  [![預覽共用的 Y 軸](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>時間 [編輯器] 面板

使用時間序列深入解析預覽時，必須先選取一個時間範圍。 所選的時間範圍會控制資料集可供使用的時間序列深入解析預覽 widget 的操作。 下列的 web 控制項可用於時間序列深入解析預覽來選取您的工作時間範圍：

  [![時間選取面板](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **內部的日期範圍滑桿工具**:使用兩個端點控制項拖放您想要將時間範圍內。 此內部日期範圍會受到外部的日期範圍滑桿控制項。

1. **加大及縮小日期範圍按鈕**：可選取兩個按鈕的任一個來加大或縮小時間範圍，設定所需的間隔。

1. **時間範圍摺疊控制項**：這個 web 控制項可讓您隱藏內部日期範圍滑桿工具除外的所有控制項。

1. **外部的日期範圍滑桿控制項**:使用的端點控制項以選取外部的日期範圍，這將可供您的內部日期範圍控制項。

1. **快速時間日期範圍下拉式清單**:使用預設的時間範圍選取項目，例如上次之間快速切換**30 分鐘**，則**過去 12 小時**，或有**自訂範圍**。 變更此值時，也會變更間隔大小滑桿工具中所討論的可用間隔範圍。

1. **間隔大小滑桿工具**：您可以使用它來透過相同的時間範圍縮放間隔。 這個動作可在大型時間配量之間提供更精確的移動控制。 它會顯示向下配量越小越毫秒的平滑趨勢。 您可以使用它以查看更細微、 高解析度的剪下，您的資料。 滑桿的預設起始點會從您所選取項目中設定為資料的最佳檢視；平衡解析度、查詢速度與細微性。

1. **日期範圍來-和-從 web 控制項**:使用此 web 控制項，您可以輕鬆地選取您想要的日期和時間範圍。 您也可以使用控制項切換不同的時區。 要套用至目前工作區的變更之後，選取**儲存**。

   [![與選取面板](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>瀏覽窗格

時間序列深入解析預覽瀏覽窗格中會出現在您的 Time Series Insights 應用程式頂端。 它提供下列功能。

### <a name="current-session-share-link-control"></a>目前工作階段共用連結控制項

  [![共用圖示](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

選取新**共用**圖示，以與小組共用的 URL 連結。

  [![共用您的執行個體 URL](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>租用戶區段

  [![租用戶選取項目](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* 會顯示您目前的時間序列深入解析登入的帳戶資訊。
* 您可以使用它來提供時間序列深入解析佈景主題之間切換。
* 使用它來瀏覽預覽[示範 web 應用程式](https://insights.timeseries.azure.com/preview/demo)。

### <a name="theme-selection"></a>佈景主題選取項目

若要選取新的佈景主題，請選取您位於右上角的設定檔圖示。 然後，選取**變更佈景主題**。

  [![佈景主題選取範圍](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> 語言選擇，也可以透過選取設定檔圖示。

Azure 時間序列深入解析預覽支援兩種佈景主題：

* **淺色佈景主題**:本文件中顯示的全部都是預設佈景主題。
* **深色主題**：可轉變總管，如下所示：

  [![選取 暗色調佈景主題](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>S1/S2 環境控制項

### <a name="preview-terms-panel"></a>預覽條款面板

此區段僅適用於嘗試在更新後的 UI 中使用總管的現有 S1/S2 環境。 您可能想要用於組合的預覽與正式推出的產品。 我們已透過現有的 UI 對更新後的總管新增了一些功能，但您可以在現有的時間序列深入解析總管中，體驗 S1/S2 環境的完整 UI。 

而不是階層中，您會看到 Time Series Insights 詞彙面板中，您在其中定義查詢您的環境中。 您可以使用它來篩選您根據述詞的資料。

  [![Where 查詢面板](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

時間序列深入解析預覽字詞編輯器使用以下參數：

**Where**：使用 where 子句來快速篩選事件，所使用的運算元集如下表所示。 如果透過選取運算元進行搜尋，述詞會依據該搜尋自動更新。 支援的運算元類型包括：

| 作業 | 支援的類型   | 注意 |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double、DateTime、TimeSpan | |
| `=`, `!=`, `<>` | String、Bool、Double、DateTime、TimeSpan、NULL |
| `IN` | String、Bool、Double、DateTime、TimeSpan、NULL | 所有的運算元都應該是相同的類型或是 NULL 常數。 |
| `HAS` | 字串 | 只有常數字串常值可以在右邊。 不允許空字串和 NULL。 |

若要深入了解支援的查詢作業和資料類型，請參閱[時間序列運算式 (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx)。

### <a name="examples-of-where-clauses"></a>範例的 where 子句

  [![其中子句範例](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**量值**：顯示所有數值資料行的下拉式清單 (**雙精度浮點數**) 您可以使用做為項目，您目前的圖表。

**分割依據**：此下拉式清單會顯示所有可用類別的資料行 （字串），您可以分組的資料在模型中。 您可以新增最多五個詞彙以檢視相同的 x 軸上。 輸入的參數，然後選取**新增**來新增新的字詞。

  [![查詢和篩選的檢視，其中一個](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

您可以顯示和隱藏項目在圖表窗格中選取 [可見] 圖示，如下圖所示。 若要完全移除查詢，請選取 紅色**X**。

  [![查詢和已篩選檢視兩個](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>後續步驟

- 深入了解[儲存體與輸入](./time-series-insights-update-storage-ingress.md)預覽版 Azure 時間序列深入解析。
- 讀取時間序列深入解析預覽文件上[資料模型化](./time-series-insights-update-tsm.md)。
- 了解[如何診斷和疑難排解](./time-series-insights-update-how-to-troubleshoot.md)您的 Time Series Insights 執行個體。
