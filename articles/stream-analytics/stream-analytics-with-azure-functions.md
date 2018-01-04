---
title: "使用 Azure Stream Analytics 工作中執行 Azure 函式 |Microsoft 文件"
description: "了解如何設定 Azure 函式做為資料流分析工作的輸出接收。"
keywords: "資料輸出、 資料流資料，Azure 函式"
documentationcenter: 
services: stream-analytics
author: SnehaGunda
manager: kfile
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 12/19/2017
ms.author: sngun
ms.openlocfilehash: adc147fc9f47e78cc0a2fcaf53f064bcfa5eee2c
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2017
---
# <a name="run-azure-functions-with-azure-stream-analytics-jobs"></a>使用 Azure Stream Analytics 工作中執行 Azure 函式 
 
> [!IMPORTANT]
> 這項功能為預覽狀態。

您可以使用 Azure Stream Analytics 執行 Azure 函式，藉由設定 Azure 函式做為其中一個資料流分析工作輸出接收。 Azure 的函式是事件驅動，視運算經驗，可讓您實作由 Azure 或協力廠商服務中發生的事件所觸發的程式碼。 此 Azure 函式的回應能力觸發程序可使其自然的輸出 Azure 串流分析工作。

Azure Stream Analytics 叫用 Azure 函式，透過 HTTP 的觸發程序。 Azure 函式的輸出配接器可讓使用者連線到資料流分析的 Azure 函式，如此可以根據資料流分析查詢觸發的事件。 

本教學課程示範如何連接到 Azure Stream Analytics [Azure Redis 快取](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md)使用[Azure 函式](../azure-functions/functions-overview.md)。 

## <a name="configure-stream-analytics-job-to-run-an-azure-function"></a>設定資料流分析工作來執行 Azure 函式 

本節示範如何設定資料流分析工作來執行 Azure 函式，將資料寫入至 Azure Redis 快取。 資料流分析工作會從事件中心讀取事件、 執行查詢，以叫用 Azure 函式。 此 Azure 函式，讀取資料流分析工作，並將它寫入至 Azure Redis 快取。

![為了說明本教學課程的圖形](./media/stream-analytics-with-azure-functions/image1.png)

