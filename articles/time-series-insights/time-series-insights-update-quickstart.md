---
title: 快速入門：探索 Azure 時間序列深入解析預覽版示範環境 | Microsoft Docs
description: 了解 Azure 時間序列深入解析預覽版示範環境。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 12/03/2018
ms.openlocfilehash: de5e853db6c6a0e98dea9251cc07b526288574e1
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276820"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>快速入門：探索 Azure 時間序列深入解析預覽版示範環境

本快速入門將說明如何在免費的示範環境中使用 Azure 時間序列深入解析預覽版總管。 您將了解如何使用網頁瀏覽器將大量的歷史產業 IoT 資料視覺化，並導覽時間序列深入解析預覽版總管的主要功能。

時間序列深入解析會提供端對端的平台即服務 (PaaS) 供應項目。 它可以內嵌、處理、儲存和查詢進行拼湊的資料瀏覽的高度相關的語境、時間序列最佳化 IoT 規模資料。 它還提供操作分析。 時間序列深入解析是針對產業 IoT 部署的獨特需求而量身訂做的特殊供應項目。

示範環境顯示了一家電力公司 Contoso。 在環境中，您可以使用時間序列深入解析，在 Contoso 資料中尋找可操作的見解，並進行簡短的根本原因分析。 Contoso 會操作兩個風力渦輪機伺服器陣列，每個都有 10 個渦輪機。 每台渦輪機都有 20 個感應器，每分鐘向 Azure IoT 中樞回報資料。 感應器會收集有關天氣狀況、葉片的俯仰和偏擺位置、發電機效能、變速箱行為，以及安全監視器的相關資訊。

您可以使用時間序列深入解析預覽版來分析過去兩年中 Contoso 不斷成長的資料集，目前該資料集為 40 GB。 它可以協助您深入了解，並預測發生嚴重失敗和移動緩慢的維護問題。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費 Azure 帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 。

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>在示範環境中來探索時間序列深入解析

1. 在瀏覽器中，移至  [Contoso 風力發電廠環境](https://insights.timeseries.azure.com/preview/samples)。  

1. 如果出現提示，請使用您的 Azure 帳戶認證登入時間序列深入解析。

### <a name="demo-step-1"></a>示範步驟 1

1. 讓我們來觀察 **Contoso Plant 1** 中的風力渦輪機 **W7**。  

    * **動作**：將檢視範圍更新為 **1/1/17 20:00 to 3/10/17 20:00 (UTC)**，新增 **Contoso Plant 1** > **W7** > **Generator System** > **GeneratorSpeed** 感應器，然後顯示結果值。

       ![快速入門一][1]

1. 近期，Contoso 發現風力發電機 **W7** 曾經起火。 我們將由此深入探究。 我們可以看到，在火災期間啟動了起火警示感應器。

    * **動作**：將視圖範圍更新為 [3/9/17 20:00 to 3/10/17 20:00 (UTC)]，並新增 [安全性系統] > [FireAlert] 感應器。

      ![快速入門二][2]

1. 我們將查看在火災期間還發生了哪些狀況。 油壓和作用中警告在起火之前都急遽升高，但在這個時間點已來不及阻止問題發生。

    * **動作**：新增 [音調系統] > [HydraulicOilPressure] 感應器和 [音調系統] > [ActiveWarning] 感應器。

      ![快速入門三][3]

1. 如果縮小範圍，我們可以看到有個導致火災發生的跡象。 這兩個感應器都出現波動。 以前發生過此問題嗎？

    * **動作**：將檢視範圍更新為 **2/24/17 20:00 to 3/10/17 20:00 (UTC)**。

      ![快速入門四][4]

1. 如果我們查看整個兩年間的所有資料，我們會發現先前的起火事件也有相同的跡象。 透過這項資料，我們可以建置及早發現此類問題的系統。

    * **動作**：將檢視範圍更新為 **1/1/16 to 12/31/17** (所有資料)。

       ![快速入門五][5]

### <a name="demo-step-2"></a>示範步驟 2

1. 其他問題更細微，且更難以診斷。 時間序列深入解析提供了各式各樣的方式，有助於追蹤困難的問題。 我們可以從中看出，**W6** 的警告感應器曾在 **6/25** 中斷運作。 但實際情況究竟為何？

    * **動作**：移除目前的感應器，將檢視範圍更新為 [6/1/17 20:00 to 7/1/17 20:00 (UTC)]，然後新增 [Contoso Plant 1] > [W6] > [安全性系統] > [VoltageActuatorSwitchWarning] 感應器。

       ![快速入門六][6]

1. 警告指出發電機輸出的電壓有問題。 但其原因為何？ 發電機的整體電力輸出以密集的時間間隔來看是正常的。 但在彙總資料後，我們可以看到明顯的下降。

    * **動作**：移除 [VoltageActuatorSwitchWarning] 感應器，新增 [發電機系統] > [ActivePower] 感應器，並將間隔更新為 [3d]。

       ![快速入門七][7]

1. 如果我們往前瀏覽資料集，我們會發現這不只是暫時性的問題。 它還在持續發生中。

    * **動作**：將時間範圍往右延伸。

       ![快速入門八][8]

1. 我們將進一步深入探詢。 我們可以新增其他感應器資料點，依階段檢視電壓。 但所有的資料點看起來都差不多。 我們將置放標記，以查看實際的值。 階段 3 的輸出似乎有問題。

    * **動作**：新增 [發電機系統] > [GridVoltagePhase1]、[GridVoltagePhase2] 和 [GridVoltagePhase3] 感應器。 在可見區域的最後一個資料點上置放標記。

       ![快速入門八][8]

1. 如果三個資料點全都以相同的比例來檢視，階段 3 的下降就會更為明顯。 至此，我們已可將此問題交由維護團隊處理，並為其提供警告成因的可靠線索。  

    * **動作**：更新顯示畫面，使所有感應器以相同的圖表比例重疊。

       ![快速入門九][9]

## <a name="next-steps"></a>後續步驟

您已準備好建立自己的時間序列深入解析預覽版環境：

> [!div class="nextstepaction"]
> [規劃時間序列深入解析預覽版環境](time-series-insights-update-plan.md)

<!-- Images -->
[1]: media/v2-update-quickstart/quickstart-one.png
[2]: media/v2-update-quickstart/quickstart-two.png
[3]: media/v2-update-quickstart/quickstart-three.png
[4]: media/v2-update-quickstart/quickstart-four.png
[5]: media/v2-update-quickstart/quickstart-five.png
[6]: media/v2-update-quickstart/quickstart-six.png
[7]: media/v2-update-quickstart/quickstart-seven.png
[8]: media/v2-update-quickstart/quickstart-eight.png
[9]: media/v2-update-quickstart/quickstart-nine.png
