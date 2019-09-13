---
title: 如何使用 Azure 地圖服務搜尋服務有效率地搜尋 |Microsoft Docs
description: 瞭解如何使用 Azure 地圖服務搜尋服務來進行搜尋的最佳做法
author: walsehgal
ms.author: v-musehg
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 25615ae8bc9bc8cadbe973f3a1859c2d43b067a9
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915568"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>使用 Azure 地圖服務搜尋服務的最佳做法

Azure 地圖服務[搜尋服務](https://docs.microsoft.com/rest/api/maps/search)包含具有各種功能的 api，例如，從位址搜尋，到搜尋特定位置的相關點（POI）資料。 在本文中，我們將分享透過 Azure 地圖服務搜尋服務來呼叫資料的最佳作法。 您將了解如何：

* 建立查詢以傳回相關的相符專案
* 限制搜尋結果
* 瞭解各種結果類型之間的差異
* 讀取位址搜尋回應結構


## <a name="prerequisites"></a>先決條件

若要對地圖服務 API 進行呼叫，您需要有地圖服務帳戶和金鑰。 如需建立帳戶的相關資訊，請遵循[管理帳戶](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account)中的指示，然後依照[取得主要金鑰](./tutorial-search-location.md#getkey)中的步驟來抓取您帳戶的主要訂用帳戶金鑰。

> [!Tip]
> 若要查詢搜尋服務，您可以使用[Postman 應用程式](https://www.getpostman.com/apps)來建立 REST 呼叫，也可以使用您偏好的任何 API 開發環境。


## <a name="best-practices-for-geocoding"></a>地理編碼的最佳作法

當您使用 Azure 地圖服務搜尋服務搜尋完整或部分位址時，它會接受您的搜尋字詞，並傳回位址的經度和緯度座標。 此程式稱為地理編碼。 在國家/地區中地理編碼的能力仰賴道路資料涵蓋範圍，以及地理編碼服務的地理編碼精確度。

請參閱[地理編碼涵蓋範圍](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage)，以深入瞭解每個國家/地區的 Azure 地圖服務地理編碼功能。

### <a name="limit-search-results"></a>限制搜尋結果

   在本節中，您將瞭解如何使用 Azure 地圖服務搜尋 Api 來限制搜尋結果。 

   > [!Note]
   > 並非所有搜尋 Api 完全支援下列參數

   **地理偏差搜尋結果**

   為了將您的結果地理偏差到使用者的相關區域，您應該一律新增最多可能的詳細位置輸入。 若要限制搜尋結果，請考慮新增下列輸入類型：

   1. `countrySet`設定參數，例如 "US，FR"。 預設搜尋行為是搜尋整個世界，可能會傳回不必要的結果。 如果您的查詢不包含`countrySet`參數，搜尋可能會傳回不正確的結果。 例如，搜尋名為**Bellevue**的城市會傳回美國和法國的結果，因為法國和美國的城市名為**Bellevue** 。

   2. 您可以使用`btmRight`和`topleft`參數來設定周框方塊，將搜尋限制在地圖上的特定區域。

   3. 若要影響結果的相關性區域，您可以定義`lat`和`lon`座標參數，並使用`radius`參數來設定搜尋區域的半徑。


   **模糊搜尋參數**

   1. 即使`minFuzzyLevel`查詢`maxFuzzyLevel`參數並未完全對應到所需的資訊，和也有助於傳回相關的相符專案。 大部分的搜尋查詢預設`minFuzzyLevel=1`為`maxFuzzyLevel=2`和，以取得效能並減少不尋常的結果。 當設定為2時`maxFuzzyLevel` ，請使用搜尋詞彙 "restrant" 的範例，使其符合「餐廳」。 根據要求的需求，可以覆寫預設的模糊層級。 

   2. 您也可以使用`idxSet`參數來指定要傳回的一組確切結果類型。 基於此目的，您可以提交以逗號分隔的索引清單，專案順序並不重要。 以下是支援的索引：

       * `Addr` - **位址範圍**：對於某些街道，會有從街道開頭和結尾處插補的位址點;這些點會以位址範圍表示。
       * `Geo` - **地理**位置：地圖上的區域，代表土地的管理部門，也就是國家/地區、州/省。
       * `PAD` - **點位址**：在地圖上的點上，可以在索引中找到具有街道名稱和數位的特定位址，例如 Soquel Dr 2501。 這是位址可用的最高精確度層級。  
       * `POI` - **重點：** 地圖上值得注意的點，而且可能會很有趣。  [取得搜尋位址](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)不會傳回 poi。  
       * `Str` - **街道**：地圖上的街道標記法。
       * `XStr` - **交叉街道/交集**：聯接的標記法;兩個街道相交的位置。


       **使用範例**：

       * idxSet = POI （僅限搜尋點） 

       * idxSet = PAD，Addr （僅搜尋位址，PAD = 點位址，位址 = 位址範圍）

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>反向地理編碼和 geography 實體類型篩選

使用[搜尋位址反向 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)執行反向地理編碼搜尋時，服務可以傳回系統管理區域的多邊形。 藉由在要求`entityType`中提供參數，您可以縮小指定 geography 實體類型的搜尋範圍。 產生的回應將包含地理位置識別碼，以及符合的實體類型。 如果您提供一個以上的實體，則端點會傳回**可用的最小實體**。 傳回的 Geometry ID 可以透過[取得多邊形服務](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)，用來取得該地理位置的幾何。

**範例要求：**

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

**回應：**

```JSON
{
    "summary": {
        "queryTime": 8,
        "numResults": 1
    },
    "addresses": [
        {
            "address": {
                "routeNumbers": [],
                "countryCode": "US",
                "countrySubdivision": "WA",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "municipality": "Redmond",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Redmond, WA",
                "boundingBox": {
                    "northEast": "47.717105,-122.034537",
                    "southWest": "47.627016,-122.164998",
                    "entity": "position"
                },
                "countrySubdivisionName": "Washington"
            },
            "position": "47.639454,-122.130455",
            "dataSources": {
                "geometry": {
                    "id": "00005557-4100-3c00-0000-0000596ae571"
                }
            },
            "entityType": "Municipality"
        }
    ]
}
```

### <a name="search-results-language"></a>搜尋結果語言

`language`參數可讓您設定要在哪種語言搜尋結果中傳回。 如果未在要求中設定語言，搜尋服務會自動預設為國家/地區中最常見的語言。 此外，當指定語言中的資料無法使用時，會使用預設語言。 如需依國家/地區 Azure 地圖服務服務的支援語言清單，請參閱[支援的語言](https://docs.microsoft.com/azure/azure-maps/supported-languages)。


### <a name="predictive-mode-auto-suggest"></a>預測模式（自動建議）

若要尋找部分查詢的更多`typeahead`相符專案，參數應該設定為 ' true '。 查詢將會被視為部分輸入，而且搜尋會進入預測模式。 否則，服務會假設所有相關資訊都已經傳入。

在下面的範例查詢中，您可以看到搜尋位址服務已查詢 "Microso"，並`typeahead`將參數設定為**true**。 如果您觀察到回應，您可以看到搜尋服務將查詢轉譯為部分查詢，而回應包含自動建議查詢的結果。

**範例查詢︰**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

**回應：**

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 25,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/10294417",
            "score": 2.594,
            "dist": 327.546040632591,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980526399,980528300",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63989,
                "lon": -122.12509
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309
                },
                "btmRightPoint": {
                    "lat": 47.64223,
                    "lon": -122.13061
                }
            }
        },
        ...,
        ...,
        ...,
        ...,
        {
            "type": "Street",
            "id": "US/STR/p0/9063400",
            "score": 2.075,
            "dist": 3655467.6406921702,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "postalCode": "28217",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28217",
                "countrySubdivisionName": "North Carolina"
            },
            "position": {
                "lat": 35.14279,
                "lon": -80.91814
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814
                },
                "btmRightPoint": {
                    "lat": 35.14279,
                    "lon": -80.91824
                }
            }
        }
    ]
}
```


### <a name="uri-encoding-to-handle-special-characters"></a>用來處理特殊字元的 URI 編碼 

若要尋找交叉街道位址，也就是「第1個 & 聯集街道、西雅圖」、特殊字元 ' & ' 必須在傳送要求之前進行編碼。 我們建議您在 URI 中編碼字元資料，其中所有字元都會使用 '% ' 字元和對應到 UTF-8 字元的兩個字元十六進位值進行編碼。

**使用範例**：

取得搜尋位址：

```
query=1st Avenue & E 111th St, New York
```

 應編碼為：

```
query"=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```


