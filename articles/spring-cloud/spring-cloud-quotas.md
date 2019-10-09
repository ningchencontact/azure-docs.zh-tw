---
title: Azure 春季雲端的服務方案和配額
description: 瞭解 Azure 春季雲端的服務配額和服務方案
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 9/27/2019
ms.author: jeconnoc
ms.openlocfilehash: 89934ee0ab0c901a904a1a5ac2fb620185571a23
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038778"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Azure 春季雲端的配額和服務方案

所有 Azure 服務都會設定資源和功能的預設限制和配額。  在預覽期間，Azure 春季雲端僅提供一個服務方案。

本文詳細說明目前預覽期間所提供的服務配額。

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Azure 春季雲端服務層和配額

在預覽期間，Azure 春季雲端僅提供一個服務層。

Resource | Amount
------- | -------
vCPU | 4
記憶體 | 8 Gb
Azure 春季雲端訂用帳戶 | 1
每個訂用帳戶每個區域的 Azure 春季雲端服務實例 | 2
每個 Azure 春季雲端服務實例的應用程式實例總數 | 50
每個春季應用程式的應用程式實例總計 | 20
永續性磁碟區 | 10 x 50 Gb

當您達到配額時，將會收到400錯誤，其為：「配額超過訂用帳戶在*您的 Azure 春季雲端服務建立*所在區域區域*的訂用*帳戶限制。

## <a name="next-steps"></a>後續步驟

某些預設限制和配額可以增加。 如果您的資源需要增加，請將您的要求傳送給我們： azure-spring-cloud@service.microsoft.com。
