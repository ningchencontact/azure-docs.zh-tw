---
title: App Service 環境管理位址 - Azure
description: 列出用於命令 App Service Environment 的管理位址
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: f76dd423cb3f7fbae6cc88d064e49dc2d56f1a1c
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045282"
---
# <a name="app-service-environment-management-addresses"></a>App Service Environment 管理位址

App Service Environment (ASE) 是單一租用戶部署 Azure 虛擬網路 (VNet) 中執行 Azure App service。  雖然 ASE 沒有 VNet 中執行，它必須仍能夠從多個 Azure App Service 所用來管理服務的固定 IP 位址。  在 ASE 中，管理流量會周遊使用者控制的網路。 如果封鎖或誤傳此流量，ASE 會暫止。 如需 ASE 網路服務相依性的詳細資訊，請閱讀[網路考量和 App Service Environment][networking]。 如需 ASE 的一般資訊，您可以從 [App Service Environment 簡介][intro]開始。

所有 ASE 都有可讓管理流量進入的公用 VIP。 這些位址的連入管理流量會進入 ASE 公用 VIP 上的連接埠 454 和 455。 本文件列出流向 ASE 之管理流量的 App Service 來源位址。 這些位址也是在名為 AppServiceManagement IP 服務標籤。

為了避免發生非對稱式路由問題管理流量的路由表中，您可以設定如下所示的位址。 依據 IP 層級流量路由，而且沒有流量方向的情況下的或流量的 TCP 回覆訊息。 如果 TCP 要求的回覆位址是它傳送至的位址不同，您會有非對稱式路由問題。 若要避免您的 ASE 管理流量的非對稱式路由問題，您需要確保從傳送到相同的位址會傳送回覆。 如需深入了解如何設定 ASE，以便在由內部部署軟體傳送輸出流量的環境中運作，請參閱[為您的 ASE 設定強制通道][forcedtunnel]

## <a name="list-of-management-addresses"></a>管理位址的清單 ##

| 區域 | 位址 |
|--------|-----------|
| 所有公用區域 | 13.64.115.203, 13.75.127.117, 13.94.141.115, 13.94.143.126, 13.94.149.179, 23.102.135.246, 23.102.188.65, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.124.47.188, 52.151.25.45, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197, 52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 65.52.14.230, 65.52.172.237, 65.52.193.203, 70.37.57.58, 70.37.89.222, 104.44.129.141, 104.44.129.243, 104.44.129.255, 104.44.134.255, 104.208.54.11, 157.55.176.93, 157.55.208.185, 191.236.154.88 |
| Microsoft Azure Government | 23.97.29.209, 13.72.53.37, 13.72.180.105, 23.97.0.17, 23.97.16.184 |

## <a name="configuring-a-network-security-group"></a>設定網路安全性群組

使用網路安全性群組中，您不需要擔心個別地址或維護您自己的組態。 沒有名為 AppServiceManagement 保持最新的所有位址與 IP 服務標籤。 若要使用此 IP 服務標籤在 NSG 中，移至入口網站開啟您網路安全性的群組 UI，然後選取 輸入的安全性規則。 如果您有預先存在的規則，用於輸入的管理流量，進行編輯。 如果未使用 ASE 建立此 NSG，或者該 NSG 是全新的，則選取 [新增]。 在來源下拉式清單中，選取 [服務標籤]。  在 來源服務標籤中，選取**AppServiceManagement**。 將來源連接埠範圍設為 \*、目的地設為 **Any**、目的地連接埠範圍設為 **454-455**、通訊協定設為 **TCP**，以及將動作設為 **Allow**。 如果您要進行此規則，您需要設定的優先順序。 

![使用服務標籤建立 NSG][1]

## <a name="configuring-a-route-table"></a>設定路由表