以下是不同語言所使用的不同方法： 

JavaScript/TypeScript：
```Javascript
encodeURIComponent(query)
```

C#VB
```csharp
Uri.EscapeDataString(query)
```

Java：
```Java
URLEncoder.encode(query, "UTF-8") 
```

Python：
```Python
import urllib.parse 
urllib.parse.quote(query)
```

C++：
```C++
#include <curl/curl.h>
curl_easy_escape(query)
```

PHP：
```PHP
urlencode(query)
```

加注
```Ruby
CGI::escape(query) 
```

快速
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

消失
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-search"></a>POI 搜尋的最佳做法

感點數（POI）搜尋可讓您依名稱要求 POI 結果，例如依名稱搜尋業務。 我們強烈建議您使用`countrySet`參數來指定應用程式需要涵蓋範圍的國家/地區，因為預設行為是搜尋整個世界，可能會傳回不必要的結果，並（或）導致較長的搜尋時間。

### <a name="brand-search"></a>品牌搜尋

為了改善結果的相關性和回應中的資訊，「感興趣」（POI）搜尋回應包含可進一步用來剖析回應的品牌資訊。

讓我們對 Microsoft 校園附近的天然氣站（Redmond，WA）進行[POI 類別搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory)要求。 如果您觀察到回應，您可以看到每個傳回之 POI 的品牌資訊。

