---
title: 開始使用 Azure 的計量瀏覽器
description: 了解如何使用 Azure 的計量瀏覽器中建立您第一次的計量圖表。
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: e611f1564896cfdecb3ce34ab7c5361e5200b48a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2019
ms.locfileid: "57537332"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>開始使用 Azure 計量瀏覽器

## <a name="where-do-i-start"></a>如何開始
Azure 監視器計量瀏覽器是 Microsoft Azure 入口網站的一個元件，可用來繪製圖表、以視覺方式串連趨勢，以及調查計量值中的突增值與突降值。 您可以使用計量瀏覽器來調查的健全狀況和資源的使用率。 以下列順序啟動：

1. [挑選的資源，而基準則](#create-your-first-metric-chart)，您會看到基本的圖表。 然後[選取時間範圍](#select-a-time-range)，是關於您的調查。

1. 請嘗試[套用維度篩選及分割](#apply-dimension-filters-and-splitting)。 篩選及分割可讓您分析計量的哪些市場區隔對整體度量的值，並找出可能的極端值。

1. 使用[進階設定](#advanced-chart-settings-and-next-steps)自訂圖表，再釘選到儀表板。 [設定警示](alerts-metric-overview.md)計量值超過或低於臨界值時收到通知。

## <a name="create-your-first-metric-chart"></a>建立您第一次的計量圖表

若要建立計量圖表中，從您的資源、 資源群組、 訂用帳戶或 Azure 監視器檢視，開啟**計量**索引標籤，並遵循下列步驟：

1. 使用資源選擇器，選取您要查看度量的資源。 (如果您開啟系統會預先選取的資源**計量**特定資源的內容中)。

    > ![選取資源](./media/metrics-getting-started/resource-picker.png)

2. 對於某些資源，您必須選擇命名空間。 命名空間是只是一種方式組織計量，讓您輕鬆地找到它們。 例如，儲存體帳戶會有不同的命名空間來儲存檔案、 資料表、 Blob 和佇列的計量。 許多資源類型只能有一個命名空間。

3. 從可用的度量清單中選取計量。

    > ![選取計量](./media/metrics-getting-started/metric-picker.png)

4. （選擇性） 您可以變更計量的彙總。 比方說，您可能想以顯示最小值、 最大值，或平均的值的度量圖表。

> [!NOTE]
> 使用**新增計量**按鈕，然後重複上述步驟，如果您想要查看多個相同的圖表中繪製的度量。 在一個檢視中的多個圖表，請選取**新增圖表**最上層顯示 按鈕。

## <a name="select-a-time-range"></a>選取時間範圍

根據預設，圖表會顯示在最近 24 小時內的計量資料。 使用**時間選擇器**變更的時間範圍、 放大，或縮小圖表上的面板。 

![變更時間範圍面板](./media/metrics-getting-started/time-picker.png)

## <a name="apply-dimension-filters-and-splitting"></a>套用維度篩選和分割

[篩選](metrics-charts.md#apply-filters-to-charts)並[分割](metrics-charts.md#apply-splitting-to-a-chart)是功能強大的診斷工具，如有維度的計量。 這些功能會顯示不同的計量區段 （「 維度值 」） 影響的計量，整體的值，並讓您識別可能的極端值。

- **篩選**可讓您選擇的維度值包含在圖表中。 比方說，您可能想要製作圖表時，顯示成功的要求*伺服器回應時間*計量。 您必須將篩選套用在*要求的成功*維度。 

- **分割**控制項是否圖表會顯示個別的維度，每個值的程式行或彙總成單一行的值。 比方說，您可以檢視一行的平均回應時間，請在所有伺服器執行個體，或請參閱每個伺服器的不同行。 您需要適用於依分割*伺服器執行個體*維度，以查看個別的行。

請參閱[的圖表範例](metric-chart-samples.md)，進行篩選及分割套用。 本文說明的步驟用來設定圖表。

## <a name="advanced-chart-settings-and-next-steps"></a>進階的圖表設定和後續步驟

您可以自訂圖表樣式的標題，並修改進階的圖表設定。 當完成自訂，請將其釘選到儀表板，以儲存您的工作。 您也可以設定計量警示。 請遵循[產品文件](metrics-charts.md)若要了解這些和其他進階的 Azure 監視器計量瀏覽器的功能。

## <a name="next-steps"></a>後續步驟

* [請參閱 Azure 服務的可用計量清單](metrics-supported.md)
* [深入了解計量瀏覽器進階功能](metrics-charts.md)
* [請參閱設定圖表的範例](metric-chart-samples.md)