下列步驟才能完成這項工作：
* [建立事件中樞與串流分析工作，做為輸入。](#create-stream-analytics-job-with-event-hub-as-input)  
* [建立 Azure Redis 快取。](#create-an-azure-redis-cache)  
* [建立 Azure 函式可將資料寫入至 Redis 快取。](#create-an-azure-function-that-can-write-data-to-the-redis-cache)    
* [更新 Azure 函式做為輸出資料流分析工作。](#update-the-stream-analytic-job-with-azure-function-as-output)  
* [您可以檢查 Redis 快取的結果。](#check-redis-cache-for-results)  

## <a name="create-stream-analytics-job-with-event-hub-as-input"></a>建立事件中樞與串流分析工作，做為輸入

請遵循[即時詐騙偵測](stream-analytics-real-time-fraud-detection.md)教學課程中建立事件中樞時，請啟動事件的產生器應用程式，並建立 Azure Stream Analytics (略過逐步完成建立查詢和輸出，您將會改為安裝Azure 的函式輸出中下一節。）

## <a name="create-an-azure-redis-cache"></a>建立 Azure Redis 快取

1. 使用中所述的步驟來建立 Azure Redis 快取[建立快取](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)Redis 快取發行項的區段。  

2. 建立 Redis 快取之後，瀏覽至建立的快取 >**便捷鍵**> 然後記下的**主要連接字串**。

   ![Redis 快取的連接字串](./media/stream-analytics-with-azure-functions/image2.png)

## <a name="create-an-azure-function-that-can-write-data-to-the-redis-cache"></a>建立 Azure 函式可將資料寫入至 Redis 快取

1. 使用[建立函式的應用程式](../azure-functions/functions-create-first-azure-function.md#create-a-function-app)Azure 函式文件集來建立 Azure 函式應用程式和[HTTP 觸發 Azure 函式](../azure-functions/functions-create-first-azure-function.md#create-function)(也稱為 Webhook) 使用**CSharp**語言。  

2. 瀏覽至**run.csx**函式，並以下列程式碼更新 (請務必取代"\<此處為您 redis 快取的連接字串\>"Redis 快取的主要連接字串的文字您擷取的前一節中）：  

   ```c#
   using System;
   using System.Net;
   using System.Threading.Tasks;
   using StackExchange.Redis;
   using Newtonsoft.Json;
   using System.Configuration;

   public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
   {
      log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
    
      // Get the request body
      dynamic dataArray = await req.Content.ReadAsAsync<object>();

      // Throw an HTTP Request Entity Too Large exception when the incoming batch(dataArray) is greater than 256 KB. Make sure that the size value is consistent with the value entered in the Stream Analytics portal.

      if (dataArray.ToString().Length > 262144)
      {        
         return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
      }
      var connection = ConnectionMultiplexer.Connect("<your redis cache connection string goes here>");
      log.Info($"Connection string.. {connection}");
    
      // Connection refers to a property that returns a ConnectionMultiplexer
      IDatabase db = connection.GetDatabase();
      log.Info($"Created database {db}");
    
      log.Info($"Message Count {dataArray.Count}");

      // Perform cache operations using the cache object. For example, the following code block adds few integral data types to the cache
      for (var i = 0; i < dataArray.Count; i++)
      {
        string time = dataArray[i].time;
        string callingnum1 = dataArray[i].callingnum1;
        string key = time + " - " + callingnum1;
        db.StringSet(key, dataArray[i].ToString());
        log.Info($"Object put in database. Key is {key} and value is {dataArray[i].ToString()}");
       
      // Simple get of data types from the cache
      string value = db.StringGet(key);
      log.Info($"Database got: {value}");
      }

      return req.CreateResponse(HttpStatusCode.OK, "Got");
}    

   ```

   當 Azure Stream Analytics 中從 Azure 函式收到 413 （Http 要求實體太大） 的例外狀況時，它會減少傳送至 Azure 函式的批次的大小。 在 Azure 的函式，使用下列程式碼來檢查 Azure Stream Analytics 不會傳送過大的批次。 請確定函式中使用的最大的批次計數和大小值與輸入資料流分析入口網站中的值一致。

   ```c#
   if (dataArray.ToString().Length > 262144)
      {        
        return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
      }
   ```

3. 在您選擇的文字編輯器中，建立名為的 JSON 檔案**project.json**為下列程式碼，然後儲存檔案在本機電腦上。 此檔案包含 c# 函式所需的 NuGet 封裝相依性。  
   
   ```json
       {
         "frameworks": {
             "net46": {
                 "dependencies": {
                     "StackExchange.Redis":"1.1.603",
                     "Newtonsoft.Json": "9.0.1"
                 }
             }
         }
     }

   ```
 
4. 請返回 Azure 入口網站 > 瀏覽至您的 Azure 函式 > 從**平台功能** 索引標籤 > 按一下**應用程式服務編輯器**，位於**開發工具**. 
 
   ![應用程式服務編輯器 畫面](./media/stream-analytics-with-azure-functions/image3.png)

5. 在應用程式服務編輯器 中，以滑鼠右鍵按一下您的根目錄，並上傳**project.json**檔案。 上傳成功之後，重新整理頁面，您現在應該會看到一個名為自動產生檔案**project.lock.json**。  自動產生檔案包含 dll 的 Project.json 檔案中指定的參考。  

   ![Project.json 檔案上傳](./media/stream-analytics-with-azure-functions/image4.png)

 

## <a name="update-the-stream-analytic-job-with-azure-function-as-output"></a>更新 Azure 函式做為輸出資料流分析工作

1. 開啟 Azure 入口網站上的 Azure 串流分析工作。  

2. 瀏覽至您的 Azure 函式 >**概觀** > **輸出** > **新增**新輸出 > 選取**Azure 函式**接收選項。 新的 Azure 函式輸出配接器會提供具有下列屬性：  

   |**屬性名稱**|**說明**|
   |---|---|
   |輸出別名| 您參考輸出作業的查詢中使用的使用者易記名稱。 |
   |匯入選項| 您可以使用 azure 的函式，從目前的訂用帳戶，或如果函式是在其他訂用帳戶中，以手動方式提供的設定。 |
   |函式應用程式| 您的 Azure 函式應用程式的名稱。 |
   |函式| 在應用程式函式 （run.csx 函式的名稱） 中的函式名稱。|
   |批次大小上限|這個屬性用來設定每個輸出批次，以便傳送至您的 Azure 函式的最大大小。 根據預設，這個值會設為 256 KB。|
   |批次計數上限|這個屬性可讓您在每個批次傳送至 Azure 函式中指定的事件數目上限。 預設最大的批次計數值為 100。 這是選用屬性。|
   |Key|這個屬性可讓您使用 Azure 的函式從另一個訂用帳戶。 提供的索引鍵值來存取您的函式。 這是選用屬性。|

3. 提供輸出別名的名稱。 在此教學課程中，我們將其命名**saop1** （您可以使用您選擇的其他任何名稱） 和填入其他詳細資料。  

4. 開啟資料流分析工作，並更新查詢下 （請務必取代"saop1 」 文字，如果名稱的輸出接收不同）：  

   ```sql
    SELECT 
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        INTO saop1
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
           JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum
   ```

5. 在命令列中執行下列命令啟動 telcodatagen.exe 應用程式 (命令會使用的格式- `telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]`)  
   
   **telcodatagen.exe 1000.2 2**
    
6.  開始串流分析工作。

## <a name="check-redis-cache-for-results"></a>檢查結果的 Redis 快取

1. Azure 入口網站瀏覽並尋找您 Redis 快取 > 選取**主控台**。  

2. 使用[Redis 快取命令](https://redis.io/commands)來確認您的資料是否為 Redis 快取。 （命令會使用格式取得 {索引鍵}。） 例如︰

   **取得"12/19/2017年 21:32:24-123414732"**

   此命令應該會列出指定之索引鍵的值：

   ![Redis 快取輸出](./media/stream-analytics-with-azure-functions/image5.png)

## <a name="known-issues"></a>已知問題

* 在 Azure 入口網站，當您嘗試重設最大的批次大小 / 最大的批次計數值設定為 empty(default)，它會變更回時儲存先前輸入的值。 刻意輸入這些欄位的預設值在此情況下。

## <a name="next-steps"></a>後續步驟
