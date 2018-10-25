---
title: 在 Azure Stack 中新增公用 IP 位址 | Microsoft Docs
description: 了解如何將更多公用 IP 位址新增到 Azure Stack。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeffgilb
ms.reviewer: scottnap
ms.openlocfilehash: b401139d417674cf58d2db264b442d7588cc34ba
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2018
ms.locfileid: "45987099"
---
# <a name="add-public-ip-addresses"></a>新增公用 IP 位址
*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*  

了解如何將更多公用 IP 位址新增到 Azure Stack。  在本文中，我們將外部位址稱為公用 IP 位址，但是，在 Azure Stack 中，這是指將 IP 位址區塊新增到您的外部網路。  外部網路是否為公用網際網路可路由傳送或位於內部網路，而且是否使用私人位址，都不影響本文的目的。  步驟均相同。 

## <a name="add-a-public-ip-address-pool"></a>新增公用 IP 位址集區
在 Azure Stack 系統的初始部署後，您可以將公用 IP 位址新增到您的 Azure Stack 系統。 了解如何[檢視公用 IP 位址使用狀況](azure-stack-viewing-public-ip-address-consumption.md)查看目前的使用量，以及公用 IP 位址在您的 Azure Stack 上的可用性。

大致而言，將新的公用 IP 位址區塊新增到 Azure Stack 的程序如下所示：

 ![新增 IP 流量](media/azure-stack-add-ips/flow.PNG)

## <a name="obtain-the-address-block-from-your-provider"></a>向您的提供者取得位址區塊
您必須執行的第一件事是取得要新增到 Azure Stack 的位址區塊。  根據您取得位址區塊的來源，您必須考慮前置時間的長度，並根據 Azure Stack 中的公用 IP 位址使用率管理這段前置時間。  

> [!IMPORTANT]
> Azure Stack 會接受您提供的任何位址區塊，只要是有效的位址區塊，而且並未與 Azure Stack 中的現有位址範圍重疊。  請確定您取得的有效位址區塊可路由傳送，而且並未與 Azure Stack 連接的外部網路重疊。  您將範圍新增到 Azure Stack 後，便無法加以移除。

## <a name="add-the-ip-address-range-to-azure-stack"></a>將 IP 位址範圍新增到 Azure Stack

1. 在網際網路瀏覽器中，瀏覽到系統管理員入口網站儀表板。  此範例中，我們將使用 https://adminportal.local.azurestack.external。  
2.  以雲端操作員的身分登入 Azure Stack 管理入口網站。
3.  在預設儀表板上，尋找 [區域管理] 清單，然後選取您想要管理的區域，例如 local。
4.  尋找 [資源提供者] 動態磚，然後按一下 [網路資源提供者]。
5.  按一下 [公用 IP 集區使用量] 動態磚。
6.  按一下 [新增 IP 集區] 按鈕。
7.  提供 IP 集區的名稱。  您選擇的名稱可供您輕鬆識別 IP 集區，因此您可以設定您喜歡的任何名稱。  可以設定與位址範圍相同的名稱，不過並非必要這麼做。
8.   輸入要以 CIDR 標記法新增的位址區塊。  例如：192.168.203.0/24
9.  您在 [位址範圍 (CIDR 區塊) 欄位] 中提供有效的 CIDR 範圍時，[起始 IP 位址]、[結束 IP 位址] 和 [可用的 IP 位址] 欄位會自動填入。  這些是唯讀而且自動產生，因此，不需要修改 [位址範圍] 欄位中的值便無法變更這些值。
10. 檢閱刀鋒視窗的資訊，並確認一切正確之後，按一下 [確定] 認可變更，並將位址範圍新增到 Azure Stack。

## <a name="update-the-acls-on-your-top-of-rack-switches"></a>在機架頂端交換器上更新 ACL
您啟用新加入的 IP 範圍最後需要處理的一件事是，在機架頂端 (ToR) 交換器上更新存取控制清單 (ACL)。  ToR 交換器上的 ACL 已鎖定，因此從 Azure Stack 外部到新加入的 IP 範圍之間的連線無法運作，直到新的範圍新增到交換器上的 ACL 為止。  

您需要連絡 OEM，並配合更新 ToR 交換器上的 ACL。  他們有對於這項操作提供支援所需的工具。


## <a name="next-steps"></a>後續步驟 
[檢閱單位節點動作](azure-stack-node-actions.md) 
