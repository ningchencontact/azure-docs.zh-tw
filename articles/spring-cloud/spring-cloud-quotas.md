---
title: Azure 春季雲端的服務方案和配額
description: 瞭解 Azure 春季雲端的服務配額和服務方案
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: 41a2b1a7d9aa5089ba2ee73cd3c5c5c5e31f5225
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607678"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Azure 春季雲端的配額和服務方案

所有 Azure 服務都會設定資源和功能的預設限制和配額。  在預覽期間，Azure 春季雲端僅提供一個服務方案。

本文詳細說明目前預覽期間所提供的服務配額。

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Azure 春季雲端服務層和配額

在預覽期間，Azure 春季雲端僅提供一個服務層。

資源 | Amount
------- | -------
vCPU | 4
記憶體 | 8 Gb
Azure 春季雲端訂用帳戶 | 1
每個訂用帳戶每個區域的 Azure 春季雲端服務實例 | 2
每個 Azure 春季雲端服務實例的應用程式實例總數 | 50
每個春季應用程式的應用程式實例總計 | 20
永續性磁碟區 | 10 x 50 Gb

當您達到配額時，您會收到400錯誤，其中會顯示「配額超過訂用帳戶在*您的 Azure 春季雲端服務建立所在區域區域*中的訂用帳戶限制」。

## <a name="next-steps"></a>後續步驟

某些預設限制和配額可以增加。 如果您的資源需要增加，請[建立支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。
