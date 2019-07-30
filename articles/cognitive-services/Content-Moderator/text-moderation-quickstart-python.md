---
title: 快速入門：在 Python 中分析文字內容 - Content Moderator
titlesuffix: Azure Cognitive Services
description: 如何使用 Content Moderator SDK for Python 來分析文字內容中的各種不當題材
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: bb0e44f83e2101a7b21e7b7ec6fdc75974c6d6d8
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333602"
---
# <a name="quickstart-analyze-text-content-for-objectionable-material-in-python"></a>快速入門：使用 Python 分析文字內容中的不當題材

本文提供可協助您開始使用 Content Moderator SDK for Python 的資訊和程式碼範例。 您將了解如何執行文字內容的字詞型篩選和分類，以仲裁可能令人反感的內容。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。 

## <a name="prerequisites"></a>必要條件
- Content Moderator 訂用帳戶金鑰。 請依照[建立認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的指示訂閱 Content Moderator 並取得金鑰。
- [Python 2.7+ 或 3.5+](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/) 工具

## <a name="get-the-content-moderator-sdk"></a>取得 Content Moderator SDK

透過開啟命令提示字元並執行下列命令，來安裝 Content Moderator Python SDK：

```bash
pip install azure-cognitiveservices-vision-contentmoderator
```

## <a name="import-modules"></a>匯入模組

建立名為 _ContentModeratorQS.py_ 的新 Python 指令碼，然後新增下列程式碼來匯入 SDK 的必要組件。 其中包含美化顯示模組，以利輕鬆讀取 JSON 回應。

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=imports)]


## <a name="initialize-variables"></a>將變數初始化

接下來，針對您的 Content Moderator 訂用帳戶金鑰和端點 URL 來新增變數。 將名稱 `CONTENT_MODERATOR_SUBSCRIPTION_KEY` 新增至您的環境變數，並以您的訂用帳戶金鑰作為其值。 針對您的基底端點 URL，請將 `CONTENT_MODERATOR_ENDPOINT` 新增至環境變數，並以您的區域特定 URL 作為其值，例如 `https://westus.api.cognitive.microsoft.com`。 **westus** 區域會產生免費試用的訂用帳戶金鑰。

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=authentication)]

系統將會仲裁檔案中的多行文字字串。 將 [content_moderator_text_moderation.txt](https://github.com/Azure-Samples/cognitive-services-content-moderator-samples/blob/master/documentation-samples/python/content_moderator_text_moderation.txt) 檔案包含在本機根資料夾中，並將其檔案名新增至您的變數：

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=textModerationFile)]

## <a name="query-the-moderator-service"></a>查詢仲裁者服務

使用您的訂用帳戶金鑰和端點 URL，來建立 **ContentModeratorClient** 執行個體。 

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=client)]

然後，使用您的用戶端及其成員 **TextModerationOperations** 執行個體來呼叫函式 `screen_text` 的仲裁 API。 如需如何呼叫它的詳細資訊，請參閱 **[screen_text](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)** \(英文\) 參考文件。

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=textModeration)]

## <a name="check-the-printed-response"></a>檢查列印的回應

執行範例並確認回應。 順利完成時，會傳回**過濾**執行個體。 成功的結果顯示如下：

```console
{'auto_corrected_text': '" Is this a garbage email abide@ abed. com, phone: '
                        '6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, '
                        'Redmond, WA 98052. Crap is the profanity here. Is '
                        'this information PII? phone 3144444444\\ n"',
 'classification': {'category1': {'score': 0.00025233393535017967},
                    'category2': {'score': 0.18468093872070312},
                    'category3': {'score': 0.9879999756813049},
                    'review_recommended': True},
 'language': 'eng',
 'normalized_text': '" Is this a garbage email abide@ abed. com, phone: '
                    '6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, '
                    'Redmond, WA 98052. Crap is the profanity here. Is this '
                    'information PII? phone 3144444444\\ n"',
 'original_text': '"Is this a grabage email abcdef@abcd.com, phone: '
                  '6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, '
                  'WA 98052. Crap is the profanity here. Is this information '
                  'PII? phone 3144444444\\n"',
 'pii': {'address': [{'index': 82,
                      'text': '1 Microsoft Way, Redmond, WA 98052'}],
         'email': [{'detected': 'abcdef@abcd.com',
                    'index': 25,
                    'sub_type': 'Regular',
                    'text': 'abcdef@abcd.com'}],
         'ipa': [{'index': 65, 'sub_type': 'IPV4', 'text': '255.255.255.255'}],
         'phone': [{'country_code': 'US', 'index': 49, 'text': '6657789887'},
                   {'country_code': 'US', 'index': 177, 'text': '3144444444'}]},
 'status': {'code': 3000, 'description': 'OK'},
 'terms': [{'index': 118, 'list_id': 0, 'original_index': 118, 'term': 'crap'}],
 'tracking_id': 'b253515c-e713-4316-a016-8397662a3f1a'}
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已開發一個簡單的 Python 指令碼，以使用 Content Moderator 服務來傳回有關指定的文字範例的相關資訊。 接下來請深入了解不同旗標和分類的意義，以便決定您所需的資料和應用程式應如何加以處理。

> [!div class="nextstepaction"]
> [文字仲裁指南](text-moderation-api.md)
