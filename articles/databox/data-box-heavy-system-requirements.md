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
ms.openlocfilehash: 6097dba9193fce4c8158137b488080107881994a
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839788"
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

### <a name="port-requirements"></a>連接埠需求

下表列出必須在您允許 SMB 或 NFS 流量的防火牆中開啟的連接埠。 在這個資料表中，in  或 inbound  指的是輸入用戶端要求存取裝置的方向。 *Out*或*輸出*指的是資料方塊大量的裝置所在部署以外傳送外部資料的方向： 例如，輸出到網際網路。

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>後續步驟

* [部署 Azure 資料箱](data-box-deploy-ordered.md)
