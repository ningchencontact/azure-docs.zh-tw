---
title: 在 Azure 型遠端監視解決方案中偵測裝置問題 | Microsoft Docs
description: 本教學課程會示範如何使用規則和動作，來自動偵測遠端監視解決方案中的閾值型裝置問題。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 06/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 1e3eaeec1d2eae3c36f285a3e4c536657504cbb8
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098476"
---
# <a name="tutorial-detect-issues-with-devices-connected-to-your-monitoring-solution"></a>教學課程：偵測與監視解決方案連線的裝置問題

在本教學課程中，您可設定遠端監視解決方案加速器，以偵測已連線 IoT 裝置的問題。 若要偵測您的裝置問題，請新增可在解決方案儀表板上產生警示的規則。

為了簡介規則和警示，本教學課程使用模擬的冷卻器裝置。 冷卻器裝置是由名為 Contoso 的組織所管理，且已連線到遠端監視解決方案加速器。 Contoso 已經有一個會在冷卻器壓力超出 298 PSI 時產生重大警示的規則。 身為 Contoso 的操作員，您會想要尋找初始壓力尖峰，以識別可能有感應器發生故障的冷卻器裝置。 若要識別這類裝置，您可新增一個可在冷卻器壓力超出 150 PSI 時產生警告警示的規則。

系統也會要求您在冷卻器裝置過去 5 分鐘內的平均濕度大於 80% 且裝置的溫度大於華氏 75 度時，針對此裝置建立重大警示。

在本教學課程中，您：

>[!div class="checklist"]
> * 檢視您解決方案中的規則
> * 建立規則
> * 建立具有多個條件的規則
> * 編輯現有的規則
> * 開啟和關閉規則

## <a name="prerequisites"></a>先決條件

若要依循本教學課程進行操作，您需要在 Azure 訂用帳戶中有一個已部署的遠端監視解決方案加速器執行個體。

如果您尚未部署遠端監視解決方案加速器，則應該先完成[部署雲端式遠端監視解決方案](quickstart-remote-monitoring-deploy.md)快速入口。

## <a name="view-the-existing-rules"></a>檢視現有的規則

解決方案加速器中的 [規則] 頁面會顯示所有目前規則的清單：

[![規則頁面](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-expanded.png#lightbox)

若只要檢視適用於冷卻器裝置的規則，請套用篩選條件：

[![篩選規則清單](./media/iot-accelerators-remote-monitoring-automate/rulesactionsfilter_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsfilter_v2-expanded.png#lightbox)

當您從清單中選取某個規則時，即可檢視該規則的詳細資訊並進行編輯：

[![檢視規則詳細資料](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-expanded.png#lightbox)

若要停用或啟用一或多個規則，請選取清單中的一或多個規則：

[![選取多個規則](./media/iot-accelerators-remote-monitoring-automate/rulesactionsmultiselect_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsmultiselect_v2-expanded.png#lightbox)

## <a name="create-a-rule"></a>建立規則

若要建立一個可在冷卻器裝置壓力超出 150 PSI 時產生警告的規則，請按一下 [新增規則]。 使用下列值來建立規則：

| 設定          | 值                                 |
| ---------------- | ------------------------------------- |
| 規則名稱        | Chiller 警告                       |
| 說明      | Chiller 壓力超出 150 PSI |
| 裝置群組     | **Chillers** 裝置群組             |
| 計算      | 立即                               |
| 條件 1 欄位| pressure                              |
| 條件 1 運算子 | 大於                      |
| 條件 1 值    | 150                               |
| 嚴重性層級  | 警告                               |

[![建立警告規則](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-expanded.png#lightbox)

若要儲存新規則，請按一下 [套用]。

您可以在 [規則] 頁面或 [儀表板] 頁面上，查看規則的觸發時間：

[![觸發的警告規則](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-expanded.png#lightbox)

## <a name="create-a-rule-with-multiple-conditions"></a>建立具有多個條件的規則

若要建立具有多個條件的規則，以便在冷卻器裝置過去 5 分鐘內的平均濕度大於 80% 且平均溫度大於華氏 75 度時產生重大警示，請按一下 [新增規則]。 使用下列值來建立規則：

| 設定          | 值                                 |
| ---------------- | ------------------------------------- |
| 規則名稱        | 冷卻器的溫度和濕度非常重要    |
| 說明      | 溼度和溫度非常重要 |
| 裝置群組     | **Chillers** 裝置群組             |
| 計算      | 平均值                               |
| 時間週期      | 5                                     |
| 條件 1 欄位| 溼度                              |
| 條件 1 運算子 | 大於                      |
| 條件 1 值    | 80                                |
| 嚴重性層級  | 重要                              |

[![建立多個條件規則第一部分](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-expanded.png#lightbox)

若要新增第二個條件，按一下 [+ 新增條件]。 使用新條件的下列值：

| 設定          | 值                                 |
| ---------------- | ------------------------------------- |
| 條件 2 欄位| 溫度                           |
| 條件 2 運算子 | 大於                      |
| 條件 2 值    | 75                                |

[![建立多個條件規則第二部分](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-expanded.png#lightbox)

若要儲存新規則，請按一下 [套用]。

您可以在 [規則] 頁面或 [儀表板] 頁面上，查看規則的觸發時間：

[![觸發的多個條件規則](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-expanded.png#lightbox)

## <a name="edit-an-existing-rule"></a>編輯現有的規則

若要對現有的規則進行變更，請從規則清單中選取該規則，然後按一下 [編輯]：

[![編輯規則](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-expanded.png#lightbox)

## <a name="disable-a-rule"></a>停用規則

若要暫時關閉某個規則，您可以在規則清單中將它停用。 選擇要停用的規則，然後選擇 [停用]。 清單中該規則的 [狀態] 會變更以指出規則現在已停用。 您可以使用相同的程序來重新啟用先前停用的規則。

[![停用規則](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-expanded.png#lightbox)

如果您從清單中選取多個規則，便可以同時啟用和停用多個規則。

<!-- ## Delete a rule

To permanently delete a rule, choose the rule in the list of rules and then choose **Delete**.

You can delete multiple rules at the same time if you select multiple rules in the list.-->

## <a name="clean-up-resources"></a>清除資源

如果您打算繼續進行下一個教學課程，請將已部署的遠端監視解決方案加速器保留下來。 若要在未使用解決方案加速器時減少其執行成本，您可以在 [設定] 面板中停止模擬裝置：

[![暫停遙測](./media/iot-accelerators-remote-monitoring-automate/togglesimulation-inline.png)](./media/iot-accelerators-remote-monitoring-automate/togglesimulation-expanded.png#lightbox)

當您準備好開始下一個教學課程時，便可重新啟動模擬裝置。

如果您不再需要解決方案加速器，則請從 [已佈建的解決方案][](https://www.azureiotsolutions.com/Accelerators#dashboard) 頁面中刪除：

![刪除解決方案](media/iot-accelerators-remote-monitoring-automate/deletesolution.png)

## <a name="next-steps"></a>後續步驟

本教學課程示範了如何使用遠端監視解決方案加速器中的 [規則] 頁面，建立及管理可在解決方案中觸發警示的規則。 若要了解如何使用解決方案加速器來管理及設定已連線的裝置，請繼續進行下一個教學課程。

> [!div class="nextstepaction"]
> [設定和管理與監視解決方案連線的裝置問題](iot-accelerators-remote-monitoring-manage.md)