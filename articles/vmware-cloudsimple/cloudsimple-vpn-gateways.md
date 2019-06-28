---
title: 在 VMware 的解決方案，由 CloudSimple-Azure VPN 閘道
description: 深入了解 CloudSimple 站對站 VPN 和點對站 VPN 概念
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a91d0f75b7e27065bd2ee0ea430a859d265afacb
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332041"
---
# <a name="vpn-gateways-overview"></a>VPN 閘道概觀

VPN 閘道用來 CloudSimple 區域網路的內部部署位置或透過公用網際網路的電腦之間傳送加密的流量。  每個區域可以有一個 VPN 閘道。 不過，您可以對相同的 VPN 閘道建立多個連線。 當您對相同的 VPN 閘道建立多個連線時，所有 VPN 通道都會共用可用的閘道頻寬。

CloudSimple 提供兩種類型的 VPN 閘道：

* 站對站 VPN 閘道
* 點對站 VPN 閘道

## <a name="site-to-site-vpn-gateway"></a>站對站 VPN 閘道

站對站 VPN 閘道用來 CloudSimple 區域網路和內部部署資料中心之間傳送加密的流量。 使用這個連線，以定義子網路/CIDR 範圍，您的內部部署網路與 CloudSimple 區域網路之間的通訊。

VPN 閘道可讓您從內部部署私人雲端，在使用服務，與您的私人雲端，從內部部署網路上的服務。  CloudSimple 提供以原則為基礎的 VPN 伺服器建立從內部部署網路連線。

站對站 VPN 的使用案例包括：

* 您從內部部署網路中的任何工作站的私用雲端 vCenter 的存取範圍。
* 使用您的內部部署 Active Directory 作為 vCenter 身分識別來源。
* 方便的傳輸 VM 範本、 Iso、 和其他檔案從您的內部部署資源到私用雲端 vCenter。
* 從您的內部部署網路，在您的私人雲端上執行的工作負載的存取範圍。

![站對站 VPN 連線拓撲](media/cloudsimple-site-to-site-vpn-connection.png)

> [!IMPORTANT]
> 1078 位元組或較低，您必須將 TCP MSS。 或如果您的 VPN 裝置不支援 MSS 固定，您可以另外設定 MTU 1118 位元組的通道介面上改為。 

### <a name="cryptographic-parameters"></a>密碼編譯參數

站對站 VPN 連線會使用下列的預設密碼編譯參數，建立安全連線。  當您建立從內部部署 VPN 裝置的連線時，必須符合參數。

站對站 VPN 連線會使用下列的預設密碼編譯參數，建立安全連線。  當您建立從內部部署 VPN 裝置的連線時，使用任何您的內部部署 VPN 閘道支援下列參數。

#### <a name="phase-1-proposals"></a>第 1 階段提案

| 參數 | 提案 1 | 提案 2 | 提案 3 |
|-----------|------------|------------|------------|
| IKE 版本 | IKEv1 | IKEv1 | IKEv1 |
| 加密 | AES 128 | AES 256 | AES 256 |
| 雜湊演算法| SHA 256 | SHA 256 | SHA 1 |
| Diffie Hellman 群組 （DH 群組） | 2 | 2 | 2 |
| 存留時間 | 28,800 秒 | 28,800 秒 | 28,800 秒 |
| 資料大小 | 4 GB | 4 GB | 4 GB |


#### <a name="phase-2-proposals"></a>第 2 階段提案 

| 參數 | 提案 1 | 提案 2 | 提案 3 |
|-----------|------------|------------|------------|
| 加密 | AES 128 | AES 256 | AES 256 |
| 雜湊演算法| SHA 256 | SHA 256 | SHA 1 |
| 完美的正向加密群組 （PFS 群組） | None | None | None |
| 存留時間 | 1,800 秒 | 1,800 秒 | 1,800 秒 |
| 資料大小 | 4 GB | 4 GB | 4 GB |

## <a name="point-to-site-vpn-gateway"></a>點對站 VPN 閘道

點對站 VPN 來 CloudSimple 區域網路和用戶端電腦之間傳送加密的流量。  點對站 VPN 是最簡單的方式來存取私用雲端網路，包括您的私用雲端的 vCenter 和工作負載的 Vm。  如果您要從遠端連線到私人雲端，請使用點對站 VPN 連線能力。

## <a name="next-steps"></a>後續步驟

* [設定 VPN 閘道](https://docs.azure.cloudsimple.com/vpn-gateway/)