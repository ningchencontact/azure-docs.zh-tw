---
title: 利用 Xamarin.Forms 開始使用 Mobile Apps
description: 遵循此教學課程，開始使用 Mobile Apps 進行 Xamarin.Forms 開發
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: b0719f6ac2f99f9e665b1265665752dd53ccbaf0
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242669"
---
# <a name="create-a-xamarinforms-app-with-azure"></a>使用 Azure 建立 Xamarin.Forms 應用程式

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

本教學課程說明如何使用 Azure App Service 的 Mobile Apps 功能作為後端，將雲端型後端服務新增到 Xamarin.Forms 行動裝置應用程式。 您會同時建立新的 Mobile Apps 後端，以及可在 Azure 中儲存應用程式資料的簡易待辦事項清單 Xamarin.Forms 應用程式。

完成本教學課程是所有其他 Xamarin.Forms 應用程式的行動應用程式教學課程的必要條件。

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，可以註冊 Azure 試用版並取得最多 10 個免費的行動應用程式，即使在試用期結束之後仍可繼續使用這些應用程式。 如需詳細資訊，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

* Visual Studio Tools for Xamarin，在 Visual Studio 2017 或更新版本或 Visual Studio for mac。 如需指示，請參閱 [Xamarin 安裝頁面][Install Xamarin]。

* (選擇性) 若要建置 iOS 應用程式，則需要安裝了 Xcode 9.0 或更新版本的 Mac。 Visual Studio for Mac 可以用來開發 iOS 應用程式或 Visual Studio 2017 或更新版本可用 （只要 Mac 在網路上可用）。

## <a name="create-a-new-mobile-apps-back-end"></a>建立新的 Mobile Apps 後端
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>建立資料庫連接，並設定用戶端和伺服器專案
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinforms-solution"></a>執行 Xamarin.Forms 方案

需要有 Visual Studio Tools for Xamarin 才能開啟解決方案，請參閱 [Xamarin 安裝指示][Install Xamarin]。 如果工具已安裝好，請遵循下列步驟來下載並開啟解決方案：

### <a name="visual-studio"></a>Visual Studio

