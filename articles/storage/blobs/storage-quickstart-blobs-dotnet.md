---
title: "Azure 快速入門 - 使用 .NET 上傳、下載及列出 Azure 儲存體中的 Blob | Microsoft Docs"
description: "在本快速入門中，您會建立儲存體帳戶和容器。 然後，使用 .NET 的儲存體用戶端程式庫將 blob 上傳至 Azure 儲存體、下載 blob，以及列出容器中的 blob。"
services: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/22/2018
ms.author: tamram
ms.openlocfilehash: 265691ff189c628156f234083645a4b2ca4b637b
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/24/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-net"></a>快速入門：使用 .NET 上傳、下載及列出 Blob

在本快速入門中，您會了解如何使用適用於 Azure 儲存體的 .NET 用戶端程式庫來上傳、下載及列出容器中的區塊 Blob。

## <a name="prerequisites"></a>先決條件

若要完成本快速入門：

* 安裝適用於 [Linux](/dotnet/core/linux-prerequisites?tabs=netcore2x) 或 [Windows](/dotnet/core/windows-prerequisites?tabs=netcore2x) 的 .NET Core 2.0

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>下載範例應用程式

本快速入門使用的範例應用程式是基本的主控台應用程式。 

使用 [git](https://git-scm.com/) 將應用程式的複本下載至您的開發環境。 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

此命令會將存放庫複製到本機的 git 資料夾。 若要開啟 Visual Studio 解決方案，請找到並開啟 storage-blobs-dotnet-quickstart 資料夾，然後按兩下 storage-blobs-dotnet-quickstart.sln。 

## <a name="configure-your-storage-connection-string"></a>設定儲存體連接字串

在應用程式，您必須提供儲存體帳戶的連接字串。 建議您在執行應用程式的本機電腦上，將此連接字串儲存在環境變數內。 請根據您的作業系統，遵循以下其中一個範例來建立環境變數。  將 \<yourconnectionstring\> 取代為您實際的連接字串。

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```
### <a name="windows"></a>Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

## <a name="run-the-sample"></a>執行範例

此範例會在 [我的文件] 中建立測試檔案、將它上傳至 Blob 儲存體、列出容器中的 Blob，然後以新名稱下載該檔案，以便比較新舊檔案。 

瀏覽至您的應用程式目錄，並使用 `dotnet run` 命令執行應用程式。

```
dotnet run
```

所顯示的輸出類似於下列範例：

```
Azure Blob storage quick start sample
Temp file = /home/admin/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt
Uploading to Blob storage as blob 'QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt'
List blobs in container.
https://mystorageaccount.blob.core.windows.net/quickstartblobs/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt
Downloading blob to /home/admin/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374_DOWNLOADED.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.
```

當您按任意鍵繼續時，它會刪除儲存體容器和檔案。 在繼續之前，請檢查 MyDocuments 找出這兩個檔案。 您可以開啟它們，並查看它們是否相同。 複製主控台視窗的 Blob URL，將它貼至瀏覽器以檢視 Blob 儲存體中的檔案內容。

您也可以使用 [Azure 儲存體總管](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 之類的工具來檢視 Blob 儲存體中的檔案。 Azure 儲存體總管是免費的跨平台工具，可讓您存取儲存體帳戶資訊。 

確認檔案之後，請按任一鍵以完成示範並刪除測試檔案。 現在您已知道這個範例的工作內容，請開啟 Program.cs 檔案中查看程式碼。 

## <a name="understand-the-sample-code"></a>了解範例程式碼

接下來，我們將透過範例程式碼來了解其運作方式。

### <a name="get-references-to-the-storage-objects"></a>取得儲存體物件的參考

第一件事是建立用來存取和管理 Blob 儲存體的物件參考。 這些物件互為建置基礎，各自都是由清單中的下一個物件所使用。

* 建立指向儲存體帳戶之 [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet) 物件的執行個體。

* 建立 [CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet) 物件的執行個體，它會指向您儲存體帳戶中的 Blob 服務。

* 建立 [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet) 物件的執行個體，它代表您要存取的容器。 容器是用來組織 Blob，就像在電腦上用資料夾組織檔案一樣。

只要您有 [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)，就可以建立 [CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet) 物件的執行個體，指向您感興趣的特定 Blob 並執行上傳、下載、複製等等作業。

> [!IMPORTANT]
> 容器名稱必須是小寫字母。 如需有關容器和 Blob 名稱的詳細資訊，請參閱[命名和參考容器、Blob 及中繼資料](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

在本節中，您要建立物件的執行個體、建立新的容器，然後設定容器上的權限，以便公開 Blob 並使用 URL 即可存取。 容器名為 **quickstartblobs**。

因為我們需要在每次執行範例時建立新的容器，所以此範例會使用 [CreateIfNotExists](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createifnotexists?view=azure-dotnet)。 在整個應用程式都使用相同容器的生產環境中，最好只呼叫一次 **CreateIfNotExists**。 或者，若要事先建立容器，以便您不需要在程式碼中加以建立。

```csharp
// Load the connection string for use with the application. The storage connection string is stored
// in an environment variable on the machine running the application called storageconnectionstring.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");
if (storageConnectionString == null)
{
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add a environment variable name 'storageconnectionstring' with the actual storage " +
        "connection string as a value.");
}
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

