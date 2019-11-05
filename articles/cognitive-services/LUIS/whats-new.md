---
title: 新功能-Language Understanding （LUIS）
titleSuffix: Azure Cognitive Services
description: 本文包含 Language Understanding 的相關新聞。
author: diberry
manager: nitinme
ms.custom: experiment-luis-0519
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: diberry
ms.openlocfilehash: 0788df1c05d70c01d4de7f7a6ba62a476252f75a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498932"
---
# <a name="whats-new-in-language-understanding"></a>Language Understanding 的新功能

瞭解服務的新功能。 這些專案包括版本資訊、影片、blog 文章和其他類型的資訊。 將此頁面加入書簽，以掌握服務的最新狀態。  

## <a name="release-notes"></a>版本資訊 

### <a name="november-4-2019---ignite"></a>2019年11月4日-Ignite

* 提升開發人員生產力
    * 正式推出的[預測端點 V3](luis-migration-api-v3.md)。 
    * 能夠以 lu （[LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)）格式匯入和匯出應用程式。 這鋪路了有效 CI/CD 程式的方式。 
* 語言擴充
    * [阿拉伯文和印度](luis-language-support.md)文（公開預覽）。
* 建置前模型
    * [預先](luis-reference-prebuilt-domains.md)建立的網域現已正式推出（GA）
    * 在 V3 中，日文預先建立的[實體](luis-reference-prebuilt-entities.md#japanese-entity-support)不支援年齡、貨幣、數位、百分比。
    * 義大利[建實體](luis-reference-prebuilt-entities.md#italian-entity-support)-年齡、貨幣、維度、數位、百分比解析已從 V2 變更。
* 增強[preview.luis.ai 入口網站](https://preview.luis.ai)中的使用者體驗-改頭換面標記體驗，以啟用建立和調試複雜模型。
* 預先語言理解功能-以較少的方式[建立複雜的語言模型](luis-concept-entity-types.md)。 
* 在模型層級定義機器學習功能，並讓模型用來做為其他模型的信號，例如使用實體做為意圖和其他實體的功能。
* 新的擴充[限制](luis-boundaries.md)-片語清單的最大上限和片語總計、新模型做為功能限制
* 以深層階層結構的格式從文字中解壓縮資訊，讓對話應用程式更強大。

    ![機器學習的實體映射](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>2019年9月3日

* Azure 撰寫資源-[立即遷移](luis-migration-authoring.md)。
    * 500每個 Azure 資源的應用程式
    * 100每個應用程式的版本
* 預建實體的土耳其文支援
* DatetimeV2 的義大利文支援

### <a name="july-23-2019"></a>2019年7月23日

* 將辨識器[文字](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3)更新為1.2。3
    * 義大利文中的年齡、溫度、維度和貨幣辨識器。
    * 改善英文版的假日辨識，以正確計算感恩節日期。
    * 改善法文 DateTime，以減少非日期和非時間實體的誤報。
    * 支援行事曆/學校/會計年度和英文 DateRange 的縮略字。
    * 已改善中文和日文的 PhoneNumber 辨識。
    * 已改善英文版 NumberRange 的支援。
    * 效能改進。

### <a name="june-24-2019"></a>2019年6月24日

* [OrdinalV2 預先](luis-reference-prebuilt-ordinal-v2.md)建立的實體以支援排序，例如下一個、上一個和最後一個。 僅限英文文化特性。

### <a name="may-6-2019---build-conference"></a>5月6日，2019-build 會議

組建2019會議已發行下列功能：

* [V3 API 遷移指南的預覽](luis-migration-api-v3.md)
* [改良的分析儀表板](luis-how-to-use-dashboard.md)
* [改良的預建網域](luis-reference-prebuilt-domains.md) 
* [動態清單實體](luis-migration-api-v3.md#dynamic-lists-passed-in-at-prediction-time)
* [外部實體](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>部落格

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>影片

### <a name="2019-build-videos"></a>2019組建影片

[如何使用 Azure 對話式 AI 來調整您的企業以進行下一代](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>服務更新

[適用于認知服務的 Azure 更新公告](https://azure.microsoft.com/updates/?product=cognitive-services)
