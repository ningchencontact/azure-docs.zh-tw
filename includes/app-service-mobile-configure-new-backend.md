---
title: 包含檔案
description: 包含檔案
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: a7c994f85d90e94d514bb4e4f91a6644ed45432c
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/31/2019
ms.locfileid: "66455143"
---
1. 下載用戶端 SDK 快速入門，為下列平台：
    
    [iOS (Objective-C)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS)  
    [iOS (Swift)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift)  
    [Android (Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android)  
    [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS)  
    [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android)  
    [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms)  
    [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova)  
    [Windows (C#)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)  

    > [!NOTE]
    > 如果您使用 iOS 專案，您需要下載"azuresdk-iOS-\*.zip 」 從[最新的 GitHub 版本](https://github.com/Azure/azure-mobile-apps-ios-client/releases/latest)。 解壓縮，並新增`MicrosoftAzureMobile.framework`專案的根目錄的檔案。
    >

2. 您必須新增資料庫連接或連接至現有的連接。 首先，判斷是否會建立資料存放區，或使用現有的帳戶。

    - **建立新的資料存放區**:如果您將建立資料存放區，請使用下列快速入門：

        [快速入門：開始使用 Azure SQL Database 中單一資料庫](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

    - **現有的資料來源**:如果您想要使用現有的資料庫連接，請遵循下列指示

        1. SQL Database 連接字串的格式- `Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`

           **{your_SQLServer}** 名稱伺服器，這可在 [概觀] 頁面，為您的資料庫，通常是"server_name.database.windows.net 」 的形式。
            **{port}** 通常是 1433年。
            **{your_catalogue}** 資料庫的名稱。
            **{your_username}** 存取您的資料庫使用者名稱。
            **{your_password}** 密碼以存取您的資料庫。

            [深入了解 SQL 連接字串格式](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)

        2. 將連接字串加入您**行動裝置應用程式**在 App Service 中，您可以管理連接字串為您的應用程式使用**組態**功能表中的選項。

            若要新增連接字串：

            1. 按一下 [**應用程式設定**] 索引標籤。

            2. 按一下  **[+] 新的連接字串**。

            3. 您必須提供**名稱**，**值**並**型別**連接字串。

            4. 型別**名稱**為 `MS_TableConnectionString`

            5. 值應該是您在前的步驟正確的連接字串。

            6. 如果您要將連接字串新增至 SQL Azure 資料庫選擇**SQLAzure**下方**型別**。

3. Azure Mobile Apps 的.NET 和 Node.js 後端 Sdk。

   - **Node.js 後端**
    
     如果您要使用 Node.js 快速入門應用程式，請遵循下列指示。

     1. 在 Azure 入口網站中，移至**簡易表**，您會看到這個畫面。
      
        ![節點的簡易表](./media/app-service-mobile-configure-new-backend/node-easy-tables.png)

     2. 請確定在已新增 SQL 連接字串**組態** 索引標籤。然後核取方塊**我了解，這會覆寫所有網站內容**然後按一下**建立 TodoItem 資料表** 按鈕。
     
        ![節點簡易表設定](./media/app-service-mobile-configure-new-backend/node-easy-tables-configuration.png)

     3. 在 [**簡易表**，按一下 **+ 新增**] 按鈕。
    
        ![節點的簡易表 [新增] 按鈕](./media/app-service-mobile-configure-new-backend/node-easy-tables-add.png)

     4. 建立`TodoItem`匿名存取的資料表。
      
        ![將資料表加入節點簡易表](./media/app-service-mobile-configure-new-backend/node-easy-tables-table-add.png)

   - **.NET 後端**
    
        如果您要使用.NET 快速入門應用程式，請遵循下列指示。

        1. 下載 Azure Mobile Apps.NET 伺服器專案，從[azure 行動應用程式-入門存放庫](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart)。

        2. 建置.NET 伺服器專案，Visual Studio 在本機。

        3. 在 Visual Studio 中，開啟 [方案總管] 中，以滑鼠右鍵按一下`ZUMOAPPNAMEService`專案中，按一下**發佈**，您會看到`Publish to App Service`視窗。 如果您使用 Mac 上，請參閱其他方式來部署應用程式[此處](https://docs.microsoft.com/azure/app-service/deploy-local-git)。
        
           ![Visual studio 發行](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. 選取**App Service**發佈的目標，然後按一下**選取現有**，然後按一下**發行**底部的視窗 按鈕。

        5. 您必須先登入 Visual Studio Azure 訂用帳戶。 選取  `Subscription`， `Resource Group`，然後選取 應用程式的名稱。 當您準備好時，按一下**確定**，這會將您所擁有的.NET 伺服器專案部署到 App Service 後端的本機。 部署完成時，您將會重新導向至`http://{zumoappname}.azurewebsites.net/`瀏覽器中。
        
           ![後端已啟動](./media/app-service-mobile-configure-new-backend/backend-is-up.png)
