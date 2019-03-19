---
title: 建立使用 Azure Mobile Apps 的通用 Windows 平台 (UWP) | Microsoft Docs
description: 遵循此教學課程，可開始使用 Azure 行動應用程式後端進行 C#、Visual Basic 或 JavaScript 的通用 Windows 平台 (UWP) 應用程式開發。
services: app-service\mobile
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: crdun
ms.openlocfilehash: 289d50fc9d71ee5ea8960fd077e91ef6f68027cb
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2019
ms.locfileid: "57771305"
---
# <a name="create-a-windows-app-with-an-azure-backend"></a>建立具有 Azure 後端的 Windows 應用程式

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>概觀

本教學課程顯示如何將雲端型後端服務新增到通用 Windows 平台 (UWP) 應用程式。 如需詳細資訊，請參閱 [什麼是 Mobile Apps？](app-service-mobile-value-prop.md)。 以下是已完成之應用程式的螢幕截圖：

![完成的電腦桌面應用程式](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)

完成本教學課程是 UWP 應用程式的所有其他行動應用程式教學課程的必要條件。

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，可以註冊 Azure 試用版並取得最多 10 個免費的行動應用程式，即使在試用期結束之後仍可繼續使用這些應用程式。 如需詳細資料，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* Windows 10。
* [Visual Studio Community 2017]。
* 熟悉如何開發 UWP 應用程式。 請瀏覽 [UWP 文件](https://docs.microsoft.com/windows/uwp/)，以了解如何[完成設定](https://docs.microsoft.com/windows/uwp/get-started/get-set-up)來建置 UWP 應用程式。

## <a name="create-a-new-azure-mobile-app-backend"></a>建立新的 Azure 行動應用程式後端

按照下列步骤创建新的移动应用后端。

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

现已预配可供移动客户端应用程序使用的 Azure 移动应用后端。 接下来，将为简单的“待办事项列表”后端下载服务器项目并将其发布到 Azure。

## <a name="configure-the-server-project"></a>設定伺服器專案

[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-client-project"></a>下載並執行用戶端專案

設定行動應用程式後端之後，您就可以建立新用戶端應用程式或修改現有的應用程式，以連接到 Azure。 在本節中，您會下載 UWP 範例應用程式專案，而該專案已自訂為要連線至行動應用程式後端。

1. 回到行動應用程式後端的 [快速啟動] 刀鋒視窗中，按一下您的 [建立新的應用程式]  >  [下載]，然後解壓縮壓縮的專案檔案至本機電腦。

    ![下載 Windows 快速入門專案](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-app-windows-quickstart.png)

2. 開啟 UWP 專案，然後按 F5 鍵以部署和執行應用程式。
3. 在應用程式的 [插入 TodoItem] 文字方塊中輸入有意義的文字 (例如「完成教學課程」(Complete the tutorial)」)，然後按一下 [儲存]。

    ![Windows 快速入门完整桌面](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    如此會傳送 POST 要求到 Azure 中代管的新行動應用程式後端。

> [!TIP]
> 如果您使用 .NET 後端，您可以將 UWP 應用程式專案新增至與伺服器專案相同的解決方案。 如此即可更輕鬆地在相同的 Visual Studio 解決方案中偵錯及測試應用程式和後端。 若要將 UWP 應用程式專案新增至後端解決方案，您必須使用 Visual Studio 2017。

## <a name="next-steps"></a>後續步驟

* [將驗證新增至應用程式中](app-service-mobile-windows-store-dotnet-get-started-users.md)  
  了解如何利用識別提供者來驗證應用程式的使用者。
* [將推播通知新增至應用程式中](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  了解如何將推播通知支援新增至應用程式，並設定行動應用程式後端以使用 Azure 通知中樞傳送推播通知。
* [为应用启用脱机同步](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  了解如何使用行動應用程式後端，將離線支援新增至應用程式。 離線同步處理可讓使用者與行動應用程式進行互動&mdash;檢視、新增或修改資料&mdash;即使沒有網路連線進也可行。

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Mobile App SDK]: https://go.microsoft.com/fwlink/?LinkId=257545
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2017]: https://go.microsoft.com/fwLink/p/?LinkID=534203
