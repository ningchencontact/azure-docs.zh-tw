---
title: "從 Azure 儲存體下載大量的隨機資料 |Microsoft 文件"
description: "了解如何使用 Azure SDK 下載大量的隨機資料從 Azure 儲存體帳戶"
services: storage
documentationcenter: 
author: georgewallace
manager: jeconnoc
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/12/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 46b0cf3666088175372b6a2e73b3dd421a4bff8b
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2017
---
# <a name="download-large-amounts-of-random-data-from-azure-storage"></a>從 Azure 儲存體下載大量的隨機資料

本教學課程是一個系列課程的第三部分。 本教學課程會示範如何從 Azure 儲存體下載大量的資料。

在系列的第三部分中，您將了解如何：

> [!div class="checklist"]
> * 更新應用程式
> * 執行應用程式
> * 驗證連接的數目

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您必須先完成上一個儲存體教學課程：[上傳大量的隨機資料至 Azure 儲存體平行][previous-tutorial]。

## <a name="remote-into-your-virtual-machine"></a>遠端登入您的虛擬機器

 若要建立遠端桌面工作階段與虛擬機器，請使用下列命令在本機電腦上。 以虛擬機器的公用 IP 位址取代 IP 位址。 出現提示時，請輸入您在建立虛擬機器時所使用的認證。

```
mstsc /v:<publicIpAddress>
```

## <a name="update-the-application"></a>更新應用程式

在上一個教學課程中，您只能上傳檔案到儲存體帳戶。 在文字編輯器中開啟 `D:\git\storage-dotnet-perf-scale-app\Program.cs`。 取代`Main`利用下列範例的方法。 此範例註解上傳工作，並 uncomments 下載工作和工作，以刪除完成時的儲存體帳戶中的內容。

```csharp
public static void Main(string[] args)
{
    Console.WriteLine("Azure Blob storage performance and scalability sample");
    // Set threading and default connection limit to 100 to ensure multiple threads and connections can be opened.
    // This is in addition to parallelism with the storage client library that is defined in the functions below.
    ThreadPool.SetMinThreads(100, 4);
    ServicePointManager.DefaultConnectionLimit = 100; // (Or More)

    bool exception = false;
    try
    {
        // Call the UploadFilesAsync function.
        UploadFilesAsync().GetAwaiter().GetResult();

        // Uncomment the following line to enable downloading of files from the storage account.  This is commented out
        // initially to support the tutorial at https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blob-scaleable-app-download-files.
        // DownloadFilesAsync().GetAwaiter().GetResult();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
        exception = true;
    }
    finally
    {
        // The following function will delete the container and all files contained in them.  This is commented out initialy
        // As the tutorial at https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blob-scaleable-app-download-files has you upload only for one tutorial and download for the other. 
        if (!exception)
        {
            // DeleteExistingContainersAsync().GetAwaiter().GetResult();
        }
        Console.WriteLine("Press any key to exit the application");
        Console.ReadKey();
    }
}
```

在更新應用程式之後，您需要建置的應用程式。 開啟`Command Prompt`並瀏覽至`D:\git\storage-dotnet-perf-scale-app`。 重建應用程式執行`dotnet build`如下列範例所示：

```
dotnet build
```

## <a name="run-the-application"></a>執行應用程式

既然已經重建應用程式就可以使用更新的程式碼執行的應用程式開始。 如果尚未開啟，請開啟`Command Prompt`並瀏覽至`D:\git\storage-dotnet-perf-scale-app`。

輸入 `dotnet run` 執行應用程式。

```
dotnet run
```

在應用程式會讀取位於儲存體帳戶中指定的容器**storageconnectionstring**。 它會逐一於使用的 blob 10 [ListBlobsSegmented](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmented?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_ListBlobsSegmented_System_String_System_Boolean_Microsoft_WindowsAzure_Storage_Blob_BlobListingDetails_System_Nullable_System_Int32__Microsoft_WindowsAzure_Storage_Blob_BlobContinuationToken_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_)方法中的容器和下載它們將會是本機電腦使用[DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_DownloadToFileAsync_System_String_System_IO_FileMode_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_)方法。
下表顯示[BlobRequestOptions](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions?view=azure-dotnet)下載時所定義的每個 blob。

