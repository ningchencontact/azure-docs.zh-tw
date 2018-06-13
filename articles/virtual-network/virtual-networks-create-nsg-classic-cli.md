---
title: 使用 Azure CLI 1.0 建立網路安全性群組 (傳統) | Microsoft Docs
description: 了解如何使用 Azure CLI 1.0 建立和部署網路安全性群組 (傳統)。
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 17d98950-5fbb-4653-bef6-d822ab37541e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: 5468801e56849498d712f51e71cfb31bf068398a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2018
ms.locfileid: "31792472"
---
# <a name="create-a-network-security-group-classic-using-the-azure-cli-10"></a>使用 Azure CLI 1.0 建立網路安全性群組 (傳統)
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

本文涵蓋之內容包括傳統部署模型。 您也可以 [在資源管理員部署模型中建立 NSG](tutorial-filter-network-traffic-cli.md)。

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

下列範例 Azure CLI 命令會假設您已根據案例建立簡單的環境。 如果您想要執行如本文件中所顯示的命令，請先建置 [建立 VNet](virtual-networks-create-vnet-classic-cli.md)以建置測試環境。

## <a name="create-an-nsg-for-the-front-end-subnet"></a>建立前端子網路的 NSG

1. 若您未曾用過 Azure CLI，請參閱[安裝和設定 Azure CLI](../cli-install-nodejs.md)。
2. 切換到傳統模式：

    ```azurecli
    azure config mode asm
    ```   

3. 建立 NSG：
   
    ```azurecli   
     azure network nsg create -l uswest -n NSG-FrontEnd
    ```
   
4. 建立允許從網際網路存取連接埠 3389 (RDP) 的安全性規則：
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389
   ```

5. 建立允許從網際網路存取連接埠 80 (HTTP) 的規則：
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80
    ```   

6. 建立 NSG 與前端子網路的關聯：
   
    ```azurecli
    azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd
   ```

## <a name="create-the-nsg-for-the-back-end-subnet"></a>建立後端子網路的 NSG

1. 建立 NSG：
   
    ```azurecli
    azure network nsg create -l uswest -n NSG-BackEnd
   ```

2. 建立允許從前端子網路存取連接埠 1433 (SQL) 的規則：
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433
   ```

3. 建立拒絕存取網際網路的規則：
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80
   ```

4. 建立 NSG 與後端子網路的關聯：
   
    ```azurecli
    azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd
    ```