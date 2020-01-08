---
title: Azure Data Lake Storage Gen2 適用于檔案 & Acl 的 .NET SDK （預覽）
description: 使用 Azure 儲存體用戶端程式庫來管理已啟用階層命名空間（HNS）之儲存體帳戶中的目錄和檔案和目錄存取控制清單（ACL）。
author: normesta
ms.service: storage
ms.date: 11/24/2019
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 7921b42475d92070884a4298f66411813c995452
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443776"
---
# <a name="use-net-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>使用 .NET 管理 Azure Data Lake Storage Gen2 中的目錄、檔案和 Acl （預覽）

本文說明如何使用 .NET 來建立和管理已啟用階層命名空間（HNS）之儲存體帳戶中的目錄、檔案和許可權。 

> [!IMPORTANT]
> 本文中所述的[DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/12.0.0-preview.6) NuGet 套件目前處於公開預覽狀態。

[封裝（NuGet）](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/12.0.0-preview.6) | [範例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [API 參考](https://azuresdkdocs.blob.core.windows.net/$web/dotnet/Azure.Storage.Files.DataLake/12.0.0-preview.6/api/index.html) | [Gen1 至 Gen2 對應](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | [提供意見](https://github.com/Azure/azure-sdk-for-net/issues)反應

## <a name="prerequisites"></a>必要條件

> [!div class="checklist"]
> * Azure 訂用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
> * 已啟用階層命名空間（HNS）的儲存體帳戶。 請遵循[這些](data-lake-storage-quickstart-create-account.md)指示來建立一個。

## <a name="set-up-your-project"></a>設定專案

若要開始使用，請安裝[DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet 套件。

如需有關如何安裝 NuGet 套件的詳細資訊，請參閱[使用 NuGet 套件管理員在 Visual Studio 中安裝和管理套件](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio)。

然後，將這些 using 語句新增至程式碼檔案的頂端。

```csharp
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using System.IO;
using Azure;
```

## <a name="connect-to-the-account"></a>連接到帳戶

若要使用本文中的程式碼片段，您必須建立代表儲存體帳戶的**DataLakeServiceClient**實例。 取得一個最簡單的方法是使用帳戶金鑰。 

這個範例會使用帳戶金鑰來建立**DataLakeServiceClient**的實例。

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

## <a name="create-a-file-system"></a>建立檔案系統

檔案系統作為檔案的容器。 您可以藉由呼叫**FileSystemClient. CreateFileSystemAsync**方法來建立一個。

這個範例會建立名為 `my-file-system`的檔案系統。 

```cs
public async Task<DataLakeFileSystemClient> CreateFileSystem
    (DataLakeServiceClient serviceClient)
{
        return await serviceClient.CreateFileSystemAsync("my-file-system");
}
```

## <a name="create-a-directory"></a>建立目錄

藉由呼叫**FileSystemClient. CreateDirectoryAsync**方法來建立目錄參考。

這個範例會將名為 `my-directory` 的目錄新增至檔案系統，然後新增名為 `my-subdirectory`的子目錄。 

```cs
public async Task<DataLakeDirectoryClient> CreateDirectory
    (DataLakeServiceClient serviceClient, string fileSystemName)
{
    DataLakeFileSystemClient fileSystemClient =
        serviceClient.GetFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        await fileSystemClient.CreateDirectoryAsync("my-directory");

    return await directoryClient.CreateSubDirectoryAsync("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>重新命名目錄或移動目錄

藉由呼叫**DirectoryClient. RenameAsync**方法來重新命名或移動目錄。 傳遞所需目錄的路徑 a 參數。 

這個範例會將子目錄重新命名為 `my-subdirectory-renamed`的名稱。

```cs
public async Task<DataLakeDirectoryClient> 
    RenameDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory");

    return await directoryClient.RenameAsync("my-directory/my-subdirectory-renamed");
}
```

這個範例會將名為 `my-subdirectory-renamed` 的目錄移到名為 `my-directory-2`之目錄的子目錄中。 

```cs
public async Task<DataLakeDirectoryClient> MoveDirectory
    (DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
            fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory-renamed");

    return await directoryClient.RenameAsync("my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>刪除目錄

藉由呼叫**DirectoryClient**方法來刪除目錄。

這個範例會刪除名為 `my-directory`的目錄。  

```cs
public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    directoryClient.Delete();
}
```

## <a name="manage-a-directory-acl"></a>管理目錄 ACL

呼叫**directoryClient. GetAccessControlAsync**方法以取得目錄的存取控制清單（ACL），並呼叫**directoryClient**方法來設定 ACL。

這個範例會取得並設定名為 `my-directory`之目錄的 ACL。 `user::rwx,group::r-x,other::rw-` 的字串會授與擁有使用者讀取、寫入和執行許可權、授與擁有群組 [讀取] 和 [執行] 許可權，並提供所有其他讀取和寫入權限。

```cs
public async Task ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        await directoryClient.GetAccessControlAsync();

    Console.WriteLine(directoryAccessControl.Acl);

    directoryClient.SetAccessControl("user::rwx,group::r-x,other::rw-");

}

```

## <a name="upload-a-file-to-a-directory"></a>將檔案上傳到目錄

首先，建立**DataLakeFileClient**類別的實例，以建立目標目錄中的檔案參考。 藉由呼叫**DataLakeFileClient. AppendAsync**方法來上傳檔案。 請務必呼叫**DataLakeFileClient. FlushAsync**方法來完成上傳。

這個範例會將文字檔上傳至名為 `my-directory`的目錄。    

```cs
public async Task UploadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = await directoryClient.CreateFileAsync("uploaded-file.txt");

    FileStream fileStream = 
        File.OpenRead("C:\\file-to-upload.txt");

    long fileSize = fileStream.Length;

    await fileClient.AppendAsync(fileStream, offset: 0);

    await fileClient.FlushAsync(position: fileSize);

}
```

## <a name="manage-a-file-acl"></a>管理檔案 ACL

藉由呼叫**DataLakeFileClient. GetAccessControlAsync**方法來取得檔案的存取控制清單（ACL），並藉由呼叫**FileClient**方法來設定 ACL。

這個範例會取得並設定名為 `my-file.txt`之檔案的 ACL。 `user::rwx,group::r-x,other::rw-` 的字串會授與擁有使用者讀取、寫入和執行許可權、授與擁有群組 [讀取] 和 [執行] 許可權，並提供所有其他讀取和寫入權限。

```cs
public async Task ManageFileACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("hello.txt");

    PathAccessControl FileAccessControl =
        await fileClient.GetAccessControlAsync();

    Console.WriteLine(FileAccessControl.Acl);

    fileClient.SetAccessControl("user::rwx,group::r-x,other::rw-");
}
```

## <a name="download-from-a-directory"></a>從目錄下載 

首先，建立代表您要下載之檔案的**DataLakeFileClient**實例。 使用**FileClient. ReadAsync**方法，並剖析傳回值以取得[資料流程](https://docs.microsoft.com/dotnet/api/system.io.stream)物件。 使用任何 .NET 檔案處理 API，將資料流程中的位元組儲存至檔案。 

這個範例會使用[BinaryReader](https://docs.microsoft.com/dotnet/api/system.io.binaryreader)和[FileStream](https://docs.microsoft.com/dotnet/api/system.io.filestream)來儲存檔案的位元組。 

```cs
public async Task DownloadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("my-image.png");

    Response<FileDownloadInfo> downloadResponse = await fileClient.ReadAsync();

    BinaryReader reader = new BinaryReader(downloadResponse.Value.Content);

    FileStream fileStream = 
        File.OpenWrite("C:\\my-image-downloaded.png");

    int bufferSize = 4096;

    byte[] buffer = new byte[bufferSize];

    int count;

    while ((count = reader.Read(buffer, 0, buffer.Length)) != 0)
    {
        fileStream.Write(buffer, 0, count);
    }

    await fileStream.FlushAsync();

    fileStream.Close();
}
```

## <a name="list-directory-contents"></a>列出目錄內容

藉由呼叫**FileSystemClient. ListPathsAsync**方法來列出目錄內容，然後逐一列舉結果。

這個範例會列印位於名為 `my-directory`目錄中的每個檔案的名稱。

```cs
public async Task ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient)
{
    IAsyncEnumerator<PathItem> enumerator = 
        fileSystemClient.ListPathsAsync("my-directory").GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    PathItem item = enumerator.Current;

    while (item != null)
    {
        Console.WriteLine(item.Name);

        if (!await enumerator.MoveNextAsync())
        {
            break;
        }
                
        item = enumerator.Current;
    }

}
```

## <a name="see-also"></a>請參閱

* [API 參考檔](https://azuresdkdocs.blob.core.windows.net/$web/dotnet/Azure.Storage.Files.DataLake/12.0.0-preview.6/api/index.html)
* [封裝（NuGet）](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/12.0.0-preview.6)
* [範例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Gen1 至 Gen2 對應](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [已知問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [提供意見反應](https://github.com/Azure/azure-sdk-for-net/issues)

