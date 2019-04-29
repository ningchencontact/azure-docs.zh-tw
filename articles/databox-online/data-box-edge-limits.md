---
title: Azure Data Box Edge 限制 | Microsoft Docs
description: 說明 Microsoft Azure Data Box Edge 的系統限制和建議大小。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: a687398032adffe5e787ef1469abe0b2627a38fe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60758019"
---
# <a name="azure-data-box-edge-limits"></a>Azure 資料方塊邊緣的限制

當您部署和操作 Microsoft Azure Data Box Edge 解決方案時，請考慮這些限制。

## <a name="data-box-edge-service-limits"></a>Data Box Edge 服務限制

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-edge-device-limits"></a>Data Box Edge 裝置限制

下表說明資料箱 Data Box Edge 的限制。

| 描述 | Value |
|---|---|
|沒有。 (每個裝置的檔案) |1 億 |
|沒有。 (每個裝置的共用) |24 |
|沒有。 共用數 |1 |
|寫入至共用的檔案大小上限| 5 TB |

## <a name="azure-storage-limits"></a>Azure 儲存體限制

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>資料上傳注意事項

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure 儲存體帳戶大小和物件大小限制

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Azure 物件大小限制

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>後續步驟

- [準備部署 Azure 資料箱閘道](data-box-gateway-deploy-prep.md)
