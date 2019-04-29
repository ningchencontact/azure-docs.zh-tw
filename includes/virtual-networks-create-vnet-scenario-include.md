---
title: 包含檔案
description: 包含檔案
services: virtual-network
author: rockboyfor
ms.service: virtual-network
ms.topic: include
origin.date: 04/13/2018
ms.date: 06/11/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 2b1f9990985951a4e6ef260954968c0e1466c298
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596996"
---
## <a name="scenario"></a>案例

為了清楚說明如何建立 VNet 和子網路，本文件會使用下列案例：

![VNet 案例](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

在這個案例中，您會建立名為 **TestVNet** 的 VNet，並包含保留的 CIDR 區塊 **192.168.0.0./16**。 VNet 包含下列子網路： 

* **FrontEnd**，使用 **192.168.1.0/24** 作為其 CIDR 區塊。
* **BackEnd**，使用 **192.168.2.0/24** 作為其 CIDR 區塊。