1. 移至 [Azure 入口網站](https://portal.azure.com/)。

2. 在行動應用程式的設定刀鋒視窗上，按一下 [快速入門]  \(在 [部署] 之下) > [Xamarin.Forms]  。 在步驟 3 中，按一下 建立新的應用程式  \(如果尚未選取的話)。  接著按一下 [下載]  按鈕。

   此動作會下載包含連線到您行動裝置應用程式之用戶端應用程式的專案。 將此壓縮專案檔案儲存到您的本機電腦，並記錄儲存位置。

3. 您所下載的專案解壓縮，然後在 Visual Studio 中開啟它。

4. 遵循下列指示來執行 Android 或 Windows 專案；如果有可用的連網 Mac 電腦，則可執行 iOS 專案。

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. 移至[Azure 入口網站](https://portal.azure.com/)並瀏覽至您所建立的行動應用程式。 在 `Overview`刀鋒視窗中，搜尋 URL，也就是您的行動裝置應用程式的公用端點。 範例-將會針對我的應用程式名稱"test123"sitename https://test123.azurewebsites.net。

2. 開啟檔案`Constants.cs`在此資料夾中-xamarin.forms/ZUMOAPPNAME。 應用程式名稱是`ZUMOAPPNAME`。

3. 在 `Constants.cs`類別中，取代`ZUMOAPPURL`變數與上述的公用端點。

    `public static string ApplicationURL = @"ZUMOAPPURL";`

    會變成

    `public static string ApplicationURL = @"https://test123.azurewebsites.net";`
    
4. 遵循下列指示來執行 Android 或 Windows 專案；如果有可用的連網 Mac 電腦，則可執行 iOS 專案。

## <a name="optional-run-the-android-project"></a>(選擇性) 執行 Android 專案

在這一節當中，您會執行 Xamarin.Android 專案。 如果未使用 Android 裝置，可以略過這一節。

### <a name="visual-studio"></a>Visual Studio

1. 以滑鼠右鍵按一下 Android (Droid) 專案，然後選取 [設為起始專案]  。

2. 在 [建置]  功能表上，選取 [組態管理員]  。

3. 在 [組態管理員]  對話方塊中，選取 Android 專案旁邊的 [建置]  和 [部署]  核取方塊，並確定共用程式碼專案已核取 [建置]  方塊。

4. 若要建置專案並在 Android 模擬器中啟動應用程式，請按 **F5** 鍵，或是按一下 [啟動]  按鈕。

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. 以滑鼠右鍵按一下 Android 專案，然後選取 [設為起始專案]  。

2. 若要建置專案並在 Android 模擬器中啟動應用程式，請選取 [執行]  功能表，再選取 [開始偵錯]  。

在應用程式中，輸入有意義的文字 (例如「了解 Xamarin」  )，然後選取加號 ( **+** )。

![Android 待辦事項應用程式][11]

此動作會傳送 post 要求到 Azure 中代管的新 Mobile Apps 後端。 要求中的資料會插入 TodoItem 資料表中。 Mobile Apps 後端會傳回資料表中儲存的項目，而該資料會顯示在清單中。

> [!NOTE]
> 會存取 Mobile Apps 後端的程式碼位於解決方案共用程式碼專案的 **TodoItemManager.cs** C# 檔案中。
>

## <a name="optional-run-the-ios-project"></a>(選擇性) 執行 iOS 專案

在這一節當中，您會執行適用於 iOS 裝置的 Xamarin.iOS 專案。 如果未使用 iOS 裝置，可以略過這一節。

### <a name="visual-studio"></a>Visual Studio

1. 以滑鼠右鍵按一下 iOS 專案，然後選取 [設為起始專案]  。

2. 在 [建置]  功能表上，選取 [組態管理員]  。

3. 在 [組態管理員]  對話方塊中，選取 iOS 專案旁邊的 [建置]  和 [部署]  核取方塊，並確定共用程式碼專案已核取 [建置]  方塊。

4. 若要建置專案並在 iPhone 模擬器中啟動應用程式，請選取 **F5** 鍵。

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. 以滑鼠右鍵按一下 iOS 專案，然後選取 [設為起始專案]  。

2. 在 [執行]  功能表上，選取 [開始偵錯]  以建置專案並在 iPhone 模擬器中啟動應用程式。

在應用程式中，輸入有意義的文字 (例如「了解 Xamarin」  )，然後選取加號 ( **+** )。

![iOS 待辦事項應用程式][10]

此動作會傳送 post 要求到 Azure 中代管的新 Mobile Apps 後端。 要求中的資料會插入 TodoItem 資料表中。 Mobile Apps 後端會傳回資料表中儲存的項目，而該資料會顯示在清單中。

> [!NOTE]
> 您會在解決方案共用程式碼專案的 **TodoItemManager.cs** C# 檔案中，找到會存取 Mobile Apps 後端的程式碼。
>

## <a name="optional-run-the-windows-project"></a>(選擇性) 執行 Windows 專案

在這一節當中，您會執行適用於 Window 裝置的 Xamarin.Forms 通用 Windows 平台 (UWP) 專案。 如果未使用 Windows 裝置，可以略過這一節。

### <a name="visual-studio"></a>Visual Studio

1. 以滑鼠右鍵按一下任意 UWP 專案，然後選取 [設為起始專案]  。

2. 在 [建置]  功能表上，選取 [組態管理員]  。

3. 在 [組態管理員]  對話方塊中，選取所選擇 Windows 專案旁邊的 [建置]  和 [部署]  核取方塊，並確定共用程式碼專案已核取 [建置]  方塊。

4. 若要建置專案並在 Windows 模擬器中啟動應用程式，請按 **F5** 鍵，或是按一下 [啟動]  按鈕 (名稱應該是**本機電腦**)。

> [!NOTE]
> Windows 專案不能在 macOS 中執行。

在應用程式中，輸入有意義的文字 (例如「了解 Xamarin」  )，然後選取加號 ( **+** )。

此動作會傳送 post 要求到 Azure 中代管的新 Mobile Apps 後端。 要求中的資料會插入 TodoItem 資料表中。 Mobile Apps 後端會傳回資料表中儲存的項目，而該資料會顯示在清單中。

![UWP 待辦事項應用程式][12]

> [!NOTE]
> 您將會在您方案的可攜式類別庫專案的 **TodoItemManager.cs** C# 檔案中，發現可存取 Mobile Apps 後端的程式碼。
>

## <a name="troubleshooting"></a>疑難排解

如果在建置解決方案時發生問題，請執行 NuGet 套件管理員，並更新為最新版的 `Xamarin.Forms`，並在 Android 專案中，更新 `Xamarin.Android` 支援套件。 快速入門專案不一定會包含最新版本。

請注意，您 Android 專案中所參考的所有支援套件都必須具有相同版本。 [Azure Mobile Apps NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) 有 Android 平台的 `Xamarin.Android.Support.CustomTabs` 相依性，因此若您的專案使用較新的支援套件，您必須直接安裝具有必要版本的此套件以避免衝突。

<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png

<!-- URLs. -->
[Install Xamarin]: https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/