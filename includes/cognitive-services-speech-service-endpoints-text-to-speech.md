---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 4f48fc0ad65984df42e2a85853f39e49d7481302
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020452"
---
### <a name="standard-and-neural-voices"></a>標準和類神經的語音

您可以使用此表格來判斷區域/端點的標準和類神經語音的可用性：

| 區域 | 端點 | 標準的語音 | 類神經的語音 |
|--------|----------|-----------------|---------------|
| 澳洲東部 | https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1 | 是 | 是 |
| 加拿大中部 | https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1 | 是 | 是 |
| 美國中部 | https://centralus.tts.speech.microsoft.com/cognitiveservices/v1 | 是 | 否 |
| 東亞 | https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1 | 是 | 否 |
| 美國東部 | https://eastus.tts.speech.microsoft.com/cognitiveservices/v1 | 是 | 是 |
| 美國東部 2 | https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1 | 是 | 否 |
| 法國中部 | https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1 | 是 | 否 |
| 印度中部 | https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1 | 是 | 是 |
| 日本東部 | https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1 | 是 | 否 |
| 南韓中部 | https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1 | 是 | 否 |
| 美國中北部 | https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1 | 是 | 否 |
| 北歐 | https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1 | 是 | 否 |
| 美國中南部 | https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1 | 是 | 是 |
| 東南亞 | https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1 | 是 | 是 |
| 英國南部 | https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1 | 是 | 是 |
| 西歐 | https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1 | 是 | 是 |
| 美國西部 | https://westus.tts.speech.microsoft.com/cognitiveservices/v1 | 是 | 否 |
| 美國西部 2 | https://westus2.tts.speech.microsoft.com/cognitiveservices/v1 | 是 | 是 |

### <a name="custom-voices"></a>自訂語音

如果您已建立自訂的語音字型，請使用您已建立的端點。 您也可以使用下面列出的端點取代`{deploymentId}`與您的語音模型的部署 ID。

| 區域 | 端點 |
|--------|----------|
| 澳洲東部 | https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| 加拿大中部 | https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| 美國中部 | https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| 東亞 | https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| 美國東部 | https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| 美國東部 2 | https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| 法國中部 | https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| 印度中部 | https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| 日本東部 | https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| 南韓中部 | https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| 美國中北部 | https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| 北歐 | https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| 美國中南部 | https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| 東南亞 | https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| 英國南部 | https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| 西歐 | https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| 美國西部 | https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| 美國西部 2 | https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
