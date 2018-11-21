---
title: Azure Blockchain Workbench 訊息整合概觀
description: 在 Azure Blockchain Workbench 中使用訊息的概觀。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/12/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: f8f3584475415cf9ca19458f6da78d34df37f438
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614356"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Azure Blockchain Workbench 訊息整合

Azure Blockchain Workbench 除了提供 REST API，也會提供以傳訊為基礎的整合。 Workbench 會透過 Azure 事件方格發佈以總帳為中心的事件，讓下游取用者能夠根據這些事件來擷取資料或採取動作。 對於需要可靠傳訊功能的用戶端，Azure Blockchain Workbench 也會將訊息傳遞至 Azure 服務匯流排端點。

## <a name="input-apis"></a>輸入 API

如果您想要從外部系統起始交易，以便建立使用者、建立合約及更新合約，您可使用傳訊輸入 API 在總帳上執行交易。 如需示範輸入 API 的範例，請參閱[傳訊整合範例](https://aka.ms/blockchain-workbench-integration-sample)。

以下是目前可用的輸入 API。

### <a name="create-user"></a>建立使用者

建立新使用者。

要求需要下列欄位：

| **名稱**             | **說明**                                      |
|----------------------|------------------------------------------------------|
| requestId            | 用戶端提供的 GUID                                |
| firstName            | 使用者的名字                              |
| lastName             | 使用者的姓氏                               |
| emailAddress         | 使用者的電子郵件地址                           |
| externalId           | 使用者的 Azure AD 物件識別碼                      |
| connectionId         | 區塊鏈連線的唯一識別碼 |
| messageSchemaVersion | 傳訊結構描述版本                            |
| messageName          | **CreateUserRequest**                               |

範例：

``` json
{
    "requestId": "e2264523-6147-41fc-bbbb-edba8e44562d",
    "firstName": "Ali",
    "lastName": "Alio",
    "emailAddress": "aa@contoso.com",
    "externalId": "6a9b7f65-ffff-442f-b3b8-58a35abd1bcd",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateUserRequest"
}
```

Blockchain Workbench 會傳回包含下列欄位的回應：

| **名稱**              | **說明**                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| requestId             | 用戶端提供的 GUID |
| userId                | 所建立使用者的識別碼 |
| userChainIdentifier   | 區塊鏈網路上所建立使用者的位址。 在 Ethereum 中，此位址是使用者的 **鏈結** 位址。 |
| connectionId          | 區塊鏈連線的唯一識別碼|
| messageSchemaVersion  | 傳訊結構描述版本 |
| messageName           | **CreateUserUpdate** |
| status                | 使用者建立要求的狀態。  如果成功，值為 [成功]。 失敗時，值為 [失敗]。     |
| additionalInformation | 其他根據狀態提供的資訊 |

來自 Blockchain Workbench 的成功**建立使用者**回應範例：

``` json
{ 
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Success", 
    "additionalInformation": { } 
} 
```

如果要求不成功，失敗相關詳細資料會包含在其他資訊中。

``` json
{
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": null, 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Failure", 
    "additionalInformation": { 
        "errorCode": 4000, 
        "errorMessage": "User cannot be provisioned on connection." 
    }
}
```

### <a name="create-contract"></a>建立合約

建立新合約。

要求需要下列欄位：

| **名稱**             | **說明**                                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId            | 用戶端提供的 GUID |
| userChainIdentifier  | 區塊鏈網路上所建立使用者的位址。 在 Ethereum 中，此位址是使用者的**鏈結**位址。 |
| applicationName      | 應用程式的名稱 |
| workflowName         | 工作流程的名稱 |
| parameters           | 可供建立合約的參數輸入 |
| connectionId         | 區塊鏈連線的唯一識別碼 |
| messageSchemaVersion | 傳訊結構描述版本 |
| messageName          | **CreateContractRequest** |

範例：

``` json
{ 
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211", 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "applicationName": "AssetTransfer", 
    "workflowName": "AssetTransfer", 
    "parameters": [ 
        { 
            "name": "description", 
            "value": "a 1969 dodge charger" 
        }, 
        { 
            "name": "price", 
            "value": "12345" 
        } 
    ], 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateContractRequest" 
}
```

Blockchain Workbench 會傳回包含下列欄位的回應：

| **名稱**                 | **說明**                                                                   |
|--------------------------|-----------------------------------------------------------------------------------|
| requestId                | 用戶端提供的 GUID                                                             |
| contractId               | Azure Blockchain Workbench 內合約的唯一識別碼 |
| contractLedgerIdentifier | 總帳上合約的位址                                            |
| connectionId             | 區塊鏈連線的唯一識別碼                               |
| messageSchemaVersion     | 傳訊結構描述版本                                                         |
| messageName              | **CreateContractUpdate**                                                      |
| status                   | 合約建立要求的狀態。  可能的值：[已提交]、[已認可]、[失敗]。  |
| additionalInformation    | 其他根據狀態提供的資訊                              |

來自 Blockchain Workbench 的已提交**建立合約**回應範例：

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Submitted"
    "additionalInformation": { }
}
```

來自 Blockchain Workbench 的已認可**建立合約**回應範例：

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

如果要求不成功，失敗相關詳細資料會包含在其他資訊中。

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": null,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Failure"
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract cannot be provisioned on connection."
    }
}
```

