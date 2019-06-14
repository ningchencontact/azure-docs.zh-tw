---
title: 在 Azure Data Factory 中建立事件型觸發程序 | Microsoft Docs
description: 了解如何在 Azure Data Factory 中建立會執行管線來回應事件的觸發程序。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/18/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 94c9c3f997143d72262c1ba3d8dbfea90d6f920c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "61347663"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>建立會執行管線來回應事件的觸發程序

本文說明可在 Data Factory 管線中建立的事件型觸發程序。

事件驅動架構 (EDA) 是常見的資料整合模式，所涉及的環節包括生產、偵測、取用和事件反應。 資料整合案例通常會要求 Data Factory 客戶根據事件來觸發管線。 Data Factory 現在與 [Azure 事件方格](https://azure.microsoft.com/services/event-grid/)整合，可讓您觸發事件上的管線。

如需此功能的 10 分鐘簡介與示範，請觀看下列影片：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> 本文章中說明的整合，仰賴 [Azure 事件方格](https://azure.microsoft.com/services/event-grid/)。 請確認您的訂用帳戶已向事件方格資源提供者註冊。 如需詳細資訊，請參閱[資源提供者和類型](../azure-resource-manager/resource-manager-supported-services.md#azure-portal)。

## <a name="data-factory-ui"></a>Data Factory UI

### <a name="create-a-new-event-trigger"></a>建立新的事件觸發程序

典型事件包括 Azure 儲存體帳戶中有檔案到達或是檔案遭到刪除。 您可以建立觸發程序以在 Data Factory 管線中回應此事件。

> [!NOTE]
> 這項整合只支援第 2 版儲存體帳戶 (一般用途)。

![建立新的事件觸發程序](media/how-to-create-event-trigger/event-based-trigger-image1.png)

### <a name="configure-the-event-trigger"></a>設定事件觸發程序

使用 **Blob path begins with** 和 **Blob path ends with** 屬性，您可以指定要收到事件的容器、資料夾和 Blob 名稱。 您可以針對 **Blob path begins with** 和 **Blob path ends with** 屬性使用各種不同的模式，如本文稍後的範例所示。 至少要有其中一個屬性。

![設定事件觸發程序](media/how-to-create-event-trigger/event-based-trigger-image2.png)

### <a name="select-the-event-trigger-type"></a>選取事件觸發程序類型

檔案一到達儲存體位置，而且已建立對應的 Blob 時，此事件就會觸發並執行 Data Factory 管線。 您可以建立觸發程序以在 Data Factory 管線中回應 Blob 建立事件和 (或) Blob 刪除事件。

![選取觸發程序類型作為事件](media/how-to-create-event-trigger/event-based-trigger-image3.png)

### <a name="map-trigger-properties-to-pipeline-parameters"></a>將觸發程序屬性對應到管線參數

當事件觸發程序對特定 Blob 引發時，事件會擷取 Blob 的資料夾路徑和檔案名稱，變成 `@triggerBody().folderPath` 和 `@triggerBody().fileName`。 若要在管線中使用這些屬性的值，您必須將屬性對應到管線參數。 在將屬性對應到參數之後，您可在整個管線中透過 `@pipeline().parameters.parameterName` 運算式存取觸發程序所擷取的值。

![將屬性對應到管線參數](media/how-to-create-event-trigger/event-based-trigger-image4.png)

舉例來說，在上方的螢幕擷取畫面中， 觸發程序設為在儲存體帳戶中建立了結尾為 `.csv` 的 Blob 路徑時引發。 這樣一來，當儲存體帳戶中的任一處建立了副檔名為 `.csv` 的 Blob 時，`folderPath` 和 `fileName` 屬性就會擷取新 Blob 的位置。 例如，`@triggerBody().folderPath` 有一個 `/containername/foldername/nestedfoldername` 這樣的值，而 `@triggerBody().fileName` 有一個 `filename.csv` 這樣的值。 在範例中，這些值會對應到管線參數 `sourceFolder` 和 `sourceFile`。 您可以分別以 `@pipeline().parameters.sourceFolder` 和 `@pipeline().parameters.sourceFile` 形式在整個管線中加以使用。

## <a name="json-schema"></a>JSON 結構描述

下表提供與事件型觸發程序相關的結構描述元素概觀：

| **JSON 元素** | **說明** | **類型** | **允許的值** | **必要** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| scope  | 儲存體帳戶的 Azure Resource Manager 資源識別碼。 | 字串 | Azure Resource Manager 識別碼 | 是 |
| **events** | 會導致引發此觸發程序的事件類型。 | 陣列    | Microsoft.Storage.BlobCreated、Microsoft.Storage.BlobDeleted | 是，這些值的任意組合。 |
| **blobPathBeginsWith** | Blob 路徑的開頭必須是提供來引發觸發程序的模式。 例如，`/records/blobs/december/` 只會針對 `records` 容器下 `december` 資料夾中的 Blob 引發觸發程序。 | 字串   | | 您必須為下列屬性中的至少一個屬性提供值：`blobPathBeginsWith` 或 `blobPathEndsWith`。 |
| **blobPathEndsWith** | Blob 路徑的結尾必須是提供來引發觸發程序的模式。 例如，`december/boxes.csv` 只會針對 `december` 資料夾中名為 `boxes` 的 Blob 引發觸發程序。 | 字串   | | 您必須為下列屬性中的至少一個屬性提供值：`blobPathBeginsWith` 或 `blobPathEndsWith`。 |

## <a name="examples-of-event-based-triggers"></a>事件型觸發程序的範例

本節提供事件型觸發程序設定的範例。

> [!IMPORTANT]
> 每當您指定容器與資料夾、容器與檔案，或容器、資料夾與檔案時，都必須包含路徑的 `/blobs/` 區段，如下列範例所示。

| 屬性 | 範例 | 描述 |
|---|---|---|
| **Blob 路徑開頭** | `/containername/` | 接收容器中任何 Blob 的事件。 |
| **Blob 路徑開頭** | `/containername/blobs/foldername/` | 接收 `containername` 容器與 `foldername` 資料夾中任何 Blob 的事件。 |
| **Blob 路徑開頭** | `/containername/blobs/foldername/subfoldername/` | 您也可以參考子資料夾。 |
| **Blob 路徑開頭** | `/containername/blobs/foldername/file.txt` | 接收 `containername` 容器下 `foldername` 資料夾中名為 `file.txt` 之 Blob 的事件。 |
| **Blob 路徑結尾** | `file.txt` | 接收任何路徑中名為 `file.txt` 之 Blob 的事件。 |
| **Blob 路徑結尾** | `/containername/blobs/file.txt` | 接收容器 `containername` 下名為 `file.txt` 之 Blob 的事件。 |
| **Blob 路徑結尾** | `foldername/file.txt` | 接收任何容器下 `foldername` 資料夾中名為 `file.txt` 之 Blob 的事件。 |

## <a name="next-steps"></a>後續步驟
如需有關觸發程序的詳細資訊，請參閱[管線執行和觸發程序](concepts-pipeline-execution-triggers.md#triggers)。
