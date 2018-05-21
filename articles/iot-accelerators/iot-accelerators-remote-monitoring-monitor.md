---
title: 遠端監視解決方案中的進階監視 - Azure | Microsoft Docs
description: 本教學課程會示範如何使用遠端監視解決方案儀表板來監視裝置。
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 02/22/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 0456594a4a7776175781968779b4540a98070b78
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2018
---
# <a name="perform-advanced-monitoring-using-the-remote-monitoring-solution"></a>使用遠端監視解決方案執行進階監視

本教學課程會示範遠端監視儀表板的功能。 為了介紹這些功能，本教學課程會使用 Contoso IoT 應用程式中的情節。

在本教學課程中，您會使用兩個模擬的 Contoso 卡車裝置，了解如何從解決方案加速器儀表板監視您的裝置。 作為 Contoso 操作員，您必須在現場監視您卡車的位置和行為。

在本教學課程中，您了解如何：

>[!div class="checklist"]
> * 篩選儀表板中的裝置
> * 檢視即時遙測
> * 檢視裝置詳細資料
> * 從您的裝置檢視警示
> * 檢視系統 KPI

## <a name="prerequisites"></a>先決條件

若要依循本教學課程進行操作，您需要在 Azure 訂用帳戶中有一個已部署的遠端監視解決方案執行個體。

如果您尚未部署遠端監視解決方案，應該先完成[部署遠端監視解決方案加速器](iot-accelerators-remote-monitoring-deploy.md)教學課程。

## <a name="choose-the-devices-to-display"></a>選擇要顯示的裝置

如需選取要在 [儀表板] 頁面上顯示哪些裝置，請使用篩選條件。 若只要顯示**卡車**裝置，請在篩選條件下拉式清單中選擇內建 [卡車] 篩選條件：

![儀表板上的卡車篩選條件](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter.png)

當您套用篩選條件時，只有符合篩選條件的裝置會顯示在 [儀表板] 頁面的地圖上：

![地圖上的卡車顯示](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap.png)

篩選條件也會決定您在 [遙測] 圖表上看到的裝置：

![卡車遙測會顯示在儀表板上](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetry.png)

如需建立、編輯和刪除篩選條件，請選擇 [管理篩選條件]。

## <a name="view-real-time-telemetry"></a>檢視即時遙測

解決方案加速器會在 [儀表板] 頁面的圖表中繪製詳細的即時遙測資料。 遙測圖表會依目前的篩選條件顯示所選取裝置的遙測資訊：

![卡車遙測繪圖](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview.png)

如需選取要檢視的遙測值，請在圖表頂端選擇遙測類型：

![卡車遙測繪圖](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry.png)

<!-- 05/01 - this features appears to have been removed
To pause the live telemetry display, choose **Flowing**. To re-enable the live display, choose **Pause**:

![Pause and restart telemetry display](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetrypause.png)-->

## <a name="use-the-map"></a>使用地圖

地圖會顯示目前篩選條件所選取之模擬卡車的相關資訊。 您可以縮放及移動瀏覽地圖，以顯示更多或更少詳細資料的位置。 地圖上的裝置圖示會指出裝置的任何作用中**警示**或**警告**。 **警示**和**警告**的數目摘要會顯示在地圖左邊。

<!-- 05/01 - cannot select a deice on the map
To view the device details, pan and zoom the map to locate the devices, then click the device on the map. The details include:

* Recent telemetry values
* Methods the device supports
* Device properties

![View device details on the dashboard](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail.png)-->

## <a name="view-alerts-from-your-devices"></a>從您的裝置檢視警示

地圖會以**警示**和**警告**醒目提示目前篩選條件中的裝置。 [警示] 面板會顯示關於您裝置中最新警示的詳細資訊：

![在儀表板上檢視系統警示](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms.png)

您可以使用 [儀表板] 篩選條件來調整最近警示的時間範圍。 根據預設，面板會顯示過去一小時內的警示：

![依時間篩選警示](./media/iot-accelerators-remote-monitoring-monitor/dashboardalarmsfilter.png)

## <a name="view-the-system-kpis"></a>檢視系統 KPI

[儀表板] 頁面會顯示系統 KPI：

![儀表板 KPI](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis.png)

您可以使用 [儀表板] 篩選條件來調整 KPI 彙總的時間範圍。 依預設，面板會顯示過去一小時內彙總的 KPI。

## <a name="next-steps"></a>後續步驟

本教學課程會示範如何使用 [儀表板] 頁面來篩選和監視遠端監視解決方案中佈建的模擬卡車：

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * 篩選儀表板中的裝置
> * 檢視即時遙測
> * 檢視裝置詳細資料
> * 從您的裝置檢視警示
> * 檢視系統 KPI

既然您已了解如何監視裝置，建議的後續步驟是了解如何：

* [使用以臨界值作為基礎的規則來偵測問題](iot-accelerators-remote-monitoring-automate.md)。
* [管理及設定您的裝置](iot-accelerators-remote-monitoring-manage.md)。
* [針對裝置問題進行疑難排解和補救](iot-accelerators-remote-monitoring-maintain.md)。
* [使用模擬裝置來測試您的解決方案](iot-accelerators-remote-monitoring-test.md)。

<!-- Next tutorials in the sequence -->