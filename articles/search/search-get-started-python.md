---
title: 快速入門：Python 和 REST Api-Azure 搜尋服務
description: 建立、 載入和使用 Python、 Jupyter Notebook 和 Azure 搜尋服務 REST API 查詢索引。
ms.date: 06/20/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 227da2739216961dcd1f2fb8c643703a1b62e51a
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67302283"
---
# <a name="quickstart-create-an-azure-search-index-using-jupyter-python-notebooks"></a>快速入門：建立使用 Jupyter Python notebook 的 Azure 搜尋服務索引
> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-fiddler.md)
> * [入口網站](search-create-index-portal.md)
> 

建置建立、 載入和查詢使用 Python 的 Azure 搜尋服務索引的 Jupyter notebook 並[Azure 搜尋服務 REST Api](https://docs.microsoft.com/rest/api/searchservice/)。 這篇文章說明如何建置 notebook 逐步解說，從頭開始。 或者，您可以執行已完成的 notebook。 若要下載複本，請前往[azure aearch-python 範例存放庫](https://github.com/Azure-Samples/azure-search-python-samples)。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

本快速入門會使用下列服務和工具。 

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section)，提供 Python 3.x 和 Jupyter Notebook。

+ [建立 Azure 搜尋服務](search-create-service-portal.md)，或在您目前的訂用帳戶下方[尋找現有服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 本快速入門中，您可以使用免費層。 

## <a name="get-a-key-and-url"></a>取得金鑰和 URL

REST 呼叫需要服務 URL 和每個要求的存取金鑰。 搜尋服務是同時建立，因此如果您將 Azure 搜尋服務新增至您的訂用帳戶，請遵循下列步驟來取得必要的資訊：

1. [登入 Azure 入口網站](https://portal.azure.com/)，並在搜尋服務的 [概觀]  頁面上取得 URL。 範例端點看起來會像是 `https://mydemo.search.windows.net`。

1. 在 [設定]   >  [金鑰]  中，取得服務上完整權限的管理金鑰。 可互換的管理金鑰有兩個，可在您需要變換金鑰時提供商務持續性。 您可以在新增、修改及刪除物件的要求上使用主要或次要金鑰。

![取得 HTTP 端點和存取金鑰](media/search-fiddler/get-url-key.png "取得 HTTP 端點和存取金鑰")

所有要求均都需要在傳送至您服務上的每個要求上使用 API 金鑰。 擁有有效的金鑰就能為每個要求在傳送要求之應用程式與處理要求之服務間建立信任。

## <a name="connect-to-azure-search"></a>連線到 Azure 搜尋服務

在這個工作中，啟動 Jupyter notebook，並確認您可以連接到 Azure 搜尋服務。 您會從您的服務要求一份索引來執行這項操作。 上 Anaconda3 與 Windows，您可以使用 Anaconda 導覽啟動 notebook。

1. 建立新的 Python3 notebook。

1. 第一個資料格中，載入程式庫用來處理 JSON 和制訂 HTTP 要求。

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. 在第二個資料格中，輸入要求項目將會在每次要求的常數。 以有效的值取代的搜尋服務名稱 （您-搜尋-服務-名稱） 和系統管理 API 金鑰 （您的系統管理員-API 金鑰）。 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2019-05-06'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

1. 第三個資料格，制訂的要求。 此 GET 要求會以您的搜尋服務索引集合為目標，並選取現有索引的 name 屬性。

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. 執行每個步驟。 如果索引存在，則回應會包含一份索引名稱。 在以下的螢幕擷取畫面，該服務已經有 azureblob 索引和 realestate-我們為範例索引。

   ![使用 HTTP 的 Jupyter notebook 中的 Python 指令碼要求到 Azure 搜尋服務](media/search-get-started-python/connect-azure-search.png "Python 指令碼在 Jupyter notebook 使用 HTTP 要求到 Azure 搜尋服務")

   相反地，空白的索引集合會傳回此回應： `{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1 - 建立索引

除非您使用入口網站，索引上必須有服務之前，您可以載入資料。 此步驟中使用[建立索引 REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)推送至服務的索引結構描述。

索引的必要項目包括名稱、 欄位的集合，以及索引鍵。 Fields 集合定義的結構*文件*。 每個欄位都有名稱、 類型和屬性，以決定欄位的使用方式 (例如，它是否全文檢索搜尋、 可篩選，或可在搜尋結果中擷取)。 索引，其中一個類型的欄位內`Edm.String`您必須指定為*金鑰*文件的身分識別。

這個索引名為"hotels-快速入門 」，並具有您在下方看到的欄位定義。 它是較大子集[Hotels 索引](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON)其他逐步解說中使用。 我們在本快速入門，為求簡單明瞭修剪。

1. 下一步 的資料格，將下列範例貼入提供結構描述的儲存格。 

    ```python
    index_schema = {
       "name": "hotels-quickstart",  
       "fields": [
         {"name": "HotelId", "type": "Edm.String", "key": "true", "filterable": "true"},
         {"name": "HotelName", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "true", "facetable": "false"},
         {"name": "Description", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "en.lucene"},
         {"name": "Description_fr", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "fr.lucene"},
         {"name": "Category", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Tags", "type": "Collection(Edm.String)", "searchable": "true", "filterable": "true", "sortable": "false", "facetable": "true"},
         {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Rating", "type": "Edm.Double", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Address", "type": "Edm.ComplexType", 
         "fields": [
         {"name": "StreetAddress", "type": "Edm.String", "filterable": "false", "sortable": "false", "facetable": "false", "searchable": "true"},
         {"name": "City", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "StateProvince", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "PostalCode", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Country", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"}
        ]
       }
      ]
    }
    ```

2. 在另一個資料格中，制訂的要求。 此 PUT 要求以您的搜尋服務索引集合為目標，並建立根據您在上一個儲存格中提供的索引結構描述的索引。

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. 執行每個步驟。

   回應會包含結構描述的 JSON 表示法。 下列螢幕擷取畫面會顯示回應的一部分。

    ![若要建立索引的要求](media/search-get-started-python/create-index.png "要求建立索引")

> [!Tip]
> 若要確認建立索引的另一種方式是檢查入口網站中的索引清單。

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - 載入文件

若要推送的文件，使用 HTTP POST 要求至您的索引 URL 端點。 REST api[新增、 更新或刪除文件](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)。 文件源自[HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) GitHub 上。

1. 在新的儲存格，提供符合索引結構描述的四個文件。 指定每個文件上傳動作。

    ```python
    documents = {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    ```   

2. 在另一個資料格中，制訂的要求。 這個 POST 要求為目標的快速入門的 hotels 索引的文件集合，並將推入上一個步驟中提供的文件。

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. 執行每個步驟，將文件推送至您的搜尋服務中的索引。 結果看起來應該類似下列的範例。 

    ![將文件傳送到索引](media/search-get-started-python/load-index.png "傳送到索引的文件")

## <a name="3---search-an-index"></a>3 - 搜尋索引

此步驟會說明如何使用索引來進行查詢[搜尋文件的 REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)。

1. 在資料格中，提供執行空的搜尋查詢運算式 (搜尋 = *)，傳回 unranked 的清單 (搜尋分數 = 1.0) 的任意文件。 根據預設，Azure 搜尋服務會傳回 50 的相符項目，一次。 為結構化，此查詢會傳回整份文件結構和值。 新增 $count = true，以取得結果中的所有文件的計數。

   ```python
   searchstring = '&search=*&$count=true'
   ```

1. 在新的儲存格，提供下列的範例，若要搜尋的詞彙"hotels"和"wifi"。 新增 $select 來指定要包含在搜尋結果中的欄位。

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'
   ```

1. 在另一個資料格中，制訂的要求。 此 GET 要求以快速入門的旅館索引的文件集合為目標，並附加您在上一個步驟中指定的查詢。

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. 執行每個步驟。 結果看起來應該類似下列的輸出。 

    ![搜尋索引](media/search-get-started-python/search-index.png "搜尋索引")

1. 請嘗試幾個其他查詢範例，以概略了語法。 您可以使用下列範例取代 searchstring，然後重新執行搜尋要求。 

   套用篩選器： 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'
   ```

   採取的前兩個結果：

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description,Category'
   ```

    依特定欄位：

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince, Tags'
   ```

## <a name="clean-up"></a>清除 

如果您不再需要您應該刪除索引。 一項免費服務僅限於三個索引。 您應該刪除您未主動使用以騰出供其他教學課程的任何索引。

刪除物件的最簡單方式是透過入口網站中，但由於這是 Python 快速入門中，下列語法會產生相同的結果：

   ```python
  url = endpoint + "indexes/hotels-quickstart" + api_version
  response  = requests.delete(url, headers=headers)
   ```

您可以要求一份現有的索引，以確認刪除索引。 如果您知道您的要求成功之後，則看不見了，旅館-快速入門。

```python
url = endpoint + "indexes" + api_version + "&$select=name"

response  = requests.get(url, headers=headers)
index_list = response.json()
pprint(index_list)
```

## <a name="next-steps"></a>後續步驟

為簡化，本快速入門會使用的 Hotels 索引精簡的版本。 您可以建立完整的版本，以便嘗試更有趣的查詢。 若要取得完整的版本和所有的 50 份文件，請執行**匯入資料**精靈中，選取*旅館範例*內建的範例資料來源。

> [!div class="nextstepaction"]
> [快速入門：在 Azure 入口網站中建立索引](search-get-started-portal.md)
