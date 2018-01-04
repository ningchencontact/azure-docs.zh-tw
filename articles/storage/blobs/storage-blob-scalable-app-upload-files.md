---
title: "上傳大量的隨機資料至 Azure 儲存體平行 |Microsoft 文件"
description: "了解如何使用 Azure SDK 上傳大量的並行的 Azure 儲存體帳戶的隨機資料"
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
ms.openlocfilehash: 98f3f69c6025d61caac20e13b573651854952432
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/23/2017
---
# <a name="upload-large-amounts-of-random-data-in-parallel-to-azure-storage"></a>上傳大量的隨機 Azure 儲存體的並行資料

本教學課程是一個系列的第二部分。 本教學課程告訴您部署的應用程式，將大量的隨機資料上傳至 Azure 儲存體帳戶。

在本系列的第二部分中，您將瞭解如何：

> [!div class="checklist"]
> * 設定連接字串
> * 建置應用程式
> * 執行應用程式
> * 驗證連接的數目

Azure blob 儲存體提供可擴充的服務，來儲存您的資料。 若要確保您的應用程式為高效能越好，了解 blob 儲存體的運作方式的建議。 重要限制的 Azure blob 中的知識，若要深入了解有關這些限制的詳細資訊請造訪： [blob 儲存體的延展性目標](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets)。

