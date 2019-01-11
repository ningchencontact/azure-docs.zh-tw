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
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: d5c18521f39b1b57b5f94a54bb3131e05abba745
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556640"
---
# <a name="visualize-data-in-the-explorer-preview"></a>在總管中將資料視覺化 (預覽)

本文描述 Azure 時間序列深入解析預覽[總管 Web 應用程式](https://insights.timeseries.azure.com/preview/samples)中的各種功能和可用選項。

## <a name="prerequisites"></a>必要條件

在使用 Azure 時間序列深入解析預覽總管之前，您必須先：

* 建立時間序列深入解析環境。 如需詳細資訊，請參閱[教學課程：Azure 時間序列深入解析預覽](./time-series-insights-update-create-environment.md)。
* 將資料存取權提供給您為帳戶建立的時間序列深入解析環境。 您可以將存取權提供給其他人和您自己。
* 將事件來源新增至 Azure 時間序列深入解析環境，以將資料推送到環境中。

## <a name="learn-about-the-azure-time-series-insights-preview-explorer"></a>了解 Azure 時間序列深入解析預覽總管

  ![explorer-one][1]

Azure 時間序列深入解析預覽總管包含下列元素：

* **導覽列**：可讓您在分析和模型頁面之間切換。
* **階層樹狀結構**：可讓您選取要繪製成圖表的特定資料元素。
* **時間序列井**：可顯示您目前選取的資料元素。
* **圖表面板**：可顯示您目前處理的圖表。
* **時間軸**：可讓您修改自己的工作時間範圍。
* **應用程式列**：可包含您的使用者管理選項 (例如目前的租用戶)，並可讓您變更佈景主題和語言設定。

## <a name="time-series-insights-preview-environment-panel"></a>時間序列深入解析預覽環境面板

環境面板可顯示您擁有存取權的所有時間序列深入解析環境。 此清單包括隨用隨付環境預覽和 S1/S2 環境 (GA)。 只要按一下想要使用的時間序列深入解析環境即可。

  ![explorer-two][2]

## <a name="time-series-insights-preview-navigation-menu"></a>時間序列深入解析預覽導覽功能表

  ![explorer-three][3]

您可以使用導覽功能表，在不同的時間序列深入解析應用程式之間切換：

* **分析**：可讓您在已建立模型或未建立模型的時間序列資料上製作圖表或執行豐富的分析。

* **模型**：可讓您將新的時間序列深入解析預覽類型、階層和執行個體推送至您的時間序列深入解析模型。

## <a name="time-series-insights-preview-model-authoring"></a>時間序列深入解析預覽模型製作

您可以透過這個應用程式，在時間序列模型中執行建立、讀取、更新及刪除 (CRUD) 作業。  

* **時間序列模型類型**：時間序列深入解析類型可讓您定義變數或公式，以執行計算。 它們會與指定的時間序列深入解析執行個體相關聯。 一個類型可以有一或多個變數。
* **時間序列模型階層**：階層是資料的系統化組織。 階層會描述時間序列深入解析資料中不同實體之間的關係。
* **時間序列模型執行個體**：執行個體就是時間序列本身。 在大部分情況下，它們是 DeviceID 或 AssetID，也就是資產在環境中的唯一識別碼。

若要深入了解時間序列模型，請參閱[時間序列模型](./time-series-insights-update-tsm.md)。

## <a name="time-series-insights-preview-model-search-panel"></a>時間序列深入解析預覽模型搜尋面板

模型搜尋面板可讓您輕鬆地搜尋及瀏覽您的時間序列模型階層，尋找想要在圖表上顯示的特定時間序列執行個體。 當您選取執行個體時，它們會同步新增至目前的圖表和資料井中。

  ![explorer-four][4]

## <a name="time-series-insights-preview-well"></a>時間序列深入解析預覽井

「井」會顯示和所選時間序列執行個體相關聯的執行個體欄位和其他中繼資料。 右側的核取方塊可讓您隱藏或顯示目前圖表中的特定執行個體。 您也可以按一下元素右邊的紅色 x 控制項，將特定資料元素從目前的資料井移除。

  ![explorer-five][5]

您也可以快顯遙測面板，以針對資料井中的元素取得更準確的深入檢視。

  ![explorer-six][6]

> [!NOTE]
> 如果您看到以下訊息，表示執行個體在選取的時間範圍內沒有任何資料。 若要解決此問題，可以加大時間範圍，或確認執行個體正在推送資料。
>
> ![explorer-seven][7]

## <a name="time-series-insights-preview-chart"></a>時間序列深入解析預覽圖表

您可以利用圖表以線條方式顯示時間序列執行個體。 您可以按一下 Web 控制項將圖表加大，以摺疊環境面板、資料模型和時間範圍控制面板。

  ![explorer-eight][8]

1. **選取的日期範圍**：可控制哪些資料元素可用於視覺效果。

1. **內部日期範圍滑桿工具**：使用兩個端點控制項，並透過拖曳它們來設定所需的日期範圍。

1. **時間範圍摺疊控制項**：可摺疊及展開時間範圍面板編輯器。

1. **Y 軸格式控制項**：可循環瀏覽可用的 Y 軸檢視選項：

    * `Default`：每個線條都有一個獨立的 Y 軸。
    * `Stacked`：可讓您在同一 Y 軸上堆疊多行線條，並依據選取的線條變更 Y 軸資料。
    * `Shared`：同時顯示所有 Y 軸資料。

1. **目前資料元素**：目前選取的資料元素與其相關資訊。

您可以在目前圖形上的某個資料點按一下滑鼠左鍵，然後將選取的區域拖曳到您選擇的端點，就能進一步向內切入到特定資料配量。 在灰色、選取的區域按一下滑鼠右鍵，然後按一下縮放，如下圖所示：

  ![explorer-nine][9]

執行縮放動作之後，將會看到您選取的資料集。 按一下 Y 軸格式控制項，以循環瀏覽您時間序列深入解析資料的三個 Y 軸表示法。

  ![explorer-ten][10]

這裡提供一個共用 Y 軸的範例：

  ![explorer-eleven][11]

## <a name="time-series-insights-preview-time-editor-panel"></a>時間序列深入解析預覽時間編輯器面板

使用時間序列深入解析預覽時，必須先選取一個時間範圍。 選取的時間範圍會控制資料集，而這些資料集可用於和時間序列深入解析預覽小工具搭配操作。 時間序列深入解析預覽提供下列 Web 控制項，用於選取您的工作時間範圍。

  ![explorer-twelve][12]

1. **內部日期範圍滑桿工具**：使用兩個端點控制項，並透過拖曳它們來設定所需的日期範圍。 此內部日期範圍會受到外部日期範圍滑桿控制項限制。

1. **加大及縮小日期範圍按鈕**：可選取兩個按鈕的任一個來加大或縮小時間範圍，設定所需的間隔。

1. **時間範圍摺疊控制項**：這個 Web 控制項可讓您隱藏內部日期範圍滑桿工具以外的所有控制項。

1. **外部日期範圍滑桿控制項**：使用此端點控制項選取外部日期範圍，這將可供內部日期範圍控制項使用。

1. **快速時間日期範圍下拉式清單**：可讓您快速切換預先設定的時間範圍選取項目，例如過去 30 分鐘、過去 12 小時，或自訂範圍。 變更此值時，也會變更間隔大小滑桿工具中所討論的可用間隔範圍。

1. **間隔大小滑桿工具**：可讓您透過相同的時間範圍縮放間隔。 這個動作可在大型時間配量之間提供更精確的移動控制。 它會顯示向下配量最小至毫秒的平滑趨勢，讓您得以查看細微、高解析度的資料剪輯。 滑桿的預設起始點會從您所選取項目中設定為資料的最佳檢視；平衡解析度、查詢速度與細微性。

1. **日期範圍結束與開始日期 Web 控制項**：您可以運用此 Web 控制項，輕鬆地按一下並選取所需的日期和時間範圍。 您也可以使用控制項切換不同的時區。 進行變更之後，若要套用至您目前的工作區，請選取 [儲存]。

  ![explorer-thirteen][13]

## <a name="time-series-insights-preview-navigation-panel"></a>時間序列深入解析預覽導覽面板

時間序列深入解析預覽導覽面板提供以下功能：

  ![explorer-fourteen][14]

### <a name="current-session-share-link-control"></a>目前工作階段共用連結控制項

  ![explorer-fifteen][15]

選取連結 Web 控制項 (反白顯示) 即可產生 URL，以儲存或共用您目前的 Azure 時間序列深入解析執行中工作階段，其中包括：

* 目前選取的時間範圍
* 目前選取的間隔大小
* 目前選取的資料井

### <a name="tenant-section"></a>租用戶區段

  ![explorer-sixteen][16]

* 可顯示您目前的時間序列深入解析登入帳戶資訊。
* 可讓您在可用的時間序列深入解析佈景主題之間切換。

### <a name="theme-selection"></a>佈景主題選取項目

Azure 時間序列深入解析預覽支援兩種佈景主題：

* **淺色主題**：本文件中顯示的全部都是預設佈景主題。
* **深色主題**：可轉變總管，如下所示：

  ![explorer-seventeen][17]

這裡也能讓您在支援的語言之間變更。

## <a name="s1s2-environment-controls"></a>S1/S2 環境控制項

### <a name="time-series-insights-preview-terms-panel"></a>時間序列深入解析預覽字詞面板

此區段僅適用於嘗試在更新後的 UI 中使用總管的現有 S1/S2 環境。 您可能希望將 GA 產品和預覽結合使用。 我們已透過現有的 UI 對更新後的總管新增了一些功能，但您可以在現有的時間序列深入解析總管中，體驗 S1/S2 環境的完整 UI。  

您會看到時間序列深入解析字詞面板取代了階層，您可以在其中定義環境中的查詢。 它可以讓您根據述詞篩選資料。

  ![explorer-eighteen][18]

時間序列深入解析預覽字詞編輯器使用以下參數：

**Where**：Where 子句可讓您使用下表所列的運算元集合快速篩選事件。 如果透過選取運算元進行搜尋，述詞會依據該搜尋自動更新。 支援的運算元類型包括：

| 作業 | 支援的類型   | 注意 |
| --- | --- | --- |
| `<`、`>`、`<=`、`>=` | Double、DateTime、TimeSpan | |
| `=`、`!=`, `<>` | String、Bool、Double、DateTime、TimeSpan、NULL |
| `IN` | String、Bool、Double、DateTime、TimeSpan、NULL | 所有的運算元都應該是相同的類型或是 NULL 常數。 |
| `HAS` | 字串 | 右側只允許常數字串常值。 不允許空字串和 NULL。 |

請閱讀[時間序列運算式 (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx)，以深入了解支援的查詢作業和資料類型。

### <a name="examples-of-where-clauses"></a>Where 子句範例

  ![explorer-nineteen][19]

**量值**：此下拉式清單會顯示所有數值資料行 (**雙精度浮點數**)，您可以使用這些資料行作為目前圖表的元素。

**分割依據**：此下拉式清單會顯示模型中所有可用的類別資料行 (字串)，您可以依據這些資料行將資料分組。 您可以新增最多五個要在相同 x 軸上檢視的字詞。 輸入您想要的參數，然後選取 [新增] 加入新字詞。

  ![explorer-twenty][20]

您可以透過選取如下圖所示的可見圖示，來顯示及隱藏圖表面板中的元素。 您可以按一下紅色 **x**，將查詢完全移除。

  ![explorer-twenty-one][21]

## <a name="next-steps"></a>後續步驟

請參閱下列文章：
* [Azure 時間序列深入解析預覽版儲存體和輸入](./time-series-insights-update-storage-ingress.md)
* [資料模型](./time-series-insights-update-tsm.md)
* [診斷與疑難排解](./time-series-insights-update-how-to-troubleshoot.md)

<!-- Images -->
[1]: media/v2-update-explorer/explorer-one.png
[2]: media/v2-update-explorer/explorer-two.png
[3]: media/v2-update-explorer/explorer-three.png
[4]: media/v2-update-explorer/explorer-four.png
[5]: media/v2-update-explorer/explorer-five.png
[6]: media/v2-update-explorer/explorer-six.png
[7]: media/v2-update-explorer/explorer-seven.png
[8]: media/v2-update-explorer/explorer-eight.png
[9]: media/v2-update-explorer/explorer-nine.png
[10]: media/v2-update-explorer/explorer-ten.png
[11]: media/v2-update-explorer/explorer-eleven.png
[12]: media/v2-update-explorer/explorer-twelve.png
[13]: media/v2-update-explorer/explorer-thirteen.png
[14]: media/v2-update-explorer/explorer-fourteen.png
[15]: media/v2-update-explorer/explorer-fifteen.png
[16]: media/v2-update-explorer/explorer-sixteen.png
[17]: media/v2-update-explorer/explorer-seventeen.png
[18]: media/v2-update-explorer/explorer-eighteen.png
[19]: media/v2-update-explorer/explorer-nineteen.png
[20]: media/v2-update-explorer/explorer-twenty.png
[21]: media/v2-update-explorer/explorer-twenty-one.png
