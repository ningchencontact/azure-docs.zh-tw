---
title: 在 Xamarin.iOS 應用程式中開始使用 Azure App Service Mobile Apps | Microsoft Docs
description: 遵循本教學課程，開始使用 Mobile Apps 進行 Xamarin.iOS 開發。
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 26095757f8079f43e620e5f067f461f67c7042f9
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72024848"
---
# <a name="create-a-xamarinios-app"></a>建立 Xamarin.iOS 應用程式
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center 支援行動應用程式開發的端對端和整合式服務中心。 開發人員可以使用**組建**、**測試**和**散發**服務來設定持續整合和傳遞管線。 部署應用程式之後，開發人員可以使用**分析**和**診斷**服務來監視其應用程式的狀態和使用，並與使用**推**播服務的使用者互動。 開發人員也可以利用**驗證**來驗證其使用者和**資料**服務，以保存及同步雲端中的應用程式資料。
> 如果您想要在您的行動應用程式中整合雲端服務，請立即註冊 App Center [App center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) 。

## <a name="overview"></a>總覽
本教學課程說明如何使用 Azure 行動 app 後端，將雲端型後端服務加入至 Xamarin.iOS 行動 app。  您會建立新的行動應用程式後端，以及可在 Azure 中儲存應用程式資料的簡易「待辦事項清單」 Xaamrin.iOS 應用程式。

您必須先完成此教學課程，才能進行所有其他在 Azure App Service 中使用 Mobile Apps 的 Xamarin.iOS 相關教學課程。

## <a name="prerequisites"></a>必要條件
若要完成本教學課程，您需要下列必要條件：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，請註冊 Azure 試用版並取得最多 10 個免費的行動應用程式，即使在試用期結束之後仍可繼續使用這些應用程式。 如需詳細資訊，請參閱 < [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* Visual Studio for Mac。 請參閱[設定和安裝以取得 Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* 具有 Xcode 9.0 或更新版本的 Mac。
  
## <a name="create-an-azure-mobile-app-backend"></a>建立 Azure 行動應用程式後端
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>建立資料庫連接，並設定用戶端和伺服器專案
[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinios-app"></a>執行 Xamarin iOS 應用程式
1. 開啟 [Xamarin. iOS] 專案。

2. 移至  [Azure 入口網站](https://portal.azure.com/)，然後流覽至您所建立的行動應用程式。 在 [`Overview`] 分頁上，尋找您的行動應用程式的公用端點 URL。 範例-我的應用程式名稱 "test123" 的 sitename 將會 https://test123.azurewebsites.net 。

3. 開啟此資料夾中 `QSTodoService.cs` 的檔案-[xamarin. iOS/z]。 應用程式名稱為 `ZUMOAPPNAME`。

4. 在 `QSTodoService` 類別中，將 `ZUMOAPPURL` 變數取代為上述公用端點。

    `const string applicationURL = @"ZUMOAPPURL";`

    滿
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. 按 F5 鍵，以在 iPhone 模擬器中部署和執行應用程式。

6. 在應用程式中，輸入有意義的文字，例如*完成教學*課程，然後按一下 [+] 按鈕。

    ![][10]

    要求中的資料會插入 TodoItem 資料表中。 行動應用程式後端會傳回資料表中儲存的項目，而該資料會顯示在清單中。

   > [!NOTE]
   > 您可以檢閱存取行動應用程式後端以查詢與插入資料的程式碼，您可在 ToDoActivity.cs C# 檔案中找到此程式碼。
   
<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png