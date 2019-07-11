---
title: 安裝語音容器
titleSuffix: Azure Cognitive Services
description: 詳細說明了語音轉換文字 helm 圖表組態選項。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 1b46c58d3f3c804052e637f7bde2e1a456764dba
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717207"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>語音轉換文字 (子圖表： 圖表/speechToText)

若要覆寫 「 保護傘 」 圖表，加上前置詞`speechToText.`上任何參數，讓它更具體。 比方說，它會覆寫的對應參數例如`speechToText.numberOfConcurrentRequest`會覆寫`numberOfConcurrentRequest`。

|參數|描述|預設|
| -- | -- | -- |
| `enabled` | 是否**語音轉換文字**服務已啟用。 | `false` |
| `numberOfConcurrentRequest` | 並行要求數目**語音轉換文字**服務。 此圖表會自動計算 CPU 和記憶體資源，根據此值。 | `2` |
| `optimizeForAudioFile`| 是否需要針對透過音訊檔案的音訊輸入最佳化服務。 如果`true`，此圖表會配置更多的 CPU 資源，以服務。 | `false` |
| `image.registry`| **語音轉換文字**docker 映像登錄。 | `containerpreview.azurecr.io` |
| `image.repository` | **語音轉換文字**docker 映像儲存機制。 | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | **語音轉換文字**docker 映像標記。 | `latest` |
| `image.pullSecrets` | 提取映像祕密**語音轉換文字**docker 映像。 | |
| `image.pullByHash`| 是否會提取 docker 映像，依據雜湊。 如果`true`，`image.hash`需要。 | `false` |
| `image.hash`| **語音轉換文字**docker 映像雜湊。 僅當使用`image.pullByHash: true`。  | |
| `image.args.eula` （必要） | 表示您接受授權。 唯一有效的值是 `accept` | |
| `image.args.billing` （必要） | 使用 Azure 入口網站的 [語音概觀] 頁面上的帳單寄送的端點 URI 值。 | |
| `image.args.apikey` （必要） | 用來追蹤帳單資訊。 ||
| `service.type` | Kubernetes 服務的型別**語音轉換文字**服務。 請參閱[Kubernetes 服務型別指示](https://kubernetes.io/docs/concepts/services-networking/service/)如需詳細資訊，並確認雲端提供者支援。 | `LoadBalancer` |
| `service.port`|  連接埠**語音轉換文字**服務。 | `80` |
| `service.autoScaler.enabled` | 是否[水平 Pod 自動調整程式](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)已啟用。 如果`true`，則`speech-to-text-autoscaler`將部署的 Kubernetes 叢集中。 | `true` |
| `service.podDisruption.enabled` | 是否[Pod 中斷預算](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)已啟用。 如果`true`，則`speech-to-text-poddisruptionbudget`將部署的 Kubernetes 叢集中。 | `true` |