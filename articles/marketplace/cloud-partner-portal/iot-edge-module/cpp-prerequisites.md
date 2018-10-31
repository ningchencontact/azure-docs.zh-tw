---
title: Azure IoT Edge 模組先決條件 | Microsoft Docs
description: 發佈 IoT Edge 模組的先決條件。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 7271a97dc7ab9de1840d809ded0ba1c2940ed83f
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2018
ms.locfileid: "49432435"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>IoT Edge 模組發佈先決條件

本文將說明發佈 IoT Edge 模組供應項目所需先決條件。

若要深入了解 IoT Edge 模組，以及在 Microsoft Azure Marketplace 發佈模組的優點，請參閱 [IoT Edge 模組發行指南](https://docs.microsoft.com/azure/marketplace/iot-edge-module)。

## <a name="publishing-prerequisites"></a>發佈先決條件

若要將 IoT Edge 模組發佈至 Microsoft Azure Marketplace，您必須滿足下列先決條件：

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- 可存取 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。 如需詳細資訊，請參閱 [Azure Marketplace 和 AppSource 發行指南](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)。
- 同意 [Azure Marketplace 條款](https://azure.microsoft.com/support/legal/marketplace-terms/)
- 將您的 IoT Edge 模組技術資產裝載於 Azure Container Registry 中。  如需詳細資訊，請參閱[準備 IoT Edge 模組的技術資產](./cpp-create-technical-assets.md)
- 準備好 IoT Edge 模組的中繼資料以供使用。 例如，(非完整清單)：
    - 標題
    - 描述 (HTML 格式)
    - 標誌影像 (PNG 格式且影像大小固定，包括 40x40px、90x90px、115x115px、255x115px)
    - 使用規定及隱私權原則
    - 預設的模組設定包括：路由、對應項所需屬性、createOptions 以及環境變數。
    - 文件
    - 支援連絡人

## <a name="next-steps"></a>後續步驟

- [準備 IoT Edge 模組的技術資產](./cpp-create-technical-assets.md)
- [建立 IoT Edge 模組供應項目](./cpp-create-offer.md) (英文)
