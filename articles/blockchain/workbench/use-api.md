---
title: 使用 Azure Blockchain Workbench REST API
description: 如何使用 Azure Blockchain Workbench REST API 的案例
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 02/21/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: eb4b87a008b9e43de7e7a5f7895449303f1e44a6
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593976"
---
# <a name="using-the-azure-blockchain-workbench-rest-api"></a>使用 Azure Blockchain Workbench REST API

Azure Blockchain Workbench REST API 讓開發人員和資訊工作者能夠組建大量與區塊鏈應用程式整合的項目。 本文會引導您瞭解 Workbench REST API 的數種重要方法。 例如，假設開發人員想要建立自訂的區塊鏈用戶端。 此區塊鏈用戶端可讓已登入的使用者檢視其指派的區塊鏈應用程式並與其互動。 用戶端可讓使用者檢視合約執行個體，並對智慧合約採取動作。 用戶端在已登入使用者的內容中使用 Workbench REST API，以執行下列動作：

* 列出應用程式
* 列出應用程式的工作流程
* 列出工作流程的智慧合約執行個體
* 列出合約的可用動作
* 為合約執行動作

可以[從 GitHub 下載](https://github.com/Azure-Samples/blockchain)案例中使用的區塊鏈應用程式範例。

## <a name="list-applications"></a>列出應用程式

一旦使用者登入區塊鏈用戶端，第一個工作就是擷取為該使用者所有的 Blockchain Workbench 應用程式。 在此案例中，使用者可以存取兩個應用程式：

1. [資產轉送](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
2. [冷藏運輸](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

使用[應用程式 GET API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget)：

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

回應會列出使用者在 Blockchain Workbench 中可以存取的所有區塊鏈應用程式。 Blockchain Workbench 管理員可取得所有區塊鏈應用程式。 非 Workbench 管理員可取得擁有該區塊鏈至少一個相關聯應用程式角色或相關聯智慧合約執行個體角色的所有區塊鏈。

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications?skip=2",
    "applications": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
            "displayName": "Asset Transfer",
            "createdByUserId": 1,
            "createdDtTm": "2018-04-28T05:59:14.4733333",
            "enabled": true,
            "applicationRoles": null
        },
        {
            "id": 2,
            "name": "RefrigeratedTransportation",
            "description": "Application to track end-to-end transportation of perishable goods.",
            "displayName": "Refrigerated Transportation",
            "createdByUserId": 7,
            "createdDtTm": "2018-04-28T18:25:38.71",
            "enabled": true,
            "applicationRoles": null
        }
    ]
}
```

## <a name="list-workflows-for-an-application"></a>列出應用程式的工作流程

當使用者選取適用的區塊鏈應用程式 (例如**資產轉送**) 之後，區塊鏈用戶端就會擷取特定區塊鏈應用程式的所有工作流程。 接著使用者會先選取適用的工作流程，系統才會對使用者顯示該工作流程的所有智慧合約執行個體。 每個區塊鏈應用程式都有一或多個工作流程，每個工作流程都會有零個執行個體或有智慧合約執行個體。 如果區塊鏈用戶端應用程式只有一個工作流程，則建議略過可讓使用者選取適用工作流程的使用者體驗流程。 在此案例中，**資產轉送**只會有一個工作流程，也稱做**資產轉送**。

使用[應用程式工作流程 GET API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget)：

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

回應會列出使用者在 Blockchain Workbench 中可以存取的指定區塊鏈應用程式的所有工作流程。 Blockchain Workbench 管理員可取得所有區塊鏈工作流程。 非 Workbench 管理員可取得擁有該工作流程至少一個相關聯應用程式角色或與智慧合約執行個體角色相關聯的所有工作流程。

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications/1/workflows?skip=1",
    "workflows": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Handles the business logic for the asset transfer scenario",
            "displayName": "Asset Transfer",
            "applicationId": 1,
            "constructorId": 1,
            "startStateId": 1
        }
    ]
}
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>列出工作流程的智慧合約執行個體

使用者選取適用的工作流程後，在此個案中為**資產轉送**，區塊鏈用戶端將會收到指定工作流程的所有智慧合約執行個體。 您可以使用此資訊來顯示工作流程的所有智慧合約執行個體。 或者，您可以讓使用者深入了解任何已顯示的智慧合約執行個體。 在此範例中，請將使用者視為想要與其中一個智慧合約執行個體互動，以採取動作。

使用[合約 GET API](/rest/api/azure-blockchain-workbench/contractsv2/contractsget)：

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

回應會列出指定工作流程的所有智慧合約執行個體。 Workbench 管理員可取得所有智慧合約執行個體。 非 Workbench 管理員可取得擁有該智慧合約執行個體至少一個相關聯應用程式角色或與智慧合約執行個體角色相關聯的所有智慧合約執行個體。

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts?skip=3&workflowId=1",
    "contracts": [
        {
            "id": 1,
            "provisioningStatus": 2,
            "connectionID": 1,
            "ledgerIdentifier": "0xbcb6127be062acd37818af290c0e43479a153a1c",
            "deployedByUserId": 1,
            "workflowId": 1,
            "contractCodeId": 1,
            "contractProperties": [
                {
                    "workflowPropertyId": 1,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 2,
                    "value": "My first car"
                },
                {
                    "workflowPropertyId": 3,
                    "value": "54321"
                },
                {
                    "workflowPropertyId": 4,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 5,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 6,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 7,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 8,
                    "value": "0xd882530eb3d6395e697508287900c7679dbe02d7"
                }
            ],
            "transactions": [
                {
                    "id": 1,
                    "connectionId": 1,
                    "transactionHash": "0xf3abb829884dc396e03ae9e115a770b230fcf41bb03d39457201449e077080f4",
                    "blockID": 241,
                    "from": "0xd882530eb3d6395e697508287900c7679dbe02d7",
                    "to": null,
                    "value": 0,
                    "isAppBuilderTx": true
                }
            ],
            "contractActions": [
                {
                    "id": 1,
                    "userId": 1,
                    "provisioningStatus": 2,
                    "timestamp": "2018-04-29T23:41:14.9333333",
                    "parameters": [
                        {
                            "name": "Description",
                            "value": "My first car"
                        },
                        {
                            "name": "Price",
                            "value": "54321"
                        }
                    ],
                    "workflowFunctionId": 1,
                    "transactionId": 1,
                    "workflowStateId": 1
                }
            ]
        }
    ]
}
```

