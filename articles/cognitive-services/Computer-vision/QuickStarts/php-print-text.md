---
title: 電腦視覺 API PHP 快速入門 OCR | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 在本快速入門中，您會在認知服務中使用電腦視覺與 PHP，擷取影像中的印刷文字。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 85df021357c76330be21ceff77d79491edcc23b0
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2018
ms.locfileid: "43750353"
---
# <a name="quickstart-extract-printed-text-ocr---rest-php"></a>快速入門：擷取印刷文字 (OCR) - REST、PHP

在本快速入門中，您會使用「電腦視覺」來擷取影像中的印刷文字，也稱為光學字元辨識 (OCR)。

## <a name="prerequisites"></a>必要條件

若要使用「電腦視覺」，您需要訂用帳戶金鑰，請參閱[取得訂用帳戶金鑰](../Vision-API-How-to-Topics/HowToSubscribe.md)。

## <a name="ocr-request"></a>OCR 要求

您可以使用 [OCR 方法](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) (英文)，偵測影像中的印刷文字，然後將辨識出的字元擷取到電腦可使用的字元資料流中。

若要執行範例，請執行下列步驟：

1. 請將下列程式碼複製到編輯器中。
1. 將 `<Subscription Key>` 取代為您的有效訂用帳戶金鑰。
1. 必要時，請將 `uriBase` 變更為使用您取得訂用帳戶金鑰的位置。
1. (選擇性) 將 `imageUrl` 設為您想要分析的影像。
1. 以 `.php` 副檔名儲存檔案。
1. 在具有 PHP 支援的瀏覽器視窗中開啟檔案。

這個範例會使用 PHP5 [HTTP_Request2](http://pear.php.net/package/HTTP_Request2) 套件。

```php
<?php
<html>
<head>
    <title>OCR Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

$imageUrl = 'https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/' .
    'Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . 'ocr');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'language' => 'unk',
    'detectOrientation' => 'true'
);
$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body parameters
$body = json_encode(array('url' => $imageUrl));

// Request body
$request->setBody($body);

try
{
    $response = $request->send();
    echo "<pre>" .
        json_encode(json_decode($response->getBody()), JSON_PRETTY_PRINT) . "</pre>";
}
catch (HttpException $ex)
{
    echo "<pre>" . $ex . "</pre>";
}
?>
</body>
</html>
```

## <a name="ocr-response"></a>OCR 回應

成功時，傳回的 OCR 結果會包含區域、線條和字組的文字、週框方塊，例如：

```json
{
    "language": "en",
    "orientation": "Up",
    "textAngle": 0,
    "regions": [
        {
            "boundingBox": "21,16,304,451",
            "lines": [
                {
                    "boundingBox": "28,16,288,41",
                    "words": [
                        {
                            "boundingBox": "28,16,288,41",
                            "text": "NOTHING"
                        }
                    ]
                },
                {
                    "boundingBox": "27,66,283,52",
                    "words": [
                        {
                            "boundingBox": "27,66,283,52",
                            "text": "EXISTS"
                        }
                    ]
                },
                {
                    "boundingBox": "27,128,292,49",
                    "words": [
                        {
                            "boundingBox": "27,128,292,49",
                            "text": "EXCEPT"
                        }
                    ]
                },
                {
                    "boundingBox": "24,188,292,54",
                    "words": [
                        {
                            "boundingBox": "24,188,292,54",
                            "text": "ATOMS"
                        }
                    ]
                },
                {
                    "boundingBox": "22,253,297,32",
                    "words": [
                        {
                            "boundingBox": "22,253,105,32",
                            "text": "AND"
                        },
                        {
                            "boundingBox": "144,253,175,32",
                            "text": "EMPTY"
                        }
                    ]
                },
                {
                    "boundingBox": "21,298,304,60",
                    "words": [
                        {
                            "boundingBox": "21,298,304,60",
                            "text": "SPACE."
                        }
                    ]
                },
                {
                    "boundingBox": "26,387,294,37",
                    "words": [
                        {
                            "boundingBox": "26,387,210,37",
                            "text": "Everything"
                        },
                        {
                            "boundingBox": "249,389,71,27",
                            "text": "else"
                        }
                    ]
                },
                {
                    "boundingBox": "127,431,198,36",
                    "words": [
                        {
                            "boundingBox": "127,431,31,29",
                            "text": "is"
                        },
                        {
                            "boundingBox": "172,431,153,36",
                            "text": "opinion."
                        }
                    ]
                }
            ]
        }
    ]
}
```

## <a name="next-steps"></a>後續步驟

探索用來分析影像、偵測名人和地標、建立縮圖及擷取印刷和手寫文字的「電腦視覺 API」。 若要快速地試驗「電腦視覺 API」，請嘗試 [Open API 測試主控台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console) (英文)。

> [!div class="nextstepaction"]
> [探索電腦視覺 API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44) (英文)
