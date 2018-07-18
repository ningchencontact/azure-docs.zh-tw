---
title: 從 Azure 解決方案監視您的 IoT 裝置 |Microsoft Docs
description: 在本教學課程中，您會了解如何使用遠端監視解決方案加速器來監視 IoT 裝置。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 06/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 5f42ed0fa5362959e5619f2d550ca1ae3711ed65
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097456"
---
# <a name="tutorial-monitor-your-iot-devices"></a>教學課程：監視 IoT 裝置

在本教學課程中，您會使用遠端監視解決方案加速器來監視已連線的 IoT 裝置。 您可使用解決方案儀表板來檢視遙測、裝置資訊、警示和 KPI。

為了介紹這些監視功能，本教學課程使用兩個模擬的卡車裝置。 卡車是由名為 Contoso 的組織所管理，且已連線到遠端監視解決方案加速器。 作為 Contoso 操作員，您必須在現場監視您卡車的位置和行為。

在本教學課程中，您：

>[!div class="checklist"]
> * 篩選儀表板中的裝置
> * 檢視即時遙測
> * 檢視裝置詳細資料
> * 從您的裝置檢視警示
> * 檢視系統 KPI

## <a name="prerequisites"></a>先決條件

若要依循本教學課程進行操作，您需要在 Azure 訂用帳戶中有一個已部署的遠端監視解決方案加速器執行個體。

如果您尚未部署遠端監視解決方案加速器，則應該先完成[部署雲端式遠端監視解決方案](quickstart-remote-monitoring-deploy.md)快速入口。

## <a name="choose-the-devices-to-display"></a>選擇要顯示的裝置

若要選取要在 [儀表板] 頁面上顯示哪些已連線的裝置，請使用篩選條件。 若只要顯示**卡車**裝置，請在篩選條件下拉式清單中選擇內建 [卡車] 篩選條件：

[![儀表板上的卡車篩選條件](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-expanded.png#lightbox)

當您套用篩選條件時，只有符合篩選條件的裝置會顯示在 [儀表板] 頁面的地圖上：

[![只有卡車會顯示在地圖上](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-expanded.png#lightbox)

篩選條件也會決定您在 [遙測] 圖表上看到的裝置：

[![卡車遙測會顯示在儀表板上](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetry-expanded.png#lightbox)

如需建立、編輯和刪除篩選條件，請選擇 [管理裝置群組]。

## <a name="view-real-time-telemetry"></a>檢視即時遙測

解決方案加速器會在 [儀表板] 頁面的圖表中繪製即時遙測。 遙測圖表的頂關會依目前的篩選條件顯示所選裝置的可用遙測類型：

[![卡車遙測類型](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-expanded.png#lightbox)

若要檢視溫度遙測，請按一下 [溫度]：

[![卡車溫度遙測繪圖](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-expanded.png#lightbox)

## <a name="use-the-map"></a>使用地圖

地圖會顯示目前篩選條件所選取之模擬卡車的相關資訊。 您可以縮放及移動瀏覽地圖，以顯示更多或更少詳細資料的位置。 地圖上的裝置圖示顏色會指出裝置是否有任何有效的**警示**或**警告**。 **警示**和**警告**的數目摘要會顯示在地圖左邊。

若要檢視裝置詳細資料，請移動瀏覽及縮放地圖來找出裝置，然後選取地圖上的裝置。 接著按一下裝置標籤以開啟 [裝置詳細資料] 面板。 裝置詳細資料包括：

* 最近的遙測值
* 裝置支援的方法
* 裝置屬性

[![在儀表板上檢視裝置詳細資料](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-expanded.png#lightbox)

## <a name="view-alerts"></a>檢視警示

[警示] 面板會顯示關於您裝置中最新警示的詳細資訊：

[![在儀表板上檢視裝置警示](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-expanded.png#lightbox)

您可以使用篩選條件來調整最近警示的時間範圍。 根據預設，面板會顯示過去一小時內的警示：

[![依時間篩選警示](./media/iot-accelerators-remote-monitoring-monitor/dashboardalarmsfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardalarmsfilter-expanded.png#lightbox)

## <a name="view-the-system-kpis"></a>檢視系統 KPI

[儀表板] 頁面會在 [Analytics] 面板中顯示解決方案加速器所計算的系統 KPI：

[![儀表板 KPI](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-expanded.png#lightbox)

此儀表板會針對目前裝置和時間範圍篩選條件所選取的警示顯示三個 KPI：

* 針對已觸發最多警示的規則顯示作用中警示數目。
* 依裝置類型顯示警示的比例。
* 重大警示的警示百分比。

為警示設定時間範圍以及控制會顯示哪些裝置的相同篩選條件會決定 KPI 的彙總方式。 依預設，面板會顯示過去一小時內彙總的 KPI。

## <a name="clean-up-resources"></a>清除資源

如果您打算繼續進行下一個教學課程，請將已部署的遠端監視解決方案加速器保留下來。 若要在未使用解決方案加速器時減少其執行成本，您可以在 [設定] 面板中停止模擬裝置：

[![暫停遙測](./media/iot-accelerators-remote-monitoring-monitor/togglesimulation-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/togglesimulation-expanded.png#lightbox)

當您準備好開始下一個教學課程時，便可重新啟動模擬裝置。

如果您不再需要解決方案加速器，則請從 [已佈建的解決方案][](https://www.azureiotsolutions.com/Accelerators#dashboard) 頁面中刪除：

![刪除解決方案](media/iot-accelerators-remote-monitoring-monitor/deletesolution.png)

## <a name="next-steps"></a>後續步驟

本教學課程示範了如何使用遠端監視解決方案加速器中的 [儀表板] 頁面，來篩選和監視模擬的卡車。 若要了解如何使用解決方案加速器來偵測已連線裝置的問題，請繼續進行下一個教學課程。

> [!div class="nextstepaction"]
> [偵測與監視解決方案連線的裝置問題](iot-accelerators-remote-monitoring-automate.md)