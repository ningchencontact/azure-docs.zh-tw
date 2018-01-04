---
title: "Azure 通知中樞卸除的通知診斷"
description: "了解如何診斷 Azure 通知中心中的已卸除通知的一般問題。"
services: notification-hubs
documentationcenter: Mobile
author: jwhitedev
manager: kpiteira
editor: 
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 47808fcdb419dd44cfbd19ac7882b78b4c846b2c
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/03/2018
---
# <a name="diagnose-dropped-notifications-in-notification-hubs"></a>診斷在通知中樞已卸除的通知

如何疑難排解當從應用程式所傳送的通知，不會出現在用戶端裝置上其中一個最常見的問題，從 Azure 通知中心的客戶。 他們想要知道位置及原因已被卸除的通知，以及如何修正此問題。 這篇文章識別通知可能會卸除或裝置所接收的原因。 了解如何分析，並判斷根本原因。 

請務必先了解如何通知中樞將通知推送至裝置。

![通知中心架構][0]

資料流程中的一般傳送通知，傳送訊息從*應用程式後端*通知中心。 通知中樞執行一些處理上所有註冊。 處理列入考量的設定的標記和標記運算式來判斷 「 目標 」。 目標是需要接收推播通知的所有註冊。 這些註冊可以跨越任何或所有我們支援的平台： iOS、 Google、 Windows、 Windows Phone，Kindle 和用於中國 Android 百度。

建立目標，通知中樞將推送通知給*推播通知服務*裝置平台。 範例，包括在 Apple Push Notification service (APNs) Apple 和 Firebase 雲端傳訊 (FCM) google。 通知中樞推播通知分成多個批次的註冊。 根據在 Azure 入口網站中設定的認證各自的推播通知服務驗證通知中樞**設定通知中樞**。 推播通知服務會轉送至個別通知*用戶端裝置*。 

請注意通知傳送的最後階段所花費的平台推送通知服務與裝置之間的位置。 任何推播通知處理程序 （用戶端、 應用程式後端、 通知中樞和平台推送通知服務） 中的四個主要元件可能會導致卸除的通知。 如需有關的通知中樞架構的詳細資訊，請參閱[通知中心概觀]。

若要將通知傳遞失敗可能會發生在初始測試執行階段。 已卸除的通知，在這個階段可能表示設定問題。 如果將通知傳遞失敗發生在生產環境中，但是部分或全部的通知可能會被捨棄。 在此情況下，會指出的更深入的應用程式或傳訊模式的問題。 

下一節探討，通知可能會被捨棄，範圍從一般到更少見的案例。

## <a name="notification-hubs-misconfiguration"></a>通知中心設定不正確
已成功傳送通知給個別的推播通知服務，通知中心需要驗證自己的開發人員的應用程式內容中。 要執行此，開發人員會建立個別的平台 （Google、 Apple、 Windows 等等） 開發人員帳戶。 然後，開發人員會使用他們用來取得認證的平台註冊他們的應用程式。 

您必須在 Azure 入口網站加入平台的認證。 如果不收到任何通知裝置，第一個步驟應該是確保通知中心中設定的正確的認證。 憑證必須符合建立以特定平台開發人員帳戶的應用程式。 

若要完成此程序的逐步指示，請參閱[開始使用 Azure 通知中樞]。

以下是一些常見的錯誤設定檢查：

* **一般**
   
    * 請確定您的通知中樞名稱 （不含錯字） 是在這些位置中每個相同：

        * 讓您註冊從用戶端。
        * 位置從後端傳送通知。
        * 設定推播通知服務認證的位置。
    
    * 請確定您使用正確的共用的存取簽章設定字串，在用戶端和應用程式後端。 一般而言，您必須使用**DefaultListenSharedAccessSignature**在用戶端和**DefaultFullSharedAccessSignature**應用程式後的結束 （授與權限將通知傳送至通知中心）。

* **APNs 設定**
   
    您必須維護兩個不同的集線器： 生產環境中，一個中樞及進行測試的另一個中樞。 這表示您必須上傳到個別的憑證和您要在生產環境中使用的中樞比集線器沙箱環境中使用的憑證。 請勿嘗試將不同類型的憑證上傳至相同的中樞。 這可能會導致通知失敗。 
    
    如果不小心將不同類型的憑證上傳至相同的中樞，我們建議您刪除中樞，並與新的中樞重頭開始。 如果基於某些原因您無法刪除的中樞，最少，您必須從中樞刪除所有現有的註冊。 

