---
title: Azure Blockchain Workbench 組態參考
description: Azure Blockchain Workbench 應用程式組態概觀。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 3/20/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 5569a7608a61b4e79a03264e0ccf62682782264b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Azure Blockchain Workbench 組態參考

 Azure Blockchain Workbench 應用程式是以組態中繼資料和智慧合約程式碼所定義的多方工作流程。 組態中繼資料會定義區塊鏈應用程式的高階工作流程和互動模型。 智慧合約則會定義區塊鏈應用程式的商務邏輯。 Workbench 會使用組態和智慧合約程式碼來產生區塊鏈應用程式使用者體驗。

組態中繼資料會指定每個區塊鏈應用程式的下列資訊： 

* 區塊鏈應用程式的名稱和說明
* 可在區塊鏈應用程式中採取行動或參與的使用者唯一角色
* 一或多個工作流程。 每個工作流程都可作為狀態機器以控制商務邏輯的流程。 工作流程既可彼此獨立，也可彼此互動。

所定義的每個工作流程都會指定下列項目：

* 工作流程的名稱和說明
* 工作流程的狀態。  每個狀態都是商務邏輯控制流程中的一個階段。 
* 轉換至下一個狀態的動作
* 允許將每個動作初始化的使用者角色
* 代表程式碼檔案中商務邏輯的智慧合約

## <a name="application"></a>Application

區塊鏈應用程式包含組態中繼資料、工作流程，以及可在應用程式中採取行動或參與的使用者角色。