|屬性|值|說明|
|---|---|---|
|[DisableContentMD5Validation](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.disablecontentmd5validation?view=azure-dotnet)| true| 這個屬性停用檢查上傳內容的 MD5 雜湊。 停用 MD5 驗證將會產生更快的傳輸。 但不會確認有效性或傳輸的檔案的完整性。 |
|[StorBlobContentMD5](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.storeblobcontentmd5?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_StoreBlobContentMD5)| false| 此屬性決定，是否計算並儲存 MD5 雜湊。   |

`DownloadFilesAsync`工作在下列範例所示：

```csharp
private static async Task DownloadFilesAsync()
{
    CloudBlobClient blobClient = GetCloudBlobClient();

    // Define the BlobRequestionOptions on the download, including disabling MD5 hash validation for this example, this improves the download speed.
    BlobRequestOptions options = new BlobRequestOptions
    {
        DisableContentMD5Validation = true,
        StoreBlobContentMD5 = false
    };

    // Retrieve the list of containers in the storage account.  Create a directory and configure variables for use later.
    BlobContinuationToken continuationToken = null;
    List<CloudBlobContainer> containers = new List<CloudBlobContainer>();
    do
    {
        var listingResult = await blobClient.ListContainersSegmentedAsync(continuationToken);
        continuationToken = listingResult.ContinuationToken;
        containers.AddRange(listingResult.Results);
    }
    while (continuationToken != null);

    var directory = Directory.CreateDirectory("download");
    BlobResultSegment resultSegment = null;
    Stopwatch time = Stopwatch.StartNew();

    // Download the blobs
    try
    {
        List<Task> tasks = new List<Task>();
        int max_outstanding = 100;
        int completed_count = 0;

        // Create a new instance of the SemaphoreSlim class to define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        // Iterate through the containers
        foreach (CloudBlobContainer container in containers)
        {
            do
            {
                // Return the blobs from the container lazily 10 at a time.
                resultSegment = await container.ListBlobsSegmentedAsync(null, true, BlobListingDetails.All, 10, continuationToken, null, null);
                continuationToken = resultSegment.ContinuationToken;
                {
                    foreach (var blobItem in resultSegment.Results)
                    {

                        if (((CloudBlob)blobItem).Properties.BlobType == BlobType.BlockBlob)
                        {
                            // Get the blob and add a task to download the blob asynchronously from the storage account.
                            CloudBlockBlob blockBlob = container.GetBlockBlobReference(((CloudBlockBlob)blobItem).Name);
                            Console.WriteLine("Downloading {0} from container {1}", blockBlob.Name, container.Name);
                            await sem.WaitAsync();
                            tasks.Add(blockBlob.DownloadToFileAsync(directory.FullName + "\\" + blockBlob.Name, FileMode.Create, null, options, null).ContinueWith((t) =>
                            {
                                sem.Release();
                                Interlocked.Increment(ref completed_count);
                            }));

                        }
                    }
                }
            }
            while (continuationToken != null);
        }

        // Creates an asynchronous task that completes when all the downloads complete.
        await Task.WhenAll(tasks);
    }
    catch (Exception e)
    {
        Console.WriteLine("\nError encountered during transfer: {0}", e.Message);
    }

    time.Stop();
    Console.WriteLine("Download has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());
    Console.ReadLine();
}
```

### <a name="validate-the-connections"></a>驗證的連接

雖然下載檔案時，您可以確認並行連接數目至儲存體帳戶。 開啟`Command Prompt`和型別`netstat -a | find /c "blob:https"`。 這個命令會顯示目前正在使用開啟的連接數目`netstat`。 下列範例相似的輸出，以自行執行教學課程時看到的內容。 您可以看到的範例中，超過 280 連接已開啟時從儲存體帳戶下載隨機的檔案。

```
C:\>netstat -a | find /c "blob:https"
289

C:\>
```

## <a name="next-steps"></a>後續步驟

一部分的三個數列，您已了解有關從儲存體帳戶，例如如何下載大量的隨機資料：

> [!div class="checklist"]
> * 執行應用程式
> * 驗證連接的數目

若要確認在入口網站的輸送量和延遲計量數列的第四部分前進。

> [!div class="nextstepaction"]
> [請確認在入口網站的輸送量和延遲計量](storage-blob-scalable-app-verify-metrics.md)

[previous-tutorial]: storage-blob-scalable-app-upload-files.md