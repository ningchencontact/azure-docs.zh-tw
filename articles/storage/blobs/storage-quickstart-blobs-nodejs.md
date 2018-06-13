---
title: Azure 快速入門 - 使用 Node.js 在物件儲存體中建立 Blob | Microsoft Docs
description: 在本快速入門中，您會在物件 (Blob) 儲存體中建立儲存體帳戶和容器。 然後，使用 Node.js 的儲存體用戶端程式庫將 blob 上傳至 Azure 儲存體、下載 blob，以及列出容器中的 blob。
services: storage
author: craigshoemaker
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 04/09/2018
ms.author: cshoe
ms.openlocfilehash: 07ac5195d9653affdbb0f305ac1697cecd379319
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
ms.locfileid: "32190974"
---
# <a name="quickstart-upload-download-and-list-blobs-using-nodejs"></a>快速入門：使用 Node.js 上傳、下載及列出 Blob

在本快速入門中，您會了解如何使用 Node.js 以透過 Azure Blob 儲存體在容器中上傳、下載及列出區塊 Blob。

若要完成本快速入門，您需要 [Azure 訂用帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>下載範例應用程式

本快速入門中的[應用程式範例](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git)是簡單的 Node.js 主控台應用程式。 若要開始，請使用下列命令將存放庫複製到您的機器：

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

若要開啟應用程式，請尋找 storage-blobs-node-quickstart 資料夾，然後在您慣用的程式碼編輯環境中加以開啟。

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>設定儲存體連接字串

在執行應用程式之前，您必須先提供儲存體帳戶的連接字串。 存放庫範例包含名為 .env.example 的檔案。 您可以藉由移除 .example 副檔名來將這個檔案重新命名，從而產生名為 .env 的檔案。 在 .env 檔案內，於 AZURE_STORAGE_CONNECTION_STRING 索引鍵後面新增您的連接字串值。

## <a name="install-required-packages"></a>安裝必要的套件

在應用程式目錄中，執行 npm install 來安裝應用程式的所需套件。

```bash
npm install
```

## <a name="run-the-sample"></a>執行範例
相依性已安裝好，因此您可以對指令碼傳遞命令來執行範例。 例如，若要建立 Blob 容器，您可以使用下列命令：

```bash
node index.js --command createContainer
```

可用的命令包括︰

| 命令 | 說明 |
|---------|---------|
|createContainer | 建立名為 test-container 的容器 (即使該容器早已存在也會成功) |
|*upload*          | 將 example.txt 檔案上傳至 test-container 容器 |
|*download*        | 將「範例」Blob 的內容下載至 example.downloaded.txt |
|*delete*          | 刪除「範例」Blob |
|list            | 在主控台列出 test-container 容器的內容 |


## <a name="understanding-the-sample-code"></a>了解程式碼範例
此程式碼範例會使用幾個模組來與檔案系統和命令列連接。 

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').load();
}
const path = require('path');
const args = require('yargs').argv;
const storage = require('azure-storage');
```

這些模組的目的如下： 

- dotenv 會將名為 .env 之檔案中所定義的環境變數載入至目前的執行內容
- path 是必要項目，有此項目才能判斷要上傳至 Blob 儲存體之檔案的絕對檔案路徑
- yargs 會公開簡單的介面來存取命令列引數
- azure-storage 是 Node.js 的 [Azure 儲存體 SDK](https://docs.microsoft.com/javascript/api/azure-storage) 模組

接下來，系統會初始化一系列變數：

```javascript
const blobService = storage.createBlobService();
const containerName = 'test-container';
const sourceFilePath = path.resolve('./example.txt');
const blobName = path.basename(sourceFilePath, path.extname(sourceFilePath));
```

這些變數會設定為下列值：

- blobService 會設定為 Azure Blob 服務的新執行個體
- containerName 會設定為容器的名稱
- sourceFilePath 會設定為要上傳之檔案的絕對路徑
- blobName 的建立是藉由擷取檔案名稱並移除副檔名來達成

在下列實作中，每個 blobService 函式都會包裝在 Promise 中，以允許存取 JavaScript 的 async 函式和 await 運算子來簡化 [Azure 儲存體 API](/nodejs/api/azure-storage/blobservice) 的回呼性質。 當每個函式都有成功的回應傳回時，promise 會使用相關資料以及動作特有的訊息來解析。

### <a name="create-a-blob-container"></a>建立 Blob 容器

CreateContainer 函式會呼叫 [createContainerIfNotExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists) 並為 Blob 設定適當的存取層級。

```javascript
const createContainer = () => {
    return new Promise((resolve, reject) => {
        blobService.createContainerIfNotExists(containerName, { publicAccessLevel: 'blob' }, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' created` });
            }
        });
    });
};
```

**createContainerIfNotExists** 的第二個參數 (options) 會接受 [publicAccessLevel](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists) 的值。 publicAccessLevel 的 Blob 值會指定要公開的特定 Blob 資料。 此設定可和 container 存取層級相比較，後者可授與列出容器內容的能力。

使用 **createContainerIfNotExists** 可讓應用程式執行 createContainer 命令多次，而不會在容器早已存在時傳回錯誤。 在生產環境中，由於整個應用程式都使用相同的容器，因此您往往只會呼叫 **CreateIfNotExists** 一次。 在這些情況下，您可以透過入口網站或透過 Azure CLI 事先建立容器。

### <a name="upload-a-blob-to-the-container"></a>將 Blob 上傳至容器

upload 函式會使用 [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromLocalFile) 函式將檔案從檔案系統上傳及寫入或覆寫到 Blob 儲存體。 

```javascript
const upload = () => {
    return new Promise((resolve, reject) => {
        blobService.createBlockBlobFromLocalFile(containerName, blobName, sourceFilePath, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Upload of '${blobName}' complete` });
            }
        });
    });
};
```
在應用程式範例的內容中，名為 example.txt 的檔案會上傳至名為 test-container 之容器內的「範例」Blob 中。 其他可供將內容上傳至 Blob 的方法包括使用[文字](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText)和[串流](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromStream)。

若要確認檔案有上傳至您的 Blob 儲存體，您可以使用 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)來檢視您帳戶中的資料。

### <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

list 函式會呼叫 [listBlobsSegmented](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText) 方法以傳回容器中 Blob 中繼資料的清單。 

```javascript
const list = () => {
    return new Promise((resolve, reject) => {
        blobService.listBlobsSegmented(containerName, null, (err, data) => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Items in container '${containerName}':`, data: data });
            }
        });
    });
};
```

呼叫 listBlobsSegmented 會將 Blob 中繼資料傳回為 [BlobResult](/nodejs/api/azure-storage/blobresult) 執行個體的陣列。 結果會以 5,000 的增量批次 (區段) 傳回。 如果容器中有 5,000 個以上的 Blob，結果中會包含 **continuationToken** 的值。 若要列出 Blob 容器的後續區段，您可以將接續權杖回傳給 **listBlobSegmented** 作為第二個引數。

### <a name="download-a-blob-from-the-container"></a>從容器下載 Blob

download 函式會使用 [getBlobToLocalFile](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_getBlobToLocalFile) 將 Blob 的內容下載至指定的絕對檔案路徑。

```javascript
const download = () => {
    const dowloadFilePath = sourceFilePath.replace('.txt', '.downloaded.txt');
    return new Promise((resolve, reject) => {
        blobService.getBlobToLocalFile(containerName, blobName, dowloadFilePath, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Download of '${blobName}' complete` });
            }
        });
    });
};
```
此處所示的實作會變更要將 .downloaded.txt 附加至檔案名稱的來源檔案路徑。 在真實世界的內容中，您可以在選取下載目的地時變更位置以及檔案名稱。

### <a name="delete-blobs-in-the-container"></a>刪除容器中的 Blob

DeleteBlock 函式 (別名為 delete 主控台命令) 會呼叫 [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteBlobIfExists) 函式。 顧名思義，此函式不會傳回 Blob 早已刪除的錯誤。

```javascript
const deleteBlock = () => {
    return new Promise((resolve, reject) => {
        blobService.deleteBlobIfExists(containerName, blobName, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Block blob '${blobName}' deleted` });
            }
        });
    });
};
```

### <a name="upload-and-list"></a>上傳和列出

使用 promises 的其中一個優點是能夠將命令鏈結在一起。 **UploadAndList** 函式會示範在上傳檔案之後直接列出 Blob 的內容有多輕鬆。

```javascript
const uploadAndList = () => {
    return _module.upload().then(_module.list);
};
```

### <a name="calling-code"></a>呼叫程式碼

為了對命令列公開所實作的函式，每個函式都會對應至物件常值。

```javascript
const _module = {
    "createContainer": createContainer,
    "upload": upload,
    "download": download,
    "delete": deleteBlock,
    "list": list,
    "uploadAndList": uploadAndList
};
```

現在有了 _module，您可以從命令列使用每個命令。

```javascript
const commandExists = () => exists = !!_module[args.command];
```

如果指定的命令不存在，系統會將 _module 的屬性呈現在主控台上，以作為對使用者提供的說明文字。 

executeCommand 函式是 async 函式，其會使用 await 運算子呼叫指定的命令，並將任何資料訊息記錄至主控台。

```javascript
const executeCommand = async () => {
    const response = await _module[args.command]();

    console.log(response.message);

    if (response.data) {
        response.data.entries.forEach(entry => {
            console.log('Name:', entry.name, ' Type:', entry.blobType)
        });
    }
};
```

最後，執行程式碼會先呼叫 commandExists 以確認系統已將已知的命令傳遞至指令碼。 如果選取現有命令，則命令會執行並將任何錯誤記錄到主控台。

```javascript
try {
    const cmd = args.command;

    console.log(`Executing '${cmd}'...`);

    if (commandExists()) {
        executeCommand();
    } else {
        console.log(`The '${cmd}' command does not exist. Try one of these:`);
        Object.keys(_module).forEach(key => console.log(` - ${key}`));
    }
} catch (e) {
    console.log(e);
}
```

## <a name="clean-up-resources"></a>清除資源
如果您不打算使用本文所建立的資料或帳戶，建議您將其刪除以避免任何不想要的帳單。 若要刪除 Blob 和容器，您可以使用 [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice?view=azure-node-latest#deleteBlobIfExists_container__blob__options__callback_) 和 [deleteContainerIfExists](/nodejs/api/azure-storage/blobservice?view=azure-node-latest#deleteContainerIfExists_container__options__callback_) 方法。 您也可以[透過入口網站](../common/storage-create-storage-account.md)刪除儲存體帳戶。

## <a name="resources-for-developing-nodejs-applications-with-blobs"></a>可供使用 Blob 開發 Node.js 應用程式的資源

請參閱以下可供使用 Blob 儲存體進行 Node.js 開發的額外資源：

### <a name="binaries-and-source-code"></a>二進位檔和原始程式碼

- 檢視及安裝 GitHub 上適用於 Azure 儲存體的 [Node.js 用戶端程式庫原始程式碼](https://github.com/Azure/azure-storage-node)。

### <a name="client-library-reference-and-samples"></a>用戶端程式庫參考和範例

- 如需 Node.js 用戶端程式庫的詳細資訊，請參閱 [Node.js API 參考](https://docs.microsoft.com/javascript/api/overview/azure/storage)。
- 探索使用 Node.js 用戶端程式庫所撰寫的 [Blob 儲存體範例](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=nodejs&term=blob)。

## <a name="next-steps"></a>後續步驟

本快速入門會示範如何使用 Node.js 在本機磁碟和 Azure Blob 儲存體之間上傳檔案。 若要深入了解 Blob 儲存體的用法，請繼續閱讀 Blob 儲存體操作說明。

> [!div class="nextstepaction"]
> [Blob 儲存體作業操作說明](storage-nodejs-how-to-use-blob-storage.md)

如需 Azure 儲存體的 Node.js 參考，請參閱 [azure-storage 套件](https://docs.microsoft.com/javascript/api/azure-storage)。
