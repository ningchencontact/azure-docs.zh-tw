---
title: 使用 Azure Data Factory 從 REST 來源複製資料 | Microsoft Docs
description: 了解如何使用 Azure Data Factory 管線中的複製活動，從雲端或內部部署 REST 來源將資料複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: jingwang
ms.openlocfilehash: ee47f464c59bd9deed98671f19cfcc6d2c3c1b39
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58762475"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 REST 端點複製資料

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 REST 端點複製資料。 本文是以 [Azure Data Factory 中的複製活動](copy-activity-overview.md)為基礎，該文提供複製活動的一般概觀。

此 REST 連接器、[HTTP 連接器](connector-http.md)和 [Web 資料表連接器](connector-web-table.md)之間的差異是：

- **REST 連接器**專門支援從 RESTful API 複製資料； 
- **HTTP 連接器**可廣泛地用來從任何 HTTP 端點擷取資料，例如下載檔案。 在此 REST 連接器可供使用之前，您可能會使用 HTTP 連接器從 RESTful API 複製資料，這是可支援的方式，但功能性比 REST 連接器低。
- **Web 資料表連接器**可從 HTML 網頁擷取資料表內容。

## <a name="supported-capabilities"></a>支援的功能

您可以將資料從 REST 來源複製到任何支援的接收資料存放區。 如需複製活動作為來源和接收端支援的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)。

具體而言，此泛型 REST 連接器支援：