## <a name="list-available-actions-for-a-contract"></a>列出合約的可用動作

一旦使用者決定深入了解某個合約之後，區塊鏈用戶端接著就會根據合約狀態顯示可用的使用者動作。 在此範例中，使用者正在查看其所建立新智慧合約的所有可用動作：

* 修改：可讓使用者修改的描述和價格的資產。
* 終止：可讓使用者結束資產的合約。

使用[合約動作 GET API](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget)：

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

回應會根據所指定智慧合約執行個體的目前狀態列出使用者可採取的所有動作。 如果使用者具有相關聯的應用程式角色，或與所指定智慧合約目前狀態的智慧合約執行個體角色的相關聯，則使用者會得到所有適用的動作。

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts/1/actions?skip=2",
    "workflowFunctions": [
        {
            "id": 2,
            "name": "Modify",
            "description": "Modify the description/price attributes of this asset transfer instance",
            "displayName": "Modify",
            "parameters": [
                {
                    "id": 1,
                    "name": "description",
                    "description": "The new description of the asset",
                    "displayName": "Description",
                    "type": {
                        "id": 2,
                        "name": "string",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                },
                {
                    "id": 2,
                    "name": "price",
                    "description": "The new price of the asset",
                    "displayName": "Price",
                    "type": {
                        "id": 3,
                        "name": "money",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                }
            ],
            "workflowId": 1
        },
        {
            "id": 3,
            "name": "Terminate",
            "description": "Used to cancel this particular instance of asset transfer",
            "displayName": "Terminate",
            "parameters": [],
            "workflowId": 1
        }
    ]
}
```

## <a name="execute-an-action-for-a-contract"></a>為合約執行動作

接著使用者可決定為指定的智慧合約執行個體採取動作. 在此情況下，假定情節為使用者想要將資產的描述與價格修改為下列動作：

* Description:「 我已更新的汽車 」
* 價格：54321

使用[合約動作 POST API](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost)：

``` http
POST /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
actionInformation: {
    "workflowFunctionId": 2,
    "workflowActionParameters": [
        {
            "name": "description",
            "value": "My updated car"
        },
        {
            "name": "price",
            "value": "54321"
        }
    ]
}
```

使用者只能夠根據所指定智慧合約執行個體目前狀態，以及使用者相關聯的應用程式角色或智慧合約執行個體角色，來執行動作。 如果 post 成功，會傳回 HTTP 200 OK 回應，但無回應本文。

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench REST API 參考](https://docs.microsoft.com/rest/api/azure-blockchain-workbench) (英文)