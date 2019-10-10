---
title: 包含檔案
description: 包含檔案
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f7167cbb26e69941cade01ab8c0b8d9dc633f0d2
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168410"
---
使用自訂 IPsec 原則時，請記住下列需求：

* **Ike** -若是 ike，您可以從 ike 加密選取任何參數，加上來自 ike 完整性的任何參數，再加上來自 DH 群組的任何參數。
* **Ipsec** -若為 ipsec，您可以從 ipsec 加密中選取任何參數，再加上來自 ipsec 完整性的任何參數，加上 PFS。 如果 IPsec 加密或 IPsec 完整性的任何參數是 GCM，則這兩個設定的參數必須是 GCM。

>[!NOTE]
> 使用自訂 IPsec 原則時，回應者和啟動器沒有任何概念（不同于預設 IPsec 原則）。 兩端（內部部署和 Azure VPN 閘道）都會針對 IKE 階段1和 IKE 階段2使用相同的設定。 同時支援 IKEv1 和 IKEv2 通訊協定。 不支援 Azure 做為回應者。
>

**可用的設定和參數**

| 設定 | 參數 |
|--- |--- |
| IKE 加密 | AES256、AES192、AES128 |
| IKE 完整性 | SHA384，SHA256，SHA1 |
| DH 群組 | DHGroup24、ECP384、ECP256、DHGroup14、DHGroup2048、DHGroup2 |
| IPsec 加密 | GCMAES256、GCMAES192、GCMAES128、AES256、AES192、AES128 |
| IPsec 完整性 | GCMASE256、GCMAES192、GCMAES128、SHA256、SHA1 |
| PFS 群組 | PFS24、ECP384、ECP256、PFS2048 作為、PFS2 |
