---
title: 使用 REST API 來擷取計量
titlesuffix: Azure Load Balancer
description: 使用 Azure REST API，來收集 Load Balancer 在指定時間和日期範圍內的健康情況和使用量計量。
services: sql-database
author: KumudD
ms.reviewer: routlaw
manager: jeconnoc
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: article
ms.date: 06/06/2017
ms.author: KumudD
ms.openlocfilehash: 9f5206ef5348ee8fd7b3fe981a9cfe4afc1367fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60734536"
---
# <a name="get-load-balancer-utilization-metrics-using-the-rest-api"></a>使用 REST API 取得 Load Balancer 使用量計量

此操作說明會說明如何使用 [Azure REST API](/rest/api/azure/)，來收集 [Standard Load Balancer](/azure/load-balancer/load-balancer-standard-overview) 在一段時間內所處理的位元組數目。

REST API 的完整參考文件和其他範例可於 [Azure 監視器 REST 參考](/rest/api/monitor)中取得。 

## <a name="build-the-request"></a>建立要求

請使用下列 GET 要求以從 Standard Load Balancer 收集 [ByteCount 計量](/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics)。 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>要求標頭

以下是必要標頭： 

|要求標頭|描述|  
|--------------------|-----------------|  
|*Content-Type:*|必要。 設定為 `application/json`。|  
|*Authorization:*|必要。 設定為無效的 `Bearer` [存取權杖](/rest/api/azure/#authorization-code-grant-interactive-clients)。 |  

### <a name="uri-parameters"></a>URI 參數

| 名稱 | 描述 |
| :--- | :---------- |
| subscriptionId | 可識別 Azure 訂用帳戶的訂用帳戶識別碼。 如果您有多個訂用帳戶，請參閱[使用多個訂用帳戶](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)。 |
| resourceGroupName | 包含資源的資源群組名稱。 您可以從 Azure Resource Manager API、CLI 或入口網站取得這個值。 |
| loadBalancerName | Azure Load Balancer 的名稱。 |
| metricnames | 以逗號分隔的有效 [Load Balancer 計量](/azure/load-balancer/load-balancer-standard-diagnostics)清單。 |
| api-version | 要用於要求的 API 版本。<br /><br /> 本文件涵蓋 api-version `2018-01-01`，內含於上述 URL 中。  |
| 時間範圍 | 查詢的時間範圍。 它是格式如下的字串：`startDateTime_ISO/endDateTime_ISO`。 此選用參數會設定為傳回此範例中一天份的資料。 |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Request body

這項作業不需要要求本文。

## <a name="handle-the-response"></a>处理响应

成功返回指标值列表以后，会返回状态代码 200。 錯誤碼的完整清單可於[參考文件](/rest/api/monitor/metrics/list#errorresponse)中取得。

## <a name="example-response"></a>範例回應 

```json
{
    "cost": 0,
    "timespan": "2018-06-05T03:00:00Z/2018-06-07T03:00:00Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/Microsoft.Insights/metrics/ByteCount",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "ByteCount",
                "localizedValue": "Byte Count"
            },
            "unit": "Count",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-06T17:24:00Z",
                            "total": 1067921034.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:25:00Z",
                            "total": 0.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:26:00Z",
                            "total": 3781344.0
                        },
                    ]
                }
            ]
        }
    ],
    "namespace": "Microsoft.Network/loadBalancers",
    "resourceregion": "eastus"
}
```
