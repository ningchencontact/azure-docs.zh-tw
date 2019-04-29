---
title: 診斷 Azure 通知中樞卸除的通知
description: 了解如何透過 Azure 通知中樞卸除的通知診斷常見問題。
services: notification-hubs
documentationcenter: Mobile
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 04/04/2019
ms.author: jowargo
ms.openlocfilehash: 4fc4175c03baa4ddb81507dd4001fcdbe7c7058b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61458641"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>診斷 Azure 通知中樞卸除的通知

Azure 通知中樞相關常見的問題是如何針對從應用程式的通知不會出現在用戶端裝置上時進行疑難排解。 客戶想要知道位置及原因而捨棄通知，以及如何修正此問題。 本文介紹卸除通知的可能原因，或裝置未收到通知的可能原因。 它也會說明如何判斷根本原因。

請務必先了解通知中樞如何推播通知至裝置。

![通知中樞架構][0]

一般傳送通知流程中，訊息會從*應用程式後端*傳送至通知中樞。 通知中樞會處理所有註冊。 它會納入考量，已設定的標記和標記運算式，以判斷目標。 目標是需要接收推播通知註冊。 這些註冊可以跨越任何我們支援的平台：Android 百度 （在中國的 Android 裝置）、 火災 OS (Amazon) iOS、 Windows、 和 Windows Phone。

透過建立目標，通知中樞會將推播通知到裝置平台的*推播通知服務*。 範例包括適用於 Apple 的 Apple Push Notification service (APNs)，以及適用於 Google 的 Firebase Cloud Messaging (FCM)。 通知中樞跨多個批次註冊推播通知。 它使用各個推播通知服務，根據您設定在 Azure 入口網站中下方的認證進行驗證**設定通知中樞**。 隨後，推播通知服務會將通知轉送到各個*用戶端裝置*。

通知傳遞的最終階段是平台的推播通知服務與裝置之間。 通知傳遞無法在任何推播通知程序 （用戶端、 應用程式後端、 通知中樞，以及平台的推播通知服務） 中的四個階段。 如需更多通知中樞架構的相關資訊，請參閱[通知中樞概觀]。

若要將通知傳遞失敗可能會發生在初始測試/暫存階段。 在此階段卸除的通知可能表示設定有問題。 如果在生產環境中發生，將通知傳遞失敗，但是部分或全部通知可能會被捨棄。 更深入的應用程式或傳訊模式問題會在此情況下表示。

下一節探討所在通知可能會被捨棄，範圍從常見到罕見的案例。

## <a name="notification-hubs-misconfiguration"></a>通知中樞設定錯誤 ##

若要將通知傳送給各個推播通知服務，通知中樞必須驗證本身的應用程式內容中。 您必須建立開發人員帳戶的目標平台通知服務 （Microsoft、 Apple、 Google 等）。 然後，您必須將您的應用程式向您取得的權杖或金鑰，您用來處理與目標 PNS 的 OS。

您必須將平台認證新增至 Azure 入口網站。 如果裝置未不收到任何通知，第一個步驟是確保正確的認證已設定通知中樞。 憑證必須符合特定平台開發人員帳戶下建立的應用程式。

若要完成此程序的逐步指示，請參閱[開始使用 Azure 通知中樞]。

以下是一些常見的錯誤設定檢查：

### <a name="notification-hub-name-location"></a>通知中樞名稱的位置

確認通知中樞名稱與以下各項位置相同 (無錯別字)：
   * 讓您註冊從用戶端
   * 傳送通知的位置從後端
   * 設定推播通知服務認證的位置

請確定您在用戶端上使用正確的共用的存取簽章設定字串，且應用程式後端。 一般而言，您必須使用**DefaultListenSharedAccessSignature**用戶端上並**DefaultFullSharedAccessSignature**應用程式後端。 這會授與將通知傳送至通知中樞的權限。

### <a name="apn-configuration"></a>APN 組態 ###

您必須維護兩個不同的中樞： 一個用於生產環境，另一個用於測試。 您必須上傳您在個別的中樞，您將在生產環境中使用的憑證/中樞比的沙箱環境中使用的憑證。 請勿嘗試將不同類型的憑證上傳至相同的中樞。 它會導致通知失敗。

如果不小心將不同類型的憑證上傳至相同的中樞，您應該刪除中樞，並使用新的中樞重新開始。 如果基於某些原因無法刪除中樞，則您至少必須從中樞刪除所有現有註冊。

