---
title: 以手動方式停止，或在 Azure Container Instances 中啟動容器
description: 了解如何以手動方式停止或開始在 Azure Container Instances 中的容器群組。
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 50f3ecf69561313a5bda67827cfb02d2f61d461f
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610199"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>以手動方式停止，或在 Azure Container Instances 中啟動容器

[重新啟動原則](container-instances-restart-policy.md)容器群組中的設定會決定容器執行個體開始或停止預設的方式。 您可以覆寫預設設定，方法是以手動方式停止或啟動容器群組。

## <a name="stop"></a>Stop

例如，使用手動停止執行中容器的群組- [az 容器停止][ az-container-stop]命令或 Azure 入口網站。 針對特定容器工作負載，可能會想要停止長時間執行的容器群組定義以節省成本之後。 

*當容器群組輸入 [停止] 狀態時，它會終止，並回收群組中的所有容器。它不會保留容器狀態。*

已停止的容器群組中的容器會循環，雖然[資源](container-instances-container-groups.md#resource-allocation)會維持配置供使用。 因此，計費會繼續已停止的容器群組。

如果容器群組已終止，停止動作會有任何作用 （處於 「 成功 」 或 「 失敗的狀態 」）。 例如，執行一次容器工作成功執行的容器群組終止處於成功狀態。 嘗試停止，因為狀態不會變更狀態的群組。 

## <a name="start"></a>Start

時已停止的容器群組 – 可能是因為自行終止的容器，或您手動停止群組-您可以啟動容器。 例如，使用[az 容器開始][ az-container-start]命令或 Azure 入口網站，以手動方式啟動群組中的容器。 如果更新任何容器的容器映像，會提取新映像。 

啟動容器群組會開始相同容器設定的新部署。 這個動作可協助您快速重複使用已知的容器群組設定，按照預期的方式運作。 您不需要建立新的容器群組，即可執行相同的工作負載。

容器群組中的所有容器會都啟動此動作。 您無法啟動特定容器群組中。

您手動啟動或重新啟動的容器群組之後，容器群組會根據設定的重新啟動原則執行。
  
## <a name="restart"></a>重新啟動

執行-例如，使用，您可以重新啟動的容器群組[az 容器重新啟動][ az-container-restart]命令。 此動作會重新啟動容器群組中的所有容器。 如果更新任何容器的容器映像，會提取新映像。 

您想要對部署問題進行疑難排解時，重新啟動容器群組很有幫助。 例如，如果暫存資源限制導致您的容器無法成功執行，重新啟動群組可能會解決此問題。

容器群組中的所有容器都會重新都啟動此動作。 您無法在群組中，以重新啟動特定的容器。

手動重新啟動的容器群組之後，根據所設定的容器群組執行重新啟動原則。

## <a name="next-steps"></a>後續步驟

深入了解[重新啟動原則設定](container-instances-restart-policy.md)Azure Container Instances 中。

除了以手動方式停止及啟動容器群組以現有的組態，您可以[更新設定](container-instances-update.md)執行的容器群組。

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
