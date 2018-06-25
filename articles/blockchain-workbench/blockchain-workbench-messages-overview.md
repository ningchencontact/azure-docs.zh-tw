---
title: Azure Blockchain Workbench 訊息整合概觀
description: 在 Azure Blockchain Workbench 中使用訊息的概觀。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/16/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: f45396c3af285026e16ce641bd37bf0eadcee56d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2018
ms.locfileid: "34607595"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Azure Blockchain Workbench 訊息整合

Azure Blockchain Workbench 除了提供 REST API，也會提供以傳訊為基礎的整合。 Workbench 會透過 Azure 事件方格發佈以總帳為中心的事件，讓下游取用者能夠根據這些事件來擷取資料或採取動作。 對於需要可靠傳訊功能的用戶端，Azure Blockchain Workbench 也會將訊息傳遞至 Azure 服務匯流排端點。

開發人員也已經表現出，他們非常想要能夠讓外部系統通訊起始交易，以建立使用者、建立合約和更新總帳上的合約。 雖然這項功能目前尚未在公開預覽中出現，但您可以在 [http://aka.ms/blockchain-workbench-integration-sample](http://aka.ms/blockchain-workbench-integration-sample) 找到提供該功能的範例。

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

| Name    | 說明  |
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

| Name | 說明 |
|-----|--------------|
| ChainID | 要求的相關聯鏈結唯一識別碼。|
| BlockId | 總帳上區塊的唯一識別碼。|
| ContractId | 合約的唯一識別碼。|
| ContractAddress |       總帳上合約的位址。|
| TransactionHash  |     總帳上交易的雜湊。|
| OriginatingAddress |   交易建立者的位址。|
| ActionName       |     動作的名稱。|
| IsUpdate        |      識別這是否為更新。|
| 參數       |     物件清單，這些物件可識別傳送至動作的參數名稱、值和資料類型。|
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

| Name                     | 說明                                                                                                                                                                   |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ContractActionId         | 此合約動作的唯一識別碼                                                                                                                                |
| ChainIdentifier          | 鏈結的唯一識別碼                                                                                                                                           |
| ConnectionId             | 連線的唯一識別碼                                                                                                                                      |
| UserChainIdentifier      | 區塊鏈網路上所建立使用者的位址。 在 Ethereum 中，這會是使用者的「鏈結」位址。                                                     |
| ContractLedgerIdentifier | 總帳上合約的位址。                                                                                                                                        |
| WorkflowFunctionName     | 工作流程函式的名稱。                                                                                                                                                |
| WorkflowName             | 工作流程的名稱。                                                                                                                                                         |
| WorkflowBlobStorageURL   | Blob 儲存體中合約的 URL。                                                                                                                                      |
| ContractActionParameters | 合約動作的參數。                                                                                                                                           |
| TransactionHash          | 總帳上交易的雜湊。                                                                                                                                    |
| 佈建狀態      | 動作目前的佈建狀態。</br>0 – 已建立</br>1 – 處理中</br>2 – 完成</br> 完成表示總帳確認已成功新增此項目。                                               |
|                          |                                                                                                                                                                               |

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

| Name    | 說明                              |
|---------|------------------------------------------|
| 位址 | 已對其募集資金的使用者位址。 |
| 餘額 | 使用者餘額的餘額。         |
| ChainID | 鏈結的唯一識別碼。     |


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

| Name           | 說明                                                            |
|----------------|------------------------------------------------------------------------|
| ChainId        | 已在其中新增區塊的鏈結唯一識別碼。             |
| BlockId        | Azure Blockchain Workbench 內區塊的唯一識別碼。 |
| BlockHash      | 區塊的雜湊。                                                 |
| BlockTimeStamp | 區塊的時間戳記。                                            |

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

| Name            | 說明                                                            |
|-----------------|------------------------------------------------------------------------|
| ChainId         | 已在其中新增區塊的鏈結唯一識別碼。             |
| BlockId         | Azure Blockchain Workbench 內區塊的唯一識別碼。 |
| TransactionHash | 交易的雜湊。                                           |
| 從            | 交易建立者的位址。                      |
| 至              | 交易預定接收者的位址。              |
| 值           | 交易中所包含的值。                                 |
| IsAppBuilderTx  | 識別這是否為 Blockchain Workbench 交易。                         |

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

| Name            | 說明                                                                       |
|-----------------|-----------------------------------------------------------------------------------|
| ContractId      | 這是 Azure Blockchain Workbench 內合約的唯一識別碼。 |
| ChainIdentifier | 這是鏈結上合約的識別碼。                             |

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

| Name          | 說明                          |
|---------------|--------------------------------------|
| OperationName | 作業的名稱。           |
| RequestId     | 要求的唯一識別碼。 |

``` csharp
public class MessageModelBase
{
    public string OperationName { get; set; }
    public string RequestId { get; set; }
}
```

### <a name="contractinputparameter"></a>ContractInputParameter

包含參數的名稱、值和類型。

| Name  | 說明                 |
|-------|-----------------------------|
| Name  | 參數名稱。  |
| 值 | 參數的值。 |
| 類型  | 參數的類型。  |

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

| Name  | 說明                |
|-------|----------------------------|
| id    | 屬性的識別碼。    |
| Name  | 屬性的名稱。  |
| 值 | 屬性的值。 |
| 類型  | 屬性的類型。  |

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
> [智慧合約整合模式](blockchain-workbench-integration-patterns.md)