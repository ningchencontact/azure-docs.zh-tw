---
title: 在 Azure IoT Central 應用程式中分析裝置資料 | Microsoft Docs
description: 在 Azure IoT Central 應用程式中分析裝置資料。
author: lmasieri
ms.author: lmasieri
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: ffe8b350c1b5cea23aeb65092c7912c6d6c1ed89
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052965"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>如何使用分析來分析您的裝置資料

*本文適用於操作員、建置員及系統管理員。*

Azure IoT 中心提供豐富的分析功能，讓來自您裝置的大量資料變得更有意義。 若要開始，請瀏覽左側導覽功能表上的 [分析]  。

## <a name="querying-your-data"></a>查詢您的資料

您必須選擇 [裝置集合]  、新增 [篩選]  (選擇性)，然後選取 [時段]  來開始作業。 一旦您完成時，選取**顯示結果**開始將資料視覺化。

* **裝置集合：** [裝置集合](howto-use-device-sets.md)是使用者定義的裝置群組。 例如，位在奧克蘭 (Oakland) 的所有冰箱或所有 2.0 版風力渦輪機。

* **篩選：** 您可以選擇性地在搜尋中加入篩選條件，以鎖定您要找的資料。 您一次最多可新增 10 個篩選條件。 比方說，針對位在奧克蘭的所有冰箱，您可以尋找溫度超過 60 度的冰箱。
* **時間週期：** 依預設，我們會擷取過去 10 分鐘的資料。 您可以將此值變更為其中一個預先定義的時間範圍，或選取自訂時段。

  ![分析查詢](media/howto-create-analytics/analytics-query.png)

## <a name="visualizing-your-data"></a>將資料視覺化

查詢資料後，您就可以開始將資料視覺化。 您可以顯示/隱藏量值、變更資料彙總方式，以及依據不同的裝置屬性進一步分割資料。  

* **分割依據：** 依據裝置屬性來分割資料可讓您深入鑽研資料。 例如，您可以依據裝置識別碼和位置來分割您的結果。

* **量值：** 您可以選擇顯示/隱藏裝置一次報告的不同遙測項目 (最多 10 個)。 量值是指溫度和溼度等項目。

* **彙總：** 依預設，我們會以資料平均值來彙總資料，但您可以選擇將資料彙總變更為符合需求的其他項目。

   ![分析視覺化分割依據](media/howto-create-analytics/analytics-splitby.png)

## <a name="interacting-with-your-data"></a>與資料互動

您可以變更您的查詢結果，以符合您的視覺效果需求的各種方式。 您可以替代的圖形檢視和格線檢視之間、 放大和縮小、 重新整理您的資料集，及變更線條的顯示方式。

* **顯示格線：** 您的結果會提供以資料表格式，讓您可以檢視每個資料點的特定值。 此檢視也符合協助工具標準。
* **顯示圖表：** 您的結果會顯示在行格式，以協助您識別向上或向下趨勢和異常。

  ![顯示分析的格線檢視](media/howto-create-analytics/analytics-showgrid.png)

縮放可讓您在首頁上您的資料。 如果您想將焦點放在結果集內的某個時段，請使用資料指標擷取想要放大的區域，並使用可用的控制項來執行下列其中一個動作：

* **放大：** 一旦您已選取一段時間，在縮放已啟用，並可讓您放大您的資料。
* **縮小：** 此控制項可讓您將最後的顯示比例縮小一個層級。 比方說，如果您縮放在您的資料三次，大約需要縮放您備份一個步驟一次。
* **顯示比例重設：** 在您執行了多個縮放層級後，您可以使用顯示比例重設控制項來返回原始的結果集。

  ![對您的資料執行縮放](media/howto-create-analytics/analytics-zoom.png)

您可以變更線條樣式來符合您的需求。 您有四個選項：

* **線條：** 每個資料點之間平穩直線。
* **平滑：** 每個點之間曲線。
* **步階：** 在圖表上的每個點之間的行是一個步驟。
* **散佈圖：** 所有資料點是圖表上繪製，而不需要連接它們的線條。

  ![分析中可用的不同線條類型](media/howto-create-analytics/analytics-linetypes.png)

最後，您也可以選擇三種模式的其中一個，以排列您的資料在 y 軸：

* **堆疊：** 每個量值的圖形都會堆疊起來，而且每個圖形都有自己的 Y 軸。 當您已選取多個量值，而且想要有這些量值的不同檢視時，堆疊圖形很有用。
* **未堆疊：** 每個量值的圖形都會根據一個 Y 軸繪製，但是 Y 軸的值會根據醒目提示的量值變更。 當您想要重疊多個量值，而且想要查看在相同的時間範圍內這些量值的模式，未堆疊的圖形很有用。
* **共用 Y 軸：** 所有圖形都會共用相同的 Y 軸，而該軸的值不會變更。 當您在利用分割依據來切割資料時，如果想要查看單一量值，共用 Y 軸圖形很有用。

  ![以不同視覺效果模式排列 Y 軸的資料](media/howto-create-analytics/analytics-yaxis.png)

## <a name="next-steps"></a>後續步驟

您現在已了解如何為 Azure IoT 中心應用程式建立自訂分析，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [準備及連線 Node.js 應用程式](howto-connect-nodejs.md)