### <a name="create-contract-action"></a>建立合約動作

建立新的合約動作。

要求需要下列欄位：

| **名稱**                 | **說明**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId                | 用戶端提供的 GUID |
| userChainIdentifier      | 區塊鏈網路上所建立使用者的位址。 在 Ethereum 中，這是使用者的**鏈結**位址。 |
| contractLedgerIdentifier | 總帳上合約的位址 |
| workflowFunctionName     | 工作流程函式的名稱 |
| parameters               | 可供建立合約的參數輸入 |
| connectionId             | 區塊鏈連線的唯一識別碼 |
| messageSchemaVersion     | 傳訊結構描述版本 |
| messageName              | **CreateContractActionRequest** |

範例：

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398",
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "workflowFunctionName": "modify",
    "parameters": [
        {
            "name": "description",
            "value": "a 1969 dodge charger"
        },
        {
            "name": "price",
            "value": "12345"
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionRequest"
}
```

Blockchain Workbench 會傳回包含下列欄位的回應：

| **名稱**              | **說明**                                                                   |
|-----------------------|-----------------------------------------------------------------------------------|
| requestId             | 用戶端提供的 GUID|
| contractId            | Azure Blockchain Workbench 內合約的唯一識別碼 |
| connectionId          | 區塊鏈連線的唯一識別碼 |
| messageSchemaVersion  | 傳訊結構描述版本 |
| messageName           | **CreateContractActionUpdate** |
| status                | 合約動作要求的狀態。 可能的值：[已提交]、[已認可]、[失敗]。                         |
| additionalInformation | 其他根據狀態提供的資訊 |

來自 Blockchain Workbench 的已提交**建立合約動作**回應範例：

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

來自 Blockchain Workbench 的已認可**建立合約動作**回應範例：

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Committed"
    "additionalInformation": { }
}
```

如果要求不成功，失敗相關詳細資料會包含在其他資訊中。

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Failure"
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract action cannot be provisioned on connection."
    }
}
```

### <a name="input-api-error-codes-and-messages"></a>輸入 API 錯誤碼和訊息

**錯誤碼 4000：不正確的要求錯誤**
- 無效的 connectionId
- CreateUserRequest deserialization 失敗
- CreateContractRequest deserialization 失敗
- CreateContractActionRequest deserialization 失敗
- 應用程式 {依應用程式名稱識別} 不存在
- 應用程式 {依應用程式名稱識別} 沒有工作流程
- UserChainIdentifier 不存在
- 合約 {依總帳識別碼識別} 不存在
- 合約 {依總帳識別碼識別} 沒有函式 {工作流程函式名稱}
- UserChainIdentifier 不存在

**錯誤碼 4090：衝突錯誤**
- 使用者已經存在
- 合約已經存在
- 合約動作已經存在

**錯誤碼 5000：內部伺服器錯誤**
- 例外狀況訊息

## <a name="event-notifications"></a>事件通知

事件通知可用來通知使用者和下游系統，讓其知道 Blockchain Workbench 中所發生的事件以及 Blockchain Workbench 所連線到的區塊鏈網路。 事件通知可直接在程式碼中取用，或與 Logic Apps 和 Flow 等工具搭配使用，以觸發將資料傳送到下游系統的流程。

如需各種可收到訊息的詳細資料，請參閱[通知訊息參考](#notification-message-reference)。

### <a name="consuming-event-grid-events-with-azure-functions"></a>使用 Azure Functions 取用事件方格事件

如果使用者想要使用事件方格來收到有關 Blockchain Workbench 中所發生事件的通知，您可以使用 Azure Functions 從事件方格取用事件。

1. 在 Azure 入口網站中建立 **Azure 函式應用程式**。
2. 建立新的函式。
3. 找到事件方格範本。 隨即會顯示用於讀取訊息的基本範本程式碼。 視需要修改程式碼。
4. 儲存函式。 
5. 從 Blockchain Workbench 的資源群組中選取事件方格。

### <a name="consuming-event-grid-events-with-logic-apps"></a>使用 Logic Apps 取用事件方格事件

1.  在 Azure 入口網站中建立新的 **Azure 邏輯應用程式**。
2.  在入口網站中開啟 Azure 邏輯應用程式時，系統會提示您選取觸發程序。 選取 [Azure 事件方格 - 發生資源事件時]。
3. 當工作流程設計工具顯示時，系統會提示您登入。
4. 選取訂用帳戶。 資源作為 **Microsoft.EventGrid.Topics**。 從 Azure Blockchain Workbench 資源群組的資源名稱中選取 [資源名稱]。
5. 從 Blockchain Workbench 的資源群組中選取事件方格。

## <a name="using-service-bus-topics-for-notifications"></a>使用服務匯流排主題來進行通知

服務匯流排主題可用來通知使用者在 Blockchain Workbench 中所發生的事件。 

1.  在 Workbench 的資源群組內瀏覽至服務匯流排。
2.  選取 [主題]。
3.  選取 [workbench-external]。
4.  對此主題建立新的訂用帳戶。 取得其金鑰。
5.  建立程式，從這個訂用帳戶訂閱事件。

### <a name="consuming-service-bus-messages-with-logic-apps"></a>使用 Logic Apps 取用服務匯流排訊息

1. 在 Azure 入口網站中建立新的 **Azure 邏輯應用程式**。
2. 在入口網站中開啟 Azure 邏輯應用程式時，系統會提示您選取觸發程序。 在搜尋方塊中輸入**服務匯流排**，然後針對在與服務匯流排互動時所要使用的類型，選取適當的觸發程序。 例如，**服務匯流排 - 主題訂用帳戶中收到訊息時 (自動完成)**。
3. 當工作流程設計工具顯示時，指定服務匯流排的連線資訊。
4. 選取訂用帳戶，並指定 **workbench-external** 的主題。
5. 開發應用程式邏輯來利用這個觸發程序所傳來的訊息。

## <a name="notification-message-reference"></a>通知訊息參考

根據 **OperationName**，通知訊息具有下列其中一種訊息類型。

### <a name="accountcreated"></a>AccountCreated

表示已要求將新帳戶新增至指定的鏈結。

| 名稱    | 說明  |
|----------|--------------|
| UserId  | 所建立使用者的識別碼。 |
| ChainIdentifier | 區塊鏈網路上所建立使用者的位址。 在 Ethereum 中，這會是使用者的**鏈結**位址。 |

``` csharp
public class NewAccountRequest : MessageModelBase
{
  public int UserID { get; set; }
  public string ChainIdentifier { get; set; }
}
```

### <a name="contractinsertedorupdated"></a>ContractInsertedOrUpdated

表示已提出要求要在分散式總帳上插入或更新合約。

| 名稱 | 說明 |
|-----|--------------|
| ChainID | 要求相關鏈結的唯一識別碼 |
| BlockId | 總帳上區塊的唯一識別碼 |
| ContractId | 合約的唯一識別碼 |
| ContractAddress |       總帳上合約的位址 |
| TransactionHash  |     總帳上交易的雜湊 |
| OriginatingAddress |   交易建立者的位址 |
| ActionName       |     動作的名稱 |
| IsUpdate        |      識別這是否為更新 |
| 參數       |     物件清單，這些物件可識別傳送至動作的參數名稱、值和資料類型 |
| TopLevelInputParams |  在合約連線至一或多個其他合約的情況下，這些是來自最上層合約的參數。 |

``` csharp
public class ContractInsertOrUpdateRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public int ContractId { get; set; }
    public string ContractAddress { get; set; }
    public string TransactionHash { get; set; }
    public string OriginatingAddress { get; set; }
    public string ActionName { get; set; }
    public bool IsUpdate { get; set; }
    public List<ContractProperty> Parameters { get; set; }
    public bool IsTopLevelUpdate { get; set; }
    public List<ContractInputParameter> TopLevelInputParams { get; set; }
}
```

#### <a name="updatecontractaction"></a>UpdateContractAction

表示已提出要求要對分散式總帳上的特定合約執行動作。

| 名稱                     | 說明                                                                                                                                                                   |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ContractActionId         | 此合約動作的唯一識別碼 |
| ChainIdentifier          | 鏈結的唯一識別碼 |
| ConnectionId             | 連線的唯一識別碼 |
| UserChainIdentifier      | 區塊鏈網路上所建立使用者的位址。 在 Ethereum 中，此位址是使用者的**鏈結**位址。 |
| ContractLedgerIdentifier | 總帳上合約的位址 |
| WorkflowFunctionName     | 工作流程函式的名稱 |
| WorkflowName             | 工作流程的名稱 |
| WorkflowBlobStorageURL   | Blob 儲存體中合約的 URL |
| ContractActionParameters | 合約動作的參數 |
| TransactionHash          | 總帳上交易的雜湊 |
| 佈建狀態      | 動作目前的佈建狀態。</br>0 – 已建立</br>1 – 處理中</br>2 – 完成</br> 完成表示總帳確認已成功新增此項目 |

```csharp
public class ContractActionRequest : MessageModelBase
{
    public int ContractActionId { get; set; }
    public int ConnectionId { get; set; }
    public string UserChainIdentifier { get; set; }
    public string ContractLedgerIdentifier { get; set; }
    public string WorkflowFunctionName { get; set; }
    public string WorkflowName { get; set; }
    public string WorkflowBlobStorageURL { get; set; }
    public IEnumerable<ContractActionParameter> ContractActionParameters { get; set; }
    public string TransactionHash { get; set; }
    public int ProvisioningStatus { get; set; }
}
```

### <a name="updateuserbalance"></a>UpdateUserBalance

表示已提出要求要在特定分散式總帳上更新使用者餘額。

> [!NOTE]
> 此訊息只會針對需要帳戶資金的總帳來產生。
> 

| 名稱    | 說明                              |
|---------|------------------------------------------|
| 位址 | 已對其募集資金的使用者位址 |
| 餘額 | 使用者餘額的餘額         |
| ChainID | 鏈結的唯一識別碼     |


``` csharp
public class UpdateUserBalanceRequest : MessageModelBase
{
    public string Address { get; set; }
    public decimal Balance { get; set; }
    public int ChainID { get; set; }
}
```

### <a name="insertblock"></a>InsertBlock

訊息會表示已提出要求要在分散式總帳上新增區塊。

| 名稱           | 說明                                                            |
|----------------|------------------------------------------------------------------------|
| ChainId        | 已在其中新增區塊的鏈結唯一識別碼             |
| BlockId        | Azure Blockchain Workbench 內區塊的唯一識別碼 |
| BlockHash      | 區塊的雜湊                                                 |
| BlockTimeStamp | 區塊的時間戳記                                            |

``` csharp
public class InsertBlockRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string BlockHash { get; set; }
    public int BlockTimestamp { get; set; }
}
```

### <a name="inserttransaction"></a>InsertTransaction

訊息會提供有關要在分散式總帳上新增交易的要求詳細資料。

| 名稱            | 說明                                                            |
|-----------------|------------------------------------------------------------------------|
| ChainId         | 已在其中新增區塊的鏈結唯一識別碼             |
| BlockId         | Azure Blockchain Workbench 內區塊的唯一識別碼 |
| TransactionHash | 交易的雜湊                                           |
| 從            | 交易建立者的位址                      |
| 至              | 交易預定接收者的位址              |
| 值           | 交易中所包含的值                                 |
| IsAppBuilderTx  | 識別這是否為 Blockchain Workbench 交易                         |

``` csharp
public class InsertTransactionRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string TransactionHash { get; set; }
    public string From { get; set; }
    public string To { get; set; }
    public decimal Value { get; set; }
    public bool IsAppBuilderTx { get; set; }
}
```

### <a name="assigncontractchainidentifier"></a>AssignContractChainIdentifier

提供合約鏈結識別碼指派的詳細資料。 例如，在 Ethereum 區塊鏈中，總帳上合約的位址。

| 名稱            | 說明                                                                       |
|-----------------|-----------------------------------------------------------------------------------|
| ContractId      | Azure Blockchain Workbench 內合約的唯一識別碼 |
| ChainIdentifier | 鏈結上合約的識別碼                             |

``` csharp
public class AssignContractChainIdentifierRequest : MessageModelBase
{
    public int ContractId { get; set; }
    public string ChainIdentifier { get; set; }
}
```

## <a name="classes-used-by-message-types"></a>訊息類型所使用的類別

### <a name="messagemodelbase"></a>MessageModelBase

所有訊息的基本模型。

| 名稱          | 說明                          |
|---------------|--------------------------------------|
| OperationName | 作業的名稱           |
| RequestId     | 要求的唯一識別碼 |

``` csharp
public class MessageModelBase
{
    public string OperationName { get; set; }
    public string RequestId { get; set; }
}
```

### <a name="contractinputparameter"></a>ContractInputParameter

包含參數的名稱、值和類型。

| 名稱  | 說明                 |
|-------|-----------------------------|
| 名稱  | 參數的名稱  |
| 值 | 參數的值 |
| 類型  | 參數的類型  |

``` csharp
public class ContractInputParameter
{
    public string Name { get; set; }
    public string Value { get; set; }
    public string Type { get; set; }
}
```

#### <a name="contractproperty"></a>ContractProperty

包含屬性的識別碼、名稱、值和類型。

| 名稱  | 說明                |
|-------|----------------------------|
| id    | 屬性的識別碼    |
| 名稱  | 屬性的名稱  |
| 值 | 屬性的值 |
| 類型  | 屬性的類型  |

``` csharp
public class ContractProperty
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Value { get; set; }
    public string DataType { get; set; }
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [智慧合約整合模式](integration-patterns.md)