- 使用 **GET** 或 **POST** 方法，從 REST 端點擷取資料。
- 使用下列其中一種驗證來擷取資料︰**匿名**、**基本**、**AAD 服務主體**和 **Azure 資源的受控識別**。
- REST API 中的**[分頁](#pagination-support)**。
- 複製 REST JSON 回應的[原狀](#export-json-response-as-is)或使用[結構描述對應](copy-activity-schema-and-type-mapping.md#schema-mapping)加以剖析。 僅支援 **JSON** 格式的回應承載。

> [!TIP]
> 若要在 Data Factory 中設定 REST 連接器之前，測試擷取資料的要求，請先了解 API 規格中的標頭和本文需求。 您可以使用 Postman 或網頁瀏覽器之類的工具進行驗證。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，您可使用這些屬性來定義 REST 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 REST 連結服務支援的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | **型別**屬性必須設為**RestService**。 | 是 |
| url | REST 服務的基底 URL。 | 是 |
| enableServerCertificateValidation | 連線到端點時，是否要驗證伺服器端的 SSL 憑證。 | 否<br /> (預設值為 **true**) |
| authenticationType | 用來連線到 REST 服務的驗證類型。 允許的值為 **Anonymous**、**Basic**、**AadServicePrincipal** 和 **ManagedServiceIdentity**。 請分別參閱下列有關更多屬性和範例的對應區段。 | 是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或自我裝載整合執行階段 (如果您的資料存放區位於私人網路中)。 如果未指定，此屬性會使用預設的 Azure Integration Runtime。 |否 |

### <a name="use-basic-authentication"></a>使用基本驗證

將 **authenticationType** 屬性設定為 [Basic]。 除了上一節所述的一般屬性以外，請指定下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| userName | 用來存取 REST 端點的使用者名稱。 | 是 |
| password | 使用者 (**userName** 值) 的密碼。 將此欄位標記為 **SecureString** 類型，將它安全地儲存在 Data Factory 中。 您也可以[參考 Azure Key Vault 中儲存的認證](store-credentials-in-key-vault.md)。 | 是 |

**範例**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<REST endpoint>",
            "userName": "<user name>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-aad-service-principal-authentication"></a>使用 AAD 服務主體驗證

將 **authenticationType** 屬性設定為 [AadServicePrincipal]。 除了上一節所述的一般屬性以外，請指定下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| servicePrincipalId | 指定 Azure Active Directory 應用程式的用戶端識別碼。 | 是 |
| servicePrincipalKey | 指定 Azure Active Directory 應用程式的金鑰。 將此欄位標記為 **SecureString**，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 是 |
| tenant | 指定您的應用程式所在租用戶的資訊 (網域名稱或租用戶識別碼)。 將滑鼠游標暫留在 Azure 入口網站右上角，即可擷取它。 | 是 |
| aadResourceId | 指定您要求授權的 AAD 資源，例如 `https://management.core.windows.net`。| 是 |

**範例**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a>使用 Azure 資源的受控識別驗證

將 **authenticationType** 屬性設定為 [ManagedServiceIdentity]。 除了上一節所述的一般屬性以外，請指定下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| aadResourceId | 指定您要求授權的 AAD 資源，例如 `https://management.core.windows.net`。| 是 |

**範例**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "ManagedServiceIdentity",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

本節提供 REST 資料集所支援的屬性清單。 

如需定義資料集的區段和屬性完整清單，請參閱[資料集和連結服務](concepts-datasets-linked-services.md)。 

若要從 REST 複製資料，以下是支援的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 資料集的 **type** 屬性必須設定為 [RestResource]。 | 是 |
| relativeUrl | 包含資料之資源的相對 URL。 若未指定此屬性，則只會使用在連結服務定義中指定的 URL。 | 否 |
| requestMethod | HTTP 方法。 允許的值為 **Get** (預設值) 和 **Post**。 | 否 |
| additionalHeaders | 其他 HTTP 要求標頭。 | 否 |
| requestBody | HTTP 要求的主體。 | 否 |
| paginationRules | 用來撰寫下一個頁面要求的分頁規則。 請參閱[分頁支援](#pagination-support)區段以取得詳細資料。 | 否 |

**範例 1：搭配使用 Get 方法和分頁**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": {
                "x-user-defined": "helloworld"
            },
            "paginationRules": {
                "AbsoluteUrl": "$.paging.next"
            }
        }
    }
}
```

**範例 2：使用 Post 方法**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST REST request>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

本節提供 REST 來源所支援的屬性清單。

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)。 

### <a name="rest-as-source"></a>REST 作為來源

複製活動的 **source** 區段支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 複製活動來源的 **type** 屬性必須設定為 [RestSource]。 | 是 |
| httpRequestTimeout | 用來取得回應的 HTTP 要求會有的逾時值 (**TimeSpan** 值)。 此值是取得回應的逾時值，而非讀取回應資料的逾時值。 預設值為 **00:01:40**。  | 否 |
| requestInterval | 傳送下一個頁面要求之前的等候時間。 預設值為 [00:00:01] |  否 |

**範例**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "RestSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="pagination-support"></a>分頁支援

一般來說，REST API 會將單一要求的回應承載大小限制在一個合理的數字；如果傳回大量資料，就會將結果分割成多個頁面，並且要求呼叫端傳送連續要求，以取得結果的下一頁。 通常，單一頁面要求是動態的，並且由前頁回應所傳回的資訊所組成。

此泛型 REST 連接器支援下列分頁模式： 

* 下一個要求的絕對或相對 URL = 目前的回應主體中的屬性值
* 下一個要求的絕對或相對 URL = 目前的回應標頭中的標頭值
* 下一個要求的查詢參數 = 目前回應本文中的屬性值
* 下一個要求的查詢參數 = 目前回應標頭中的標頭值
* 下一個要求的標頭 = 目前回應本文中的屬性值
* 下一個要求的標頭 = 目前回應標頭中的標頭值

**分頁規則**會定義為資料集中的字典，資料集包含一個或多個區分大小寫的機碼值組。 此設定將用來產生從第二個頁面開始的要求。 連接器會在取得 HTTP 狀態碼 204 (沒有內容) 時，或任何 "paginationRules" 中的 JSONPath 運算式傳回 null 時，停止反覆查詢。

分頁規則中的**支援金鑰**：

| Key | 描述 |
|:--- |:--- |
| AbsoluteUrl | 指示 URL 發出下一個要求。 它可以是**絕對 URL 或相對 URL**。 |
| QueryParameters.*request_query_parameter* 或 QueryParameters['request_query_parameter'] | 使用者定義的 "request_query_parameter" 會參考下一個 HTTP 要求 URL 中的一個查詢參數名稱。 |
| Headers.*request_header* 或 Headers['request_header'] | 使用者定義的 "request_header" 會參考下一個 HTTP 要求中的一個標頭名稱。 |

分頁規則中的**支援值**：

| 值 | 描述 |
|:--- |:--- |
| Headers.*response_header* 或 Headers['response_header'] | 使用者定義的 "response_header" 會參考目前 HTTP 回應中的一個標頭名稱，其值會用來發出下一個要求。 |
| JSONPath 運算式會以 "$" 開頭 (代表回應本文的根) | 回應本文應只包含一個 JSON 物件。 JSONPath 運算式應會傳回單一基本值，而這會用來發出下一個要求。 |

**範例：**

Facebook 圖形 API 會傳回採用下列結構的回應，在該案例中，下個頁面的 URL 會在 ***paging.next*** 中指出：

```json
{
    "data": [
        {
            "created_time": "2017-12-12T14:12:20+0000",
            "name": "album1",
            "id": "1809938745705498_1809939942372045"
        },
        {
            "created_time": "2017-12-12T14:14:03+0000",
            "name": "album2",
            "id": "1809938745705498_1809941802371859"
        },
        {
            "created_time": "2017-12-12T14:14:11+0000",
            "name": "album3",
            "id": "1809938745705498_1809941879038518"
        }
    ],
    "paging": {
        "cursors": {
            "after": "MTAxNTExOTQ1MjAwNzI5NDE=",
            "before": "NDMyNzQyODI3OTQw"
        },
        "previous": "https://graph.facebook.com/me/albums?limit=25&before=NDMyNzQyODI3OTQw",
        "next": "https://graph.facebook.com/me/albums?limit=25&after=MTAxNTExOTQ1MjAwNzI5NDE="
    }
}
```

相對應的 REST 資料集組態 (特別是 `paginationRules`) 會類似下列內容：

```json
{
    "name": "MyFacebookAlbums",
    "properties": {
            "type": "RestResource",
            "typeProperties": {
                "relativeUrl": "albums",
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                }
            },
            "linkedServiceName": {
                "referenceName": "MyRestService",
                "type": "LinkedServiceReference"
            }
    }
}
```

## <a name="export-json-response-as-is"></a>匯出 JSON 回應的原狀

您可以使用此 REST 連接器將 REST API 的 JSON 回應原狀匯出到各種檔案型存放區。 若要完成這種無結構描述的複製，請略過資料集中的「結構」(也稱為「結構描述」) 區段，以及複製活動中的結構描述。

## <a name="schema-mapping"></a>結構描述對應

若要將資料從 REST 端點複製到表格式接收器中，請參閱[結構描述對應](copy-activity-schema-and-type-mapping.md#schema-mapping)。

## <a name="next-steps"></a>後續步驟

如需 Azure Data Factory 中複製活動作為來源和接收端支援的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)。
