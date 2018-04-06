---
title: 使用 Azure 入口網站建立具有區域備援公用 IP 位址前端的公用 Load Balancer Standard | Microsoft Docs
description: 了解如何使用 Azure 入口網站建立具有區域備援公用 IP 位址前端的公用 Load Balancer Standard
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: 10a264609469245d4743886b58730304da3df7bb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-azure-portal"></a>使用 Azure 入口網站建立具有區域備援公用 IP 位址前端的公用 Load Balancer Standard

本文會逐步說明如何使用 Public IP Standard 位址來建立具有區域備援前端的公用 [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard)。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="register-for-availability-zones-preview"></a>註冊可用性區域預覽
 
可用性區域為預覽版功能，可供開發和測試案例使用。 此功能支援選取 Azure 資源和區域以及 VM 大小系列。 如需如何開始使用，以及有哪些 Azure 資源、區域和 VM 大小系列可供用來試用可用性區域，請參閱[可用性區域概觀](https://docs.microsoft.com/azure/availability-zones/az-overview)。 如需支援，您可以透過 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) 與我們聯繫或[開啟 Azure 支援票證](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。  

## <a name="log-in-to-azure"></a>登入 Azure 

在 https://portal.azure.com 上登入 Azure 入口網站。

## <a name="create-a-zone-redundant-load-balancer"></a>建立區域備援負載平衡器

1. 透過瀏覽器瀏覽至 Azure 入口網站：[http://portal.azure.com](http://portal.azure.com)，並使用您的 Azure 帳戶登入。
2. 在畫面的左上方，選取 [建立資源] > [網路] > [負載平衡器]。
3. 在 [建立負載平衡器] 的 [名稱] 底下輸入 **myPublicLB**。
4. 在 [類型] 底下選取 [公用]。
5. 在 SKU 底下選取 [標準 (預覽)]。
6. 按一下 [公用 IP 位址]，按一下 [建立新的]，在 [建立公用 IP 位址] 頁面上的 [名稱] 底下輸入 **myPublicIPStandard**，然後在 [可用性區域 (預覽)] 中選取 [區域備援]。
7. 在 [位置] 底下選取 [美國東部 2]，然後按一下 [確定]。 負載平衡器接著會開始部署，並且需要幾分鐘的時間才能順利完成部署。

    ![使用 Azure 入口網站建立區域備援 Load Balancer Standard](./media/load-balancer-get-started-internet-az-portal/create-zone-redundant-load-balancer-standard.png)


## <a name="next-steps"></a>後續步驟
- 了解如何[在可用性區域中建立公用 IP](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)