| 欄位 | 說明 | 必要 |
|-------|-------------|:--------:|
| ApplicationName | 唯一的應用程式名稱。 對應的智慧合約必須針對適用的合約類別使用相同的 **ApplicationName**。  | yes |
| DisplayName | 應用程式的易記顯示名稱。 | yes |
| 說明 | 應用程式的說明。 | 否 |
| ApplicationRoles | [ApplicationRoles](#application-roles) 的集合。 可在應用程式中採取行動或參與的使用者角色。  | yes |
| 工作流程 | [工作流程](#workflows)的集合。 每個工作流程都可作為狀態機器以控制商務邏輯的流程。 | yes |

如需範例，請參閱[組態檔範例](#configuration-file-example)。

## <a name="workflows"></a>工作流程

您可以將應用程式的商務邏輯塑造為狀態機器，在此機器中採取動作將會造成商務邏輯的流程從某個狀態進入到另一個狀態。 工作流程是這類狀態和動作的集合。 每個工作流程都是由一或多個智慧合約所組成，這些合約可代表程式碼檔案中的商務邏輯。 可執行檔合約是工作流程的執行個體。

| 欄位 | 說明 | 必要 |
|-------|-------------|:--------:|
| Name | 唯一的工作流程名稱。 對應的智慧合約必須針對適用的合約類別使用相同的**名稱**。 | yes |
| DisplayName | 工作流程的易記顯示名稱。 | yes |
| 說明 | 工作流程的說明。 | 否 |
| Initiators | [ApplicationRoles](#application-roles) 的集合。 對已獲授權可在工作流程中建立合約的使用者所指派的角色。 | yes |
| StartState | 工作流程初始狀態的名稱。 | yes |
| properties | [識別碼](#identifiers)的集合。 代表可在使用者體驗工具中進行鏈結關閉讀取或視覺化的資料。 | yes |
| 建構函式 | 定義用於建立工作流程執行個體的輸入參數。 | yes |
| Functions | 可在工作流程中執行的[函式](#functions)集合。 | yes |
| States | 工作流程[狀態](#states)的集合。 | yes |

如需範例，請參閱[組態檔範例](#configuration-file-example)。

## <a name="type"></a>類型

支援的資料類型。

| 類型 | 說明 |
|-------|-------------|
| 位址  | 區塊鏈位址類型，例如「合約」或「使用者」 |
| 布林     | 布林值資料類型 |
| 合約 | 合約類型的位址 |
| int      | 整數資料類型 |
| money    | 金錢資料類型 |
| state    | 工作流程狀態 |
| 字串   | 字串資料類型 |
| user     | 使用者類型的位址 |
| 分析     | 時間資料類型 |
|`[ Application Role Name ]`| 應用程式角色中所指定的任何名稱。 限制使用者只能成為該角色類型。 |

## <a name="constructor"></a>建構函式

定義工作流程執行個體的輸入參數。

| 欄位 | 說明 | 必要 |
|-------|-------------|:--------:|
| 參數 | 需要起始智慧合約的[識別碼](#identifiers)集合。 | yes |

### <a name="constructor-example"></a>建構函式範例

``` json
{
  "Parameters": [
    {
      "Name": "description",
      "Description": "The description of this asset",
      "DisplayName": "Description",
      "Type": {
        "Name": "string"
      }
    },
    {
      "Name": "price",
      "Description": "The price of this asset",
      "DisplayName": "Price",
      "Type": {
        "Name": "money"
      }
    }
  ]
}

```

## <a name="functions"></a>Functions

定義可在工作流程中執行的函式。

| 欄位 | 說明 | 必要 |
|-------|-------------|:--------:|
| Name | 函式的唯一名稱。 對應的智慧合約必須針對適用的函式使用相同的**名稱**。 | yes |
| DisplayName | 函式的易記顯示名稱。 | yes |
| 說明 | 函式的說明 | 否 |
| 參數 | 對應至函式參數的[識別碼](#identifiers)集合。 | yes |

### <a name="functions-example"></a>函式範例

``` json
"Functions": [
  {
    "Name": "Modify",
    "DisplayName": "Modify",
    "Description": "Modify the description/price attributes of this asset transfer instance",
    "Parameters": [
      {
        "Name": "description",
        "Description": "The new description of the asset",
        "DisplayName": "Description",
        "Type": {
          "Name": "string"
        }
      },
      {
        "Name": "price",
        "Description": "The new price of the asset",
        "DisplayName": "Price",
        "Type": {
          "Name": "money"
        }
      }
    ]
  },
  {
    "Name": "Terminate",
    "DisplayName": "Terminate",
    "Description": "Used to cancel this particular instance of asset transfer",
    "Parameters": []
  }
]

```

## <a name="states"></a>狀態

工作流程內唯一狀態的集合。 每個狀態都會擷取商務邏輯控制流程中的一個步驟。 

| 欄位 | 說明 | 必要 |
|-------|-------------|:--------:|
| Name | 狀態的唯一名稱。 對應的智慧合約必須針對適用的狀態使用相同的**名稱**。 | yes |
| DisplayName | 狀態的易記顯示名稱。 | yes |
| 說明 | 狀態的說明。 | 否 |
| PercentComplete | Blockchain Workbench 使用者介面中所顯示的整數值，用來顯示商務邏輯控制流程內的進度。 | yes |
| Style | 指出狀態是表示成功還是失敗狀態的視覺提示。 有效值有兩個：`Success` 或 `Failure`。 | yes |
| Transitions | 可從目前狀態進入到下一組狀態的[轉換](#transitions)集合。 | 否 |

### <a name="states-example"></a>狀態範例

``` json
"States": [
    {
      "Name": "Active",
      "DisplayName": "Active",
      "Description": "The initial state of the asset transfer workflow",
      "PercentComplete": 20,
      "Style": "Success",
      "Transitions": [
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Cancels this instance of asset transfer",
          "Function": "Terminate",
          "NextStates": [ "Terminated" ],
          "DisplayName": "Terminate Offer"
        },
        {
          "AllowedRoles": [ "Buyer" ],
          "AllowedInstanceRoles": [],
          "Description": "Make an offer for this asset",
          "Function": "MakeOffer",
          "NextStates": [ "OfferPlaced" ],
          "DisplayName": "Make Offer"
        },
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Modify attributes of this asset transfer instance",
          "Function": "Modify",
          "NextStates": [ "Active" ],
          "DisplayName": "Modify"
        }
      ]
    },
    {
      "Name": "Accepted",
      "DisplayName": "Accepted",
      "Description": "Asset transfer process is complete",
      "PercentComplete": 100,
      "Style": "Success",
      "Transitions": []
    },
    {
      "Name": "Terminated",
      "DisplayName": "Terminated",
      "Description": "Asset transfer has been cancelled",
      "PercentComplete": 100,
      "Style": "Failure",
      "Transitions": []
    }
  ]
```

## <a name="transitions"></a>轉換

下一個狀態的可用動作。 一或多個使用者角色可在每個狀態執行動作，動作可能會讓某個狀態轉換為工作流程中的另一個狀態。 

| 欄位 | 說明 | 必要 |
|-------|-------------|:--------:|
| AllowedRoles | 允許起始轉換的應用程式角色清單。 屬於指定角色的所有使用者都可執行該動作。 | 否 |
| AllowedInstanceRoles | 參與或指定於智慧合約中、允許起始轉換的使用者角色清單。 執行個體角色會定義在工作流程的**屬性**中。 相對於屬於角色類型的所有使用者，這些使用者代表參與或指定於智慧合約中的使用者。 | 否 |
| DisplayName | 轉換的易記顯示名稱。 | yes |
| 說明 | 轉換的說明。 | 否 |
| 函式 | 要起始轉換的函式名稱。 | yes |
| NextStates | 成功轉換之後下一個可能狀態的集合。 | yes |

### <a name="transitions-example"></a>轉換範例

``` json
"Transitions": [
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Cancels this instance of asset transfer",
    "Function": "Terminate",
    "NextStates": [ "Terminated" ],
    "DisplayName": "Terminate Offer"
  },
  {
    "AllowedRoles": [ "Buyer" ],
    "AllowedInstanceRoles": [],
    "Description": "Make an offer for this asset",
    "Function": "MakeOffer",
    "NextStates": [ "OfferPlaced" ],
    "DisplayName": "Make Offer"
  },
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Modify attributes of this asset transfer instance",
    "Function": "Modify",
    "NextStates": [ "Active" ],
    "DisplayName": "Modify"
  }
]

```

## <a name="application-roles"></a>應用程式角色

應用程式角色會針對要在應用程式中採取行動或參與的使用者，定義一組可對其指派的角色。 應用程式角色可用來限制區塊鏈應用程式和對應的工作流程內所能進行的行動和參與。 

| 欄位 | 說明 | 必要 |
|-------|-------------|:--------:|
| Name | 應用程式角色的唯一名稱。 對應的智慧合約必須針對適用的角色使用相同的**名稱**。 系統會保留基底類型名稱。 您不能使用和[類型](#type)相同的名稱來為應用程式角色命名| yes |
| 說明 | 應用程式角色的說明。 | 否 |

### <a name="application-roles-example"></a>應用程式角色範例

``` json
"ApplicationRoles": [
  {
    "Name": "Appraiser",
    "Description": "User that signs off on the asset price"
  },
  {
    "Name": "Buyer",
    "Description": "User that places an offer on an asset"
  }
]
```
## <a name="identifiers"></a>識別項

識別碼代表用來說明工作流程屬性、建構函式和函式參數的資訊集合。 

| 欄位 | 說明 | 必要 |
|-------|-------------|:--------:|
| Name | 屬性或參數的唯一名稱。 對應的智慧合約必須針對適用的屬性或參數使用相同的**名稱**。 | yes |
| DisplayName | 屬性或參數的易記顯示名稱。 | yes |
| 說明 | 屬性或參數的說明。 | 否 |

### <a name="identifiers-example"></a>識別碼範例

``` json
"Properties": [
  {
    "Name": "State",
    "DisplayName": "State",
    "Description": "Holds the state of the contract",
    "Type": {
      "Name": "state"
    }
  },
  {
    "Name": "Description",
    "DisplayName": "Description",
    "Description": "Describes the asset being sold",
    "Type": {
      "Name": "string"
    }
  }
]
```

## <a name="configuration-file-example"></a>組態檔範例

下列範例會定義基本的要求-回應應用程式，讓要求者傳送要求，並讓回應者傳送要求的回應。

``` json
{
  "ApplicationName": "HelloBlockchain",
  "DisplayName": "Hello, Blockchain!",
  "Description": "A simple application to send request and get response",
  "ApplicationRoles": [
    {
      "Name": "Requestor",
      "Description": "A person sending a request."
    },
    {
      "Name": "Responder",
      "Description": "A person responding to a request"
    }
  ],
  "Workflows": [
    {
      "Name": "RequestResponse",
      "DisplayName": "Request Response",
      "Description": "A simple workflow to send a request and receive a response.",
      "Initiators": [ "Requestor" ],
      "StartState": "Request",
      "Properties": [
        {
          "Name": "State",
          "DisplayName": "State",
          "Description": "Holds the state of the contract.",
          "Type": {
            "Name": "state"
          }
        },
        {
          "Name": "Requestor",
          "DisplayName": "Requestor",
          "Description": "A person sending a request.",
          "Type": {
            "Name": "Requestor"
          }
        },
        {
          "Name": "Responder",
          "DisplayName": "Responder",
          "Description": "A person sending a response.",
          "Type": {
            "Name": "Responder"
          }
        },
        {
          "Name": "RequestMessage",
          "DisplayName": "Request Message",
          "Description": "A request message.",
          "Type": {
            "Name": "string"
          }
        },
        {
          "Name": "ResponseMessage",
          "DisplayName": "Response Message",
          "Description": "A response message.",
          "Type": {
            "Name": "string"
          }
        }
      ],
      "Constructor": {
        "Parameters": [
          {
            "Name": "message",
            "Description": "...",
            "DisplayName": "Request Message",
            "Type": {
              "Name": "string"
            }
          }
        ]
      },
      "Functions": [
        {
          "Name": "SendRequest",
          "DisplayName": "Request",
          "Description": "...",
          "Parameters": [
            {
              "Name": "requestMessage",
              "Description": "...",
              "DisplayName": "Request Message",
              "Type": {
                "Name": "string"
              }
            }
          ]
        },
        {
          "Name": "SendResponse",
          "DisplayName": "Response",
          "Description": "...",
          "Parameters": [
            {
              "Name": "responseMessage",
              "Description": "...",
              "DisplayName": "Response Message",
              "Type": {
                "Name": "string"
              }
            }
          ]
        }
      ],
      "States": [
        {
          "Name": "Request",
          "DisplayName": "Request",
          "Description": "...",
          "PercentComplete": 50,
          "Value": 0,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": ["Responder"],
              "AllowedInstanceRoles": [],
              "Description": "...",
              "Function": "SendResponse",
              "NextStates": [ "Respond" ],
              "DisplayName": "Send Response"
            }
          ]
        },
        {
          "Name": "Respond",
          "DisplayName": "Respond",
          "Description": "...",
          "PercentComplete": 90,
          "Value": 1,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": ["Requestor"],
              "Description": "...",
              "Function": "SendRequest",
              "NextStates": [ "Request" ],
              "DisplayName": "Send Request"
            }
          ]
        }
      ]
    }
  ]
}
```
## <a name="next-steps"></a>後續步驟

[部署 Azure Blockchain Workbench](blockchain-workbench-deploy.md)