### <a name="fcm-configuration"></a>FCM 設定 ###

1. 請確認*伺服器金鑰*從 Firebase 相符項目取得您在 Azure 入口網站中註冊的伺服器金鑰。

   ![Firebase 伺服器金鑰][3]

2. 確認您已在用戶端上設定**專案識別碼**。 您可以從 Firebase 儀表板取得**專案識別碼**的值。

   ![Firebase 專案識別碼][1]

## <a name="application-issues"></a>應用程式問題 ##

### <a name="tags-and-tag-expressions"></a>標記與標記運算式 ###

如果您使用標記或標記運算式來區分對象時，就可以時傳送通知時，會找到任何目標。 此錯誤根據指定的標記或標記運算式，在傳送呼叫中。

檢閱註冊以確認標記相符時傳送通知。 然後，確認來自具有這些註冊用戶端通知回條。

例如，假設您使用通知中樞的所有註冊都使用標記 「 政治 」。 如果您再傳送標記通知 「 體育 」，將不會傳送通知到任何裝置。 複雜的案例可能涉及您註冊使用"Tag A"的所在的標記運算式*或*"Tag B"，但您的目標 」 標記 & & Tag B" 自我診斷秘訣一節，在本文稍後會說明如何檢閱您的註冊和其標記。

### <a name="template-issues"></a>範本問題 ###

如果您使用範本，請確定遵循[範本]中所述的指導方針。

### <a name="invalid-registrations"></a>無效的註冊 ###

如果已正確設定通知中樞，並正確地使用標記或標記運算式，會找到有效的目標。 通知應該傳送給這些目標。 然後，通知中樞會以並行的方式一連串地傳送數個處理批次。 每個批次會將訊息傳送至一組註冊。

> [!NOTE]
> 通知中樞處理平行批次，因此無法保證通知的傳遞順序。

通知中樞是 「-大多數-一次 」 訊息傳遞模式最佳化。 我們會嘗試刪除重複的項目，避免系統將通知多次傳遞給同一部裝置。 註冊會檢查以確保只有一個訊息之前傳送至推播通知服務會傳送每一裝置的識別碼。

每個批次會傳送至推播通知服務，而後者會接受和驗證註冊。 在此過程中，就可以推播通知服務會偵測批次中的一或多個註冊錯誤。 推播通知服務接著會將錯誤傳回通知中樞，並停止處理程序。 推播通知服務會完全卸除該批次。 這是特別有用的 apns 來說，它會使用 TCP 串流通訊協定。

在此情況下，從資料庫移除失敗的註冊。 然後，我們會針對該批次中的其餘裝置，重試通知傳遞。

