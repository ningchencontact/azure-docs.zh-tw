---
title: 啟用 Azure 行動應用程式的離線同步處理 (Cordova) | Microsoft Docs
description: 了解如何在 Cordova 應用程式中使用 App Service 行動應用程式快取和同步離線資料
documentationcenter: cordova
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 44c54b570a38eb1a3b9ca773893599d1d497dfa2
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110970"
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>啟用 Cordova 行動應用程式的離線同步處理
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

此教學課程介紹適用於 Cordova 之 Azure 行動應用程式的離線同步處理功能。 借助脱机同步，最终用户即使在没有网络连接时也能够与移动应用进行交互（查看、添加或修改数据）。 變更會儲存在本機資料庫中。  裝置恢復上線後，這些變更就會與遠端服務進行同步處理。

本教程基于完成教程 [Apache Cordova 快速入门]时创建的移动应用的 Cordova 快速入门解决方案。 在本教學課程中，您將會更新快速入門方案來新增 Azure Mobile Apps 的離線功能。  我們也會在應用程式中反白顯示離線特有的程式碼。

若要了解有关脱机同步功能的详细信息，请参阅主题 [Azure Mobile Apps 中的離線資料同步處理]。 API 使用方式的詳細資訊，請參閱 [API 文件](https://azure.github.io/azure-mobile-apps-js-client)。

## <a name="add-offline-sync-to-the-quickstart-solution"></a>在快速入门解决方案中添加脱机同步功能
應用程式中必須新增離線同步處理程式碼。 離線同步處理需要 cordova-sqlite-storage 外掛程式，此外掛程式會在專案中納入 Azure Mobile Apps 外掛程式時自動新增至應用程式。 快速入門專案包含上述兩個外掛程式。

1. 在 Visual Studio 的 [方案總管] 中開啟 index.js，並將下列程式碼

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable;      // Reference to a table endpoint on backend

    取代為此程式碼：

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable,      // Reference to a table endpoint on backend
           syncContext;        // Reference to offline data sync context

2. 接下來，將下列程式碼：

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

    取代為此程式碼：

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');
        var store = new WindowsAzure.MobileServiceSqliteStore('store.db');

        store.defineTable({
          name: 'todoitem',
          columnDefinitions: {
              id: 'string',
              text: 'string',
              complete: 'boolean',
              version: 'string'
          }
        });

        // Get the sync context from the client
        syncContext = client.getSyncContext();

    前面新增的程式碼會初始化本機存放區，並定義與 Azure 後端所使用的資料行值相符的本機資料表  (您不需要在此程式碼中包含所有資料行值)。`version` 欄位由行動後端維護，用於解決衝突。

    调用 **getSyncContext**可获取对同步上下文的引用。 对于调用 `.push()` 时客户端应用修改的所有表，此同步上下文通过跟踪和推送这些表中的更改来帮助保持表关系。

3. 將應用程式的 URL 更新為行動應用程式的應用程式 URL。

4. 接下來，將此程式碼：

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    取代為此程式碼：

        // Initialize the sync context with the store
        syncContext.initialize(store).then(function () {

        // Get the local table reference.
        todoItemTable = client.getSyncTable('todoitem');

        syncContext.pushHandler = {
            onConflict: function (pushError) {
                // Handle the conflict.
                console.log("Sync conflict! " + pushError.getError().message);
                // Update failed, revert to server's copy.
                pushError.cancelAndDiscard();
              },
              onError: function (pushError) {
                  // Handle the error
                  // In the simulated offline state, you get "Sync error! Unexpected connection failure."
                  console.log("Sync error! " + pushError.getError().message);
              }
        };

        // Call a function to perform the actual sync
        syncBackend();

        // Refresh the todoItems
        refreshDisplay();

        // Wire up the UI Event Handler for the Add Item
        $('#add-item').submit(addItemHandler);
        $('#refresh').on('click', refreshDisplay);

    上述程式碼會初始化同步處理內容，然後呼叫 getSyncTable (而不是 getTable) 來取得本機資料表的參考。

    此程式碼使用本機資料庫來進行所有建立、讀取、更新和刪除 (CRUD) 資料表作業。

    這個範例會對同步處理衝突執行簡單的錯誤處理。 實際的應用程式會處理各種錯誤，例如網路狀況、伺服器衝突及其他問題。 如需程式碼範例，請參閱 [離線同步處理範例]。

5. 接下来，添加此函数以执行实际同步操作。

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    您可以呼叫 **syncContext.push()**，以決定何時將變更推送至行動應用程式後端。 例如，您可以在繫結至同步處理按鈕的按鈕事件處理常式中呼叫 **syncBackend**。

## <a name="offline-sync-considerations"></a>離線同步處理考量

在範例中，只有在應用程式啟動時，才會於登入的回呼函式中呼叫 **syncContext** 的 **push** 方法。  在實際的應用程式中，您也可以透過手動方式或在網路狀態變更時觸發此同步處理功能。

對資料表執行提取時，如果該資料表有內容所追蹤的擱置中本機更新，則提取作業會自動觸發推送。 在此範例中重新整理、新增和完成項目時，您可以省略明確的 **push** 呼叫，因為它可能是多餘的。

在所提供的代码中，会查询远程 todoItem 表中的所有记录，也可以筛选记录，只需将查询 ID 和查询传递给 **push** 即可。 如需詳細資訊，請參閱 [Azure Mobile Apps 中的離線資料同步處理]中的*增量同步處理*一節。

## <a name="optional-disable-authentication"></a>（可选）禁用身份验证

如果您不想在測試離線同步處理之前設定驗證，請將登入的回呼函式註解化，但讓回呼函式內部的程式碼保持取消註解狀態。  將登入那幾行註解化之後，程式碼如下︰

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

現在，當您執行應用程式，應用程式會與 Azure 後端同步處理。

## <a name="run-the-client-app"></a>執行用戶端應用程式
離線同步現在已啟用，您可以在每個平台執行用戶端應用程式至少一次，以填入本機存放區資料庫。 稍后，模拟脱机场景，并在应用处于脱机状态时修改本地存储中的数据。

## <a name="optional-test-the-sync-behavior"></a>（可选）测试同步行为
在本節中，您將修改用戶端專案，使用對後端而言無效的應用程式 URL，以模擬離線案例。 當您新增或變更資料項目時，這些變更會保留在本機存放區，直到重新建立連接時，才會同步處理至後端資料存放區。

1. 在 [方案總管] 中開啟 index.js 專案檔案，然後將應用程式 URL 變更為指向無效的 URL，如下列程式碼所示︰

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. 在 index.html 中，以相同的無效 URL 更新 CSP `<meta>` 元素。

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. 生成并运行客户端应用，请注意，如果应用在登录后尝试与后端同步，则会在控制台中记录异常。 您新增的任何項目在推送至行動後端之前，只存在於本機存放區。 用戶端應用程式運作時就如同已連接至後端一樣。

4. 關閉應用程式並重新加以開啟，以驗證您所建立的新項目持續存留於本機存放區中。

5. （可选）使用 Visual Studio 查看 Azure SQL 数据库表，看看后端数据库中的数据是否未更改。

    在 Visual Studio 中，開啟 [伺服器總管] 。 瀏覽至 [Azure]->[SQL Database 中您的資料庫]。 在資料庫上按一下滑鼠右鍵，並選取 [在 SQL Server 物件總管中開啟] 。 現在您可以瀏覽至您的 SQL Database 資料表和其內容。

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>（可选）测试与移动后端的重新连接

在本節中，您會將應用程式重新連接至行動後端，以模擬應用程式回到線上狀態。 當您登入時，資料會同步處理至行動後端。

1. 重新開啟 index.js，並還原應用程式 URL。
2. 重新開啟 index.html，並修正 CSP `<meta>` 元素中的應用程式 URL。
3. 重建並執行用戶端應用程式。 應用程式在登入後會嘗試與行動應用程式後端同步處理。 請確認偵錯主控台沒有記錄任何例外狀況。
4. （可选）使用 SQL Server 对象资源管理器或 Fiddler 之类的 REST 工具查看更新后的数据。 請注意，後端資料庫與本機存放區之間尚未同步處理資料。

    请注意，数据已在数据库和本地存储之间进行同步，并包含在应用断开连接时添加的项目。

## <a name="additional-resources"></a>其他資源
* [Azure Mobile Apps 中的離線資料同步處理]
* [用于 Apache Cordova 的 Visual Studio 工具]

## <a name="next-steps"></a>後續步驟
* 在[離線同步處理範例]中，檢閱更進階的離線同步處理功能，例如衝突解決
* 在 [API 文件](https://azure.github.io/azure-mobile-apps-js-client)中，檢閱離線同步處理 API 參考

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Apache Cordova 快速入门]: app-service-mobile-cordova-get-started.md
[離線同步處理範例]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
[Azure Mobile Apps 中的離線資料同步處理]: app-service-mobile-offline-data-sync.md
[Cloud Cover: Offline Sync in Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: https://www.visualstudio.com/
[用于 Apache Cordova 的 Visual Studio 工具]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
