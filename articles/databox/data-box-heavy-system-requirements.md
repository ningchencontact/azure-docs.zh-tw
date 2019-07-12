---
title: Microsoft Azure 資料方塊沈重的系統需求 |Microsoft Docs
description: 了解軟體與您 Azure 資料方塊大量的網路需求
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 4b41af691bb864dd11b001a6217a7b1e0318d6e0
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592315"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Azure 資料方塊繁重的系統需求

本文說明 Azure 資料方塊大量裝置及連接到裝置的用戶端的重要的系統需求。 我們建議您仔細檢閱資訊，再部署您的資料方塊 （大量），然後參考回，視需要在部署和後續作業期間。

系統需求包括：

* **主機連接到資料方塊大量的軟體需求**-描述支援的平台、 本機 web UI 的瀏覽器、 SMB 用戶端，以及主機可連接到資料箱的任何其他需求。
* **資料方塊大量的網路需求**-提供最佳的大量資料方塊中的裝置作業的網路需求的相關資訊。

## <a name="software-requirements"></a>軟體需求

軟體需求包括支援的作業系統、支援的本機 Web UI 瀏覽器和 SMB 用戶端相關資訊。

### <a name="supported-operating-systems-for-clients"></a>支援用戶端的作業系統

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Linux 用戶端的支援檔案系統

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>支援的儲存體帳戶

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>支援的儲存體類型

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>支援的網頁瀏覽器

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>網路需求

您的資料中心必須有高速網路。 最快的複製速度，您可以利用兩個 40 GbE 連接，以平行方式 （每個節點一個）。 如果您沒有 40 GbE 可用，建議您有至少兩個 10 GbE 連接 （每個節點一個）。

## <a name="next-steps"></a>後續步驟

* [部署 Azure 資料箱](data-box-deploy-ordered.md)
