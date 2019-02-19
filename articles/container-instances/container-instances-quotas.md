---
title: Azure Container Instances 配額和區域可用性
description: Azure Container Instances 服務的預設配額和區域可用性。
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 02/08/2019
ms.author: danlep
ms.openlocfilehash: 35e846aa5954e3714d301c9c75cf42b31961fdfe
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56160572"
---
# <a name="quotas-and-region-availability-for-azure-container-instances"></a>Azure Container Instances 的配額和區域可用性

所有 Azure 服務都包含資源和功能的特定預設限制和配額。 下列各節詳細說明數個 Azure Container Instances (ACI) 資源的預設資源限制，以及 Azure 區域中 ACI 服務的可用性。

## <a name="service-quotas-and-limits"></a>服務配額和限制

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

## <a name="region-availability"></a>區域可用性

具有指定 CPU 和記憶體限制的 Azure Container Instances 可於下列區域取得。 以下是本文章發行時的值。 如需最新資訊，請使用[清單功能](/rest/api/container-instances/listcapabilities/listcapabilities) API。 搭配[虛擬網路](container-instances-vnet.md) (預覽) 或 [GPU 資源](container-instances-gpu.md) (預覽) 使用 Azure Container Instances 時，可用性和資源限制可能會有所不同。

| 位置 | 作業系統 | CPU | 記憶體 (GB) |
| -------- | -- | :---: | :-----------: |
| 加拿大中部、美國中部、美國東部 2 | Linux | 4 | 16 |
| 美國東部、北歐、西歐、美國西部、美國西部 2 | Linux | 4 | 14 |
| 日本東部 | Linux | 2 | 8 |
| 澳大利亞東部、東南亞 | Linux | 2 | 7 |
| 印度中部、東亞、美國中北部、美國中南部、印度南部 | Linux | 2 | 3.5 |
| 美國東部、西歐、美國西部 | Windows | 4 | 14 |
| 澳大利亞東部、加拿大中部、印度中部、美國中部、東亞、美國東部 2、日本東部、美國中北部、北歐、美國中南部、印度南部、東南亞、美國西部 2 | Windows | 2 | 3.5 |

在這些資源限制下建立的容器執行個體都會受限於部署地區的可用性。 地區負載過重時，您在部署執行個體時可能會失敗。 若要減少這類的部署失敗，請嘗試部署 CPU 和記憶體設定較低階的執行個體，或過一段時間再部署。

在 [aka.ms/aci/feedback](https://aka.ms/aci/feedback) 讓小組知道其他必要的區域或增加的 CPU/記憶體限制。

如需有關為容器執行個體部署疑難排解的詳細資訊，請參閱[為 Azure Container Instances 的部署問題疑難排解](container-instances-troubleshooting.md)。

## <a name="next-steps"></a>後續步驟

某些預設限制和配額可以增加。 若要要求增加一或多個支援此類增加的資源，請提交 [Azure 支援要求][azure-support] (針對 [問題類型] 選取 [配額])。

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
