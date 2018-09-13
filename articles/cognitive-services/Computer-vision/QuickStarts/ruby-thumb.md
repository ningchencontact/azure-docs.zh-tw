---
title: 電腦視覺 API Ruby 快速入門 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 在本快速入門中，您會在認知服務中使用電腦視覺與 Ruby，從影像產生縮圖。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: a5f6c345b3e1f5e4dab923d43dd239344c66aab1
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2018
ms.locfileid: "43750376"
---
# <a name="quickstart-generate-a-thumbnail---rest-ruby"></a>快速入門：產生縮圖 - REST、Ruby

在本快速入門中，您會使用「電腦視覺」從影像產生縮圖。

## <a name="prerequisites"></a>必要條件

若要使用「電腦視覺」，您需要訂用帳戶金鑰，請參閱[取得訂用帳戶金鑰](../Vision-API-How-to-Topics/HowToSubscribe.md)。

## <a name="get-thumbnail-request"></a>取得縮圖要求

您可以使用 [Get Thumbnail 方法](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) (英文)，產生影像的縮圖。 您指定高度和寬度，可能與輸入影像的外觀比例不同。 電腦視覺會使用智慧型裁剪以智慧方式識別感興趣的區域，並且根據該區域產生裁剪的座標。

若要執行範例，請執行下列步驟：

1. 請將下列程式碼複製到編輯器中。
1. 將 `<Subscription Key>` 取代為您的有效訂用帳戶金鑰。
1. 必要時，請將 `uri` 值變更為您取得訂用帳戶金鑰的位置。
1. (選擇性) 變更要分析的影像 (`{\"url\":\"...`)。
1. 以 `.rb` 副檔名儲存檔案。
1. 開啟 Ruby 命令提示字元並執行檔案，例如：`ruby myfile.rb`。

```ruby
require 'net/http'

# You must use the same location in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from westus,
# replace "westcentralus" in the URL below with "westus".
uri = URI('https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail')
uri.query = URI.encode_www_form({
    # Request parameters
    'width' => '100',
    'height' => '100',
    'smartCropping' => 'true'
})

request = Net::HTTP::Post.new(uri.request_uri)

# Request headers
# Replace <Subscription Key> with your valid subscription key.
request['Ocp-Apim-Subscription-Key'] = '<Subscription Key>'
request['Content-Type'] = 'application/json'

request.body =
    "{\"url\": \"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/" +
    "Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end

#puts response.body
```

## <a name="get-thumbnail-response"></a>Get Thumbnail 回應

成功的回應會包含縮圖影像二進位檔。 如果要求失敗，回應會包含錯誤碼和訊息以協助判斷問題所在。

## <a name="next-steps"></a>後續步驟

探索用來分析影像、偵測名人和地標、建立縮圖及擷取印刷和手寫文字的「電腦視覺 API」。 若要快速地試驗「電腦視覺 API」，請嘗試 [Open API 測試主控台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console) (英文)。

> [!div class="nextstepaction"]
> [探索電腦視覺 API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44) (英文)
