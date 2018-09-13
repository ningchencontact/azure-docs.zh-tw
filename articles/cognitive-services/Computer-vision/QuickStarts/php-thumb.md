---
title: 電腦視覺 API PHP 快速入門建立縮圖 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 在本快速入門中，您會在認知服務中使用電腦視覺與 PHP，從影像產生縮圖。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 7170e469d042c4406a555fddaa25bff53236f365
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2018
ms.locfileid: "43750616"
---
# <a name="quickstart-generate-a-thumbnail---rest-php"></a>快速入門：產生縮圖 - REST、PHP

在本快速入門中，您會使用「電腦視覺」從影像產生縮圖。

## <a name="prerequisites"></a>必要條件

若要使用「電腦視覺」，您需要訂用帳戶金鑰，請參閱[取得訂用帳戶金鑰](../Vision-API-How-to-Topics/HowToSubscribe.md)。

## <a name="get-thumbnail-request"></a>取得縮圖要求

您可以使用 [Get Thumbnail 方法](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) (英文)，產生影像的縮圖。 您指定高度和寬度，可能與輸入影像的外觀比例不同。 電腦視覺會使用智慧型裁剪以智慧方式識別感興趣的區域，並且根據該區域產生裁剪的座標。

若要執行範例，請執行下列步驟：

1. 請將下列程式碼複製到編輯器中。
1. 將 `<Subscription Key>` 取代為您的有效訂用帳戶金鑰。
1. 必要時，請將 `uriBase` 變更為使用您取得訂用帳戶金鑰的位置。
1. (選擇性) 將 `imageUrl` 設為您想要分析的影像。
1. 以 `.php` 副檔名儲存檔案。
1. 在具有 PHP 支援的瀏覽器視窗中開啟檔案。

這個範例會使用 PHP5 [HTTP_Request2](http://pear.php.net/package/HTTP_Request2) 套件。

```php
<html>
<head>
    <title>Get Thumbnail Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

$imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . 'generateThumbnail');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'width' => '100',  // Width of the thumbnail.
    'height' => '100', // Height of the thumbnail.
    'smartCropping' => 'true',
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

## <a name="get-thumbnail-response"></a>Get Thumbnail 回應

成功的回應會包含縮圖影像二進位檔。 如果要求失敗，回應會包含錯誤碼和訊息以協助判斷問題所在。

## <a name="next-steps"></a>後續步驟

探索用來分析影像、偵測名人和地標、建立縮圖及擷取印刷和手寫文字的「電腦視覺 API」。 若要快速地試驗「電腦視覺 API」，請嘗試 [Open API 測試主控台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console) (英文)。

> [!div class="nextstepaction"]
> [探索電腦視覺 API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44) (英文)
