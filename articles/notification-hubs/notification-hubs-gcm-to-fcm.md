---
title: Azure 通知中樞和 Google Firebase 雲端通訊 (FCM) 移轉
description: 說明 Azure 通知中樞如何解決 FCM 移轉至 Google GCM。
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: jowargo
ms.openlocfilehash: 4cbfc67bc66e84b4743f3326db40872241e5d474
ms.sourcegitcommit: f24b62e352e0512dfa2897362021b42e0cb9549d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/11/2019
ms.locfileid: "59506396"
---
# <a name="azure-notification-hubs-and-the-google-firebase-cloud-messaging-fcm-migration"></a>Azure 通知中樞和 Google Firebase 雲端通訊 (FCM) 移轉

## <a name="current-state"></a>目前狀態

當 Google 宣布其移轉從 Google 雲端通訊 (GCM) 至 Firebase 雲端通訊 (FCM)，我們必須調整我們如何傳送通知至 Android 裝置，以因應變更推播服務。

我們更新我們的服務後端，然後發佈到我們的 API 和 Sdk 所需的更新。 使用我們的實作中，我們擬定決策來維持與現有客戶的影響降到最低的 GCM 通知結構描述相容性。 這表示我們目前將通知傳送至 Android 裝置使用 FCM FCM 舊版模式。 最後，我們想要新增的 FCM，包括新功能及裝載格式，則為 true 的支援。 這是較長期的變更，和目前的移轉致力於維持與現有的應用程式和 Sdk 相容性。 您可以使用 GCM 或 FCM Sdk （以及我們的 SDK) 的應用程式中，我們會確保會正確地傳送通知。

有些客戶最近收到一封電子郵件，從 Google 的 GCM 端點用於通知的應用程式的相關的警告。 這是只是警告，不會中斷 – 您的應用程式的 Android 通知仍會傳送至 Google 進行處理和 Google 仍會處理它們。 有些客戶在其服務組態中明確指定 GCM 端點仍使用已被取代的端點。 我們已識別出此鴻溝，並努力修正此問題，Google 傳送電子郵件時。

我們已取代該已被取代的端點，並部署修正程式。

## <a name="going-forward"></a>從現在開始

Google 的 FCM 常見問題集是說您不需要採取任何動作。 在  [FCM 常見問題集](https://developers.google.com/cloud-messaging/faq)，Google 說 「 用戶端 Sdk 和 GCM 語彙基元會繼續無限期地運作。 不過，您無法為目標的 Google Play 服務 Android 應用程式中的最新版本，除非您將移轉至 FCM。 」

如果您的應用程式使用 GCM 程式庫，請繼續進行，並遵循升級至 FCM 程式庫，在您的應用程式的 Google 的指示。 我們的 SDK 與都相容，因此您不需要更新我們這一端的應用程式中的任何項目 （只要您是我們的 SDK 版本最新狀態）。

## <a name="questions-and-answers"></a>問與答

以下是一些常見問題的答案我們從客戶聽過：

**問：** 我要做為相容的截止日期的項目 (Google 的目前截止日期是 5 月 29 日，並可能變更)？

**答：** 不做任何動作。 我們會維持與現有的 GCM 通知結構描述的相容性。 GCM 金鑰仍能運作正常，將任何 GCM Sdk 和應用程式所使用的程式庫。

即使您決定要升級至 FCM Sdk 和程式庫，以善用新功能、 GCM 金鑰仍可運作。 您可能會切換成使用 FCM 索引鍵，如果您想，但請確定您要 Firebase 新增至您現有的 GCM 專案建立新的 Firebase 專案時。 這樣會保證與客戶執行的應用程式仍然使用 GCM Sdk 和程式庫的較舊版本的回溯相容性。

如果您是建立新的 FCM 專案，並且未附加至現有的 GCM 專案中，以新的 FCM 密碼更新通知中樞後，您將無法將通知推播到您目前的應用程式安裝，因為新的 FCM 金鑰有沒有連結至舊的 GCM專案。

**問：** 為什麼我會收到這封電子郵件有關所使用的舊 GCM 端點？ 我不得不？

**答：** 不做任何動作。 我們已將其移轉至新的端點，並即將完成，因此不不需要任何變更。 不會中斷，我們有一個遺漏的端點只將來自 Google 造成警告訊息。

**問：** 如何才能轉至新的 FCM Sdk 和程式庫而不會中斷現有的使用者？

答：隨時升級。 Google 也還沒有宣佈現有 GCM Sdk 和程式庫的任何已被取代。 若要確保您不會中斷推播通知給您現有的使用者，請確定當您建立新的 Firebase 專案，您要與您現有的 GCM 專案產生關聯。 這可確保新的 Firebase 密碼適用於 GCM Sdk 和程式庫，執行較舊版本的應用程式的使用者，以及使用 FCM Sdk 和程式庫的應用程式的新使用者。

**問：** 何時我的通知使用 FCM 的新功能和結構描述？

**答：** 一旦我們將更新發佈到我們的 API 和 Sdk，但請密切注意 – 我們希望能有任何您未來的幾個月。
