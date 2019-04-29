---
title: Azure IoT Edge 模組先決條件 | Microsoft Docs
description: 發佈 IoT Edge 模組的先決條件。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pbutlerm
ms.openlocfilehash: a4f1023bdf8a49fccbbda1fd0dc537f83a3acee1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60910331"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>IoT Edge 模組發佈先決條件

本文將說明發佈 IoT Edge 模組供應項目所需先決條件。  如果您尚未這樣做，請檢閱[IoT Edge 模組發佈指南](../..//iot-edge-module.md)。


## <a name="publishing-prerequisites"></a>發佈先決條件

若要將 IoT Edge 模組發佈至 Microsoft Azure Marketplace，您必須滿足下列先決條件：

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- 可存取 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。 如需詳細資訊，請參閱 [Azure Marketplace 和 AppSource 發行指南](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)。
- 同意 [Azure Marketplace 條款](https://azure.microsoft.com/support/legal/marketplace-terms/)
- 將您的 IoT Edge 模組技術資產裝載於 Azure Container Registry 中。  如需詳細資訊，請參閱[準備 IoT Edge 模組的技術資產](./cpp-create-technical-assets.md)
- 準備好 IoT Edge 模組的中繼資料以供使用。 例如，準備下列資產：
    - 標題
    - 描述 (HTML 格式)
    - 標誌影像 (PNG 格式且影像大小固定，包括 40x40px、90x90px、115x115px、255x115px)
    - 使用規定及隱私權原則
    - 預設的模組設定包括：路由、對應項所需屬性、createOptions 以及環境變數。
    - 模組文件
    - 支援連絡人


## <a name="next-steps"></a>後續步驟

一旦[備妥您的 IoT Edge 模組技術資產](./cpp-create-technical-assets.md)，您就可以開始[建立您的 IoT Edge 模組供應項目](./cpp-create-offer.md)。 
