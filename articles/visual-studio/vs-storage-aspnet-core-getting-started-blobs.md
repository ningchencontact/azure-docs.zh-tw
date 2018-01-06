---
title: "開始使用 Azure Blob 儲存體和 Visual Studio 已連接服務 (ASP.NET Core) |Microsoft 文件"
description: "如何開始使用 Azure Blob 儲存體中的 ASP.NET Core 專案在 Visual Studio 中，使用 Visual Studio 連接至儲存體帳戶之後已連接服務"
services: storage
documentationcenter: 
author: camsoper
manager: wpickett
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: casoper
ms.openlocfilehash: 42390effd6a2d2a8afe9350e0a77d3c0a17b6129
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2018
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>開始使用 Azure Blob 儲存體和 Visual Studio 已連接服務 (ASP.NET Core)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Azure Blob 儲存體是為物件或 blob 時，將非結構化的資料儲存在雲端中的服務。 Blob 儲存體可以儲存任何類型的文字或二進位資料，例如文件、媒體檔案或應用程式安裝程式。 Blob 儲存體也稱為物件儲存體。

本教學課程會示範如何撰寫 ASP.NET Core 程式碼的一些常見案例，使用 Blob 儲存體。 案例包括建立 blob 容器，以及上傳、列出、下載和刪除 blob。

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>必要條件

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

## <a name="set-up-the-development-environment"></a>設定開發環境

本節逐步引導開發環境設定。 這包括建立 ASP.NET 模型檢視控制器 (MVC) 應用程式、 新增已連線的服務連接、 加入控制器，並指定必要的命名空間指示詞。

### <a name="create-an-aspnet-mvc-app-project"></a>建立 ASP.NET MVC 應用程式專案

1. 開啟 Visual Studio。

1. 從主功能表中，選取**檔案** > **新增** > **專案**。

1. 在**新專案**對話方塊中，選取**Web** > **ASP.NET Core Web 應用程式** > **AspNetCoreStorage**. 然後選取 [確定]。

    ![螢幕擷取畫面的 Visual Studio 新專案 對話方塊](./media/vs-storage-aspnet-core-getting-started-blobs/new-project.png)

