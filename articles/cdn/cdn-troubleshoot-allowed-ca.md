---
title: 可在 Azure CDN 上啟用自訂 HTTPS 的允許憑證授權單位 | Microsoft Docs
description: 如果您使用自己的憑證來啟用自訂網域上的 HTTPS，就必須使用允許的憑證授權單位 (CA) 來建立它。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 8ce141fbf3d767159d16495bff5a93d791224c17
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331890"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>可在 Azure CDN 上啟用自訂 HTTPS 的允許憑證授權單位

針對 **Microsoft 所提供的 Azure CDN 標準**端點上的 Azure 內容傳遞網路 (CDN) 自訂網域，當您[使用自己的憑證啟用 HTTPS 功能](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates)時，必須使用允許的憑證授權單位 (CA) 來建立您的 SSL 憑證。 否則，如果您使用非允許的 CA 或自我簽署憑證，系統將會拒絕您的要求。

> [!NOTE]
> 使用您自己的憑證來啟用自訂 HTTPS 的選項，僅適用於 **Microsoft 所提供的 Azure CDN 標準**設定檔。 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

