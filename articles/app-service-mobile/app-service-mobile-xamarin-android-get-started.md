---
title: 開始使用適用於 Xamarin.Android 應用程式的Azure 行動應用程式
description: 遵循此教學課程，可開始使用 Azure Mobile Apps 進行 Xamarin Android 開發。
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: a94b302ad813094cb4ce67e4bffc3dd43586366b
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027052"
---
# <a name="create-a-xamarinandroid-app"></a>建立 Xamarin.Android 應用程式
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center 支援行動應用程式開發的端對端和整合式服務中心。 開發人員可以使用**組建**、**測試**和**散發**服務來設定持續整合和傳遞管線。 部署應用程式之後，開發人員可以使用**分析**和**診斷**服務來監視其應用程式的狀態和使用，並與使用**推**播服務的使用者互動。 開發人員也可以利用**驗證**來驗證其使用者和**資料**服務，以保存及同步雲端中的應用程式資料。
> 如果您想要在您的行動應用程式中整合雲端服務，請立即註冊 App Center [App center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) 。

## <a name="overview"></a>總覽
本教學課程將示範如何將雲端後端服務加入至 Xamarin.Android 應用程式。 如需詳細資訊，請參閱 [什麼是 Mobile Apps？](app-service-mobile-value-prop.md)。

以下是完成之應用程式的螢幕擷取畫面：

![][0]

完成本教學課程是 Xamarin Android 應用程式所有其他行動應用程式教學課程的必要條件。

## <a name="prerequisites"></a>必要條件
若要完成本教學課程，您需要下列必要條件：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，請註冊 Azure 試用版並取得最多 10 個免費的 Mobile Apps。 如需詳細資訊，請參閱 < [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* Visual Studio 和 Xamarin。 如需相關指示，請參閱 [設定和安裝 Visual Studio 和 Xamarin](/visualstudio/cross-platform/setup-and-install) 。

## <a name="create-an-azure-mobile-app-backend"></a>建立 Azure 行動應用程式後端
依照下列步驟建立行動應用程式後端。

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

您現在已佈建 Azure 行動應用程式後端，可供您的行動用戶端應用程式使用。 接下來，下載簡易「待辦事項清單」後端的伺服器專案，然後將專案發佈至 Azure。

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>建立資料庫連接，並設定用戶端和伺服器專案
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinandroid-app"></a>執行 Xamarin Android 應用程式
1. 開啟 [Xamarin] 專案。

2. 移至  [Azure 入口網站](https://portal.azure.com/)，然後流覽至您所建立的行動應用程式。 在 [`Overview`] 分頁上，尋找您的行動應用程式的公用端點 URL。 範例-我的應用程式名稱 "test123" 的 sitename 將會 https://test123.azurewebsites.net 。

3. 開啟此資料夾中 `ToDoActivity.cs` 的檔案-[xamarin]/[z]/[ToDoActivity]。 應用程式名稱為 `ZUMOAPPNAME`。

4. 在 `ToDoActivity` 類別中，將 `ZUMOAPPURL` 變數取代為上述公用端點。

    `const string applicationURL = @"ZUMOAPPURL";`

    滿
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. 按 F5 鍵以部署並執行應用程式。

6. 在應用程式中輸入有意義的文字 (例如 Complete the tutorial)，然後按一下 [新增] 按鈕。

    ![][10]

    要求中的資料會插入 TodoItem 資料表中。 行動應用程式後端會傳回資料表中儲存的項目，而該資料會顯示在清單中。

   > [!NOTE]
   > 您可以檢閱存取行動應用程式後端以查詢與插入資料的程式碼，您可在 ToDoActivity.cs C# 檔案中找到此程式碼。
   
## <a name="troubleshooting"></a>疑難排解
如果在建置解決方案時發生問題，請執行 NuGet 套件管理員，並更新 `Xamarin.Android` 支援套件。 快速入門專案不一定會包含最新版本。

請注意，您專案中參考的所有支援套件都必須有相同的版本。 [Azure Mobile Apps NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) 有 Android 平台的 `Xamarin.Android.Support.CustomTabs` 相依性，因此若您的專案使用較新的支援套件，您必須直接安裝具有必要版本的此套件以避免衝突。

<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png
<!-- URLs. -->
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
