---
title: 偵測遠端監視解決方案中的裝置問題 - Azure | Microsoft Docs
description: 本教學課程將說明如何使用規則和動作，來自動偵測遠端監視解決方案中的臨界值型裝置問題。
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 5acf35ed19a5b6baa2885fd58cfb7fbbe1ac3cd8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="detect-issues-using-threshold-based-rules"></a>使用臨界值型規則來偵測問題

本教學課程將說明遠端監視解決方案中的規則引擎功能。 為了介紹這些功能，本教學課程會使用 Contoso IoT 應用程式中的情節。

Contoso 有一個會在 **Chiller** 裝置回報的壓力超出 250 PSI 時產生重大警示的規則。 身為操作員，您會想要尋找初始壓力尖峰，以識別可能有感應器發生故障的 **Chiller** 裝置。 為了識別這些裝置，您會建立一個在壓力超出 150 PSI 時產生警告的規則。

您也已經知道當**冷卻器**裝置的平均濕度在過去 5 分鐘內大於 80% 且**冷卻器**裝置的溫度在過去 5 分鐘內大於華氏 75 度時必須觸發重大警示。

在本教學課程中，您了解如何：

>[!div class="checklist"]
> * 檢視您解決方案中的規則
> * 建立新的規則
> * 建立具有多個條件的新規則
> * 編輯現有的規則
> * 刪除規則

## <a name="prerequisites"></a>先決條件

若要依循本教學課程進行操作，您需要在 Azure 訂用帳戶中有一個已部署的遠端監視解決方案執行個體。

如果您尚未部署遠端監視解決方案，應該先完成[部署遠端監視解決方案加速器](iot-suite-remote-monitoring-deploy.md)教學課程。

## <a name="view-the-rules-in-your-solution"></a>檢視您解決方案中的規則

解決方案中的 [規則] 頁面會顯示所有目前規則的清單：

![[規則和動作] 頁面](media/iot-suite-remote-monitoring-automate/rulesactions_v2.png)

若只要檢視適用於 **Chiller** 裝置的規則，請套用篩選條件：

![篩選規則清單](media/iot-suite-remote-monitoring-automate/rulesactionsfilter_v2.png)

當您從清單中選取某個規則時，即可檢視該規則的詳細資訊並進行編輯：

![檢視規則詳細資料](media/iot-suite-remote-monitoring-automate/rulesactionsdetail_v2.png)

若要停用、啟用或刪除一或多個規則，請選從清單中選取多個規則：

![選取多個規則](media/iot-suite-remote-monitoring-automate/rulesactionsmultiselect_v2.png)

## <a name="create-a-new-rule"></a>建立新的規則

若要新增一個在 **Chiller** 裝置中壓力超出 150 PSI 時產生重大警告的規則，請選擇 [新增規則]：

![建立規則](media/iot-suite-remote-monitoring-automate/rulesactionsnewrule_v2.png)

使用下列值來建立規則：

| 設定          | 值                                 |
| ---------------- | ------------------------------------- |
| 規則名稱        | Chiller 警告                       |
| 說明      | Chiller 壓力超出 150 PSI |
| 裝置群組     | **Chillers** 裝置群組             |
| 計算      | 立即                               |
| 條件 1 欄位| pressure                              |
| 條件 1 運算子 | 大於                      |
| 條件 1 值    | 150                               |
| 嚴重性等級  | 警告                               |

若要儲存新規則，請選擇 [套用]。

您可以在 [規則] 頁面或 [儀表板] 頁面上，檢視規則的觸發時間。

## <a name="create-a-new-rule-with-multiple-conditions"></a>建立具有多個條件的新規則

若要建立具有多個條件的新規則，以便當**冷卻器**裝置的平均濕度在過去 5 分鐘內大於 80% 且**冷卻器**裝置的溫度在過去 5 分鐘內大於華氏 75 度時產生重大警示，請選擇 [新增規則]：

![建立多重規則](media/iot-suite-remote-monitoring-automate/rulesactionsnewrule_mult_v2.png)

使用下列值來建立規則：

| 設定          | 值                                 |
| ---------------- | ------------------------------------- |
| 規則名稱        | 冷卻器的溫度和濕度非常重要    |
| 說明      | 溼度和溫度非常重要 |
| 裝置群組     | **Chillers** 裝置群組             |
| 計算      | 平均值                               |
| 時間週期      | 5                                     |
| 條件 1 欄位| 溼度                              |
| 條件 1 運算子 | 大於                      |
| 條件 1 值    | 80                               |
| 嚴重性等級  | 重要                              |

若要新增第二個條件，按一下 [+ 新增條件]。

![建立條件 2](media/iot-suite-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2.png)

在新的條件中使用下列值：

| 設定          | 值                                 |
| ---------------- | ------------------------------------- |
| 條件 2 欄位| 溫度                           |
| 條件 2 運算子 | 大於                      |
| 條件 2 值    | 75                                |

若要儲存新規則，請選擇 [套用]。

您可以在 [規則] 頁面或 [儀表板] 頁面上，檢視規則的觸發時間。

## <a name="edit-an-existing-rule"></a>編輯現有的規則

若要對現有的規則進行變更，請從規則清單中選取該規則。

![編輯規則](media/iot-suite-remote-monitoring-automate/rulesactionsedit_v2.png)

<!--## Disable a rule

To temporarily switch off a rule, you can disable it in the list of rules. Choose the rule to disable, and then choose **Disable**. The **Status** of the rule in the list changes to indicate the rule is now disabled. You can re-enable a rule that you previously disabled using the same procedure.

![Disable rule](media/iot-suite-remote-monitoring-automate/rulesactionsdisable.png)

You can enable and disable multiple rules at the same time if you select multiple rules in the list.-->

<!--## Delete a rule

To permanently delete a rule, choose the rule in the list of rules and then choose **Delete**.

You can delete multiple rules at the same time if you select multiple rules in the list.-->

## <a name="next-steps"></a>後續步驟

本教學課程已示範如何：

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * 檢視您解決方案中的規則
> * 建立新的規則
> * 編輯現有的規則
> * 刪除規則

既然您已了解如何使用臨界值型規則來偵測問題，建議接下來的步驟是了解如何：

* [管理及設定您的裝置](./iot-suite-remote-monitoring-manage.md)。
* [針對裝置問題進行疑難排解和補救](./iot-suite-remote-monitoring-maintain.md)。
* [使用模擬裝置來測試您的解決方案](iot-suite-remote-monitoring-test.md)。

<!-- Next tutorials in the sequence -->