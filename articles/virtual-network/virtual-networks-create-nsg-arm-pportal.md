---
title: 建立網路安全性群組 - Azure 入口網站 | Microsoft Docs
description: 了解如何使用 Azure 入口網站建立和部署網路安全性群組。
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 5bc8fc2e-1e81-40e2-8231-0484cd5605cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d8a66de0b0239fef12168733eca7af85c8b08f82
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2018
---
# <a name="create-a-network-security-group-using-the-azure-portal"></a>使用 Azure 入口網站建立網路安全性群組

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

本文涵蓋之內容包括資源管理員部署模型。 您也可以 [在傳統部署模型中建立 NSG](virtual-networks-create-nsg-classic-ps.md)。

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]


## <a name="create-the-nsg-frontend-nsg"></a>建立 NSG-FrontEnd NSG
若要根據案例建立 **NSG-FrontEnd** NSG，請完成下列步驟：

1. 透過瀏覽器瀏覽至 https://portal.azure.com，並視需要使用您的 Azure 帳戶登入。
2. 選取 [+ 建立資源 >] > [網路安全性群組]。
   
    ![Azure 入口網站 - NSG](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)
3. 在 [網路安全性群組] 下方，選取 [新增]。
   
    ![Azure 入口網站 - NSG](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)
4. 在 [建立網路安全性群組] 下方的 RG-NSG 資源群組中建立名為 NSG-FrontEnd 的 NSG，然後選取 [建立]。
   
    ![Azure 入口網站 - NSG](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>建立現有 NSG 中的規則
若要從 Azure 入口網站建立現有 NSG 中的規則，請完成下列步驟：

1. 選取 [所有服務]，然後搜尋**網路安全性群組**。 當**網路安全性群組**出現時，請選取它。
2. 在 NSG 清單中選取 [NSG-FrontEnd] > [輸入安全性規則]
   
    ![Azure 入口網站 - NSG-FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)
3. 在 [輸入安全性規則] 清單中，選取 [新增]。
   
    ![Azure 入口網站 - 新增規則](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)
4. 在 [新增輸入安全性規則] 下方，建立一個名為 web-rule 的規則，優先順序設為 [200]，以允許從任何來源經由 [TCP] 與連接埠 [80] 存取任何 VM，然後選取 [確定]。 請注意，這些設定大部分已是預設值。
   
    ![Azure 入口網站 - 規則設定](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)
5. 數秒後，您會看到 NSG 中的新規則。
   
    ![Azure 入口網站 - 新增規則](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)
6. 重複步驟 6，建立名為 rdp-rule 的輸入規則，設定優先順序為 [250]，並允許透過 [TCP] 連接埠 [3389] 從任何來源存取任何 VM。

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>建立 NSG 與 FrontEnd 子網路的關聯

1. 選取 [所有服務 >]，輸入**資源群組**，在**資源群組**出現時加以選取，然後選取 [RG-NSG]。
2. 在 [RG-NSG] 下方，選取 [...] > [TestVNet]。
   
    ![Azure 入口網站 - TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)
3. 在 [設定] 下方，選取 [子網路] > [FrontEnd] > [網路安全性群組] > [NSG-FrontEnd]。
   
    ![Azure 入口網站 - 子網路設定](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)
4. 在 [FrontEnd] 刀鋒視窗中，選取 [儲存]。
   
    ![Azure 入口網站 - 子網路設定](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>建立 NSG-BackEnd NSG
若要建立 **NSG-BackEnd** NSG 並將它與 **BackEnd** 子網路建立關聯，請完成下列步驟：

1. 若要建立名為 NSG-BackEnd 的 NSG，請重複[建立 NSG-FrontEnd NSG](#Create-the-NSG-FrontEnd-NSG) 中的步驟。
2. 若要建立下表中的**輸入**規則，請重複[在現有 NSG 中建立規則](#Create-rules-in-an-existing-NSG)中的步驟。
   
   | 輸入規則 | 輸出規則 |
   | --- | --- |
   | ![Azure 入口網站 - 輸入規則](./media/virtual-networks-create-nsg-arm-pportal/figure17.png) |![Azure 入口網站 - 輸出規則](./media/virtual-networks-create-nsg-arm-pportal/figure18.png) |
3. 若要建立 **NSG-Backend** NSG 與 **BackEnd** 子網路的關聯，請重複[建立 NSG 與 FrontEnd 子網路的關聯](#Associate-the-NSG-to-the-FrontEnd-subnet)中的步驟。

## <a name="next-steps"></a>後續步驟
* 了解如何 [管理現有的 NSG](manage-network-security-group.md)
* [啟用 NSG 的記錄](virtual-network-nsg-manage-log.md) 。