1. 在**新 ASP.NET Core Web 應用程式**對話方塊中，選取**.NET Core** > **ASP.NET Core 2.0** > **Web 應用程式 (Model-View-Controller)**。 然後選取 [確定]。

    ![螢幕擷取畫面的新 ASP.NET Core Web 應用程式 對話方塊](./media/vs-storage-aspnet-core-getting-started-blobs/new-mvc.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>使用已連線的服務連接到 Azure 儲存體帳戶

1. 在 [方案總管] 中，於專案上按一下滑鼠右鍵。

2. 從內容功能表中，選取**新增** > **已連接服務**。

1. 在**已連接服務**對話方塊中，選取**雲端儲存體與 Azure 儲存體**，然後選取**設定**。

    ![對話方塊中的 已連接服務的螢幕擷取畫面](./media/vs-storage-aspnet-core-getting-started-blobs/connected-services.png)

1. 在**Azure 儲存體**對話方塊中，選取要用於此教學課程中的 Azure 儲存體帳戶。 若要建立新的 Azure 儲存體帳戶，請選取**建立新的儲存體帳戶**，並完成表單。 在選取的現有儲存體帳戶，或建立一個新之後, 選取**新增**。 Visual Studio 安裝 NuGet 封裝的儲存體連接字串和 Azure 儲存體， **appsettings.json**。

> [!TIP]
> 若要了解如何建立儲存體帳戶與[Azure 入口網站](https://portal.azure.com)，請參閱[建立儲存體帳戶](../storage/common/storage-create-storage-account.md#create-a-storage-account)。
>
> 您也可以建立儲存體帳戶使用[Azure PowerShell](../storage/common/storage-powershell-guide-full.md)， [Azure CLI](../storage/common/storage-azure-cli.md)，或[Azure 雲端殼層](../cloud-shell/overview.md)。


### <a name="create-an-mvc-controller"></a>建立 MVC 控制器 

1. 在**方案總管 中**，以滑鼠右鍵按一下**控制器**。

2. 從內容功能表中，選取**新增** > **控制器**。

    ![方案總管 的螢幕擷取畫面](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-menu.png)

1. 在**新增 Scaffold**對話方塊中，選取**MVC 控制器-空白**，然後選取**新增**。

    ![對話方塊中的新增 Scaffold 的螢幕擷取畫面](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller.png)

1. 在**加入空白的 MVC 控制器**對話方塊中，名稱控制器*BlobsController*，然後選取**新增**。

    ![螢幕擷取畫面的加入空白的 MVC 控制器 對話方塊](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-name.png)

1. 加入下列`using`指示詞加入`BlobsController.cs`檔案：

    ```csharp
    using System.IO;
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>連接到儲存體帳戶並取得容器的參考

Blob 容器是 blob 和資料夾的巢狀階層。 這份文件中的步驟的其餘部分要求 blob 容器的參考，讓程式碼應該放在自己的重複使用性的方法。

下列步驟建立方法以使用中的連接字串連接到儲存體帳戶**appsettings.json**。 步驟也會建立容器的參考。 中的連接字串設定**appsettings.json**具名格式`<storageaccountname>_AzureStorageConnectionString`。 

1. 開啟 `BlobsController.cs` 檔案。

1. 將方法呼叫**GetCloudBlobContainer**傳回**CloudBlobContainer**。 請務必取代`<storageaccountname>_AzureStorageConnectionString`中的索引鍵的實際名稱**Web.config**。
    
    ```csharp
    private CloudBlobContainer GetCloudBlobContainer()
    {
        var builder = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("appsettings.json");
        IConfigurationRoot Configuration = builder.Build();
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.Parse(Configuration["ConnectionStrings:aspnettutorial_AzureStorageConnectionString"]);
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
        return container;
    }
    ```

> [!NOTE]
> 即使*測試 blob 容器*不存在，這段程式碼會建立它的參考。 這是讓容器可以使用建立`CreateIfNotExists`下一個步驟所示的方法。

## <a name="create-a-blob-container"></a>建立 Blob 容器

下列步驟說明如何建立 blob 容器：

1. 將方法呼叫`CreateBlobContainer`傳回`ActionResult`。

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. 取得`CloudBlobContainer`物件，代表所需的 blob 容器名稱的參考。 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. 呼叫`CloudBlobContainer.CreateIfNotExists`方法來建立容器，如果它尚未存在。 `CloudBlobContainer.CreateIfNotExists`方法會傳回**true**如果容器不存在，而且已成功建立。 否則，方法會傳回**false**。    

    ```csharp
    ViewBag.Success = container.CreateIfNotExistsAsync().Result;
    ```

1. 更新`ViewBag`與 blob 容器的名稱。

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    下圖顯示已完成`CreateBlobContainer`方法：

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExistsAsync().Result;
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. 在**方案總管 中**，以滑鼠右鍵按一下**檢視**資料夾。

2. 從內容功能表中，選取**新增** > **新資料夾**。 將新的資料夾命名*Blob*。 

1. 在**方案總管 中**，依序展開**檢視**資料夾，然後以滑鼠右鍵按一下**Blob**。

4. 從內容功能表中，選取**新增** > **檢視**。

1. 在**加入檢視**對話方塊方塊中，輸入**CreateBlobContainer**檢視名稱，然後選取**新增**。

1. 開啟 `CreateBlobContainer.cshtml` 並加以修改，以便其如下列程式碼片段所示：

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>
    
    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. 在**方案總管 中**，依序展開**檢視** > **共用**資料夾，然後開啟`_Layout.cshtml`。

1. 看起來未排序清單看起來像這樣： `<ul class="nav navbar-nav">`。  最後一個之後`<li>`項目在清單中，加入下列 HTML 新增另一個瀏覽功能表項目：

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="CreateBlobContainer">Create blob container</a></li>
    ```

1. 執行應用程式，並選取**建立 Blob 容器**來查看結果類似下列的螢幕擷取畫面：
  
    ![建立螢幕擷取畫面的 blob 容器](./media/vs-storage-aspnet-core-getting-started-blobs/create-blob-container-results.png)

    如前所述，`CloudBlobContainer.CreateIfNotExists`方法會傳回**true**只有當容器不存在，而且會建立。 因此，如果容器存在，則當執行應用程式，則方法會傳回**false**。

## <a name="upload-a-blob-into-a-blob-container"></a>將 Blob 上傳至 blob 容器

當[建立 blob 容器](#create-a-blob-container)，將檔案上傳至該容器。 本節逐步解說將本機檔案上傳至 blob 容器。 沒有名為的 blob 容器的步驟假設*測試 blob 容器*。 

1. 開啟 `BlobsController.cs` 檔案。

1. 將方法呼叫`UploadBlob`所傳回的字串。

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. 內`UploadBlob`方法，取得`CloudBlobContainer`物件，代表所需的 blob 容器名稱的參考。 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Azure 儲存體支援不同的 blob 類型。 本教學課程使用區塊 Blob。 若要擷取至區塊 blob 的參考，請呼叫`CloudBlobContainer.GetBlockBlobReference`方法。

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > Blob 名稱是用來擷取 blob 的 URL 的一部分而且可以是任何字串，包括檔案的名稱。

1. 在 blob 參考之後，您可以上傳任何資料流，藉由呼叫 blob 參考物件的`UploadFromStream`方法。 `UploadFromStream`方法會建立 blob，如果它不存在，或如果存在的話，覆寫它。 (變更*&lt;檔案上傳至 >*要上傳檔案的完整路徑。)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStreamAsync(fileStream).Wait();
    }
    ```
    
    下圖顯示已完成`UploadBlob`方法 （包含要上傳檔案的完整路徑）：

    ```csharp
    public string UploadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenRead(@"c:\src\sample.txt"))
        {
            blob.UploadFromStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. 在**方案總管 中**，依序展開**檢視** > **共用**資料夾，然後開啟`_Layout.cshtml`。

1. 最後一個之後`<li>`項目在清單中，加入下列 HTML 新增另一個瀏覽功能表項目：

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="UploadBlob">Upload blob</a></li>
    ```

1. 執行應用程式，並選取 [上傳 blob]。 Word*成功 ！* 應該會出現。
    
    ![成功驗證的螢幕擷取畫面](./media/vs-storage-aspnet-core-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>列出 blob 容器中的 blob

下一節將說明如何列出 blob 容器中的 blob。 範例程式碼參考在區段中建立的 *test-blob-container*，[建立 blob 容器](#create-a-blob-container)。

1. 開啟 `BlobsController.cs` 檔案。

1. 將方法呼叫`ListBlobs`傳回`ActionResult`。

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. 內`ListBlobs`方法，取得`CloudBlobContainer`物件，代表 blob 容器的參考。 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. 若要列出 blob 容器中的 blob，請使用`CloudBlobContainer.ListBlobsSegmentedAsync`方法。 `CloudBlobContainer.ListBlobsSegmentedAsync`方法會傳回`BlobResultSegment`。 這包含`IListBlobItem`物件，可以轉型為`CloudBlockBlob`， `CloudPageBlob`，或`CloudBlobDirectory`物件。 下列程式碼片段列舉 blob 容器中的所有 blob。 每個 blob 會轉換成適當的物件，其類型為基礎。 其名稱 (如果是 URI 或`CloudBlobDirectory`) 加入至清單。

    ```csharp
    List<string> blobs = new List<string>();
    BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
    foreach (IListBlobItem item in resultSegment.Results)
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob blob = (CloudPageBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory dir = (CloudBlobDirectory)item;
            blobs.Add(dir.Uri.ToString());
        }
    }

    return View(blobs);
    ```
    下圖顯示已完成`ListBlobs`方法：

    ```csharp
    public ActionResult ListBlobs()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        List<string> blobs = new List<string>();
        BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
        foreach (IListBlobItem item in resultSegment.Results)
        {
            if (item.GetType() == typeof(CloudBlockBlob))
            {
                CloudBlockBlob blob = (CloudBlockBlob)item;
                blobs.Add(blob.Name);
            }
            else if (item.GetType() == typeof(CloudPageBlob))
            {
                CloudPageBlob blob = (CloudPageBlob)item;
                blobs.Add(blob.Name);
            }
            else if (item.GetType() == typeof(CloudBlobDirectory))
            {
                CloudBlobDirectory dir = (CloudBlobDirectory)item;
                blobs.Add(dir.Uri.ToString());
            }
        }

        return View(blobs);
    }
    ```

1. 在**方案總管 中**，依序展開**檢視**資料夾，然後以滑鼠右鍵按一下**Blob**。

2. 從內容功能表中，選取**新增** > **檢視**。

1. 在**加入檢視**對話方塊方塊中，輸入`ListBlobs`檢視名稱，然後選取**新增**。

1. 開啟`ListBlobs.cshtml`，並取代為下列程式碼的內容：

    ```html
    @model List<string>
    @{
        ViewBag.Title = "List blobs";
    }
    
    <h2>List blobs</h2>
    
    <ul>
        @foreach (var item in Model)
        {
            <li>@item</li>
        }
    </ul>
    ```

1. 在**方案總管 中**，依序展開**檢視** > **共用**資料夾，然後開啟`_Layout.cshtml`。

1. 最後一個之後`<li>`項目在清單中，加入下列 HTML 新增另一個瀏覽功能表項目：

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="ListBlobs">List blobs</a></li>
    ```

