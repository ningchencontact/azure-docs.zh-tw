---
title: 如何搜尋有效率地使用 Azure 地圖服務搜尋服務 |Microsoft Docs
description: 了解如何使用搜尋服務中使用 Azure 地圖服務搜尋服務的最佳作法
ms.author: v-musehg
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 9cb0f89b4a48d7139adb35dcef48c0115b005c57
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65205613"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>若要使用 Azure 地圖服務搜尋服務的最佳作法

Azure 地圖服務[搜尋服務](https://docs.microsoft.com/rest/api/maps/search)包括 Api 各種功能，例如的地址搜尋來搜尋特定的位置周圍的感興趣點 (POI) 資料。 在本文中，我們不會共用可透過 Azure 地圖服務搜尋服務資料的最佳作法。 您將了解如何：

* 建置查詢，傳回相關的相符項目
* 限制搜尋結果
* 了解各種結果型別之間的差異
* 讀取的地址搜尋回應結構


## <a name="prerequisites"></a>必要條件

若要對地圖服務 API 進行呼叫，您需要有地圖服務帳戶和金鑰。 如需建立帳戶和擷取金鑰的相關資訊，請參閱[如何管理 Azure 地圖服務帳戶和金鑰](how-to-manage-account-keys.md)。

> [!Tip]
> 若要查詢的搜尋服務，您可以使用[Postman 應用程式](https://www.getpostman.com/apps)來建置 REST 呼叫，或者您可以使用任何您偏好的 API 開發環境。


## <a name="best-practices-for-geocoding"></a>地理編碼的最佳作法

當您搜尋完整或部分的地址，使用 Azure 地圖服務搜尋服務時，它會接受您的搜尋詞彙，並傳回位址的經度和緯度座標。 此程序稱為地理編碼。 在國家/地區中地理編碼的能力仰賴道路資料涵蓋範圍，以及地理編碼服務的地理編碼精確度。

請參閱[地理編碼涵蓋範圍](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage)要深入了解 Azure 地圖服務地理編碼功能依國家/地區。

### <a name="limit-search-results"></a>限制搜尋結果

   在本節中，您將學習如何使用 Azure 地圖服務搜尋服務 Api 來限制搜尋結果。 

   > [!Note]
   > 並非所有的搜尋服務 Api 完全支援下面所列的參數

   **地理偏差搜尋結果**

   地理偏差才能將結果與相關的區域，為您的使用者，您應該一律加入詳細的最大的可能位置的輸入。 若要限制搜尋結果，請考慮加入下列的輸入的類型：

   1. 設定`countrySet`參數，例如"US，FR"。 預設的搜尋行為是搜尋全世界，可能會傳回不必要的結果。 如果您的查詢不包含`countrySet`參數，搜尋可能會傳回不正確的結果。 例如，搜尋名為 city **Bellevue**將從美國和法國，傳回的結果，因為沒有名為的城市**Bellevue**美國和法國中。

   2. 您可以使用`btmRight`和`topleft`參數來設定的周框方塊以將搜尋限制到特定區域在地圖上。

   3. 若要影響的區域相關的結果，您可以定義`lat`並`lon`協調參數及設定搜尋區域使用的半徑`radius`參數。


   **模糊搜尋參數**

   1. `minFuzzyLevel`和`maxFuzzyLevel`，幫助傳回相關的相符項目，即使查詢參數並不完全對應到所需的資訊。 大部分的搜尋查詢預設為`minFuzzyLevel=1`和`maxFuzzyLevel=2`以取得效能並減少不尋常的結果。 搜尋詞彙的範例 「 restrant"，當符合 「 餐廳 」 到`maxFuzzyLevel`設為 2。 可覆寫預設的模糊層級，根據要求的需求。 

   2. 您也可以指定結果型別，若要使用傳回一組正確`idxSet`參數。 因此您可以送出索引的逗號分隔的清單，其項目順序並不重要。 以下是支援的索引：

       * `Addr` - **位址範圍**:某些街道有開頭和結尾 street; 插補的地址點這些點會表示為位址範圍。
       * `Geo` - **地理位置**:表示行政區 land，也就是對應、 國家/地區、 州、 縣 （市） 上的區域。
       * `PAD` - **點地址**:特定的地址的街道名稱與數字可以找到在索引中，例如 Soquel Dr 2501 在地圖上的點。 它是精確度的最高層級的可用位址。  
       * `POI` - **感興趣的點**:在地圖上的點值得注意且可能會感興趣。  [取得搜尋地址](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)不會傳回 Poi。  
       * `Str` - **街道**:街道地圖表示法。
       * `XStr` - **交叉街道/交集**:連接; 表示法兩個街道相交所在的位置。


       **使用範例**:

       * idxSet = POI （只搜尋感興趣的點為單位） 

       * idxSet = 板、 Addr (搜尋，解決板 = 點地址，Addr = 位址範圍)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>反轉地理編碼和地理位置的實體型別篩選條件

執行與反向地理代碼搜尋時[搜尋地址反向 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)，服務必須能夠傳回的系統管理區域的多邊形。 藉由提供參數`entityType`在要求中，您可以縮小搜尋指定的地理位置的實體類型。 產生的回應會包含地理位置 ID，以及比對的實體類型。 如果您提供多個實體，端點會傳回**可用的最小實體**。 傳回幾何識別碼可用來取得透過該地理位置的幾何[取得多邊形服務](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)。

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

### <a name="search-results-language"></a>搜尋結果的語言

`language`參數可讓您設定哪些語言搜尋服務中不應傳回結果。 如果在要求中未設定的語言，搜尋服務就會自動預設為最常用的語言，在 國家/地區中。 此外，無法使用指定的語言中的資料時，會使用預設語言。 請參閱[支援的語言](https://docs.microsoft.com/azure/azure-maps/supported-languages)如需依國家/地區的 Azure Maps 服務方面的支援語言的清單。


### <a name="predictive-mode-auto-suggest"></a>預測的模式 （自動建議）

若要尋找更多符合項目部分的查詢，`typeahead`參數應該設定為 'true'。 查詢會視為部分輸入，並搜尋會在輸入預測的模式。 否則，服務會假設傳入的所有相關資訊。

在此範例查詢低於您所見"F"，便會查詢搜尋地址服務與`typeahead`參數設定為 **，則為 true**。 如果您發現回應時，您可以看到搜尋服務會解譯為部分查詢的查詢，並回應包含自動建議查詢的結果。

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


### <a name="uri-encoding-to-handle-special-characters"></a>若要處理特殊字元編碼的 URI 

若要交叉街道地址，也就是 「 1 街 & Union Street，Seattle"、 特殊字元編碼傳送要求之前的 '&' 需求。 我們建議編碼字元資料中的 URI，其中所有的字元使用 '%' 字元編碼和兩個字元的十六進位值，對應至其 UTF-8 字元集。

**使用範例**:

取得搜尋地址：

```
query=1st Avenue & E 111th St, New York
```

 應該會編碼為：

```
query"=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```


以下是不同的方法，可以使用不同的語言： 

JavaScript/TypeScript:
```Javascript
encodeURIComponent(query)
```

C#/ VB:
```C#
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

Ruby:
```Ruby
CGI::escape(query) 
```

Swift:
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

移至：
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-search"></a>POI 搜尋服務的最佳作法

點感興趣 (POI) 搜尋可讓您依名稱，例如，依名稱搜尋企業要求 POI 結果。 我們強烈建議您將`countrySet`參數來指定您的應用程式需要涵蓋範圍的位置的國家/地區，因為預設行為會搜尋整個世界中，可能會傳回不必要的結果及/或導致較長搜尋時間。

### <a name="brand-search"></a>品牌搜尋

若要改善結果並在回應中的資訊的相關性的感興趣點 (POI) 搜尋回應會包含可以進一步用來剖析回應的品牌資訊。

讓我們[POI 分類搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory)加油站附近 Microsoft 園區 (Redmond，WA) 的要求。 如果您發現回應時，您可以看到傳回的每個 POI 的商標資訊。

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

POI 搜尋服務支援使用官方的機場代碼搜尋機場。 例如，**海**(西雅圖 Tacoma International Airport)。 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>鄰近搜尋

若要擷取只 POI 結果周圍的特定位置，[附近的搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby)可能是正確的選擇。 此端點只會傳回 POI 的結果，並不會在搜尋查詢參數。 若要限制結果，建議設定 radius。

## <a name="understanding-the-responses"></a>了解回應

讓我們將地址搜尋要求對 Azure Maps [search 服務](https://docs.microsoft.com/rest/api/maps/search)西雅圖地址。 如果您仔細看看下列的要求 URL，我們已設定`countrySet`參數來**美國**搜尋在美國地址。

**範例查詢︰**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

進一步讓我們看看下面的回應結構。 在回應中將結果物件的結果類型會不同。 如果您仔細觀察您可以看到我們有三種不同的結果物件，，還有"點 Address"、"Street"，"跨 Street"。 請注意，地址搜尋不會傳回 Poi。 `Score`針對每個回應物件的參數表示相同的回應中的其他物件的相對的符合分數。 請參閱[取得搜尋地址](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)要深入了解回應物件參數。

**支援的結果類型：**

* **端點位址：** 使用特定的地址的街道名稱與數字在地圖上的點。 最高層級的可用位址的精確度。 

* **位址範圍：** 某些街道有開頭和結尾 street; 插補的地址點這些點會表示為位址範圍。 

* **地理位置：** 表示行政區 land，也就是對應、 國家/地區、 州、 縣 （市） 上的區域。 

* **POI-（感興趣的點）：** 在地圖上的點值得注意且可能會感興趣。

* **Street:** 街道地圖表示法。 會解析成的緯度/經度座標，包含的地址的街道地址。 可能不會處理門牌號碼。 

* **交叉街道：** 交集。 連接; 的表示法兩個街道相交所在的位置。

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

當回應類型是**幾何**，它可以包含會在傳回的 geometry 識別碼**dataSources** "geometry"和"id"中的物件。 例如，[取得多邊形服務](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)可讓您要求的幾何資料，以 GeoJSON 格式，例如城市或機場的外框的一組實體。 您可以使用此界限資料[地理圍欄](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence)或是[幾何內的搜尋 Poi](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)。


[搜尋地址](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)或是[搜尋模糊](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)API 回應可包括**geometry 識別碼**"geometry"和"id"下的資料來源物件中傳回。


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>後續步驟

* 了解[如何建置 Azure 地圖服務搜尋服務要求](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)。
* 探索 Azure Maps[搜尋服務 API 文件](https://docs.microsoft.com/rest/api/maps/search)。 