* **FCM 組態** 
   
    1. 請確認*伺服器金鑰*取自 Firebase 符合您在 Azure 入口網站中註冊的伺服器金鑰。
   
    ![Firebase 伺服器金鑰][3]
   
    2. 確定您已經設定**專案 ID**用戶端上。 您可以取得的值**專案 ID** Firebase 儀表板。
   
    ![Firebase 專案識別碼][1]

## <a name="application-issues"></a>應用程式問題
* **標記和標記運算式**

    如果您使用標記或標記運算式分割您的對象時，很可能當您傳送通知時，沒有目標找到，以標記或您在您傳送的呼叫中指定的標記運算式為基礎。 
    
    檢閱您的註冊，以確保沒有相符的標記時傳送通知。 然後，確認通知回條，只能從有這些登錄的用戶端。 
    
    例如，如果與通知中樞的所有註冊所都產生的使用標記"政治"和"運動，「 傳送標記通知未傳送通知給任何裝置。 複雜的案例可能涉及您註冊使用 「 標記的"或者"標記 B"，標記運算式，但時傳送通知，以目標 」 標記 & & 標記 B 」。 在本文稍後 self-diagnosis 秘訣一節，在我們示範您如何檢閱您的註冊和其標記。 

* **範本問題**

    如果您使用範本，請確定您遵循所述的指導方針[範本]。 

