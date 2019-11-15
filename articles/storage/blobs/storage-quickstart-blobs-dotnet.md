---
title: 快速入門：Azure Blob 儲存體程式庫 v12 - .NET
description: 在本快速入門中，您將了解如何使用適用於 .NET 的 Azure Blob 儲存體用戶端程式庫 12 版，在 Blob (物件) 儲存體中建立容器與 Blob。 接下來，您要了解如何將 Blob 下載到本機電腦，以及如何列出容器中的所有 Blob。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 18f4053a2f8b1b5c880b46cf6eeb46c0dbf97f15
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825386"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-net"></a>快速入門：適用於 .NET 的 Azure Blob 儲存體用戶端程式庫 v12

開始使用適用於 .NET 的 Azure Blob 儲存體用戶端程式庫 v12。 Azure Blob 儲存體是 Microsoft 針對雲端推出的物件儲存體解決方案。 請依照下列步驟來安裝套件，並試用基本工作的範例程式碼。 Blob 儲存體已針對儲存大量非結構化資料最佳化。

> [!NOTE]
> 若要開始使用舊版 SDK，請參閱[快速入門：適用於 .NET 的 Azure Blob 儲存體用戶端程式庫](storage-quickstart-blobs-dotnet-legacy.md)。

使用適用於 .NET 的 Azure Blob 儲存體用戶端程式庫 v12：

* 建立容器
* 將 Blob 上傳至 Azure 儲存體
* 列出容器中的所有 Blob
* 將 Blob 下載到本機電腦
* 刪除容器