若要获取有关针对注册的失败传递尝试的更多错误信息，可以使用通知中心 REST API [按消息遥测：获取通知消息遥测数据](https://msdn.microsoft.com/library/azure/mt608135.aspx)和 [PNS 反馈](https://msdn.microsoft.com/library/azure/mt705560.aspx)。 如需範例程式碼，請參閱[傳送 REST 範例](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/)。

## <a name="push-notification-service-issues"></a>推播通知服務問題

推播通知服務接收通知之後，它會傳送通知給裝置。 此時，通知中樞有裝置的通知傳遞無法控制。

因為平台通知服務已臻完備，因此通知經常觸及更多在幾秒鐘的時間中的裝置。 如果推播通知服務正在進行節流，通知中樞會套用指數退避法策略。 如果推播通知服務仍無法連線 30 分鐘內，有原則會在到期並永久捨棄訊息的位置。

如果推播通知服務嘗試傳遞通知但裝置已離線，推播通知服務會儲存通知。 它會儲存在有限期間內的時間。 並且在可使用裝置時，再行傳遞。

每個應用程式會儲存只有一個最近的通知。 如果裝置離線時傳送多則通知，每個新的通知都會導致捨棄的最後一個。 保持最新的通知會呼叫*聯合*在 APNs 中並*摺疊*FCM 中。 （FCM 使用摺疊索引鍵）。當裝置處於離線狀態很長的時間時，則會捨棄已儲存裝置的通知。 如需詳細資訊，請參閱 < [APNs 概觀]並[關於 FCM 訊息]。

使用通知中樞，您可以使用一般 SendNotification API 透過 HTTP 標頭傳遞聯合索引鍵。 例如，對於 .NET SDK，您會使用 `SendNotificationAsync`。 SendNotification API 也會傳遞到各個推播通知服務的 HTTP 標頭。

## <a name="self-diagnosis-tips"></a>自我診斷秘訣

下面介绍了诊断通知中心已删除通知的根本原因的各种途径。

### <a name="verify-credentials"></a>驗證認證 ###

#### <a name="push-notification-service-developer-portal"></a>推播通知服務開發人員入口網站 ####

確認個別推播通知服務開發人員入口網站 (APNs、FCM、Windows 通知服務等等) 中的認證。 如需詳細資訊，請參閱[教學課程：使用 Azure 通知中樞將通知傳送至通用 Windows 平台應用程式](https://docs.microsoft.com/en-us/azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification)。

#### <a name="azure-portal"></a>Azure 入口網站 ####

若要檢閱並符合與您從推播通知服務開發人員入口網站取得的認證，請移至**存取原則**在 Azure 入口網站中的索引標籤。

![Azure 入口網站存取原則][4]

### <a name="verify-registrations"></a>驗證註冊

#### <a name="visual-studio"></a>Visual Studio ####

在 Visual Studio 中，您可以連線至 Azure，透過 [伺服器總管] 來檢視和管理多項 Azure 服務，包括通知中樞。 這個捷徑是主要用於開發/測試環境。

![Visual Studio 伺服器總管][9]

您可以檢視和管理您的中樞內的所有註冊。 註冊可以分類的平台、 原生或範本註冊、 標記、 推播通知服務識別碼，註冊識別碼和到期日。 您也可以在此頁面上編輯註冊。 它对于编辑标记特别有用。

以滑鼠右鍵按一下您的通知中樞，在**伺服器總管**，然後選取**診斷**。 

![Visual Studio 伺服器總管：診斷 功能表](./media/notification-hubs-diagnosing/diagnose-menu.png)

会看到以下页面：

![Visual Studio：診斷頁面](./media/notification-hubs-diagnosing/diagnose-page.png)

切换到“设备注册”页：

![Visual Studio：裝置註冊](./media/notification-hubs-diagnosing/VSRegistrations.png)

若要发送测试通知消息，可以使用“测试性发送”页：

![Visual Studio：測試傳送](./media/notification-hubs-diagnosing/test-send-vs.png)

> [!NOTE]
> 使用 Visual Studio 編輯註冊，只在開發/測試，且註冊數量有限。 如果您要編輯大量註冊，請考慮使用匯出和匯入中所述的註冊功能[How To:匯出及修改註冊大量](https://msdn.microsoft.com/library/dn790624.aspx)。

#### <a name="service-bus-explorer"></a>服務匯流排總管 ####

許多客戶會使用[Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer)來檢視和管理其通知中樞。 服務匯流排總管是開放原始碼專案。 

### <a name="verify-message-notifications"></a>驗證訊息通知

#### <a name="azure-portal"></a>Azure 入口網站 ####

若要將測試通知傳送到您的用戶端，而不需要運作後端服務，請在 [支援 + 疑難排解] 下，選取 [測試傳送]。

![測試 Azure 中的傳送功能][7]

#### <a name="visual-studio"></a>Visual Studio ####

您也可以從 Visual Studio 傳送測試通知。

![測試 Visual Studio 中的傳送功能][10]

如需更多使用通知中樞與 Visual Studio 伺服器總管的相關資訊，請參閱下列文章：

* [如何檢視通知中樞的裝置註冊](https://docs.microsoft.com/en-us/previous-versions/windows/apps/dn792122(v=win.10))
* [深入探討：Visual Studio 2013 Update 2 RC 和 Azure SDK 2.3]
* [宣佈發行 Visual Studio 2013 Update 3 和 Azure SDK 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>偵錯失敗的通知並檢閱通知結果

#### <a name="enabletestsend-property"></a>EnableTestSend 屬性 ####

當您傳送通知，以透過通知中樞時，通知是一開始已排入佇列。 通知中樞會確定正確的目標，然後將通知傳送至推播通知服務。 如果您使用 REST API 或任何用戶端 Sdk，在傳回傳送呼叫只是表示郵件已排入佇列使用通知中樞。 它並不提供深入了解通知中樞最終將通知傳送至推播通知服務時，發生了什麼事。

如果您的通知未抵達用戶端裝置，通知中樞嘗試將它傳送給推播通知服務時，可能已發生錯誤。 例如，承載大小可能會超過推播通知服務允許的上限，或通知中樞中設定的認證可能無效。

若要深入了解推播通知服務的錯誤，您可以使用 [EnableTestSend] 屬性。 當您從入口網站或 Visual Studio 用戶端傳送測試訊息時，會自動啟用該屬性。 您可以使用這個屬性，以查看詳細的偵錯資訊同時也會透過 Api。 目前，您可以在 .NET SDK 中使用該屬性。 它會加入所有用戶端 Sdk 最終。

若要搭配使用 `EnableTestSend` 屬性與 REST 呼叫，請在傳送呼叫結尾附加名為 test 的查詢字串參數。 例如︰

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>.NET SDK 範例 ####

以下是使用 .NET SDK 傳送原生快顯視窗 (快顯) 通知的範例：

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

在執行的結尾處，`result.State` 只會指出`Enqueued`。 結果不會提供任何深入了解推播通知發生了什麼事。

接著，您可以使用 `EnableTestSend` 布林值屬性。 初始化 `NotificationHubClient` 時，請使用 `EnableTestSend` 屬性，以取得關於傳送通知時發生的推播通知服務錯誤詳細狀態。 傳送呼叫需要更多時間才能傳回，因為它必須先通知中樞將通知傳遞給推播通知服務。

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

#### <a name="sample-output"></a>範例輸出 ####

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

此訊息表示在通知中樞設定的認證不正確，或沒有在中樞的註冊問題。 刪除此註冊並讓用戶端重新傳送訊息之前，先建立註冊。

> [!NOTE]
> 使用 `EnableTestSend` 屬性會進行大幅度的節流處理。 只會在開發/測試環境與一組有限的註冊，請使用此選項。 偵錯通知會傳送到只有 10 個裝置。 在處理中偵錯傳送，在每分鐘 10 次也有所限制。

### <a name="review-telemetry"></a>檢閱遙測 ###

#### <a name="azure-portal"></a>Azure 入口網站 ####

在入口網站中，您可以取得通知中樞上所有活動的簡要概觀。

1. 在 [概觀] 索引標籤上，您可以依平台查看註冊、通知和錯誤的彙總檢視。

   ![通知中樞概觀儀表板][5]

2. 在 [監視器] 索引標籤上，您可以新增許多其他平台特定度量以獲得更深入的了解。 您可以專門查看通知中樞嘗試將通知傳送至推播通知服務時所傳回的錯誤。

   ![Azure 入口網站活動記錄][6]

3. 首先，檢閱**內送郵件**、**註冊作業**，以及**成功通知**。 然後，移至每個平台的索引標籤，以檢閱特定於推播通知服務的錯誤。

4. 如果您通知中樞的驗證設定不正確，會出現 **PNS 驗證錯誤**訊息。 它表示要检查推送通知服务凭据。

#### <a name="programmatic-access"></a>以程式設計方式存取 ####

如需有關以程式設計方式存取的詳細資訊，請參閱 <<c0> [ 以程式設計方式存取](https://docs.microsoft.com/en-us/previous-versions/azure/azure-services/dn458823(v=azure.100))。

> [!NOTE]
> 幾個與遙測有關的功能，例如匯出和匯入註冊，以及透過 API 進行遙測存取，只能在標準服務層級上使用。 如果您嘗試使用這些功能，從免費或基本服務層時，如果您使用 SDK，您會收到例外狀況訊息。 如果您使用直接從 REST Api 的功能，您會收到 HTTP 403 （禁止） 錯誤。
>
> 若要使用遙測相關功能，請先確定在 Azure 入口網站中，您使用標準服務層。  

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
[通知中樞概觀]: notification-hubs-push-notification-overview.md
[開始使用 Azure 通知中樞]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[範本]: https://msdn.microsoft.com/library/dn530748.aspx
[APNs 概觀]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[關於 FCM 訊息]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: https://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: https://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[深入探討：Visual Studio 2013 Update 2 RC 和 Azure SDK 2.3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[宣佈發行 Visual Studio 2013 Update 3 和 Azure SDK 2.4]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Programmatic telemetry access]: https://msdn.microsoft.com/library/azure/dn458823.aspx