[分割區命名](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#subheading47)設計使用 blob 的高執行應用程式時，是另一個重要的因素。 Azure 儲存體使用小數位數和負載平衡，以範圍為基礎的資料分割配置。 此設定表示具有類似的命名慣例或前置詞的檔案移到相同的磁碟分割。 這個邏輯也包含檔案已上傳到容器的名稱。 在本教學課程中，您可以使用具有 Guid 的名稱，也為隨機產生的內容檔案。 他們再上傳至五個不同的隨機名稱的容器。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您必須先完成上一個儲存體教學課程：[建立虛擬機器和可擴充的應用程式的儲存體帳戶][previous-tutorial]。

## <a name="remote-into-your-virtual-machine"></a>遠端登入您的虛擬機器

在本機電腦上使用下列命令，以與虛擬機器建立遠端桌面工作階段。 以虛擬機器的公用 IP 位址取代 IP 位址。 出現提示時，輸入您建立虛擬機器時使用的認證。

```
mstsc /v:<publicIpAddress>
```

## <a name="configure-the-connection-string"></a>設定連接字串

在 Azure 入口網站中，瀏覽至儲存體帳戶。 選取**存取金鑰**下**設定**儲存體帳戶中。 複製**連接字串**從主要或次要金鑰。 登入您在上一個教學課程中建立的虛擬機器。 開啟**命令提示字元**系統管理員身分執行`setx`命令搭配`/m`參數，此命令會將電腦設定環境變數。 環境變數不可以使用，直到您重新載入**命令提示字元**。 取代 **\<storageConnectionString\>** 在下列範例中：

```
setx storageconnectionstring "<storageConnectionString>" /m
```

完成後，請開啟另一個**命令提示字元**，瀏覽至`D:\git\storage-dotnet-perf-scale-app`和型別`dotnet build`建置應用程式。

## <a name="run-the-application"></a>執行應用程式

瀏覽至 `D:\git\storage-dotnet-perf-scale-app`。

輸入 `dotnet run` 執行應用程式。 第一次執行`dotnet`會填入您的本機套件快取，以改善還原速度以及啟用離線存取。 此命令會佔用一分鐘的時間才能完成，並只發生一次。

```
dotnet run
```

應用程式會建立五個隨機命名的容器，並開始將暫存目錄中的檔案上傳至儲存體帳戶。 應用程式會將最小執行緒設定為 100 而[DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit(v=vs.110).aspx) 100 到確保大量的並行連線允許時要執行應用程式。

除了設定的執行緒和連線限制設定[BlobRequestOptions](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions?view=azure-dotnet)如[UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet)方法會設定為使用平行處理原則，並停用 MD5 雜湊驗證。 在 100 mb 的區塊上傳檔案，這項設定提供更佳的效能，但可能會很費時，如果使用不良執行網路如同失敗整個 100 mb 的區塊會重試。

|屬性|值|說明|
|---|---|---|
|[ParallelOperationThreadCount](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.paralleloperationthreadcount?view=azure-dotnet)| 8| 設定成的區塊中斷 blob 上傳時。 最高的效能，這個值應該是 8 次的核心數目。 |
|[DisableContentMD5Validation](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.disablecontentmd5validation?view=azure-dotnet)| true| 這個屬性停用檢查上傳內容的 MD5 雜湊。 停用 MD5 驗證將會產生更快的傳輸。 但不會確認有效性或傳輸的檔案的完整性。   |
|[StorBlobContentMD5](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.storeblobcontentmd5?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_StoreBlobContentMD5)| false| 此屬性決定的 MD5 雜湊計算並儲存的檔案。   |
| [RetryPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.retrypolicy?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_RetryPolicy)| 具有 10 個最大重試 2 秒輪詢 |判斷要求的重試原則。 連接失敗時重試，在此範例中[ExponentialRetry](/dotnet/api/microsoft.windowsazure.storage.retrypolicies.exponentialretry?view=azure-dotnet)原則設定為 2 秒輪詢及重試次數上限為 10。 當達到接近您的應用程式時，此設定很重要[blob 儲存體的延展性目標](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets)。  |

`UploadFilesAsync`工作在下列範例所示：

```csharp
private static async Task UploadFilesAsync()
{
    // Create random 5 characters containers to upload files to.
    CloudBlobContainer[] containers = await GetRandomContainersAsync();
    var currentdir = System.IO.Directory.GetCurrentDirectory();

    // path to the directory to upload
    string uploadPath = currentdir + "\\upload";
    Stopwatch time = Stopwatch.StartNew();
    try
    {
        Console.WriteLine("Iterating in directory: {0}", uploadPath);
        int count = 0;
        int max_outstanding = 100;
        int completed_count = 0;

        // Define the BlobRequestionOptions on the upload.
        // This includes defining an exponential retry policy to ensure that failed connections are retried with a backoff policy. As multiple large files are being uploaded
        // large block sizes this can cause an issue if an exponential retry policy is not defined.  Additionally parallel operations are enabled with a thread count of 8
        // This could be should be multiple of the number of cores that the machine has. Lastly MD5 hash validation is disabled for this example, this improves the upload speed.
        BlobRequestOptions options = new BlobRequestOptions
        {
            ParallelOperationThreadCount = 8,
            DisableContentMD5Validation = true,
            StoreBlobContentMD5 = false
        };
        // Create a new instance of the SemaphoreSlim class to define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        List<Task> tasks = new List<Task>();
        Console.WriteLine("Found {0} file(s)", Directory.GetFiles(uploadPath).Count());

        // Iterate through the files
        foreach (string path in Directory.GetFiles(uploadPath))
        {
            // Create random file names and set the block size that is used for the upload.
            var container = containers[count % 5];
            string fileName = Path.GetFileName(path);
            Console.WriteLine("Uploading {0} to container {1}.", path, container.Name);
            CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

            // Set block size to 100MB.
            blockBlob.StreamWriteSizeInBytes = 100 * 1024 * 1024;
            await sem.WaitAsync();

            // Create tasks for each file that is uploaded. This is added to a collection that executes them all asyncronously.  
            tasks.Add(blockBlob.UploadFromFileAsync(path, null, options, null).ContinueWith((t) =>
            {
                sem.Release();
                Interlocked.Increment(ref completed_count);
            }));
            count++;
        }

        // Creates an asynchronous task that completes when all the uploads complete.
        await Task.WhenAll(tasks);

        time.Stop();

        Console.WriteLine("Upload has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());

        Console.ReadLine();
    }
    catch (DirectoryNotFoundException ex)
    {
        Console.WriteLine("Error parsing files in the directory: {0}", ex.Message);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }
}
```

下列範例是 Windows 系統上執行的已截斷的應用程式輸出。

```
Created container https://mystorageaccount.blob.core.windows.net/9efa7ecb-2b24-49ff-8e5b-1d25e5481076
Created container https://mystorageaccount.blob.core.windows.net/bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b
Created container https://mystorageaccount.blob.core.windows.net/9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f
Created container https://mystorageaccount.blob.core.windows.net/47646f1a-c498-40cd-9dae-840f46072180
Created container https://mystorageaccount.blob.core.windows.net/38b2cdab-45fa-4cf9-94e7-d533837365aa
Iterating in directory: D:\git\storage-dotnet-perf-scale-app\upload
Found 50 file(s)
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\1d596d16-f6de-4c4c-8058-50ebd8141e4d.txt to container 9efa7ecb-2b24-49ff-8e5b-1d25e5481076.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\242ff392-78be-41fb-b9d4-aee8152a6279.txt to container bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\38d4d7e2-acb4-4efc-ba39-f9611d0d55ef.txt to container 9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\45930d63-b0d0-425f-a766-cda27ff00d32.txt to container 47646f1a-c498-40cd-9dae-840f46072180.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\5129b385-5781-43be-8bac-e2fbb7d2bd82.txt to container 38b2cdab-45fa-4cf9-94e7-d533837365aa.
...
Upload has been completed in 142.0429536 seconds. Press any key to continue
```

### <a name="validate-the-connections"></a>驗證的連接

正在上傳檔案，而您可以確認並行連接數目至儲存體帳戶。 開啟**命令提示字元**和型別`netstat -a | find /c "blob:https"`。 這個命令會顯示目前正在使用開啟的連接數目`netstat`。 下列範例相似的輸出，以自行執行教學課程時看到的內容。 您可以看到的範例中，將隨機檔案上傳至儲存體帳戶時 800 連接已開啟。 這個值會在整個執行上傳變更。 上傳以平行區塊的區塊，就會大幅減少傳送內容所需的時間量。

```
C:\>netstat -a | find /c "blob:https"
800

C:\>
```

## <a name="next-steps"></a>後續步驟

在數列的第二個部分，您會學到儲存體帳戶，以平行方式，例如如何上傳大量的隨機資料：

> [!div class="checklist"]
> * 設定連接字串
> * 建置應用程式
> * 執行應用程式
> * 驗證連接的數目

前進到三個部分從儲存體帳戶下載大量的資料數列。

> [!div class="nextstepaction"]
> [上傳大量的大型檔案，以平行方式來儲存體帳戶](storage-blob-scalable-app-download-files.md)

[previous-tutorial]: storage-blob-scalable-app-create-vm.md
