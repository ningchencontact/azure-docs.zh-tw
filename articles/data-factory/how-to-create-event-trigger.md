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
ms.date: 06/20/2018
ms.author: douglasl
ms.openlocfilehash: 457983021034d83e0eed05bd91eae1ac30c046da
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296875"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>建立會執行管線來回應事件的觸發程序

本文說明可在 Data Factory 管線中建立的事件型觸發程序。

事件驅動架構 (EDA) 是常見的資料整合模式，所涉及的環節包括生產、偵測、取用和事件反應。 資料整合案例通常會要求 Data Factory 客戶根據事件來觸發管線。

## <a name="data-factory-ui"></a>Data Factory UI

### <a name="create-a-new-event-trigger"></a>建立新的事件觸發程序

典型事件包括 Azure 儲存體帳戶中有檔案到達或是檔案遭到刪除。 您可以建立觸發程序以在 Data Factory 管線中回應此事件。

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
-   **Blob path begins with**('/containername/foldername') – 接收 containername 容器和 foldername 資料夾中任何 Blob 的事件。
-   **Blob path begins with**('/containername/foldername/file.txt') – 接收 containername 容器下 foldername 資料夾中 Blob 具名 file.txt 的事件。
-   **Blob path ends with**('file.txt') – 接收任何路徑中 Blob 具名 file.txt 的事件。
-   **Blob path ends with**('/containername/file.txt') – 接收 containername 容器下 Blob 具名 file.txt 的事件。
-   **Blob path ends with**('foldername/file.txt') – 接收任何容器下 foldername 資料夾中 Blob 具名 file.txt 的事件。

## <a name="next-steps"></a>後續步驟
如需有關觸發程序的詳細資訊，請參閱[管線執行和觸發程序](concepts-pipeline-execution-triggers.md#triggers)。
