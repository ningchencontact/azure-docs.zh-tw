---
title: 包含檔案
description: 包含檔案
services: virtual-network
author: genli
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 29b2ac1a5dc128028dbd40e683c1b45e6208d124
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
## <a name="scenario"></a>案例

為了清楚說明如何建立 VNet 和子網路，本文件會使用下列案例：

![VNet 案例](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

在這個案例中，您會建立名為 **TestVNet** 的 VNet，並包含保留的 CIDR 區塊 **192.168.0.0./16**。 VNet 包含下列子網路： 

* **FrontEnd**，使用 **192.168.1.0/24** 作為其 CIDR 區塊。
* **BackEnd**，使用 **192.168.2.0/24** 作為其 CIDR 區塊。