* **無效的註冊**

    如果通知中樞已正確設定，而且已正確地使用任何標記或標記運算式，如果找到有效的目標。 通知傳送給這些目標。 通知中樞，然後關閉數個處理批次，以平行方式引發。 每個批次會將訊息傳送至一組的註冊。 

    > [!NOTE]
    > 處理以平行方式執行，因為無法保證傳遞通知的順序。 

    通知中心適合 「 在最一次 」 訊息的傳遞模型。 我們嘗試刪除重複作業，以便通知將超過一次傳遞至裝置。 若要確保這種情況，我們會檢查登錄，並確定推播通知服務傳送訊息前只能有一個訊息傳送每個裝置識別碼。 

    當每個批次傳送推播通知服務，而後者則會接受和驗證註冊，可能會推播通知服務將會偵測具有一或多個註冊的錯誤，批次中。 在此情況下，通知中樞推播通知服務會傳回錯誤並停止處理程序。 推播通知服務會完全卸除該批次。 特別是使用 APNS，使用 TCP 資料流通訊協定。 

    我們會最佳化適用於大部分在一次的傳遞。 但在此情況下，從資料庫中移除失敗的註冊。 然後，我們會重試傳送通知的裝置，該批次中的其餘部分。

    若要取得針對註冊的傳遞失敗的嘗試的相關錯誤更多資訊，您可以使用通知中心 REST Api[每個訊息遙測： 取得通知訊息遙測](https://msdn.microsoft.com/library/azure/mt608135.aspx)和[PNS 意見反應](https://msdn.microsoft.com/library/azure/mt705560.aspx). 範例程式碼，請參閱[傳送 REST 範例](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample)。

## <a name="push-notification-service-issues"></a>推播通知服務問題
平台推送通知服務已收到通知訊息之後，它負責的推播通知服務，傳遞至裝置的通知。 此時，通知中心超出圖片並沒有任何控制能力時，或將通知傳遞到裝置。 

平台通知服務會穩定，因為通知通常在幾秒鐘的時間達到推播通知服務的裝置。 如果推播通知服務正在進行節流，通知中心會套用指數型輪詢策略。 如果您仍然無法連到 30 分鐘內使用推播通知服務，我們已有原則過期並永久卸除這些訊息。 

如果推播通知服務嘗試傳遞通知，但裝置已離線，通知會儲存推播通知服務一段有限的時間。 當裝置變成可用時，通知被傳遞到裝置。 

每個應用程式，會儲存只有一個新的通知。 如果裝置離線時，會傳送多個通知，每個新的通知會導致捨棄先前的通知。 保持最新的通知指*聯合通知*中 APNs，和*摺疊*FCM （這會使用摺疊的索引鍵） 中。 如果裝置保持離線一段時間，則會捨棄任何儲存裝置的通知。 如需詳細資訊，請參閱[APNs 概觀]和[有關 FCM 訊息]。

與 Azure 通知中心，您可以使用泛型的 SendNotification 應用程式開發介面透過 HTTP 標頭傳遞聯合的索引鍵。 例如，若是.NET SDK，您會使用**SendNotificationAsync**。 SendNotification API 也會使用 HTTP 標頭做為傳遞的是個別的推播通知服務。 

## <a name="self-diagnosis-tips"></a>Self-diagnosis 秘訣
以下是診斷根本原因的已卸除的通知，通知中心中的路徑：

### <a name="verify-credentials"></a>驗證認證
* **推播通知服務開發人員入口網站**
   
    確認個別的推播通知服務開發人員入口網站 （APNs、 FCM、 Windows 通知服務，等等） 中的認證。 如需詳細資訊，請參閱[開始使用 Azure 通知中樞]。

* **Azure 入口網站**
   
    若要檢閱並符合的認證與您取得的推播通知服務開發人員入口網站中，在 Azure 入口網站中，前往**存取原則** 索引標籤。 
   
    ![Azure 入口網站存取原則][4]

### <a name="verify-registrations"></a>驗證註冊
* **Visual Studio**
   
    如果您使用 Visual Studio 進行開發時，您可以連接至 Azure，透過 [伺服器總管] 來檢視和管理多個 Azure 服務，包括通知中樞。 這能為您的開發/測試環境帶來許多助益。 
   
    ![Visual Studio 伺服器總管][9]
   
    您可以檢視和管理您的中樞，以平台，原生分類中的所有註冊或範本註冊、 任何標記、 推播通知服務識別碼、 註冊 ID 和到期日。 您也可以編輯這個頁面上的註冊。 這是特別適用於編輯標記。 
   
    ![Visual Studio 裝置註冊][8]
   
   > [!NOTE]
   > 使用 Visual Studio 來編輯登錄，只在開發/測試，和與有限數目的註冊。 如果您要編輯您的大量的註冊，請考慮使用匯出和匯入中所述的註冊功能[匯出及修改大量註冊](https://msdn.microsoft.com/library/dn790624.aspx)。
   > 
   > 
* **服務匯流排總管**
   
    許多客戶使用[Service Bus Explorer]來檢視和管理其通知中樞。 Service Bus Explorer 是開放原始碼專案。 如需範例，請參閱[Service Bus Explorer 的程式碼]。

### <a name="verify-message-notifications"></a>驗證訊息通知
* **Azure 入口網站**
   
    若要將測試通知傳送到您的用戶端而不需要服務的後端啟動並執行，在**支援 + 疑難排解**，選取**測試傳送**。 
   
    ![在 Azure 中的測試傳送功能][7]
* **Visual Studio**
   
    您也可以從 Visual Studio 傳送測試通知。
   
    ![在 Visual Studio 中測試傳送功能][10]
   
    如需使用通知中樞與 Visual Studio 伺服器總管的詳細資訊，請參閱下列文章： 
   
   * [檢視裝置註冊通知中樞]
   * [深入探討： Visual Studio 2013 Update 2 RC 和 Azure SDK 2.3]
   * [宣佈發行 Visual Studio 2013 Update 3 和 Azure SDK 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>偵錯失敗的通知，並檢閱通知結果
**EnableTestSend 屬性**

當您一開始傳送通知中樞通知時，通知在佇列中等候通知中心中的處理。 通知中心會決定正確的目標，並便傳送通知給推播通知服務。 如果您使用 REST API 或任何 Sdk 用戶端，傳送呼叫成功傳回只代表訊息已與通知中樞成功佇列。 您不需要任何深入了解最後推播通知服務傳送訊息的通知中樞時，發生了什麼事。 

如果您的通知未抵達用戶端裝置時，它可能會通知中樞嘗試將訊息傳送給推播通知服務時發生錯誤。 例如，承載大小可能會超過推播通知服務，允許的最大值或設定通知中樞中的認證可能無效。 

若要取得深入了解推播通知服務錯誤，您可以使用[EnableTestSend]屬性。 當您從入口網站或 Visual Studio 用戶端傳送測試訊息時，會自動啟用此屬性。 若要查看偵錯的詳細的資訊，您可以使用這個屬性。 您也可以使用透過應用程式開發介面的屬性。 目前，您可以使用.NET SDK 中。 最後，它會加入至所有用戶端 Sdk。 

若要使用**EnableTestSend**屬性與 REST 呼叫，附加查詢字串參數呼叫*測試*傳送呼叫的結尾。 例如︰ 

    https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

**範例 (.NET SDK)**

以下是使用.NET SDK 將通知傳送到原生快顯視窗 （快顯通知） 的範例：

```csharp
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);
```

在執行時，結尾**結果。狀態**只是指出**佇列**。 結果不會提供任何深入了解您的推播通知發生了什麼事。 

接下來，您可以使用**EnableTestSend**布林值屬性。 使用**EnableTestSend**屬性，當您初始化**NotificationHubClient**以取得詳細的狀態，關於推播通知服務發生錯誤時傳送通知。 傳送呼叫會需要額外的時間，因為它會傳回通知中樞已決定結果推播通知服務傳送通知之後，才傳回。 

```csharp
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }
```

**範例輸出**

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong

此訊息表示，通知中心中所設定的認證無效或具有中樞的註冊發生問題。 我們建議您刪除這個註冊，並可讓用戶端重新傳送訊息之前，先建立註冊。 

> [!NOTE]
> 使用**EnableTestSend**屬性有很大節流處理。 只會在開發/測試環境，並以一組有限的註冊，請使用此選項。 我們會傳送偵錯通知到只有 10 部裝置。 我們也會有處理偵錯傳送到每分鐘 10 個的限制。 
> 
> 

### <a name="review-telemetry"></a>檢閱遙測
* **使用 Azure 入口網站**
   
    在網站中，您可以取得通知中樞的所有活動的快速概觀。 
   
    1. 在**概觀**索引標籤上，您可以看到依平台的註冊、 通知和錯誤的彙總的檢視。 
   
        ![通知中心概觀儀表板][5]
   
    2. 在**監視器**索引標籤上，您可以將許多其他平台特定度量，如需深入了解。 您可以查看特別相關推播通知服務通知中樞嘗試將推播通知服務傳送通知時所傳回的任何錯誤。 
   
        ![Azure 入口網站活動記錄檔][6]
   
    3. 首先，檢閱**內送訊息**，**註冊作業**，和**成功通知**。 接著，請移至每個平台 索引標籤，檢閱推播通知服務特有的錯誤。 
   
    4. 如果您的通知中樞的驗證設定不正確，訊息**PNS 驗證錯誤**隨即出現。 這是很好的指標，以檢查推播通知服務認證。 

* **程式設計存取**

    如需有關以程式設計方式存取的詳細資訊，請參閱下列文章： 

    * [以程式設計方式遙測存取]  
    * [遙測存取透過 Api 範例] 

    > [!NOTE]
    > 遙測相關的許多功能，要匯出和匯入的註冊和應用程式開發介面，透過遙測存取是僅適用於 Standard 服務層。 如果您嘗試使用這些功能從免費或基本服務層，如果您使用 SDK，而 HTTP 403 （禁止） 錯誤如果您使用直接從 REST Api 的功能，您會收到例外狀況訊息。 
    >
    >若要使用遙測相關功能，請先確定在 Azure 入口網站使用 Standard 服務層。  
> 
> 

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/FCMConfigure.png
[3]: ./media/notification-hubs-diagnosing/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png

<!-- LINKS -->
[通知中心概觀]: notification-hubs-push-notification-overview.md
[開始使用 Azure 通知中樞]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[範本]: https://msdn.microsoft.com/library/dn530748.aspx 
[APNs 概觀]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[有關 FCM 訊息]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: http://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer]: https://msdn.microsoft.com/library/dn530751.aspx#sb_explorer
[Service Bus Explorer 的程式碼]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[檢視裝置註冊通知中樞]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx 
[深入探討： Visual Studio 2013 Update 2 RC 和 Azure SDK 2.3]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs 
[宣佈發行 Visual Studio 2013 Update 3 和 Azure SDK 2.4]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ 
[EnableTestSend]: https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[以程式設計方式遙測存取]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[遙測存取透過 Api 範例]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel

