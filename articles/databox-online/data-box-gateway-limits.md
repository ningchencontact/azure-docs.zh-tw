---
title: Azure 資料箱閘道限制 | Microsoft Docs
description: 說明 Microsoft Azure 資料箱閘道的系統限制和建議大小。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 02/05/2019
ms.author: alkohli
ms.openlocfilehash: f785e9e540af01b74678cf75159775cd2888e09e
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55959573"
---
# <a name="azure-data-box-gateway-limits-preview"></a>Azure 資料箱閘道限制 (預覽)


當您部署和操作 Microsoft Azure 資料箱閘道解決方案時，請考慮這些限制。 

> [!IMPORTANT] 
> 資料箱閘道處於預覽狀態。 部署此解決方案之前，請先檢閱[預覽版使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 


## <a name="data-box-gateway-service-limits"></a>資料箱閘道服務限制

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>資料箱閘道裝置限制

下表說明資料箱閘道裝置的限制。

| 說明 | 值 |
|---|---|
|每個裝置的 檔案數 |1 億 <br> 限制約為每 2 TB 的磁碟空間可以有 2 千 5 百萬個檔案，最大限制為 1 億個 |
|每個裝置的 共用數 |24 |
|每個容器的 共用數 |1 |
|寫入至共用的檔案大小上限|針對 2 TB 的虛擬裝置，檔案大小上限為 500 GB。 <br> 檔案大小上限會以上述比例隨著資料磁碟大小增加，直到達到上限 5 TB 為止。 |

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
