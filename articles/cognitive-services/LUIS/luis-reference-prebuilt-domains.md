---
title: 預先建置的網域參考
titleSuffix: Azure
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
ms.openlocfilehash: 6c0ecf70935d4504472156f6bda726d94f130dbb
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148188"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>您 LUIS 應用程式預先建置的網域參考
此參考提供[預先建立的定義域](luis-how-to-use-prebuilt-domains.md)相關資訊，這是 LUIS 提供之預先建立的意圖和實體集合。

相反地，[自訂網域](luis-how-to-start-new-app.md)一開始沒有任何意圖和模型。 您可以將任何預先建立的定義域意圖和實體新增至自訂模型。

# <a name="supported-domains-across-cultures"></a>支援跨文化特性的網域

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
|行事曆|行事曆是任何項目有關個人會議和約會_不_公用事件 (例如世界杯排程、 西雅圖活動行事曆或泛型的行事曆 (例如哪一天，其目前項目並 fall 開始，時人力天)。|
|通訊|進行呼叫，要求會傳送簡訊或立即訊息，找出，並新增連絡人和各種其他通訊相關的要求 （通常連出）。 連絡人的名稱唯一查詢不屬於網域通訊。|
|電子郵件|電子郵件是通訊網域的子網域。 它主要是包含傳送和接收電子郵件訊息的要求。|
|HomeAutomation|HomeAutomation 網域提供意圖和控管智慧家用裝置相關的實體。 它主要是支援的號誌和空調相關的 [控制] 命令，但有一些其他 electric 所研發的設備的一般化功能。|
|注意|請注意網域提供意圖和實體來建立備忘稿和寫下使用者的項目。|
|地點|位置包括企業 」、 「 機構 」、 「 餐廳 」、 「 公共空間和 「 地址。 網域支援尋找和詢問有關的公開位置，例如地點、 作業系統小時和距離資訊的位置。|
|RestaurantReservation|餐廳保留網域支援意圖處理餐廳的保留項目。|
|ToDo|ToDo 網域提供的使用者新增、 標示及刪除他們的 todo 項目工作清單的類型。|
|ToDo_IPA|ToDo_IPA ToDo 網域為基礎，是以展開 ToDo 中的實體的自訂的版本。 IPA 版本提供以支援待辦事項清單型別的辨識的意圖和實體。 此模型會定義三種類型的待辦事項清單： 購物清單、 購物清單，以及涵蓋其他情況下的待辦事項清單。|
|公用事業|公用程式是一般的網域之間所有 LUIS 預先建置的模型，其中包含常見的意圖和發音不同案例中的。|
|Weather|天氣網域著重於檢查天氣條件，而且有位置和時間的摘要報告，或檢查天氣狀況的時間。|
|Web|Web 網域提供的意圖和實體搜尋網站。|
