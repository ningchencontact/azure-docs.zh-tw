---
title: 在 Azure Data Factory 中建立事件型觸發程序 | Microsoft Docs
description: 了解如何在 Azure Data Factory 中建立會執行管線來回應事件的觸發程序。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: douglasl
ms.openlocfilehash: a9c15b239ee0bd0dde0b1f11691565b2676e3d07
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062116"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>建立會執行管線來回應事件的觸發程序

本文說明可在 Data Factory 管線中建立的事件型觸發程序。

事件驅動架構 (EDA) 是常見的資料整合模式，所涉及的環節包括生產、偵測、取用和事件反應。 資料整合案例通常會要求 Data Factory 客戶根據事件來觸發管線。 Data Factory 現在與 [Azure 事件方格](https://azure.microsoft.com/services/event-grid/)整合，可讓您觸發事件上的管線。

## <a name="data-factory-ui"></a>Data Factory UI

### <a name="create-a-new-event-trigger"></a>建立新的事件觸發程序

典型事件包括 Azure 儲存體帳戶中有檔案到達或是檔案遭到刪除。 您可以建立觸發程序以在 Data Factory 管線中回應此事件。

> [!NOTE]
> 這項整合只支援第 2 版儲存體帳戶 (一般用途)。

![建立新的事件觸發程序](media/how-to-create-event-trigger/event-based-trigger-image1.png)

### <a name="select-the-event-trigger-type"></a>選取事件觸發程序類型

檔案一到達儲存體位置，而且已建立對應的 Blob 時，此事件就會觸發並執行 Data Factory 管線。 您可以建立觸發程序以在 Data Factory 管線中回應 Blob 建立事件和 (或) Blob 刪除事件。

![選取觸發程序類型作為事件](media/how-to-create-event-trigger/event-based-trigger-image2.png)

### <a name="configure-the-event-trigger"></a>設定事件觸發程序

使用 **Blob path begins with** 和 **Blob path ends with** 屬性，您可以指定要收到事件的容器、資料夾和 Blob 名稱。 您可以針對 **Blob path begins with** 和 **Blob path ends with** 屬性使用各種不同的模式，如本文稍後的範例所示。 至少要有其中一個屬性。

![設定事件觸發程序](media/how-to-create-event-trigger/event-based-trigger-image3.png)

## <a name="json-schema"></a>JSON 結構描述

下表提供與事件型觸發程序相關的結構描述元素概觀：

| **JSON 元素** | **說明** | **類型** | **允許的值** | **必要** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| scope | 儲存體帳戶的 Azure Resource Manager 資源識別碼。 | 字串 | Azure Resource Manager 識別碼 | yes |
| **events** | 會導致引發此觸發程序的事件類型。 | 陣列    | Microsoft.Storage.BlobCreated、Microsoft.Storage.BlobDeleted | 是，任何組合。 |
| **blobPathBeginsWith** | Blob 路徑的開頭必須是提供來引發觸發程序的模式。 例如，'/records/blobs/december/' 只會針對 records 容器下 december 資料夾中的 Blob 引發觸發程序。 | 字串   | | 至少必須提供其中一個屬性：blobPathBeginsWith、blobPathEndsWith。 |
| **blobPathEndsWith** | Blob 路徑的結尾必須是提供來引發觸發程序的模式。 例如，'december/boxes.csv' 只會針對 december 資料夾中的 Blob 具名方塊引發觸發程序。 | 字串   | | 至少必須提供其中一個屬性：blobPathBeginsWith、blobPathEndsWith。 |

## <a name="examples-of-event-based-triggers"></a>事件型觸發程序的範例

本節提供事件型觸發程序設定的範例。

-   **Blob path begins with**('/containername/') – 接收容器中任何 Blob 的事件。
-   **Blob path begins with**('/containername/blobs/foldername') – 接收 containername 容器和 foldername 資料夾中任何 Blob 的事件。
-   **Blob path begins with**('/containername/blobs/foldername/file.txt') – 接收 containername 容器下 foldername 資料夾中 Blob (名為 file.txt) 的事件。
-   **Blob path ends with**('file.txt') – 接收任何路徑中 Blob 具名 file.txt 的事件。
-   **Blob path ends with**('/containername/blobs/file.txt') – 接收 containername 容器下 Blob (名為 file.txt) 的事件。
-   **Blob path ends with**('foldername/file.txt') – 接收任何容器下 foldername 資料夾中 Blob 具名 file.txt 的事件。

> [!NOTE]
> 每當您指定容器與資料夾、容器與檔案，或是容器、資料夾與檔案時，都必須包含路徑的 `/blobs/` 區段。

## <a name="using-blob-events-trigger-properties"></a>使用 Blob 事件觸發程序屬性

當 Blob 事件觸發程序引發時，會提供兩個變數給您的管線：folderPath 和 fileName。 若要存取這些變數，請使用 `@triggerBody().fileName` 或 `@triggerBody().folderPath` 運算式。

例如，當使用 `.csv` 作為 `blobPathEndsWith` 的值來建立 Blob 時，請考慮將觸發程序設定為引發。 當您 .csv 檔案置放到儲存體帳戶時，folderPath  和 fileName 會描述 .csv 檔案的位置。 例如，folderPath 具有 `/containername/foldername/nestedfoldername` 值，而 fileName 具有 `filename.csv` 值。

## <a name="next-steps"></a>後續步驟
如需有關觸發程序的詳細資訊，請參閱[管線執行和觸發程序](concepts-pipeline-execution-triggers.md#triggers)。