1. 執行應用程式，並選取**列出 blob**來查看結果類似下列的螢幕擷取畫面：
  
    ![Blob 清單的螢幕擷取畫面](./media/vs-storage-aspnet-core-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>下載 Blob

本節說明如何將 blob 下載。 您可以將它保存至本機儲存體或讀取到字串的內容。 範例程式碼參考在區段中建立的 *test-blob-container*，[建立 blob 容器](#create-a-blob-container)。

1. 開啟 `BlobsController.cs` 檔案。

1. 將方法呼叫`DownloadBlob`所傳回的字串。

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. 內`DownloadBlob`方法，取得`CloudBlobContainer`物件，代表 blob 容器的參考。
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. 取得 blob 的參考物件，藉由呼叫`CloudBlobContainer.GetBlockBlobReference`方法。 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. 若要下載 blob，使用`CloudBlockBlob.DownloadToStream`方法。 下列程式碼會將 blob 的內容傳輸至資料流物件。 然後，該物件會保存到本機檔案。 (變更*&lt;本機檔案名稱 >*以完整的檔案名稱，代表 blob 為下載。) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStreamAsync(fileStream).Wait();
    }
    ```
    
    下圖顯示已完成`ListBlobs`方法 （包含所建立的本機檔案的完整路徑）：
    
    ```csharp
    public string DownloadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenWrite(@"c:\src\downloadedBlob.txt"))
        {
            blob.DownloadToStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. 在**方案總管 中**，依序展開**檢視** > **共用**資料夾，然後開啟`_Layout.cshtml`。

1. 最後一個之後`<li>`項目在清單中，加入下列 HTML 新增另一個瀏覽功能表項目：

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DownloadBlob">Download blob</a></li>
    ```

1. 執行應用程式，並選取 [下載 blob] 下載 blob。 在指定的 blob`CloudBlobContainer.GetBlockBlobReference`方法呼叫中指定的位置下載`File.OpenWrite`方法呼叫。 文字*成功 ！* 應該會出現在瀏覽器中。 

## <a name="delete-blobs"></a>刪除 Blob

下列步驟說明如何刪除 blob：

1. 開啟 `BlobsController.cs` 檔案。

1. 將方法呼叫`DeleteBlob`所傳回的字串。

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. 內`DeleteBlob`方法，取得`CloudBlobContainer`物件，代表 blob 容器的參考。
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. 取得 blob 的參考物件，藉由呼叫`CloudBlobContainer.GetBlockBlobReference`方法。 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. 若要刪除的 blob，使用`Delete`方法。

    ```csharp
    blob.DeleteAsync().Wait();
    ```
    
    已完成`DeleteBlob`方法應該會出現，如下所示：
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.DeleteAsync().Wait();
        return "success!";
    }
    ```

1. 在**方案總管 中**，依序展開**檢視** > **共用**資料夾，然後開啟`_Layout.cshtml`。

1. 最後一個之後`<li>`項目在清單中，加入下列 HTML 新增另一個瀏覽功能表項目：

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DeleteBlob">Delete blob</a></li>
    ```

1. 執行應用程式，並選取**刪除 blob**刪除在指定的 blob`CloudBlobContainer.GetBlockBlobReference`方法呼叫。 文字*成功 ！* 應該會出現在瀏覽器中。 選取的瀏覽器**回**按鈕，然後再選取**列出 blob**驗證 blob 已不存在於容器。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您學會如何儲存、 清單，及藉由使用 ASP.NET Core 擷取 Azure 儲存體中的 blob。 如需了解 Azure 中的其他資料儲存選項，請檢視更多功能指南。

  * [開始使用 Azure 資料表儲存體和 Visual Studio 已連接服務 (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [開始使用 Azure 佇列儲存體和 Visual Studio 已連接服務 (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
