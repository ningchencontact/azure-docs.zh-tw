---
title: 在您的行動應用程式中使用 Visual Studio App Center 和 Azure 通知中樞推播通知的重要性
description: 深入瞭解可讓您與行動應用程式使用者互動的服務（例如 App Center）。
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 05a9bc6ccd26aaecd3e0bf615880e0543b8604f7
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795909"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>藉由傳送推播通知與應用程式使用者互動 

無論您是要建立取用者或企業應用程式，都希望使用者主動使用您的應用程式。 有時候您會想要分享即時新聞、遊戲更新、令人興奮的供應專案、產品更新或任何與您的使用者相關的資訊。 為了與您的使用者進行互動，並持續讓他們回到您的應用程式，您需要一種方式來即時與您的使用者進行通訊。

推播通知提供快速且有效率的方式，與您的應用程式使用者進行通訊。 您可以在正確的時間與使用者聯繫，並且可以通知他們所需的資訊（通常是在行動裝置上的快顯視窗或對話方塊中），不論他們正在做什麼。

## <a name="value-of-push-notifications"></a>推播通知的值
推播通知可為使用者和企業提供價值。

企業可以：
- **直接與使用者通訊**，方法是在支援的平臺上，于適當時間傳送訊息給他們。
- 將即時更新和提醒傳送給您的使用者，鼓勵他們定期與您的應用程式互動，藉此**提升使用者參與**度。
- **保留使用者**，並重新與已下載應用程式但不會使用它重新作用的非作用中使用者進行互動。
- 藉由分析使用者行為、分割使用者，以及根據行動推播通知來運用行銷活動，以**提高轉換率**。
- 將推播訊息和及時更新傳送給使用者，以**推廣產品和服務**。
- 藉由傳送個人化推播訊息 **，將使用者設為目標**。

針對應用程式使用者，推播通知：
- 即時**提供價值和資訊**。
- **提醒使用者**使用應用程式。

使用下列服務在您的行動應用程式中啟用推播通知。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center 推](/appcenter/push/)播服務可讓您將目標訊息傳送給 IOS、Android 和 Windows 使用者，藉此與使用者互動，而不需要管理使用推播 NOTIFICATION SERVICES （PNS）將通知傳送至裝置的程式。 這項服務以 Azure 通知中樞為基礎，藉由提供強大的儀表板，消除與推播通知相關的複雜性。

**主要功能**
- **將推播通知傳送至**各種平臺上的行動裝置。
- 使用通知將資料傳送至應用程式、向使用者顯示訊息，或由應用程式觸發動作。
- 通知目標： 
    - 將廣播訊息傳送至**所有已註冊的裝置**。
    - 根據裝置資訊和自訂屬性，將通知傳送**給建立的**物件。
    - 將通知傳送給**特定使用者**。
    - 將通知傳送至**特定裝置**。
- App Center 入口網站中提供了推播、裝置、錯誤的**豐富遙測**。
- **平臺支援**-IOS、Android、MacOS、Xamarin、React Native、Unity、Cordova。

**參考**
- [使用 App Center 註冊](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [開始使用 App Center Push](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Azure 通知中心
[通知中樞](/azure/notification-hubs/notification-hubs-push-notification-overview)提供容易使用且向外延展的推播引擎，可讓您將通知傳送至任何平臺及任何後端（雲端或內部部署）。

**主要功能**
- 從雲端或內部部署的任何後端，**將推播通知傳送**至任何平臺。
- 使用單一 API 呼叫將**快速廣播推播**到數百萬個行動裝置。
- 依據客戶、語言和位置量身打造推播通知。
- 動態定義及通知**客戶區段**。
- **立即擴充**到數百萬個行動裝置。
- **平臺支援**-IOS、Android、Windows、Kindle、百度。
        
**參考**
- [Azure 入口網站](https://portal.azure.com) 
- [開始使用 Azure 通知中樞](/azure/notification-hubs/)   
- [快速入門](/azure/notification-hubs/create-notification-hub-portal)
- [範例](/azure/notification-hubs/samples)