**範例查詢︰**

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

**回應：**

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 206,
        "numResults": 3,
        "offset": 0,
        "totalResults": 742169,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/245813",
            "score": 5.663,
            "dist": 1037.0280221303253,
            "info": "search:ta:840531000004190-US",
            "poi": {
                "name": "Chevron",
                "phone": "+(1)-(425)-6532200",
                "brands": [
                    {
                        "name": "Chevron"
                    }
                ],
                "url": "www.chevron.com",
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2444",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Northeast Bellevue, Bellevue",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "2444 Bel Red Rd, Bellevue, WA 98007",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63223,
                        "lon": -122.13311
                    }
                }
            ]
        },
        ...,
        {
            "type": "POI",
            "id": "US/POI/p0/7727106",
            "score": 5.662,
            "dist": 1458.645407416307,
            "info": "search:ta:840539000488527-US",
            "poi": {
                "name": "BROWN BEAR CAR WASH",
                "phone": "+(1)-(425)-6442868",
                "brands": [
                    {
                        "name": "Texaco"
                    }
                ],
                "url": "www.texaco.com/",
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "15248",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980525511",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "15248 Bel Red Rd, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62826,
                        "lon": -122.13626
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>機場搜尋

POI 搜尋支援使用官方機場代碼來搜尋機場。 例如，**海運**（西雅圖 Tacoma 國際機場）。 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>鄰近搜尋

若只要取得特定位置的 POI 結果，附近的[搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby)可能是正確的選擇。 此端點只會傳回 POI 的結果，並不會接受搜尋查詢參數。 若要限制結果，建議您設定 radius。

## <a name="understanding-the-responses"></a>瞭解回應

讓我們對 Azure 地圖服務[搜尋服務](https://docs.microsoft.com/rest/api/maps/search)提出位址搜尋要求，以取得位於西雅圖的位址。 如果您仔細看下面的 [要求 URL]，我們已將`countrySet`參數設定為 [ **US** ]，以搜尋美國地區的位址。

**範例查詢︰**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

接下來讓我們看一下下面的回應結構。 回應中結果物件的結果類型不同。 如果您仔細觀察，可以看到我們有三種不同類型的結果物件，也就是「點位址」、「街道」和「交叉街道」。 請注意，[位址搜尋] 不會傳回 Poi。 每`Score`個回應物件的參數會指示相同回應中其他物件的分數相對符合分數。 若要深入瞭解回應物件參數，請參閱[取得搜尋位址](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)。

**支援的結果類型：**

* **點位址：** 在地圖上指向具有街道名稱和數位的特定位址。 位址可用的最高精確度層級。 

* **位址範圍：** 對於某些街道，會有從街道開頭和結尾處插補的位址點;這些點會以位址範圍表示。 

* **區域**地圖上的區域，代表土地的管理部門，也就是國家/地區、州/省。 

* **POI-（感重點）：** 地圖上值得注意的點，而且可能會很有趣。

* **-** 地圖上的街道標記法。 位址會解析為包含位址之街道的緯度/經度座標。 可能不會處理門牌號碼。 

* **交叉街道：** 交集. 聯接的標記法;兩個街道相交的位置。

**回應：**

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 129,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/43076024",
            "score": 9.894,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, South Lake Union, Lower Queen Anne",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "400 Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/2440854",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Westlake, South Lake Union",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "extendedPostalCode": "981094347,981094700,981094701,981094702",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62553,
                "lon": -122.33936
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62545,
                    "lon": -122.33861
                },
                "btmRightPoint": {
                    "lat": 47.62574,
                    "lon": -122.33974
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8450985",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109,98121",
                "extendedPostalCode": "981094991,981211117,981211237,981213206",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.61691,
                "lon": -122.35251
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61502,
                    "lon": -122.35041
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.35484
                }
            }
        },
        ...,
        ...,
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/3816818",
            "score": 6.759,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street & Valley Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62574,
                "lon": -122.33861
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62664,
                    "lon": -122.33994
                },
                "btmRightPoint": {
                    "lat": 47.62484,
                    "lon": -122.33728
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>幾何

當回應類型是**geometry**時，它可以包含在 "geometry" 和 "ID" 下的資料**源**物件中傳回的 geometry 識別碼。 例如，「[取得多邊形服務](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)」可讓您要求 GeoJSON 格式的幾何資料，例如一組實體的城市或機場外框。 您可以將此界限資料用於幾何內的[地理柵欄](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) 或[搜尋 poi](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)。


[搜尋位址](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)或[搜尋模糊](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)API 回應可以包含在「geometry」和「識別碼」下的資料來源物件中傳回的**幾何識別碼**。


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>後續步驟

* 瞭解[如何建立 Azure 地圖服務搜尋服務要求](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)。
* 探索 Azure 地圖服務[搜尋服務 API 檔](https://docs.microsoft.com/rest/api/maps/search)。 
