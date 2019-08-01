---
title: 安裝語音容器
titleSuffix: Azure Cognitive Services
description: 詳細解說文字到語音轉換 helm 圖表設定選項。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: e6c7dcd3015b0b8ab5b3c719ebd2397bc814b81a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "67717238"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>文字轉換語音 (子圖表: 圖表/textToSpeech)

若要覆寫「傘」圖表, 請在`textToSpeech.`任何參數上加入前置詞, 使其更明確。 例如, 它會`textToSpeech.numberOfConcurrentRequest`覆寫對應的參數, 例如覆寫。 `numberOfConcurrentRequest`

|參數|描述|預設|
| -- | -- | -- |
| `enabled` | **文字轉換語音**服務是否已啟用。 | `false` |
| `numberOfConcurrentRequest` | **文字轉換語音**服務的並行要求數目。 此圖表會根據此值, 自動計算 CPU 和記憶體資源。 | `2` |
| `optimizeForTurboMode`| 服務是否需要針對透過文字檔的文字輸入進行優化。 如果`true`為, 則此圖表會將更多 CPU 資源配置給服務。 | `false` |
| `image.registry`| **文字轉換語音**docker 映射登錄。 | `containerpreview.azurecr.io` |
| `image.repository` | **文字轉換語音**docker 映射存放庫。 | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | **文字轉換語音**docker 映射標記。 | `latest` |
| `image.pullSecrets` | 用來提取**文字轉換語音**docker 映射的映射秘密。 | |
| `image.pullByHash`| 是否由雜湊提取 docker 映射。 如果`true`為`image.hash` , 則為必要。 | `false` |
| `image.hash`| **文字轉換語音**docker 映射雜湊。 僅用於`image.pullByHash: true`。  | |
| `image.args.eula`具備 | 表示您已接受授權。 唯一有效的值是`accept` | |
| `image.args.billing`具備 | [計費端點 URI] 值可在 Azure 入口網站的語音總覽頁面上取得。 | |
| `image.args.apikey`具備 | 用來追蹤帳單資訊。 ||
| `service.type` | **文字轉換語音**服務的 Kubernetes 服務類型。 如需詳細資訊, 請參閱[Kubernetes 服務類型指示](https://kubernetes.io/docs/concepts/services-networking/service/), 並確認雲端提供者支援。 | `LoadBalancer` |
| `service.port`|  **文字轉換語音**服務的埠。 | `80` |
| `service.autoScaler.enabled` | [水準 Pod 自動調整程式](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)是否已啟用。 如果`true`為`text-to-speech-autoscaler` , 將會部署到 Kubernetes 叢集中。 | `true` |
| `service.podDisruption.enabled` | 是否啟用[Pod 中斷預算](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)。 如果`true`為`text-to-speech-poddisruptionbudget` , 將會部署到 Kubernetes 叢集中。 | `true` |