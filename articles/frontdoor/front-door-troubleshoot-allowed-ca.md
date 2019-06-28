---
title: 可在 Azure Front Door Service 上啟用自訂 HTTPS 的允許憑證授權單位 | Microsoft Docs
description: 如果您使用自己的憑證來啟用自訂網域上的 HTTPS，就必須使用允許的憑證授權單位 (CA) 來建立它。
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: sharadag
ms.openlocfilehash: e2bab9f9e1ae099952b34e66f7250e7ecbc8e689
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330767"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door-service"></a>可在 Azure Front Door Service 上啟用自訂 HTTPS 的允許憑證授權單位

針對 Azure Front Door Service 自訂網域，當您[使用自己的憑證啟用 HTTPS 功能](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)時，必須使用允許的憑證授權單位 (CA) 來建立您的 SSL 憑證。 否則，如果您使用非允許的 CA 或自我簽署憑證，系統將會拒絕您的要求。

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
