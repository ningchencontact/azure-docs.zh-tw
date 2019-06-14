---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: 63c54f8af91b6b4a76ba49d5e6fc7b3cda9f5b98
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240328"
---
1. 使用 **Android Studio**、**匯入專案 (Eclipse ADT、Gradle 等等)** 開啟專案。 請確認選取此匯入選項，以避免發生 JDK 錯誤。

2. 開啟檔案`ToDoActivity.java`在此資料夾-W/app/src/main/java/com/範例/w。 應用程式名稱是`ZUMOAPPNAME`。

3. 移至[Azure 入口網站](https://portal.azure.com/)並瀏覽至您所建立的行動應用程式。 在 `Overview`刀鋒視窗中，搜尋 URL，也就是您的行動裝置應用程式的公用端點。 範例-將會針對我的應用程式名稱"test123"sitename https://test123.azurewebsites.net 。

4. 在 `onCreate()`方法，取代`ZUMOAPPURL`與上述的公用端點的參數。
    
    `new MobileServiceClient("ZUMOAPPURL", this).withFilter(new ProgressFilter());` 
    
    會變成
    
    `new MobileServiceClient("https://test123.azurewebsites.net", this).withFilter(new ProgressFilter());`
    
5. 按 [ **執行 '應用程式'** ] 按鈕以建置專案，並在Android 模擬器中啟動應用程式。

4. 在應用程式中輸入有意義的文字 (例如 *Complete the tutorial* )，然後按一下 [加入] 按鈕。 這會將 POST 要求傳送至先前部署的 Azure 後端。 後端會將要求中的資料插入 TodoItem SQL 資料表，並將新儲存之項目的相關資訊傳回給行動應用程式。 行動應用程式會以清單顯示此資料。
    ![Android 快速入門](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)