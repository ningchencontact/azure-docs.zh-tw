---
title: "開始使用 Azure Blob 儲存體和 Visual Studio 已連接服務 (ASP.NET) |Microsoft 文件"
description: "如何開始使用 Azure Blob 儲存體，在 Visual Studio 中，在 ASP.NET 專案中使用 Visual Studio 連接至儲存體帳戶之後已連接服務"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: b3497055-bef8-4c95-8567-181556b50d95
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: kraig
ms.openlocfilehash: cb406e528568dafd1e142943f5273ad58e550609
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2018
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>開始使用 Azure Blob 儲存體和 Visual Studio 已連接服務 (ASP.NET)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Azure Blob 儲存體是為物件或 blob 時，將非結構化的資料儲存在雲端中的服務。 Blob 儲存體可以儲存任何類型的文字或二進位資料，例如文件、媒體檔案或應用程式安裝程式。 Blob 儲存體也稱為物件儲存體。

本教學課程會示範如何撰寫一些常見案例，使用 Blob 儲存體的 ASP.NET 程式碼。 案例包括建立 blob 容器，以及上傳、列出、下載和刪除 blob。

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>必要條件

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]


[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

## <a name="create-an-mvc-controller"></a>建立 MVC 控制器 

1. 在**方案總管 中**，以滑鼠右鍵按一下**控制器**。

2. 從內容功能表中，選取**新增** > **控制器**。

    ![螢幕擷取畫面的方案總管 中，新增與反白顯示的控制站](./media/vs-storage-aspnet-getting-started-blobs/add-controller-menu.png)

1. 在**新增 Scaffold**對話方塊中，選取**MVC 5 控制器空白**，然後選取**新增**。

    ![對話方塊中的新增 Scaffold 的螢幕擷取畫面](./media/vs-storage-aspnet-getting-started-blobs/add-controller.png)

1. 在**加入控制器**對話方塊中，名稱控制器*BlobsController*，然後選取**新增**。

    ![螢幕擷取畫面加入控制器 對話方塊](./media/vs-storage-aspnet-getting-started-blobs/add-controller-name.png)

1. 加入下列`using`指示詞加入`BlobsController.cs`檔案：

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>連接到儲存體帳戶並取得容器的參考

Blob 容器是 blob 和資料夾的巢狀階層。 這份文件中的步驟的其餘部分要求 blob 容器的參考，讓程式碼應該放在自己的重複使用性的方法。

下列步驟建立方法以使用中的連接字串連接到儲存體帳戶**Web.config**。步驟也會建立容器的參考。  中的連接字串設定**Web.config**具名格式`<storageaccountname>_AzureStorageConnectionString`。 

1. 開啟 `BlobsController.cs` 檔案。

1. 將方法呼叫**GetCloudBlobContainer**傳回**CloudBlobContainer**。  請務必取代`<storageaccountname>_AzureStorageConnectionString`中的索引鍵的實際名稱**Web.config**。
    
    ```csharp
    private CloudBlobContainer GetCloudBlobContainer()
    {
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
                CloudConfigurationManager.GetSetting("<storageaccountname>_AzureStorageConnectionString"));
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
    ViewBag.Success = container.CreateIfNotExists();
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
        ViewBag.Success = container.CreateIfNotExists();
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

1. 在最後一個 **Html.ActionLink** 之後，新增下列 **Html.ActionLink**：

    ```html
    <li>@Html.ActionLink("Create blob container", "CreateBlobContainer", "Blobs")</li>
    ```

1. 執行應用程式，並選取**建立 Blob 容器**來查看結果類似下列的螢幕擷取畫面：
  
    ![建立螢幕擷取畫面的 blob 容器](./media/vs-storage-aspnet-getting-started-blobs/create-blob-container-results.png)

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
        blob.UploadFromStream(fileStream);
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
            blob.UploadFromStream(fileStream);
        }
        return "success!";
    }
    ```

1. 在**方案總管 中**，依序展開**檢視** > **共用**資料夾，然後開啟`_Layout.cshtml`。

1. 在最後一個 **Html.ActionLink** 之後，新增下列 **Html.ActionLink**：

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. 執行應用程式，並選取 [上傳 blob]。  Word*成功 ！* 應該會出現。
    
    ![成功驗證的螢幕擷取畫面](./media/vs-storage-aspnet-getting-started-blobs/upload-blob.png)
  
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
   
1. 若要列出 blob 容器中的 blob，請使用`CloudBlobContainer.ListBlobs`方法。 `CloudBlobContainer.ListBlobs`方法會傳回`IListBlobItem`物件，可以轉型為`CloudBlockBlob`， `CloudPageBlob`，或`CloudBlobDirectory`物件。 下列程式碼片段列舉 blob 容器中的所有 blob。 每個 blob 會轉換成適當的物件，其類型為基礎。 其名稱 (如果是 URI 或**CloudBlobDirectory**) 加入至清單。

    ```csharp
    List<string> blobs = new List<string>();

    foreach (IListBlobItem item in container.ListBlobs())
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

    除了 blob，blob 容器可以包含目錄。 假設沒有呼叫 blob 容器*測試 blob 容器*，與下列階層：

        foo.png
        dir1/bar.png
        dir2/baz.png

    使用上述的程式碼範例，**blob** 字串清單包含類似下列的值︰

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    此清單所示，包含只有最上層實體，不是巢狀的 (*bar.png*和*baz.png*)。 若要列出 blob 容器內的所有實體，變更的程式碼，讓**CloudBlobContainer.ListBlobs**方法傳遞**true**的**useFlatBlobListing**參數。    

    ```csharp
    //...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    //...
    ```

    設定**useFlatBlobListing**參數**true**傳回 blob 容器中的所有實體的一般清單。 這會產生下列結果：

        foo.png
        dir1/bar.png
        dir2/baz.png
    
    下圖顯示已完成**ListBlobs**方法：

    ```csharp
    public ActionResult ListBlobs()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        List<string> blobs = new List<string>();
        foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing: true))
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

