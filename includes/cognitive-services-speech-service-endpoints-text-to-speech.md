---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: d5a4b3a07854c2664de7ec60f3677b666798a9bd
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2019
ms.locfileid: "65237105"
---
### <a name="standard-and-neural-voices"></a>標準和類神經的語音

您可以使用此表格來判斷區域/端點的標準和類神經語音的可用性：

| 區域 | 端點 | 標準的語音 | 類神經的語音 |
|--------|----------|-----------------|---------------|
| 澳洲東部 | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | 有 | 有 |
| 加拿大中部 | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | 有 | 有 |
| 美國中部 | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | 有 | 無 |
| 東亞 | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | 有 | 無 |
| 美國東部 | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | 有 | 有 |
| 美國東部 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | 有 | 無 |
| 法國中部 | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | 有 | 無 |
| 印度中部 | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | 有 | 有 |
| 日本東部 | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | 有 | 無 |
| 南韓中部 | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | 有 | 無 |
| 美國中北部 | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | 有 | 無 |
| 北歐 | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | 有 | 無 |
| 美國中南部 | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | 有 | 有 |
| 東南亞 | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | 有 | 有 |
| 英國南部 | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | 有 | 有 |
| 西歐 | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | 有 | 有 |
| 美國西部 | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | 有 | 無 |
| 美國西部 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | 有 | 有 |

### <a name="custom-voices"></a>自訂語音

如果您已建立自訂的語音字型，請使用您已建立的端點。 您也可以使用下面列出的端點取代`{deploymentId}`與您的語音模型的部署 ID。

| 區域 | 端點 |
|--------|----------|
| 澳洲東部 | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 加拿大中部 | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 美國中部 | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 東亞 | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 美國東部 | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 美國東部 2 | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 法國中部 | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 印度中部 | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 日本東部 | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 南韓中部 | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 美國中北部 | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 北歐 | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 美國中南部 | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 東南亞 | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 英國南部 | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 西歐 | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 美國西部 | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 美國西部 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
