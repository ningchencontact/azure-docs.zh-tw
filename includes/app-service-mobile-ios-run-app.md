---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a5bde1a56bf6a1f5fca4b775c7c8e9bb7477eb6b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240286"
---
1. 開啟已下載的用戶端專案使用 Xcode。

2. 移至[Azure 入口網站](https://portal.azure.com/)並瀏覽至您所建立的行動應用程式。 在 `Overview`刀鋒視窗中，搜尋 URL，也就是您的行動裝置應用程式的公用端點。 範例-將會針對我的應用程式名稱"test123"sitename https://test123.azurewebsites.net。

3. Swift 的專案中，開啟 檔案`ToDoTableViewController.swift`ZUMOAPPNAME/ZUMOAPPNAME/ToDoTableViewController.swift 此資料夾中。 應用程式名稱是`ZUMOAPPNAME`。

4. 在 `viewDidLoad()`方法，取代`ZUMOAPPURL`與上述的公用端點的參數。

    `let client = MSClient(applicationURLString: "ZUMOAPPURL")`

    會變成
    
    `let client = MSClient(applicationURLString: "https://test123.azurewebsites.net")`
    
5. Objective C 專案中，開啟 檔案`QSTodoService.m`在此資料夾-W/W。 應用程式名稱是`ZUMOAPPNAME`。

6. 在 `init`方法，取代`ZUMOAPPURL`與上述的公用端點的參數。

    `self.client = [MSClient clientWithApplicationURLString:@"ZUMOAPPURL"];`

    會變成
    
    `self.client = [MSClient clientWithApplicationURLString:@"https://test123.azurewebsites.net"];`

7. 按 [執行]  按鈕以建置專案，並在 iOS 模擬器中啟動應用程式。

8. 在應用程式中，按一下加號 ( **+** ) 圖示，輸入有意義的文字 (例如 *Complete the tutorial*)，然後按一下儲存按鈕。 這會將 POST 要求傳送至先前部署的 Azure 後端。 後端會將要求中的資料插入 TodoItem SQL 資料表，並將新儲存之項目的相關資訊傳回給行動應用程式。 行動應用程式會以清單顯示此資料。

   ![在 iOS 上執行的快速入門應用程式](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure portal]: https://portal.azure.com/
