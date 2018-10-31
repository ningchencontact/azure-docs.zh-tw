---
title: 使用 .NET Framework 將事件傳送至 Azure 事件中樞 | Microsoft Docs
description: 使用 .NET Framework 開始將事件傳送至事件中樞
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: shvija
ms.openlocfilehash: ae5d89aab4ce1bd599ed9a50dc46336f8a96a2f5
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2018
ms.locfileid: "49456212"
---
# <a name="send-events-to-azure-event-hubs-using-the-net-framework"></a>使用 .NET Framework 將事件傳送至 Azure 事件中樞
Azure 事件中樞是巨量資料串流平台和事件擷取服務，每秒可接收和處理數百萬個事件。 事件中樞可以處理及儲存分散式軟體和裝置所產生的事件、資料或遙測。 傳送至事件中樞的資料可以透過任何即時分析提供者或批次/儲存體配接器來轉換和儲存。 如需事件中樞的詳細概觀，請參閱[事件中樞概觀](event-hubs-about.md)和[事件中樞功能](event-hubs-features.md)。

本教學課程會示範如何透過 .NET Framework 使用以 C# 撰寫的主控台應用程式，將事件傳送至事件中樞。 

## <a name="prerequisites"></a>必要條件
若要完成本教學課程，您需要下列必要條件：

* [Microsoft Visual Studio 2017 或更高版本](http://visualstudio.com)。

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>建立事件中樞命名空間和事件中樞
第一個步驟是使用 [Azure 入口網站](https://portal.azure.com)來建立「事件中樞」類型的命名空間，然後取得您應用程式與「事件中樞」進行通訊所需的管理認證。 若要建立命名空間和「事件中樞」，請依照[這篇文章](event-hubs-create.md)中的程序操作，然後繼續進行本教學課程中的下列步驟。

## <a name="create-a-console-application"></a>建立主控台應用程式

在 Visual Studio 中，使用 **主控台應用程式** 專案範本建立新的「Visual C# 桌面應用程式」專案。 將專案命名為 **Sender**。
   
![](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp1.png)

## <a name="add-the-event-hubs-nuget-package"></a>新增事件中樞 NuGet 封裝

1. 在 [方案總管] 中，以滑鼠右鍵按一下 [寄件者] 專案，然後按一下 [管理方案的 NuGet 封裝]。 
2. 按一下 [瀏覽] 索引標籤，然後搜尋 `WindowsAzure.ServiceBus`。 按一下 [安裝] 並接受使用條款。 
   
    ![](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp2.png)
   
    Visual Studio 會下載、安裝並新增 [Azure 服務匯流排程式庫 NuGet 封裝](https://www.nuget.org/packages/WindowsAzure.ServiceBus)的參考。

## <a name="write-code-to-send-messages-to-the-event-hub"></a>撰寫程式碼以將訊息傳送到事件中樞

1. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：
   
      ```csharp
      using System.Threading;
      using Microsoft.ServiceBus.Messaging;
      ```
2. 將下列欄位新增至 **Program** 類別，並以您在上一節中建立的事件中樞名稱替代預留位置值，以及您先前儲存的命名空間層級連接字串。
   
        ```csharp
        static string eventHubName = "Your Event Hub name";
        static string connectionString = "namespace connection string";
        ```
3. 將下列方法加入至 **Program** 類別：
   
      ```csharp
      static void SendingRandomMessages()
      {
          var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
          while (true)
          {
              try
              {
                  var message = Guid.NewGuid().ToString();
                  Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                  eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
              }
              catch (Exception exception)
              {
                  Console.ForegroundColor = ConsoleColor.Red;
                  Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                  Console.ResetColor();
              }
       
              Thread.Sleep(200);
          }
      }
      ```
   
      這個方法會以 200 毫秒的延遲時間持續將事件傳送至事件中心。
4. 最後，將下列幾行新增至 **Main** 方法：
   
      ```csharp
      Console.WriteLine("Press Ctrl-C to stop the sender process");
      Console.WriteLine("Press Enter to start now");
      Console.ReadLine();
      SendingRandomMessages();
      ```
5. 執行程式，並確定沒有任何錯誤。
  
恭喜！ 您現在已將傳送訊息到事件中樞。

## <a name="next-steps"></a>後續步驟
在本快速入門中，您已使用 .NET Framework 將訊息傳送到事件中樞。 若要了解如何使用 .NET Framework 從事件中樞接收事件，請參閱[從事件中樞接收事件 - .NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md)。

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

