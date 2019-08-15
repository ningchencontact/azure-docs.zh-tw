---
title: Azure Service Fabric CLI- sfctl container | Microsoft Docs
description: 描述 Service Fabric CLI sfctl container 命令。
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 0ce6cf7c627657cf757b0c1ef9aa22428c17a7e7
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036482"
---
# <a name="sfctl-container"></a>sfctl container
在叢集節點上執行容器相關命令。

## <a name="commands"></a>命令

|命令|描述|
| --- | --- |
| invoke-api | 針對指定程式碼封裝，叫用 Service Fabric 節點上所部署容器的容器 API。 |
| logs | 針對指定程式碼套件，取得 Service Fabric 節點上所部署容器的容器記錄。 |

## <a name="sfctl-container-invoke-api"></a>sfctl container invoke-api
針對指定程式碼封裝，叫用 Service Fabric 節點上所部署容器的容器 API。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --application-id           [必要] | 應用程式的身分識別。 <br><br> 這通常是不含 'fabric\:' URI 配置的完整應用程式名稱。 從 6.0 版開始，階層的名稱會以 "\~" 字元分隔。 例如，如果應用程式名稱是 "fabric\:/myapp/app1"，則應用程式識別在 6.0+ 中會是 "myapp\~app1"，而在舊版中會是 "myapp/app1"。 |
| --code-package-instance-id [必要] | 可唯一識別 Service Fabric 節點上所部署程式碼封裝執行個體的識別碼。 <br><br> 可由 'service code-package-list' 來擷取。 |
| --code-package-name        [必要] | 服務資訊清單中所指定的程式碼套件名稱，此資訊清單已註冊為 Service Fabric 叢集中應用程式類型的一部分。 |
| --container-api-uri-path   [必要] | 容器 REST API URI 路徑，請使用 '{id}' 來取代容器名稱/識別碼。 |
| --node-name [必要] | 節點的名稱。 |
| --service-manifest-name    [必要] | 在 Service Fabric 叢集中，註冊為應用程式類型中一部分的服務資訊清單名稱。 |
| --container-api-body | 容器 REST API 的 HTTP 要求本文。 |
| --container-api-content-type | 容器 REST API 的內容類型，預設為 'application/json'。 |
| --container-api-http-verb | 容器 REST API 的 HTTP 動詞，預設為 GET。 |
| --timeout -t | 伺服器逾時 (秒)。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細程度以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。 |

## <a name="sfctl-container-logs"></a>sfctl container logs
針對指定程式碼套件，取得 Service Fabric 節點上所部署容器的容器記錄。

### <a name="arguments"></a>引數

|引數|描述|
| --- | --- |
| --application-id           [必要] | 應用程式的身分識別。 <br><br> 這通常是不含 'fabric\:' URI 配置的完整應用程式名稱。 從 6.0 版開始，階層的名稱會以 "\~" 字元分隔。 例如，如果應用程式名稱是 "fabric\:/myapp/app1"，則應用程式識別在 6.0+ 中會是 "myapp\~app1"，而在舊版中會是 "myapp/app1"。 |
| --code-package-instance-id [必要] | 程式碼套件執行個體識別碼，可由 'service code-package-list' 來擷取。 |
| --code-package-name        [必要] | 服務資訊清單中所指定的程式碼套件名稱，此資訊清單已註冊為 Service Fabric 叢集中應用程式類型的一部分。 |
| --node-name [必要] | 節點的名稱。 |
| --service-manifest-name    [必要] | 在 Service Fabric 叢集中，註冊為應用程式類型中一部分的服務資訊清單名稱。 |
| --tail | 要從記錄結尾顯示的行數。 預設值為 100。 'all' 表示顯示完整記錄。 |
| --timeout -t | 伺服器逾時 (秒)。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|描述|
| --- | --- |
| --debug | 增加記錄詳細程度以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細程度。 使用 --debug 為完整偵錯記錄。 |


## <a name="next-steps"></a>後續步驟
- [設定](service-fabric-cli.md) Service Fabric CLI。
- 了解如何使用[範例指令碼](/azure/service-fabric/scripts/sfctl-upgrade-application)來使用 Service Fabric CLI。