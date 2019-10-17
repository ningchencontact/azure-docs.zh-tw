---
title: 建立使用 Azure Mobile Apps 的通用 Windows 平台 (UWP) | Microsoft Docs
description: 遵循此教學課程，可開始使用 Azure 行動應用程式後端進行 C#、Visual Basic 或 JavaScript 的通用 Windows 平台 (UWP) 應用程式開發。
services: app-service\mobile
documentationcenter: windows
author: elamalani
manager: crdun
editor: ''
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 3ddd5ef197740315add2960971c978addb7fe62a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388555"
---
# <a name="create-a-windows-app-with-an-azure-backend"></a>建立具有 Azure 後端的 Windows 應用程式

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center 支援行動應用程式開發的端對端和整合式服務中心。 開發人員可以使用**組建**、**測試**和**散發**服務來設定持續整合和傳遞管線。 部署應用程式之後，開發人員可以使用**分析**和**診斷**服務來監視其應用程式的狀態和使用，並與使用**推**播服務的使用者互動。 開發人員也可以利用**驗證**來驗證其使用者和**資料**服務，以保存及同步雲端中的應用程式資料。
>
> 如果您想要在您的行動應用程式中整合雲端服務，請立即註冊[App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) 。

## <a name="overview"></a>概觀

本教學課程顯示如何將雲端型後端服務新增到通用 Windows 平台 (UWP) 應用程式。 如需詳細資訊，請參閱 [什麼是 Mobile Apps？](app-service-mobile-value-prop.md)。 以下是已完成之應用程式的螢幕截圖：

![完成的電腦桌面應用程式](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)

完成本教學課程是 UWP 應用程式的所有其他行動應用程式教學課程的必要條件。

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，可以註冊 Azure 試用版並取得最多 10 個免費的行動應用程式，即使在試用期結束之後仍可繼續使用這些應用程式。 如需詳細資料，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* Windows 10。
* Visual Studio Community 2017。
* 熟悉如何開發 UWP 應用程式。 請瀏覽 [UWP 文件](https://docs.microsoft.com/windows/uwp/)，以了解如何[完成設定](https://docs.microsoft.com/windows/uwp/get-started/get-set-up)來建置 UWP 應用程式。

## <a name="create-a-new-azure-mobile-app-backend"></a>建立新的 Azure 行動應用程式後端

依照下列步驟建立新的行動應用程式後端。

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>建立資料庫連接，並設定用戶端和伺服器專案
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-client-project"></a>執行用戶端專案

1. 開啟 UWP 專案。

2. 移至  [Azure 入口網站](https://portal.azure.com/)，然後流覽至您所建立的行動應用程式。 在 [`Overview`] 分頁上，尋找您的行動應用程式的公用端點 URL。 範例-我的應用程式名稱 "test123" 的 sitename 將會 https://test123.azurewebsites.net 。

3. 在此資料夾中開啟 `App.xaml.cs` 的檔案-windows-uwp-cs/z/。 應用程式名稱為 `ZUMOAPPNAME`。

4. 在 `App` 類別中，以上述公用端點取代 `ZUMOAPPURL` 參數。

    `public static MobileServiceClient MobileService = new MobileServiceClient("ZUMOAPPURL");`

    滿
    
    `public static MobileServiceClient MobileService = new MobileServiceClient("https://test123.azurewebsites.net");`

5. 按 F5 鍵以部署並執行應用程式。

6. 在應用程式的 [插入 TodoItem] 文字方塊中輸入有意義的文字 (例如「完成教學課程」(Complete the tutorial)」)，然後按一下 [儲存]。

    ![Windows 快速入門完整桌面](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    如此會傳送 POST 要求到 Azure 中代管的新行動應用程式後端。
