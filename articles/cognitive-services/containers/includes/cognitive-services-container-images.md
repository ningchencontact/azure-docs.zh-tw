---
ms.openlocfilehash: fe9879f9574fe1496ebdf20ac76fe5b759d4ea6a
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051222"
---
### <a name="container-repositories-and-images"></a>容器存放庫和映射

下表是 Azure 認知服務提供之可用容器映射的完整清單。

#### <a name="public-container-registry-mcrmicrosoftcom"></a>公用 (container registry: `mcr.microsoft.com`)

Microsoft Container Registry 會裝載認知服務的所有公開上市 (GA) 容器。

| 服務 | 容器 | Container Registry/存放庫/映射名稱 |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [文字分析](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 關鍵片語擷取 | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [文字分析](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 語言偵測 | `mcr.microsoft.com/azure-cognitive-services/language` |
| [文字分析](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 情感分析 | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-preview-container-registry-containerpreviewazurecrio"></a>公開預覽 (container registry: `containerpreview.azurecr.io`)

容器預覽登錄會針對尚未進入公開上市 (GA) 的認知服務, 裝載所有「公開預覽」容器。 這些容器需要正式的存取要求, 才能使用它們。

| 服務 | 容器 | Container Registry/存放庫/映射名稱 |
|--|--|--|
| [異常偵測器](../../anomaly-detector/anomaly-detector-container-howto.md) | 異常偵測器 | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [電腦視覺](../../Computer-vision/computer-vision-how-to-install-containers.md) | 辨識文字 | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text` |
| [臉部](../../face/face-how-to-install-containers.md) | 臉部 | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [表單辨識器](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | 表單辨識器 | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [語音服務 API](../../speech-service/speech-container-howto.md) | 語音轉文字 | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [語音服務 API](../../speech-service/speech-container-howto.md) | 文字轉換語音 | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
