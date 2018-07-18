---
title: Bing 影像上傳以取得見解 | Microsoft Docs
description: 使用 Bing 影像搜尋 API 來上傳影像並取得影像見解的主控台應用程式。
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 12/07/2017
ms.author: v-gedod
ms.openlocfilehash: f0bf32a9951527a072fffe464f6b5f50d0f237a2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35367894"
---
# <a name="tutorial-bing-image-upload-for-insights"></a>教學課程：Bing 影像上傳以取得見解

Bing 影像搜尋 API 提供 `POST` 選項，以便上傳影像並搜尋影像相關資訊。 此 C# 主控台應用程式會使用影像搜尋端點來上傳影像，以取得影像的相關詳細資訊。
簡而言之，結果就是 JSON 物件，如下所示：

![[JSON 結果]](media/cognitive-services-bing-images-api/jsonResult.jpg)

本教學課程說明如何：

> [!div class="checklist"]
> * 在 `POST` 要求中使用影像搜尋 `/details` 端點
> * 指定要求的標頭
> * 使用 URL 參數來指定結果
> * 上傳影像資料並傳送 `POST` 要求
> * 將 JSON 結果列印到主控台

## <a name="app-components"></a>應用程式元件

本教學課程應用程式包含三個部分：

> [!div class="checklist"]
> * 端點組態，用以指定 Bing 影像搜尋端點和必要的標頭
> * 針對 `POST` 要求將影像檔案上傳至端點
> * 剖析 JSON 結果，這些結果是從 `POST` 要求傳回的詳細資料

## <a name="scenario-overview"></a>案例概觀
有[三個影像搜尋端點](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-endpoint)。 `/details` 端點可以使用 `POST` 要求，而要求本文中有影像資料。
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
緊接在 `/details?` 後面的 `modules` URL 參數指定結果包含何種詳細資料：
* `modules=All`
* `modules=RecognizedEntities` (影像中可見的人員或地點)

在 `POST` 要求中指定 `modules=All`，可取得包含下列清單的 JSON 文字：
* `bestRepresentativeQuery` - Bing 查詢，其傳回的影像類似於所上傳的影像
* `detectedObjects`，例如影像中的週框方塊或作用點
* `image` 中繼資料
* `imageInsightsToken` - 後續 `GET` 要求的權杖，用以取得 `RecognizedEntities` (影像中可見的人員或地點) 
* `imageTags`
* `pagesIncluding` - 包含影像的網頁
* `relatedSearches`
* `visuallySimilarImages`

在 `POST` 要求中指定 `modules=RecognizedEntities` 只可取得 `imageInsightsToken`，其使用於後續的 `GET` 要求中。 它可識別影像中可見的人員或地點。

## <a name="webclient-and-headers-for-the-post-request"></a>POST 要求的 WebClient 和標頭
建立 `WebClient` 物件，並設定標頭。 Bing 搜尋 API 的所有要求都需要 `Ocp-Apim-Subscription-Key`。 上傳影像的 `POST` 要求也必須指定 `ContentType: multipart/form-data`。

```
            WebClient client = new WebClient();
            client.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            client.Headers["ContentType"] = "multipart/form-data"; 
```

## <a name="upload-the-image-and-get-results"></a>上傳影像並取得結果

`WebClient` 類別包含 `UpLoadFile` 方法，該方法已針對 `POST` 要求將資料格式化。 它會將 `RequestStream` 格式化並呼叫 `HttpWebRequest`，以免非常複雜。
利用 `/details` 端點和要上傳的影像檔呼叫 `WebClient.UpLoadFile`。 回應是輕鬆轉換為 JSON 的二進位資料。 

使用 JSON 文字來初始化 `SearchResult` 結構的執行個體 (請參閱[應用程式原始程式碼](tutorial-image-post-source.md)中的內容)。
```        
         const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/details";

        // The image to upload. Replace with your file and path.
        const string imageFile = "ansel-adams-tetons-snake-river.jpg";
            
        byte[] resp = client.UploadFile(uriBase + "?modules=All", imageFile);
        var json = System.Text.Encoding.Default.GetString(resp);

        // Create result object for return
        var searchResult = new SearchResult()
        {
            jsonResult = json,
            relevantHeaders = new Dictionary<String, String>()
        };
```

## <a name="print-the-results"></a>列印結果
其餘的程式碼會剖析 JSON 結果並列印到主控台。

```
        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
            }

            return sb.ToString().Trim();
        }
```
## <a name="get-request-using-the-imageinsightstoken"></a>使用 ImageInsightsToken 的 GET 要求
若要使用隨著 `POST` 結果一起傳回的 `ImageInsightsToken`，請建立 `GET` 要求，如下所示：
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details?InsightsToken="bcid_A2C4BB81AA2C9EF8E049C5933C546449*ccid_osS7gaos*mid_BF7CC4FC4A882A3C3D56E644685BFF7B8BACEAF2
```
如果影像中有可辨識的人員或地點，此要求會傳回其相關資訊。
[快速入門](https://docs.microsoft.com/azure/cognitive-services/bing-image-search)包含數個程式碼範例。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Bing 影像搜尋 API 參考](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)

