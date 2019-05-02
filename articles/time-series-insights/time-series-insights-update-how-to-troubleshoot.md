---
title: 針對 Azure 時間序列深入解析預覽進行診斷與疑難排解 | Microsoft Docs
description: 了解如何運用 Azure 時間序列深入解析預覽進行診斷與疑難排解。
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 3ab3c680f7279ff78e0319f28f67c1cc8c203b47
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64708027"
---
# <a name="diagnose-and-troubleshoot"></a>診斷和疑難排解

本文摘要說明使用 Azure 時間序列深入解析預覽環境時，可能會遇到的幾個常見問題。 文章中也會描述每個問題的可能發生原因和解決方案。

## <a name="problem-i-cant-find-my-environment-in-the-time-series-insights-preview-explorer"></a>問題：在時間序列深入解析預覽總管中找不到我的環境

如果您沒有存取時間序列深入解析環境的權限，就可能會發生這個問題。 使用者需要讀取器層級存取角色，才能檢視他們的時間序列深入解析環境。 若要確認目前的存取層級並授與其他存取權，請瀏覽 [Azure 入口網站](https://portal.azure.com/)中，時間序列深入解析資源的 [資料存取原則] 區段。

  ![環境][1]

## <a name="problem-no-data-is-seen-in-the-time-series-insights-preview-explorer"></a>問題：時間序列深入解析預覽總管中看不到任何資料

有幾個常見原因會造成您無法在 [Azure 時間序列深入解析預覽總管](https://insights.timeseries.azure.com/preview) 中看到資料。

- 事件來源可能沒有接收資料。

    請確認事件來源 (事件中樞或 IoT 中樞)，是否正從您的標籤或執行個體接收資料。 請在 Azure 入口網站中，瀏覽至您資源的概觀頁面以進行確認。

    ![儀表板深入解析][2]

- 事件來源資料不是 JSON 格式。

    時間序列深入解析僅支援 JSON 資料。 如需 JSON 範例，請參閱[支援的 JSON 樣貌](./how-to-shape-query-json.md)。

- 事件來源索引鍵遺漏必要的權限。

  * 針對 IoT 中樞，您必須提供具有「服務連接」權限的索引鍵。

    ![組態][3]

  * 如上圖所示，**iothubowner** 與 **service** 原則都會運作，因為兩者皆有**服務連接**權限。
  * 針對事件中樞，您必須提供具有「接聽」權限的索引鍵。
  
    ![權限][4]

  * 如上圖所示，**read**與 **manage** 原則都會運作，因為兩者皆有**接聽**權限。

- 您提供的取用者群組不是時間序列深入解析專用的。

    在註冊 IoT 中樞或事件中樞期間指定的取用者群組是要用於讀取資料的。 請勿共用該取用者群組。 如果共用取用者群組，基礎事件中樞會自動且隨機地與其中一個讀取器中斷連線。 提供唯一的取用者群組讓時間序列深入解析讀取。

- 您在佈建期間指定的時間序列識別碼屬性不正確、遺漏，或為 null。

    如果時間序列識別碼屬性設定不正確，佈建環境時就可能會發生這個問題。 如需詳細資訊，請參閱[選擇時間序列識別碼的最佳做法](./time-series-insights-update-how-to-id.md)。 目前您無法將現有的時間序列深入解析環境更新為使用不同的時間序列識別碼。

## <a name="problem-some-data-shows-but-some-is-missing"></a>問題：只顯示部分資料，遺漏了部分資料

您可能傳送了沒有時間序列識別碼的資料。

- 在酬載中傳送沒有時間序列識別碼欄位的事件時，就可能會發生這個問題。 如需詳細資訊，請參閱[支援的 JSON 樣貌](./how-to-shape-query-json.md)。

- 發生這個問題的原因可能是因為您的環境正在進行節流。

    > [!NOTE]
    > 目前時間序列深入解析支援最高 6 Mbps 的擷取速率。

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>問題：我的事件來源的時間戳記屬性名稱設定沒有作用

請確定名稱和值符合下列規則︰

* 時間戳記屬性名稱區分大小寫。
* 來自事件來源的時間戳記屬性值 (JSON 字串) 格式為 `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`。 此類型字串的其中一個範例為 `“2008-04-12T12:53Z”`。

若要確保您已擷取時間戳記屬性名稱且正常運作，最簡單方式是使用時間序列深入解析預覽總管。 在時間序列深入解析預覽總管中，當您輸入時間戳記屬性名稱之後，使用圖表選取一段時間。 以滑鼠右鍵按一下選取範圍，然後選取 [探索事件] 選項。 第一個資料行標頭就是您的時間戳記屬性名稱。 它的字組 `Timestamp` 旁邊應該有 `($ts)`，而非：

* `(abc)`，這表示時間序列深入解析將資料值讀取為字串。
* 行事曆圖示，這表示時間序列深入解析將資料值讀取為日期時間。
* `#`，這表示時間序列深入解析將資料值讀取為整數。

如果未明確指定時間戳記屬性，就會使用事件的 IoT 中樞或事件中樞加入佇列時間作為預設時間戳記。

## <a name="problem-i-cant-edit-or-view-my-time-series-model"></a>問題：無法編輯或檢視時間序列模型

- 您可能存取了時間序列深入解析 S1 或 S2 環境。

   只有在 PAYG 環境中才支援時間序列模型。 如需如何透過時間序列深入解析預覽總管存取 S1/S2 環境的詳細資訊，請參閱[在總管中將資料視覺化](./time-series-insights-update-explorer.md)。

   ![Access][5]

- 您可能沒有檢視及編輯模型的權限。

   使用者需要參與者層級存取權，才能編輯及檢視他們的時間序列模型。 若要確認目前的存取層級並授與其他存取權，請瀏覽 Azure 入口網站中，時間序列深入解析資源的 [資料存取原則] 區段。

## <a name="problem-all-my-instances-in-the-time-series-insights-preview-explorer-dont-have-a-parent"></a>問題：我在時間序列深入解析預覽總管中的所有執行處理都沒有父代

如果您的環境未定義時間序列模型階層，就可能會發生這個問題。 如需詳細資訊，請參閱[使用時間序列模型](./time-series-insights-update-how-to-tsm.md)。

  ![時間序列模型][6]

## <a name="next-steps"></a>後續步驟

- 閱讀[使用時間序列模型](./time-series-insights-update-how-to-tsm.md)。

- 深入了解[支援的 JSON 樣貌](./how-to-shape-query-json.md)。

<!-- Images -->
[1]: media/v2-update-diagnose-and-troubleshoot/environment.png
[2]: media/v2-update-diagnose-and-troubleshoot/dashboard-insights.png
[3]: media/v2-update-diagnose-and-troubleshoot/configuration.png
[4]: media/v2-update-diagnose-and-troubleshoot/permissions.png
[5]: media/v2-update-diagnose-and-troubleshoot/access.png
[6]: media/v2-update-diagnose-and-troubleshoot/tsm.png