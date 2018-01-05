---
title: "了解 Azure 串流分析工作的相容性層級。 | Microsoft Docs"
description: "了解如何在最新的相容性層級中設定執行 Azure Stream Analytics 工作，而且主要變更相容性層級"
keywords: "相容性層級，資料流處理資料"
documentationcenter: 
services: stream-analytics
author: SnehaGunda
manager: kfile
editor: 
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/03/2018
ms.author: sngun
ms.openlocfilehash: 70aa82f5d8a909121c42742bb5261d6c9b8570de
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2018
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Azure 串流分析工作的相容性層級
 
相容性層級是指 Azure Stream Analytics 服務的特定版本的行為。 Azure Stream Analytics 是受管理的服務，與規則的功能更新，並提升效能。 通常更新會自動開放給使用者。 不過，一些新功能可能會導入重大變更這類變更的現有工作的行為，取用這些作業等資料的處理序變更。相容性層級用來代表在 Stream Analytics 中導入重大變更。 重大變更一律會引進新的相容性層級。 

相容性層級可確保現有的工作執行不含任何失敗。 當您建立新的資料流分析工作時，它會是最佳做法是使用最新的相容性層級可供您加以建立。 
 
## <a name="set-a-compatibility-level"></a>設定相容性層級 

相容性層級控制資料流分析工作的執行階段行為。 您可以在透過入口網站或使用設定資料流分析工作的相容性層級[建立作業的 REST API 呼叫](https://docs.microsoft.com/en-us/rest/api/streamanalytics/stream-analytics-job)。 Azure Stream Analytics 目前支援兩個相容性層級-"1.0"和"1.1"。 根據預設，相容性層級設定為 「 1.0 」 擴充功能的 Azure 資料流分析期間所導入。 若要更新的預設值，瀏覽至您現有的資料流分析工作 > 選取**相容性層級**選項**設定**區段，並將值變更。 

請確定您在更新的相容性層級之前停止工作。 如果您的工作處於執行中狀態，無法更新相容性層級。 

![在入口網站中的相容性層級](media\stream-analytics-compatibility-level/image1.png)

 
當您更新的相容性層級時，T-SQL 編譯器驗證對應至所選相容性層級的語法與作業。 

## <a name="major-changes-in-the-latest-compatibility-level-11"></a>最新的相容性層級 (1.1) 的重大變更

相容性層級 1.1 版中導入下列主要變更：

* **服務匯流排 XML 格式**  

  * **舊版：** Azure 串流分析會使用 DataContractSerializer，因此訊息內容包含的 XML 標記。 例如︰
    
   @\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{"SensorId":"1"，"溫度": 64\}\u0001 

  * **目前的版本：**訊息內容會包含直接與任何其他標記的資料流。 例如︰
  
   {"SensorId":"1"，"溫度": 64} 
 
* **保存欄位名稱區分大小寫**  

  * **舊版：**欄位名稱已變更為較低的情況下，如果 Azure Stream Analytics 引擎處理。 

  * **目前的版本：** Azure Stream Analytics 引擎處理它們時，欄位名稱會保存區分大小寫。 
 
* **FloatNaNDeserializationDisabled**  

  * **舊版：** CREATE TABLE 命令並未篩選事件的 NaN (Not a Number。 例如，Infinity、-Infinity） 浮點數資料行類型，因為他們不符合這些數字的文件範圍。

  * **目前的版本：** CREATE TABLE 可讓您指定強式的結構描述。 資料流分析引擎會驗證資料符合此結構描述。 使用這個模型，此命令可以篩選具有 NaN 值的事件。 

* **停用自動向上轉型，在 JSON 中的 datetime 字串。**  

  * **舊版：** JSON 剖析器會自動向上轉型的字串值與日期時間/日期/時區資訊類型，並再將它轉換成 UTC。 這會導致遺失時區資訊。

  * **目前的版本：**沒有沒有自動向上轉型成日期時間類型的日期/時間/區域資訊的字串值。 如此一來，就會保留時區資訊。 

## <a name="next-steps"></a>後續步驟
* [疑難排解 Azure Stream Analytics 的指南](stream-analytics-troubleshooting-guide.md)
* [資料流分析資源健全狀況刀鋒視窗](stream-analytics-resource-health.md)