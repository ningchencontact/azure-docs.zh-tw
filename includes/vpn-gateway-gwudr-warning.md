---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a852807ab685e85b76d26e5b39c99a32f645bbd7
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838169"
---
**不支援**具有 0.0.0.0/0 目的地的使用者定義路由和 GatewaySubnet 上的 nsg。 使用此設定建立的閘道將會遭到封鎖而無法建立。 閘道需要存取管理控制器，才能正常運作。
