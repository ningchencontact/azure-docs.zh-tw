---
title: 使用 Azure Data Lake Analytics 查詢 Avro 資料 | Microsoft Docs
description: 使用訊息本文屬性將裝置遙測資料路由至 Blob 儲存體，並查詢寫入至 Blob 儲存體的 Avro 格式資料。
services: iot-hub
documentationcenter: ''
author: ksaye
manager: obloch
ms.service: iot-hub
ms.topic: article
ms.date: 05/29/2018
ms.author: Kevin.Saye
ms.openlocfilehash: 98a30155c73a937042b4bea6568543fb5152d748
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726614"
---
# <a name="query-avro-data-using-azure-data-lake-analytics"></a>使用 Azure Data Lake Analytics 查詢 Avro 資料

本文說明如何查詢 Avro 資料，以有效率地將訊息從 Azure IoT 中樞路由至 Azure 服務。 根據部落格文章公告 — [Azure IoT 中樞訊息路由：現已可依據訊息本文進行路由]，IoT 中樞支援依據屬性或訊息本文的路由。 另請參閱[依據訊息本文進行路由][Routing on message bodies]。 

過往的挑戰是，當 Azure IoT 中樞將訊息路由至 Blob 儲存體時，IoT 中樞會以 Avro 格式寫入內容，而其中同時包含訊息本文和訊息屬性。 但須注意的是，IoT 中樞僅支援以 Avro 資料格式將資料寫入至 Blob 儲存體，而此格式不會用於任何其他端點。 請參閱[使用 Azure 儲存體容器時][When using Azure storage containers]。 Avro 格式很適合用來保存資料/訊息，但難以查詢資料。 相較之下，JSON 或 CSV 格式則非常適合用來查詢資料。

若要解決此問題，您可以使用多種巨量資料模式來轉換和調整資料，以因應非關聯式巨量資料的需求和格式。 Azure Data Lake Analytics (ADLA) 即為其中一種模式，屬於「依查詢量付費」模式。 這是本文的重點。 雖然您在 Hadoop 或其他解決方案中就可輕鬆執行查詢，但對於這種「依查詢量付費」的方法，使用 ADLA 通常會適合得多。 U-SQL 中的 Avro 有一個擷取程式。 請參閱 [U-SQL Avro 範例]。

## <a name="query-and-export-avro-data-to-a-csv-file"></a>查詢 Avro 資料並將其匯出至 CSV 檔案
本節將逐步引導您查詢 Avro 資料，並將其匯出至 Azure Blob 儲存體中的 CSV 檔案，但您也可以輕鬆地將資料放置在其他存放庫或資料存放區。

1. 設定 Azure IoT 中樞，使其使用訊息本文中的屬性選取訊息，將資料路由至 Azure Blob 儲存體端點。

    ![步驟 1a 的螢幕擷取畫面][img-query-avro-data-1a]

    ![步驟 1b 的螢幕擷取畫面][img-query-avro-data-1b]

2. 確定您的裝置具有編碼、內容類型和屬性或訊息本文中所需的資料，如產品文件所說明。 在裝置總管中檢視時 (請見下圖)，您可以確認這些屬性是否已正確設定。

    ![步驟 2 的螢幕擷取畫面][img-query-avro-data-2]

3. 設定 Azure Data Lake Store (ADLS) 和 Azure Data Lake Analytics 執行個體。 雖然 Azure IoT 中樞並不會對 Azure Data Lake Store 進行路由，ADLA 仍需要一個執行個體。

    ![步驟 3 的螢幕擷取畫面][img-query-avro-data-3]

4. 在 ADLA 中，將 Azure Blob 儲存體設定為另一個存放區，即 Azure IoT 中樞將資料路由到的相同 Blob 儲存體。

    ![步驟 4 的螢幕擷取畫面][img-query-avro-data-4]
 
5. 如 [U-SQL Avro 範例]所討論，我們需要 4 個 Dll。  請將這些檔案上傳至您在 ADLS 中的位置。

    ![步驟 5 的螢幕擷取畫面][img-query-avro-data-5] 

6. 在 Visual Studio 中，建立 U-SQL 專案
 
    ![步驟 6 的螢幕擷取畫面][img-query-avro-data-6]