// Create the CloudBlobClient that is used to call the Blob Service for that storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs'. 
cloudBlobContainer = cloudBlobClient.GetContainerReference("quickstartblobs" + Guid.NewGuid().ToString());
await cloudBlobContainer.CreateIfNotExistsAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-the-container"></a>將 Blob 上傳到容器

Blob 儲存體支援區塊 Blob、附加 Blob 和分頁 Blob。 最常使用的是區塊 Blob，這也是本快速入門中所使用的。

若要將檔案上傳至 Blob，請取得目標容器中的 Blob 參考。 取得 Blob 參考之後，即可使用 [CloudBlockBlob.UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync) 將資料上傳給它。 如果沒有 Blob，此作業會建立 Blob，如已存在，則予以覆寫。

範例程式碼會建立用於上傳和下載的本機檔案，將要上傳的檔案以 **localFileName** 中的 Blob 名稱儲存為 **fileAndPath**。 下列範例會將檔案上傳到名為 **quickstartblobs** 的容器。

```csharp
// Create a file in MyDocuments to test the upload and download.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the location where the blob is going to go, then upload the file.
// Upload the file you created, use localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

有數個上傳方法可以搭配 Blob 儲存體使用。 例如，如果有記憶體資料流，則您可以使用 UploadFromStreamAsync 方法，而不是 UploadFromFileAsync 方法。

區塊 Blob 可以是任何類型的文字或二進位檔案。 分頁 Blob 主要用於備份 IaaS VM 所用的 VHD 檔案。 附加 Blob 是用於記錄，例如當您想要寫入檔案，並繼續新增更多資訊時。 儲存在 Blob 儲存體中的大部分物件都是區塊 Blob。

### <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

您可以使用 [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync) 來取得容器中的檔案清單。 下列程式碼會擷取 Blob 的清單，透過它們執行迴圈，顯示找到的 Blob URI。 您可以複製命令視窗中的 URI，將它貼入至瀏覽器來檢視檔案。

如果容器中有 5,000 個或更少的 Blob，呼叫一次 ListBlobsSegmentedAsync 即可擷取所有的 Blob 名稱。 如果容器中有 5000 個以上的 Blob，服務會以 5000 為一組擷取清單，直到擷取所有的 Blob 名稱。 因此第一次呼叫此 API 時，它會傳回第一組的 5000 個 Blob 名稱和接續權杖。 第二次，您要提供權杖，服務會擷取下一組的 Blob 名稱，一直反復到接續權杖為 null 為止，這表示已擷取所有的 Blob 名稱。

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    blobContinuationToken = results.ContinuationToken;
    foreach (IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
    blobContinuationToken = results.ContinuationToken;
} while (blobContinuationToken != null);

```

### <a name="download-blobs"></a>下載 Blob

使用 [CloudBlob.DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync) 將 Blob 下載到本機磁碟。

下列程式碼會下載上一節中上傳的 Blob，將 "_DOWNLOADED" 尾碼新增至 Blob 名稱，以便您在本機磁碟上看到這兩個檔案。

```csharp
// Download blob. In most cases, you would have to retrieve the reference
//   to cloudBlockBlob here. However, we created that reference earlier, and 
//   haven't changed the blob we're interested in, so we can reuse it. 
// First, add a _DOWNLOADED before the .txt so you can see both files in MyDocuments.
destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);  
```

### <a name="clean-up-resources"></a>清除資源

如果不再需要本快速入門中上傳的 Blob，您可以使用 [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync) 刪除整個容器。 如果不再需要所建立的檔案，請一併刪除。

```csharp
Console.WriteLine("Press the 'Enter' key to delete the sample files, example container, and exit the application.");
Console.ReadLine();
// Clean up resources. This includes the container and the two temp files.
Console.WriteLine("Deleting the container");
if (cloudBlobContainer != null)
{
    await cloudBlobContainer.DeleteIfExistsAsync();
}
Console.WriteLine("Deleting the source, and downloaded files");
File.Delete(sourceFile);
File.Delete(destinationFile);
```

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何使用 .NET 在本機磁碟和 Azure Blob 儲存體之間傳輸檔案。 若要深入瞭解 Blob 儲存體的用法，請繼續閱讀 Blob 儲存體使用說明。

> [!div class="nextstepaction"]
> [Blob 儲存體作業操作說明](storage-dotnet-how-to-use-blobs.md)

如需可供您下載並執行的其他 Azure 儲存體程式碼範例，請參閱[使用 .NET 的 Azure 儲存體範例](../common/storage-samples-dotnet.md)清單。

如需儲存體總管和 Blob 的詳細資訊，請參閱[使用儲存體總管管理 Azure Blob 儲存體資源](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。
