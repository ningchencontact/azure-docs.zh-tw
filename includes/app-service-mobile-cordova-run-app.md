---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.openlocfilehash: 8d7731480b6239c572d39f52b6a0217d2ac48d25
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240246"
---
1. 瀏覽至用戶端專案 (.sln) 中的方案檔，並使用 Visual Studio 加以開啟。

2. 在 Visual Studio 中，從開始箭號旁邊的下拉式清單中選擇解決方案平台 (Android、iOS 或 Windows)。 按一下綠色箭號上的下拉式清單以選取特定的部署裝置或模擬器。 您可以使用預設 Android 平台和 Ripple 模擬器。 更進階的教學課程 (例如，推播通知) 會要求您選取支援的裝置或模擬器。

3. 開啟檔案`ToDoActivity.java`在此資料夾-W/app/src/main/java/com/範例/w。 應用程式名稱是`ZUMOAPPNAME`。

4. 移至[Azure 入口網站](https://portal.azure.com/)並瀏覽至您所建立的行動應用程式。 在 `Overview`刀鋒視窗中，搜尋 URL，也就是您的行動裝置應用程式的公用端點。 範例-將會針對我的應用程式名稱"test123"sitename https://test123.azurewebsites.net。

5. 移至`index.js`檔案在 ZUMOAPPNAME/www/js/index.js`onDeviceReady()`方法，取代`ZUMOAPPURL`與上述的公用端點的參數。

    `client = new WindowsAzure.MobileServiceClient('ZUMOAPPURL');`
    
    會變成
    
    `client = new WindowsAzure.MobileServiceClient('https://test123.azurewebsites.net');`
    
6. 若要建置和執行 Cordova 應用程式，請按 F5 或按一下綠色箭號。 如果您在模擬器中看到要求網路存取權的安全性對話方塊，請接受它。

7. 裝置或模擬器上啟動應用程式之後，請輸入有意義的文字，在**輸入新文字**，例如*完成本教學課程*，然後按一下 [**新增**] 按鈕。

後端會將要求中的資料插入 SQL Database 中的 TodoItem 資料表，並將新儲存之項目的相關資訊傳回給行動應用程式。 行動應用程式會以清單顯示此資料。

您可以對其他平台重複步驟 3 到 5。