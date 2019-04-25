---
title: 包含檔案
description: 包含檔案
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
origin.date: 03/30/2018
ms.date: 04/08/2019
ms.author: v-biyu
ms.custom: include file
ms.openlocfilehash: ce8496596f25b85719b8a6dff849ebf0fc3e5dc3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60560614"
---
在本節中，您會從 .NET 主控台應用程式將即時新聞以加註標記的範本通知形式傳送。 

1. 在 Visual Studio 中，建立新的 Visual C# 主控台應用程式：a. 在主功能表上，選取 [檔案] > [新增] > [專案]。
    b. 展開 [Visual C#]，然後選取 [Windows 桌面]。 
    c. 選取範本清單中的 [主控台應用程式 (.NET Framework)]。 
    d. 輸入應用程式的**名稱**。 
    e. 選取應用程式的**資料夾**。
    f. 選取 [確定] 以建立專案。 
2. 在 Visual Studio 主功能表上，選取 [工具] > [NuGet 套件管理員] >  [套件管理員主控台]，然後在主控台視窗中輸入下列字串：
   
    ```
    Install-Package Microsoft.Azure.NotificationHubs
    ```
   
3. 選取 [輸入]。  
    此動作會使用 [Microsoft.Azure.Notification Hubs NuGet 套件]來新增對 Azure 通知中樞 SDK 的參考。

4. 開啟 Program.cs 檔案，並新增下列 `using` 陳述式：
   
    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

5. 在 `Program` 類別中，新增或取代 (如果方法已存在) 下列方法：
   
    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

        // Send the notification as a template notification. All template registrations that contain
        // "messageParam" and the proper tags will receive the notifications.
        // This includes APNS, WNS, and MPNS template registrations.

        Dictionary<string, string> templateParams = new Dictionary<string, string>();

        foreach (var category in categories)
        {
            templateParams["messageParam"] = "Breaking " + category + " News!";
            await hub.SendTemplateNotificationAsync(templateParams, category);
        }
    }
    ```   
   
    此程式碼會分別將範本通知傳送給字串陣列中的六個標籤。 使用標籤可確保裝置只會收到已登錄類別的通知。

5. 在上述程式碼中，請使用您的通知中樞名稱及通知中樞儀表板的 *DefaultFullSharedAccessSignature* 連接字串，來取代 `<hub name>` 和 `<connection string with full access>` 預留位置。

6. 在 [主要] 方法中新增下列程式碼行：
   
    ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

7. 建置主控台應用程式。

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Add push notifications for Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Microsoft.Azure.Notification Hubs NuGet 套件]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