7. 複製下列指令碼的內容，並將其貼到新建立的檔案中。 修改醒目提示的 3 個部分：ADLA 帳戶、相關 Dll 的路徑，和儲存體帳戶的正確路徑。
    
    ![步驟 7a 的螢幕擷取畫面][img-query-avro-data-7a]

    簡易輸出至 CSV 的實際 U-SQL 指令碼：
    
    ```sql
        DROP ASSEMBLY IF EXISTS [Avro];
        CREATE ASSEMBLY [Avro] FROM @"/Assemblies/Avro/Avro.dll";
        DROP ASSEMBLY IF EXISTS [Microsoft.Analytics.Samples.Formats];
        CREATE ASSEMBLY [Microsoft.Analytics.Samples.Formats] FROM @"/Assemblies/Avro/Microsoft.Analytics.Samples.Formats.dll";
        DROP ASSEMBLY IF EXISTS [Newtonsoft.Json];
        CREATE ASSEMBLY [Newtonsoft.Json] FROM @"/Assemblies/Avro/Newtonsoft.Json.dll";
        DROP ASSEMBLY IF EXISTS [log4net];
        CREATE ASSEMBLY [log4net] FROM @"/Assemblies/Avro/log4net.dll";

        REFERENCE ASSEMBLY [Newtonsoft.Json];
        REFERENCE ASSEMBLY [log4net];
        REFERENCE ASSEMBLY [Avro];
        REFERENCE ASSEMBLY [Microsoft.Analytics.Samples.Formats];

        // Blob container storage account filenames, with any path
        DECLARE @input_file string = @"wasb://hottubrawdata@kevinsayazstorage/kevinsayIoT/{*}/{*}/{*}/{*}/{*}/{*}";
        DECLARE @output_file string = @"/output/output.csv";

        @rs =
        EXTRACT
        EnqueuedTimeUtc string,
        Body byte[]
        FROM @input_file

        USING new Microsoft.Analytics.Samples.Formats.ApacheAvro.AvroExtractor(@"
        {
        ""type"":""record"",
        ""name"":""Message"",
        ""namespace"":""Microsoft.Azure.Devices"",
        ""fields"":[{
        ""name"":""EnqueuedTimeUtc"",
        ""type"":""string""
        },
        {
        ""name"":""Properties"",
        ""type"":{
        ""type"":""map"",
        ""values"":""string""
        }
        },
        {
        ""name"":""SystemProperties"",
        ""type"":{
        ""type"":""map"",
        ""values"":""string""
        }
        },
        {
        ""name"":""Body"",
        ""type"":[""null"",""bytes""]
        }
        ]
        }");

        @cnt =
        SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
        FROM @rs;

        OUTPUT @cnt TO @output_file USING Outputters.Text(); 
    ```    

    在限用 10 個分析單位的情況下，ADLA 花費 5 分鐘執行了如下的指令碼，處理了 177 個檔案，並將輸出摘錄至 CSV 檔案。
    
    ![步驟 7b 的螢幕擷取畫面][img-query-avro-data-7b]

    檢視輸出時，您可以看到 Avro 內容已轉換為 CSV 檔案。 如果您想要剖析 JSON，請接著執行步驟 8。
    
    ![步驟 7c 的螢幕擷取畫面][img-query-avro-data-7c]

    
8. 大部分的 IoT 訊息皆為 JSON 格式。  加入以下幾行，您即可剖析 JSON 訊息，因此您可以加入 WHERE 子句，並只輸出所需的資料。

    ```sql
       @jsonify = SELECT Microsoft.Analytics.Samples.Formats.Json.JsonFunctions.JsonTuple(Encoding.UTF8.GetString(Body)) AS message FROM @rs;
    
        /*
        @cnt =
            SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
            FROM @rs;
        
        OUTPUT @cnt TO @output_file USING Outputters.Text();
        */
        
        @cnt =
            SELECT message["message"] AS iotmessage,
                   message["event"] AS msgevent,
                   message["object"] AS msgobject,
                   message["status"] AS msgstatus,
                   message["host"] AS msghost
            FROM @jsonify;
            
        OUTPUT @cnt TO @output_file USING Outputters.Text();
    ```

9. 檢視輸出時，您現在會看到 Select 命令中的每個項目。 
    
    ![步驟 8 的螢幕擷取畫面][img-query-avro-data-8]

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何查詢 Avro 資料，以有效率地將訊息從 Azure IoT 中樞路由至 Azure 服務。

若要查看使用 IoT 中樞的完整端對端解決方案範例，請參閱 [Azure IoT 遠端監視解決方案加速器][lnk-iot-sa-land]。

若要深入了解如何使用 IoT 中樞開發解決方案，請參閱 [IoT 中樞開發人員指南]。

若要深入了解 IoT 中樞的訊息路由，請參閱[使用 IoT 中樞傳送和接收訊息][lnk-devguide-messaging]。

<!-- Images -->
[img-query-avro-data-1a]: ./media/iot-hub-query-avro-data/query-avro-data-1a.png
[img-query-avro-data-1b]: ./media/iot-hub-query-avro-data/query-avro-data-1b.png
[img-query-avro-data-2]: ./media/iot-hub-query-avro-data/query-avro-data-2.png
[img-query-avro-data-3]: ./media/iot-hub-query-avro-data/query-avro-data-3.png
[img-query-avro-data-4]: ./media/iot-hub-query-avro-data/query-avro-data-4.png
[img-query-avro-data-5]: ./media/iot-hub-query-avro-data/query-avro-data-5.png
[img-query-avro-data-6]: ./media/iot-hub-query-avro-data/query-avro-data-6.png
[img-query-avro-data-7a]: ./media/iot-hub-query-avro-data/query-avro-data-7a.png
[img-query-avro-data-7b]: ./media/iot-hub-query-avro-data/query-avro-data-7b.png
[img-query-avro-data-7c]: ./media/iot-hub-query-avro-data/query-avro-data-7c.png
[img-query-avro-data-8]: ./media/iot-hub-query-avro-data/query-avro-data-8.png

<!-- Links -->
[Azure IoT 中樞訊息路由：現已可依據訊息本文進行路由]: https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/

[Routing on message bodies]: iot-hub-devguide-query-language.md#routing-on-message-bodies
[When using Azure storage containers]:iot-hub-devguide-endpoints.md#when-using-azure-storage-containers

[U-SQL Avro 範例]:https://github.com/Azure/usql/tree/master/Examples/AvroExamples

[lnk-iot-sa-land]: ../iot-accelerators/index.md
[IoT 中樞開發人員指南]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
