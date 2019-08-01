---
title: 預建網域參考-LUIS
titleSuffix: Azure Cognitive Services
description: 預先建立的定義域參考是從 Language Understanding Intelligent Service (LUIS) 預先建立的意圖和實體集合。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: ce043e23a0384a74fd5d2c9dd514045578ef836d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563459"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>您 LUIS 應用程式預先建置的網域參考
此參考提供[預先建立的定義域](luis-how-to-use-prebuilt-domains.md)相關資訊，這是 LUIS 提供之預先建立的意圖和實體集合。

相反地，[自訂網域](luis-how-to-start-new-app.md)一開始沒有任何意圖和模型。 您可以將任何預先建立的定義域意圖和實體新增至自訂模型。

# <a name="supported-domains-across-cultures"></a>跨文化特性支援的網域

唯一支援的文化特性是英文。 

<!--

The table below summarizes the currently supported domains. Support for English is usually more complete than others.


| Entity Type       | EN-US      | ZH-CN   | DE    | FR     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:| 
| Calendar    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -      | -    | -    | -     | -  |
| Communication   | ✓    | -       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Email           | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| HomeAutomation           | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Notes      | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Places    | ✓    | -       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| RestaurantReservation   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| ToDo     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| ToDo_IPA        | ✓    | ✓       | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Utilities          | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Weather        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Web    | ✓    | -        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
||||||||||||| 

-->

<br><br>

|實體類型|description|
|--|--|
|行事曆|行事曆是關於個人會議和約會的任何東西, 而_不_是公開活動 (例如, 世界盃的排程、西雅圖的活動行事曆) 或一般行事曆 (例如今天的日期、開始的時間、何時為工作日)。|
|通訊|要求進行呼叫、傳送文字或立即訊息、尋找並新增連絡人, 以及其他與其他通訊相關的要求 (通常是傳出)。 僅連絡人名稱查詢不屬於通訊網域。|
|Email|電子郵件是通訊網域的子域。 它主要包含透過電子郵件傳送和接收訊息的要求。|
|HomeAutomation|HomeAutomation 網域提供與控制智慧型家庭裝置相關的意圖和實體。 它主要支援與光源和空氣空調相關的控制命令, 但它有一些適用于其他電動設備的一般化功能。|
|注意|注意 domain 提供意圖和實體來建立便箋, 並為使用者寫下專案。|
|地點|地點包括企業、機構、餐廳、公共空間和位址。 網域支援尋找和詢問公共場所的資訊, 例如位置、上班時間和距離。|
|RestaurantReservation|餐廳保留網域支援處理餐廳保留的意圖。|
|多|ToDo 定義域提供工作清單類型, 讓使用者加入、標記和刪除其 todo 專案。|
|公用事業|公用程式網域是所有 LUIS 預建模型中的一般網域, 其中包含不同案例中的常見意圖和語句。|
|Weather|氣象領域著重于檢查天氣條件和諮詢, 並提供位置和時間, 或依天氣條件檢查時間。|
|Web|Web 網域提供搜尋網站的意圖和實體。|
