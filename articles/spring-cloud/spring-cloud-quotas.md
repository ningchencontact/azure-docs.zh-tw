---
title: Azure 春季雲端的服務方案和配額
description: 瞭解 Azure 春季雲端的服務配額和服務方案
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: a5352b371c5754672e668e53eb5e4211de9c46cc
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2020
ms.locfileid: "75891483"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Azure 春季雲端的配額和服務方案

所有 Azure 服務都會設定資源和功能的預設限制和配額。  在預覽期間，Azure 春季雲端僅提供一個服務方案。

本文詳細說明目前預覽期間所提供的服務配額。

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Azure 春季雲端服務層和配額

在預覽期間，Azure 春季雲端僅提供一個服務層。

資源 | 金額
------- | -------
vCPU | 每個服務實例4個
記憶體 | 每個服務實例 8 Gb
Azure Spring Cloud 服務執行個體 (每個訂用帳戶在每個區域) | 10
總應用程式執行個體 (每個 Azure Spring Cloud 服務執行個體) | 500
每個春季應用程式的應用程式實例總計 | 20
永續性磁碟區 | 10 x 50 GB

當您達到配額時，您會收到400錯誤，其中會顯示「配額超過訂用帳戶在*您的 Azure 春季雲端服務建立所在區域區域*中的訂用帳戶限制」。

## <a name="next-steps"></a>後續步驟

某些預設限制和配額可以增加。 如果您的資源需要增加，請[建立支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。
