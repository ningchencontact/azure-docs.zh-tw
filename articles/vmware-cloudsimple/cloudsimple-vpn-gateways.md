---
title: VMware Solution by CloudSimple 中的 VPN 閘道-Azure
description: 深入瞭解 CloudSimple 站對站 VPN 和點對站 VPN 概念
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d922f87e9a915bd5af9d2b1257dee8044773797e
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816146"
---
# <a name="vpn-gateways-overview"></a>VPN 閘道總覽

VPN 閘道是用來在內部部署位置的 CloudSimple 區域網路與透過公用網際網路的電腦之間傳送加密流量。  每個區域只能有一個 VPN 閘道。 不過，您可以對相同的 VPN 閘道建立多個連線。 當您對相同的 VPN 閘道建立多個連線時，所有 VPN 通道都會共用可用的閘道頻寬。

CloudSimple 提供兩種 VPN 閘道:

* 站對站 VPN 閘道
* 點對站 VPN 閘道

## <a name="site-to-site-vpn-gateway"></a>站對站 VPN 閘道

站對站 VPN 閘道可用來在 CloudSimple 區域網路與內部部署資料中心之間傳送加密的流量。 使用此連線來定義子網/CIDR 範圍, 以便在內部部署網路與 CloudSimple 區域網路之間進行通訊。

VPN 閘道可讓您在私人雲端上使用內部部署的服務, 以及從內部部署網路取用私人雲端上的服務。  CloudSimple 提供以原則為基礎的 VPN 伺服器, 以便從您的內部部署網路建立連線。

站對站 VPN 的使用案例包括:

* 您的私人雲端 vCenter 從內部部署網路中的任何工作站存取。
* 使用內部部署 Active Directory 作為 vCenter 身分識別來源。
* 方便將 VM 範本、Iso 和其他檔案從您的內部部署資源傳輸到您的私用雲端 vCenter。
* 從您的內部部署網路在私人雲端上執行之工作負載的協助工具。

![站對站 VPN 連線拓撲](media/cloudsimple-site-to-site-vpn-connection.png)

> [!IMPORTANT]
> 您必須將 TCP MSS 固定在1078個位元組或更低。 或者, 如果您的 VPN 裝置不支援 MSS 固定, 您也可以改為將通道介面上的 MTU 設定為1118個位元組。 

### <a name="cryptographic-parameters"></a>密碼編譯參數

站對站 VPN 連線會使用下列預設的密碼編譯參數來建立安全連線。  當您從內部部署 VPN 裝置建立連線時, 請使用您的內部部署 VPN 閘道所支援的下列任何參數。

#### <a name="phase-1-proposals"></a>第1階段提案

| 參數                       | 提案1     | 提案2     | 提案3     |
|---------------------------------|----------------|----------------|----------------|
| IKE 版本                     | IKEv1          | IKEv1          | IKEv1          |
| 加密                      | AES 128        | AES 256        | AES 256        |
| 雜湊演算法                  | SHA 256        | SHA 256        | SHA 1          |
| Diffie-hellman 群組 (DH 群組) | 2              | 2              | 2              |
| 存留時間                       | 28,800 秒 | 28,800 秒 | 28,800 秒 |
| 資料大小                       | 4 GB           | 4 GB           | 4 GB           |
| 停用的對等偵測 (DPD)       | 已停用/關閉   | 已停用/關閉   | 已停用/關閉   |


#### <a name="phase-2-proposals"></a>第2階段提案 

| 參數                                 | 提案1    | 提案2    | 提案3    |
|-------------------------------------------|---------------|---------------|---------------|
| 加密                                | AES 128       | AES 256       | AES 256       |
| 雜湊演算法                            | SHA 256       | SHA 256       | SHA 1         |
| 完整轉寄秘密群組 (PFS 群組) | None          | None          | None          |
| 存留時間                                 | 1800秒 | 1800秒 | 1800秒 |
| 資料大小                                 | 4 GB          | 4 GB          | 4 GB          |

## <a name="point-to-site-vpn-gateway"></a>點對站 VPN 閘道

點對站 VPN 是用來在 CloudSimple 區域網路與用戶端電腦之間傳送加密的流量。  點對站 VPN 是存取私人雲端網路最簡單的方式, 包括私人雲端 vCenter 和工作負載 Vm。  如果您是從遠端連線到私人雲端, 請使用點對站 VPN 連線能力。

## <a name="next-steps"></a>後續步驟

* [設定 VPN 閘道](https://docs.azure.cloudsimple.com/vpn-gateway/)