管理位址可以放在具有網際網路下一個躍點的路由表中，以確保所有輸入管理流量可以通過相同的路徑返回。 設定強制通道時，會需要這些路由。 若要建立路由表，您可以使用入口網站、PowerShell 或 Azure CLI。  從 PowerShell 提示中使用 Azure CLI 建立路由表的命令如下。 

    $rg = "resource group name"
    $rt = "route table name"
    $location = "azure location"
    az network route-table create --name $rt --resource-group $rg --location $location
    az network route-table route create -g $rg --route-table-name $rt -n 13.64.115.203 --next-hop-type Internet --address-prefix 13.64.115.203/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.75.127.117 --next-hop-type Internet --address-prefix 13.75.127.117/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.141.115 --next-hop-type Internet --address-prefix 13.94.141.115/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.143.126 --next-hop-type Internet --address-prefix 13.94.143.126/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.149.179 --next-hop-type Internet --address-prefix 13.94.149.179/32
    az network route-table route create -g $rg --route-table-name $rt -n 23.102.135.246 --next-hop-type Internet --address-prefix 23.102.135.246/32
    az network route-table route create -g $rg --route-table-name $rt -n 23.102.188.65 --next-hop-type Internet --address-prefix 23.102.188.65/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.120.64 --next-hop-type Internet --address-prefix 40.83.120.64/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.121.56 --next-hop-type Internet --address-prefix 40.83.121.56/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.125.161 --next-hop-type Internet --address-prefix 40.83.125.161/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.124.47.188 --next-hop-type Internet --address-prefix 40.124.47.188/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.151.25.45 --next-hop-type Internet --address-prefix 52.151.25.45/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.152.214 --next-hop-type Internet --address-prefix 52.165.152.214/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.153.122 --next-hop-type Internet --address-prefix 52.165.153.122/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.154.193 --next-hop-type Internet --address-prefix 52.165.154.193/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.158.140 --next-hop-type Internet --address-prefix 52.165.158.140/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.174.22.21 --next-hop-type Internet --address-prefix 52.174.22.21/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.177.147 --next-hop-type Internet --address-prefix 52.178.177.147/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.184.149 --next-hop-type Internet --address-prefix 52.178.184.149/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.190.65 --next-hop-type Internet --address-prefix 52.178.190.65/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.195.197 --next-hop-type Internet --address-prefix 52.178.195.197/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.56.50 --next-hop-type Internet --address-prefix 52.187.56.50/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.59.251 --next-hop-type Internet --address-prefix 52.187.59.251/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.63.19 --next-hop-type Internet --address-prefix 52.187.63.19/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.63.37 --next-hop-type Internet --address-prefix 52.187.63.37/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.224.105.172 --next-hop-type Internet --address-prefix 52.224.105.172/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.225.177.153 --next-hop-type Internet --address-prefix 52.225.177.153/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.14.230 --next-hop-type Internet --address-prefix 65.52.14.230/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.172.237 --next-hop-type Internet --address-prefix 65.52.172.237/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.193.203 --next-hop-type Internet --address-prefix 65.52.193.203/32
    az network route-table route create -g $rg --route-table-name $rt -n 70.37.57.58 --next-hop-type Internet --address-prefix 70.37.57.58/32
    az network route-table route create -g $rg --route-table-name $rt -n 70.37.89.222 --next-hop-type Internet --address-prefix 70.37.89.222/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.141 --next-hop-type Internet --address-prefix 104.44.129.141/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.243 --next-hop-type Internet --address-prefix 104.44.129.243/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.255 --next-hop-type Internet --address-prefix 104.44.129.255/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.134.255 --next-hop-type Internet --address-prefix 104.44.134.255/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.208.54.11 --next-hop-type Internet --address-prefix 104.208.54.11/32
    az network route-table route create -g $rg --route-table-name $rt -n 157.55.176.93 --next-hop-type Internet --address-prefix 157.55.176.93/32
    az network route-table route create -g $rg --route-table-name $rt -n 157.55.208.185 --next-hop-type Internet --address-prefix 157.55.208.185/32
    az network route-table route create -g $rg --route-table-name $rt -n 191.236.154.88 --next-hop-type Internet --address-prefix 191.236.154.88/32

建立路由表之後，您需要在 ASE 子網路上進行設定。  

## <a name="get-your-management-addresses-from-api"></a>從 API 取得管理位址 ##

您可以使用下列 API 呼叫來列出符合您 ASE 的管理位址。

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

此 API 會傳回一份 JSON 文件，其中包含適用於 ASE 的所有連入位址。 位址清單會包含管理位址、您 ASE 所使用的 VIP，以及 ASE 子網路位址範圍本身。  

若要使用 [armclient](https://github.com/projectkudu/ARMClient) 呼叫 API，請使用下列命令，但必須以您的值取代訂用帳戶識別碼、資源群組與 ASE 名稱。  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!--IMAGES-->
[1]: ./media/management-addresses/managementaddr-nsg.png

<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
