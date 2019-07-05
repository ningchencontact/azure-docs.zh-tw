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
ms.openlocfilehash: 57867eeca9f29cfc3983cbdca94c830aa7a20500
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446252"
---
# <a name="create-a-xamarinios-app"></a>建立 Xamarin.iOS 應用程式
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center 投入新的和整合式服務行動應用程式開發的核心。 開發人員可以使用**建置**，**測試**並**散發**services 設定持續整合和傳遞管線。 應用程式部署之後，開發人員可以監視的狀態和其應用程式使用的使用方式**Analytics**並**診斷**服務，並使用使用者參與**推播**服務。 開發人員也可以利用**Auth**來驗證使用者並**資料**保存和同步處理雲端中的應用程式資料的服務。 請參閱[App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-ios-get-started)今天。
>

## <a name="overview"></a>概觀
本教學課程說明如何使用 Azure 行動 app 後端，將雲端型後端服務加入至 Xamarin.iOS 行動 app。  您會建立新的行動應用程式後端，以及可在 Azure 中儲存應用程式資料的簡易「待辦事項清單」  Xaamrin.iOS 應用程式。

您必須先完成此教學課程，才能進行所有其他在 Azure App Service 中使用 Mobile Apps 的 Xamarin.iOS 相關教學課程。

## <a name="prerequisites"></a>必要條件
若要完成本教學課程，您需要下列必要條件：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，請註冊 Azure 試用版並取得最多 10 個免費的行動應用程式，即使在試用期結束之後仍可繼續使用這些應用程式。 如需詳細資訊，請參閱 < [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* Visual Studio for Mac. 請參閱[設定和安裝 Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* Mac Xcode 9.0 或更新版本。
  
## <a name="create-an-azure-mobile-app-backend"></a>建立 Azure 行動應用程式後端
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>建立資料庫連接，並設定用戶端和伺服器專案
[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinios-app"></a>執行 Xamarin.iOS 應用程式
1. 開啟 Xamarin.iOS 專案。

2. 移至[Azure 入口網站](https://portal.azure.com/)並瀏覽至您所建立的行動應用程式。 在 `Overview`刀鋒視窗中，搜尋 URL，也就是您的行動裝置應用程式的公用端點。 範例-將會針對我的應用程式名稱"test123"sitename https://test123.azurewebsites.net 。

3. 開啟檔案`QSTodoService.cs`在此資料夾中-xamarin.iOS/ZUMOAPPNAME。 應用程式名稱是`ZUMOAPPNAME`。

4. 在 `QSTodoService`類別中，取代`ZUMOAPPURL`變數與上述的公用端點。

    `const string applicationURL = @"ZUMOAPPURL";`

    會變成
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. 按 F5 鍵來部署，並在 iPhone 模擬器中執行應用程式。

6. 在應用程式中，輸入有意義的文字，例如*完成本教學課程*，然後按一下 [+] 按鈕。

    ![][10]

    要求中的資料會插入 TodoItem 資料表中。 行動應用程式後端會傳回資料表中儲存的項目，而該資料會顯示在清單中。

   > [!NOTE]
   > 您可以檢閱存取行動應用程式後端以查詢與插入資料的程式碼，您可在 ToDoActivity.cs C# 檔案中找到此程式碼。
   
<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png