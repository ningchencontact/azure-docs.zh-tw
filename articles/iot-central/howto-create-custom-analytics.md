---
title: 使用自訂的分析來擴充 Azure IoT Central |Microsoft Docs
description: 身為方案開發人員，IoT Central 應用程式設定為執行自訂的分析和視覺效果。 此解決方案會使用 Azure Databricks。
author: dominicbetts
ms.author: dobett
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: e039e2b8d9c183b5bfee1bee47e4addc4c873bf7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66743436"
---
# <a name="extend-azure-iot-central-with-custom-analytics"></a>使用自訂的分析來擴充 Azure IoT Central

本使用方法指南所顯示的是，解決方案開發人員，如何擴充您的 IoT Central 應用程式與自訂的分析和視覺效果。 此範例會使用[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/)工作區來分析 IoT Central 的遙測資料流，並產生視覺效果，例如[方塊繪圖](https://wikipedia.org/wiki/Box_plot)。

本操作說明指南示範如何超越什麼它已經運用 IoT Central[內建的分析工具](howto-create-analytics.md)。

在本使用說明指南中，您將了解如何：

* Stream IoT Central 應用程式使用的遙測*連續資料匯出*。
* 建立 Azure Databricks 環境分析並繪製的裝置遙測資料。

## <a name="prerequisites"></a>必要條件

若要完成此操作指南中的步驟，您必須具備有效的 Azure 訂用帳戶。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

### <a name="iot-central-application"></a>IoT Central 應用程式

建立 IoT Central 應用程式來自[Azure IoT Central-我的應用程式](https://aka.ms/iotcentral)頁面使用下列設定：

| 設定 | 值 |
| ------- | ----- |
| 付款計劃 | Pay-As-You-Go |
| 應用程式範本 | Contoso 範例 |
| 應用程式名稱 | 接受預設值，或選擇您自己的名稱 |
| URL | 接受預設值，或選擇您自己唯一的 URL 前置詞 |
| 目錄 | Azure Active Directory 租用戶 |
| Azure 訂用帳戶 | 您的 Azure 訂用帳戶 |
| 區域 | 美國東部 |

範例和螢幕擷取畫面，在此發行項使用**美國東部**區域。 選擇靠近您的位置，並確定您在相同的區域中建立的所有資源。

### <a name="resource-group"></a>資源群組

使用[Azure 入口網站來建立資源群組](https://portal.azure.com/#create/Microsoft.ResourceGroup)稱為**IoTCentralAnalysis**以包含您所建立的其他資源。 在您的 IoT Central 應用程式的相同位置中建立您的 Azure 資源。

### <a name="event-hubs-namespace"></a>事件中樞命名空間

使用[Azure 入口網站來建立事件中樞命名空間](https://portal.azure.com/#create/Microsoft.EventHub)使用下列設定：

| 設定 | 值 |
| ------- | ----- |
| 名稱    | 選擇您的命名空間名稱 |
| 定價層 | 基本 |
| 訂用帳戶 | 您的訂用帳戶 |
| 資源群組 | IoTCentralAnalysis |
| 位置 | 美國東部 |
| 輸送量單位 | 1 |

### <a name="azure-databricks-workspace"></a>Azure Databricks 工作區

使用[Azure 入口網站來建立 Azure Databricks 服務](https://portal.azure.com/#create/Microsoft.Databricks)使用下列設定：

| 設定 | 值 |
| ------- | ----- |
| 工作區名稱    | 選擇您的工作區名稱 |
| 訂用帳戶 | 您的訂用帳戶 |
| 資源群組 | IoTCentralAnalysis |
| 位置 | 美國東部 |
| 定價層 | 標準 |

當您已建立必要的資源，您**IoTCentralAnalysis**資源群組看起來像下列螢幕擷取畫面：

![IoT Central 分析資源群組](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>建立事件中樞

您可以設定為持續將遙測匯出至事件中樞的 IoT Central 應用程式。 在本節中，您可以建立事件中樞，以從您的 IoT Central 應用程式接收遙測資料。 事件中樞提供遙測資料至您的 Stream Analytics 作業進行處理。

1. 在 Azure 入口網站中，瀏覽至您的事件中樞命名空間，然後選取 **+ 事件中樞**。
1. 命名您的事件中樞**centralexport**，然後選取**建立**。
1. 在您的命名空間中的事件中樞的清單中，選取**centralexport**。 然後選擇**共用存取原則**。
1. 選取  **+ 新增**。 建立名為的原則**接聽**具有**接聽**宣告。
1. 準備就緒的原則時，在清單中，選取它，然後將複製**連接字串-主索引鍵**值。
1. 請記下的此連接字串，您使用稍後當您設定您的 Databricks notebook 來從事件中樞讀取時。

您的事件中樞命名空間看起來像下列螢幕擷取畫面：

![事件中樞命名空間](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>在 IoT 中心中設定匯出

瀏覽至[IoT Central 應用程式](https://aka.ms/iotcentral)您範本所建立的 Contoso。 在本節中，您可以設定應用程式串流至事件中樞的遙測從模擬的裝置。 若要匯出的設定：

1. 瀏覽至**連續資料匯出**頁面上，選取 **+ 新增**，然後**Azure 事件中樞**。
1. 使用下列設定來設定匯出，然後選取**儲存**:

    | 設定 | 值 |
    | ------- | ----- |
    | 顯示名稱 | 匯出至事件中樞 |
    | Enabled | 另一 |
    | 事件中樞命名空間 | 事件中樞命名空間名稱 |
    | 事件中樞 | centralexport |
    | 度量 | 另一 |
    | 裝置 | 關閉 |
    | 裝置範本 | 關閉 |

![連續資料匯出組態](media/howto-create-custom-analytics/cde-configuration.png)

等到匯出狀態變成**執行**再繼續。

## <a name="configure-databricks-workspace"></a>設定 Databricks 工作區

在 Azure 入口網站中，瀏覽至您的 Azure Databricks 服務，然後選取**啟動工作區**。 新的索引標籤會在您的瀏覽器中開啟，並將您登入您的工作區。

### <a name="create-a-cluster"></a>建立叢集

在  **Azure Databricks**頁面上，在常見的工作清單，選取**新叢集**。

使用下表中的資訊，來建立叢集：

| 設定 | 值 |
| ------- | ----- |
| 叢集名稱 | centralanalysis |
| 叢集模式 | 標準 |
| Databricks 執行階段版本 | 5.3 (Scala 2.11, Spark 2.4.0) |
| Python 版本 | 3 |
| 啟用自動調整 | 否 |
| 非使用狀態幾分鐘後終止 | 30 |
| 背景工作類型 | Standard_DS3_v2 |
| 背景工作角色 | 1 |
| 驅動程式類型 | 背景工作角色相同 |

建立叢集可能需要幾分鐘的時間，請等候完成再繼續建立叢集。

### <a name="install-libraries"></a>安裝程式庫

在 **叢集**頁面上，等候叢集狀態**執行**。

下列步驟顯示如何將您的範例需要的程式庫匯入叢集：

1. 在上**叢集**頁面上，等到狀態**centralanalysis**互動式叢集**執行**。

1. 選取叢集，然後選擇**程式庫** 索引標籤。

1. 在 **程式庫**索引標籤上，選擇**安裝新**。

1. 在 **安裝程式庫**頁面上，選擇**Maven**做為程式庫來源。

1. 在 **座標**文字方塊中，輸入下列值： `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. 選擇**安裝**安裝在叢集上的 程式庫。

1. 媒體櫃狀態現在是**已安裝**:

    ![安裝的程式庫](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>匯入 Databricks notebook

若要匯入包含 Python 程式碼分析和視覺化您的 IoT Central 遙測 Databricks notebook 中使用下列步驟：

1. 瀏覽至**工作區**Databricks 環境中的頁面。 選取您的帳戶名稱旁邊的下拉式清單，然後選擇**匯入**。

1. 選擇此選項，從 URL 匯入，並輸入下列位址： [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. 若要匯入 notebook，請選擇**匯入**。

1. 選取 **工作區**若要檢視匯入的 notebook:

    ![匯入的 notebook](media/howto-create-custom-analytics/import-notebook.png)

1. 編輯第一個 Python 資料格，以新增您先前儲存的事件中樞連接字串中的程式碼：

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>執行分析

若要執行分析，您必須將 notebook 連結至叢集：

1. 選取  **Detached** ，然後選取**centralanalysis**叢集。
1. 如果叢集未執行，請加以啟動。
1. 若要啟動的 notebook，請選取 [執行] 按鈕。

您可能會看到在最後一個儲存格錯誤。 若是如此，檢查先前的儲存格正在執行、 等候一分鐘讓一些資料寫入至儲存體，然後重新執行最後一個儲存格。

### <a name="view-smoothed-data"></a>檢視平滑化的資料

在筆記本中，向下儲存格以查看依裝置類型，輪流平均溼度的圖 14 中捲動。 此圖會不斷更新串流的遙測資料送達時：

![經過平滑處理遙測繪圖](media/howto-create-custom-analytics/telemetry-plot.png)

您可以調整大小在 notebook 中的圖表。

### <a name="view-box-plots"></a>檢視方塊的繪圖

在筆記本中，向下捲動資料格以查看 20[方塊繪圖](https://en.wikipedia.org/wiki/Box_plot)。 方塊繪圖都會靜態的資料為基礎，因此您必須重新執行資料格來加以更新：

![盒狀圖](media/howto-create-custom-analytics/box-plots.png)

您可以調整大小在 notebook 中的繪圖。

## <a name="tidy-up"></a>整理一下

不妨整理一下之後此操作說明,，並避免不必要的成本，刪除**IoTCentralAnalysis**在 Azure 入口網站中的資源群組。

您可以刪除 IoT Central 應用程式，從**管理**應用程式內的頁面。

## <a name="next-steps"></a>後續步驟

在此操作指南中，您已了解如何：

* Stream IoT Central 應用程式使用的遙測*連續資料匯出*。
* 建立 Azure Databricks 環境分析並繪製遙測資料。

既然您已經知道如何建立自訂的分析，建議的下一個步驟是了解如何[視覺化及分析您在 Power BI 儀表板中的 Azure IoT Central 資料](howto-connect-powerbi.md)。
