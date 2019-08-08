---
title: 回應 Azure Blob 儲存體事件 | Microsoft Docs
description: 使用 Azure Event Grid 以訂閱 Blob 儲存體事件。
author: normesta
ms.author: normesta
ms.date: 01/30/2018
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: cbrooks
ms.openlocfilehash: b79039c45006b81201d455cc4bbbf1b3468a3923
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845538"
---
# <a name="reacting-to-blob-storage-events"></a>回應 Blob 儲存體事件

Azure 儲存體事件可讓應用程式使用新式無伺服器架構, 對事件做出反應, 例如建立和刪除 blob。 它不需要複雜的程式碼或昂貴且無效率的輪詢服務來執行此動作。

相反地, 事件會透過[Azure 事件方格](https://azure.microsoft.com/services/event-grid/)推送至「訂閱者」, 例如 Azure Functions、Azure Logic Apps, 甚至是您自己的自訂 HTTP 接聽程式, 而您只需支付使用的部分。

Blob 儲存體事件會可靠地傳送到 Event Grid 服務, 透過豐富的重試原則和寄不出的信件傳遞, 為您的應用程式提供可靠的傳遞服務。

常見的 Blob 儲存體事件案例包括映像或影片處理、搜尋索引，或任何檔案導向的工作流程。 非同步檔案上傳非常適合事件。 在變更不常見但情況需要立即回應的情況下，以事件為基礎的架構可能特別有效。

如果您想要立即試用, 請參閱下列任何快速入門文章:

|如果您想要使用此工具:    |請參閱這篇文章: |
|--|-|
|Azure 入口網站    |[快速入門：使用 Azure 入口網站將 Blob 儲存體事件路由至 web 端點](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure CLI    |[快速入門：使用 PowerShell 將儲存體事件路由至 web 端點](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Powershell    |[快速入門：使用 Azure CLI 將儲存體事件路由至 web 端點](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="the-event-model"></a>事件模型

事件方格會使用[事件訂閱](../../event-grid/concepts.md#event-subscriptions)將事件訊息路由傳送至訂閱者。 此圖說明事件發行者、事件訂閱和事件處理常式之間的關聯性。

![Event Grid 模型](./media/storage-blob-event-overview/event-grid-functional-model.png)

首先, 訂閱某個事件的端點。 然後, 當觸發事件時, 事件方格服務會將該事件的相關資料傳送至端點。

若要瞭解, 請參閱[Blob 儲存體事件架構](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)文章:

> [!div class="checklist"]
> * Blob 儲存體事件的完整清單, 以及每個事件的觸發方式。
> * 事件方格會針對每個事件傳送的資料範例。
> * 出現在資料中的每個索引鍵值組的用途。

## <a name="filtering-events"></a>篩選事件

Blob 事件訂閱可以根據事件類型來篩選，也可以依據容器名稱和建立或刪除之物件的 Blob 名稱進行篩選。  篩選可以在事件訂用帳戶的[建立](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest)期間或[稍後](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest)套用至事件訂用帳戶。 事件格線中的主體篩選是根據 “begins with” 和 “ends with” 的相符項目來運作，以將具有相符主體的事件傳遞給訂閱者。

若要深入瞭解如何套用篩選器, 請參閱[篩選事件方格的事件](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)。

Blob 儲存體事件的主體使用格式：

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

若要比對儲存體帳戶的所有事件，您可以將主體篩選條件保留空白。

若要比對建立於共用前置詞的一組容器之 Blob 事件，請使用 `subjectBeginsWith` 篩選條件，例如：

```
/blobServices/default/containers/containerprefix
```

若要比對建立於特定容器之 Blob 事件，請使用 `subjectBeginsWith` 篩選條件，例如：

```
/blobServices/default/containers/containername/
```

若要比對建立於共用 Blob 名稱前置詞的特定容器之 Blob 事件，請使用 `subjectBeginsWith` 篩選條件，例如：

```
/blobServices/default/containers/containername/blobs/blobprefix
```

若要比對建立於共用 Blob 名稱前置詞的特定容器之 Blob 事件，請使用 `subjectEndsWith` 篩選條件，例如 ".log" 或 ".jpg"。 如需詳細資訊，請參閱 [Event Grid 概念](../../event-grid/concepts.md#event-subscriptions)。

## <a name="practices-for-consuming-events"></a>消費事件做法

處理 Blob 儲存體事件的應用程式應該遵循幾個建議做法：
> [!div class="checklist"]
> * 由於可設定多個訂用帳戶以將事件路由至相同的事件處理常式，因此重要的是，不要假設事件來自於特定來源，而要檢查訊息主題以確定其來自預期的儲存體帳戶。
> * 同樣地，檢查 eventType 也是必須進行的步驟之一，而且不要假設您收到的所有事件都是您預期的類型。
> * 由於訊息可能會在延遲一段時間後以錯誤順序送達，請使用 [etag] 欄位以了解您的物件資訊是否仍是最新狀態。  使用 [排序器] 欄位以了解任何特定物件上的事件順序。
> * 請使用 [blobType] 欄位以了解 Blob 允許何種類型的作業，以及您應該使用何種類型的用戶端程式庫來存取 Blob。 有效值為 `BlockBlob` 或 `PageBlob`。 
> * 請使用帶有 `CloudBlockBlob` 和 `CloudAppendBlob` 建構函式的 [url] 欄位存取 Blob。
> * 請忽略您不了解的欄位。 此做法將有助於保持未來可能新增功能的彈性。
> * 如果您想要確保只有在完全認可區塊 Blob 時才會觸發**microsoft.storage.blobcreated**事件`CopyBlob`, 請篩選、 `PutBlob` `PutBlockList`或`FlushWithClose` REST API 呼叫的事件。 只有在資料完全認可至區塊 Blob 之後, 這些 API 呼叫才會觸發**microsoft.storage.blobcreated**事件。 若要瞭解如何建立篩選準則, 請參閱[篩選事件方格的事件](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)。


## <a name="next-steps"></a>後續步驟

深入了解 Event Grid 並嘗試 Blob 儲存體事件：

- [關於 Event Grid](../../event-grid/overview.md)
- [將 Blob 儲存體事件路由至自訂的 Web 端點](storage-blob-event-quickstart.md)