[API 參考文件](/dotnet/api/azure.storage.blobs) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs) | [套件 (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs/12.0.0) | [範例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* Azure 儲存體帳戶 - [建立儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* 適用於您作業系統的 [NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) \(英文\) 目前版本。 請務必取得 SDK，而不是執行階段。

## <a name="setting-up"></a>設定

本節會引導您準備專案以搭配適用於 .NET 的 Azure Blob 儲存體用戶端程式庫 v12 使用。

### <a name="create-the-project"></a>建立專案

建立一個名為 *BlobQuickstartV12* 的 .NET Core 應用程式。

1. 在主控台視窗中 (例如 cmd、PowerShell 或 Bash)，使用 `dotnet new` 命令建立名為 *BlobQuickstartV12* 的新主控台應用程式。 此命令會建立簡單的 "Hello World" C# 專案，內含單一原始程式檔：*Program.cs*。

   ```console
   dotnet new console -n BlobQuickstartV12
   ```

1. 切換至新建立的 *BlobQuickstartV12* 目錄。

   ```console
   cd BlobQuickstartV12
   ```

1. 在 *BlobQuickstartV12* 目錄內，建立另一個名為 *data* 的目錄。 這是將建立和儲存 Blob 資料檔案的位置。

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>安裝套件

若您仍在應用程式目錄中，請使用 `dotnet add package` 命令安裝適用於 .NET 套件的 Azure Blob 儲存體用戶端程式庫。

```console
dotnet add package Azure.Storage.Blobs
```

### <a name="set-up-the-app-framework"></a>設定應用程式架構

從專案目錄：

1. 在編輯器中開啟 Program.cs  檔案
1. 移除 `Console.WriteLine("Hello World!");` 陳述式
1. 新增 `using` 指示詞
1. 更新 `Main` 方法宣告以支援非同步程式碼

此程式碼如下：

```csharp
using Azure.Storage;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using System;
using System.IO;
using System.Threading.Tasks;

namespace BlobQuickstartV12
{
    class Program
    {
        static async Task Main()
        {
        }
    }
}
```

### <a name="copy-your-credentials-from-the-azure-portal"></a>從 Azure 入口網站複製您的認證

當應用程式範例向 Azure 儲存體發出要求時，該要求必須獲得授權。 若要對要求授權，請以連接字串的形式將儲存體帳戶認證新增至應用程式。 請依照下列步驟檢視您的儲存體帳戶認證：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 找出您的儲存體帳戶。
3. 在儲存體帳戶概觀的 [設定]  區段中，選取 [存取金鑰]  。 在此處，您可以檢視帳戶存取金鑰，和每個金鑰的完整連接字串。
4. 尋找 [金鑰 1]  下方的 [連接字串]  值，然後選取 [複製]  按鈕以複製連接字串。 在下一個步驟中，您會將連接字串值新增至環境變數。

    ![顯示如何從 Azure 入口網站複製連接字串的螢幕擷取畫面](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>設定儲存體連接字串

在複製您的連接字串後，請在執行應用程式的本機電腦上，將該字串寫入至新的環境變數中。 若要設定環境變數，請開啟主控台視窗，並遵循您的作業系統所適用的指示。 將 `<yourconnectionstring>` 用實際的連接字串取代。

#### <a name="windows"></a>Windows

```cmd
setx CONNECT_STR "<yourconnectionstring>"
```

在 Windows 中新增環境變數之後，您必須啟動新的命令視窗執行個體。

#### <a name="linux"></a>Linux

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="macos"></a>macOS

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="restart-programs"></a>重新啟動程式

新增環境變數之後，請重新啟動任何需要讀取環境變數的執行中程式。 例如，在繼續之前，先重新啟動您的開發環境或編輯器。

## <a name="object-model"></a>物件模型

Azure Blob 儲存體已針對儲存大量非結構化資料最佳化。 非結構化資料是指不遵守特定資料模型或定義的資料，例如文字或二進位資料。 Blob 儲存體提供三種類型資源：

* 儲存體帳戶
* 儲存體帳戶中的容器
* 容器中的 Blob

下圖顯示資源之間的關係。

![Blob 儲存體架構圖](./media/storage-blob-introduction/blob1.png)

使用下列 .NET 類別與這些資源互動：

* [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient)：`BlobServiceClient` 類別可讓您操作 Azure 儲存體資源和 Blob 容器。
* [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient)：`BlobContainerClient` 類別可讓您操作 Azure 儲存體容器及其 Blob。
* [BlobClient](/dotnet/api/azure.storage.blobs.blobclient)：`BlobClient` 類別可讓您操作 Azure 儲存體 Blob。
* [BlobDownloadInfo](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo)：`BlobDownloadInfo` 類別表示從下載 Blob 傳回的屬性和內容。

## <a name="code-examples"></a>程式碼範例

這些範例程式碼片段會示範如何使用適用於 .NET 的 Azure Blob 儲存體用戶端程式庫執行下列動作：

* [取得連接字串](#get-the-connection-string)
* [建立容器](#create-a-container)
* [將 Blob 上傳至容器](#upload-blobs-to-a-container)
* [列出容器中的 Blob](#list-the-blobs-in-a-container)
* [下載 Blob](#download-blobs)
* [刪除容器](#delete-a-container)

### <a name="get-the-connection-string"></a>取得連接字串

下列程式碼會從[設定儲存體連接字串](#configure-your-storage-connection-string)一節中建立的環境變數，擷取儲存體帳戶的連接字串。

在 `Main` 方法內新增此程式碼：

```csharp
Console.WriteLine("Azure Blob storage v12 - .NET quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called CONNECT_STR. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
string connectionString = Environment.GetEnvironmentVariable("CONNECT_STR");
```

### <a name="create-a-container"></a>建立容器

決定新容器的名稱。 下列程式碼會將 GUID 值附加到容器名稱，以確保它是唯一的。

> [!IMPORTANT]
> 容器名稱必須是小寫字母。 如需為容器和 Blob 命名的詳細資訊，請參閱[命名和參考容器、Blob 及中繼資料](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

建立 [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) 類別的執行個體。 接著，呼叫 [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) 方法，以便在您的儲存體帳戶中建立容器。

將此程式碼加入到 `Main` 方法的結尾處：

```csharp
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

//Create a unique name for the container
string containerName = "quickstartblobs" + Guid.NewGuid().ToString();

// Create the container and return a container client object
BlobContainerClient containerClient = await blobServiceClient.CreateBlobContainerAsync(containerName);
```

### <a name="upload-blobs-to-a-container"></a>將 Blob 上傳至容器

下列程式碼片段：

1. 在本機 *data* 目錄中建立一個文字檔。
1. 從[建立容器](#create-a-container)一節的容器上呼叫 [GetBlobClient](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobclient) 方法，以取得 [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) 物件的參考。
1. 呼叫 [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) 方法，將本機文字檔上傳至 Blob。 如果 Blob 不存在，此方法會建立 Blob，若已存在，則會加以覆寫。

將此程式碼加入到 `Main` 方法的結尾處：

```csharp
// Create a local file in the ./data/ directory for uploading and downloading
string localPath = "./data/";
string fileName = "quickstart" + Guid.NewGuid().ToString() + ".txt";
string localFilePath = Path.Combine(localPath, fileName);

// Write text to the file
await File.WriteAllTextAsync(localFilePath, "Hello, World!");

// Get a reference to a blob
BlobClient blobClient = containerClient.GetBlobClient(fileName);

Console.WriteLine("Uploading to Blob storage as blob:\n\t {0}\n", blobClient.Uri);

// Open the file and upload its data
using FileStream uploadFileStream = File.OpenRead(localFilePath);
await blobClient.UploadAsync(uploadFileStream);
uploadFileStream.Close();
```

### <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

呼叫 [GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) 方法，以列出容器中的 Blob。 在此案例中，只有一個 Blob 新增至容器，所以清單作業只會傳回一個 Blob。

將此程式碼加入到 `Main` 方法的結尾處：

```csharp
Console.WriteLine("Listing blobs...");

// List all blobs in the container
await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
{
    Console.WriteLine("\t" + blobItem.Name);
}
```

### <a name="download-blobs"></a>下載 Blob

呼叫 [DownloadAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadasync) 方法，以下載先前建立的 Blob。 此範例程式碼會將 "DOWNLOADED" 的尾碼加入至檔案名稱，讓您可以在本機檔案系統中看到這兩個檔案。

將此程式碼加入到 `Main` 方法的結尾處：

```csharp
// Download the blob to a local file
// Append the string "DOWNLOAD" before the .txt extension so you can see both files in MyDocuments
string downloadFilePath = localFilePath.Replace(".txt", "DOWNLOAD.txt");

Console.WriteLine("\nDownloading blob to\n\t{0}\n", downloadFilePath);

// Download the blob's contents and save it to a file
BlobDownloadInfo download = await blobClient.DownloadAsync();

using FileStream downloadFileStream = File.OpenWrite(downloadFilePath);
await download.Content.CopyToAsync(downloadFileStream);
downloadFileStream.Close();
```

### <a name="delete-a-container"></a>刪除容器

下列程式碼會使用 [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync) 刪除整個容器，以清除應用程式所建立的資源。 它也會刪除應用程式所建立的本機檔案。

應用程式會在刪除 Blob、容器和本機檔案之前呼叫 `Console.ReadLine`，藉以暫停使用者輸入。 這是很好的機會，可以在刪除資源之前，先確認實際上已正確地建立這些資源。

將此程式碼加入到 `Main` 方法的結尾處：

```csharp
// Clean up
Console.Write("Press any key to begin clean up");
Console.ReadLine();

Console.WriteLine("Deleting blob container...");
await containerClient.DeleteAsync();

Console.WriteLine("Deleting the local source and downloaded files...");
File.Delete(localFilePath);
File.Delete(downloadFilePath);

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>執行程式碼

此應用程式會在本機 *MyDocuments* 資料夾中建立測試檔案，並將它上傳到 Blob 儲存體。 範例會接著列出容器中的 Blob，並下載具有新名稱的檔案，您便可比較舊檔案和新檔案。

瀏覽至您的應用程式目錄，並建置和執行應用程式。

```console
dotnet build
```

```console
dotnet run
```

應用程式的輸出類似下列範例：

```output
Azure Blob storage v12 - .NET quickstart sample

Uploading to Blob storage as blob:
         https://mystorageacct.blob.core.windows.net/quickstartblobs60c70d78-8d93-43ae-954d-8322058cfd64/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Listing blobs...
        quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Downloading blob to
        ./data/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31DOWNLOADED.txt

Press any key to begin clean up
Deleting blob container...
Deleting the local source and downloaded files...
Done
```

在開始清除程序之前，請檢查 *MyDocuments* 資料夾，找出這兩個檔案。 您可以開啟它們，並觀察它們是否相同。

確認檔案之後，按 **Enter** 鍵以刪除測試檔案並完成示範。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用 .NET 上傳、下載及列出 Blob。

若要查看 Blob 儲存體範例應用程式，請繼續查看：

> [!div class="nextstepaction"]
> [Azure Blob 儲存體 SDK v12 .NET 範例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)

* 如需教學課程、範例、快速入門及其他文件，請瀏覽[適用於 .NET 和 .NET Core 開發人員的 Azure](/dotnet/azure/)。
* 若要深入了解 .NET Core，請參閱[在 10 分鐘內開始使用 .NET](https://www.microsoft.com/net/learn/get-started/)。
