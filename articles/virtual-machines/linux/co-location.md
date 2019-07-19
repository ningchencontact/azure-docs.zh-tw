---
title: 共置 Linux Azure Vm |Microsoft Docs
description: 瞭解如何共同尋找 Azure VM 資源, 以改善延遲。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/28/2019
ms.author: cynthn
ms.openlocfilehash: d6d8986117bd2899ea0de0aa6490954aef6c3a56
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850336"
---
# <a name="co-locate-resources-for-improved-latency"></a>共同找出資源以改善延遲

在 Azure 中部署您的應用程式時, 將實例分散到不同區域或可用性區域會建立網路延遲, 這可能會影響應用程式的整體效能。 

## <a name="preview-proximity-placement-groups"></a>預覽：鄰近放置群組

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>後續步驟

使用 Azure CLI 將 VM 部署至[鄰近放置群組](proximity-placement-groups.md)。