1. 在最後一個 **Html.ActionLink** 之後，新增下列 **Html.ActionLink**：

    ```html
    <li>@Html.ActionLink("List blobs", "ListBlobs", "Blobs")</li>
    ```

1. 執行應用程式，並選取**列出 blob**來查看結果類似下列的螢幕擷取畫面：
  
    ![Blob 清單的螢幕擷取畫面](./media/vs-storage-aspnet-getting-started-blobs/listblobs.png)

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
        blob.DownloadToStream(fileStream);
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
            blob.DownloadToStream(fileStream);
        }
        return "success!";
    }
    ```

1. 在**方案總管 中**，依序展開**檢視** > **共用**資料夾，然後開啟`_Layout.cshtml`。

1. 在最後一個 **Html.ActionLink** 之後，新增下列 **Html.ActionLink**：

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. 執行應用程式，並選取 [下載 blob] 下載 blob。 在指定的 blob`CloudBlobContainer.GetBlockBlobReference`方法呼叫中指定的位置下載`File.OpenWrite`方法呼叫。  文字*成功 ！* 應該會出現在瀏覽器中。 

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
    blob.Delete();
    ```
    
    已完成`DeleteBlob`方法應該會出現，如下所示：
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.Delete();
        return "success!";
    }
    ```

1. 在**方案總管 中**，依序展開**檢視** > **共用**資料夾，然後開啟`_Layout.cshtml`。

1. 在最後一個 **Html.ActionLink** 之後，新增下列 **Html.ActionLink**：

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. 執行應用程式，並選取**刪除 blob**刪除在指定的 blob`CloudBlobContainer.GetBlockBlobReference`方法呼叫。 文字*成功 ！* 應該會出現在瀏覽器中。 選取的瀏覽器**回**按鈕，然後再選取**列出 blob**驗證 blob 已不存在於容器。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您學會如何儲存、 清單，及藉由使用 ASP.NET 擷取 Azure 儲存體中的 blob。 如需了解 Azure 中的其他資料儲存選項，請檢視更多功能指南。

  * [開始使用 Azure 資料表儲存體和 Visual Studio 已連接服務 (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [開始使用 Azure 佇列儲存體和 Visual Studio 已連接服務 (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
