---
title: 使用 PowerShell 的 azure 區塊鏈服務協會管理
description: 如何管理使用 PowerShell 的 Azure 區塊鏈 Service 協會成員
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: bef0c5d776e8ab6424b8604a49782088c45b0538
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028226"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>管理使用 PowerShell 的 Azure 區塊鏈 Service 中的協會成員

您可以使用 PowerShell 來管理您的 Azure 區塊鏈服務區塊鏈協會成員。 以系統管理員權限的成員可以邀請、 新增、 移除和變更的區塊鏈協會中的所有參與者的角色。 具有使用者權限的成員可以檢視所有參與的區塊鏈協會，而且可以變更其成員的顯示名稱。

## <a name="prerequisites"></a>必要條件

* [建立使用 Azure 入口網站的區塊鏈成員](create-member.md)
* 如需有關 consortia、 成員和節點的詳細資訊，請參閱[Azure 區塊鏈 Service 協會](consortium.md)

## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 

您也可以移至 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)，從另一個瀏覽器索引標籤啟動 Cloud Shell。 選取 [複製] 即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後按 enter 鍵加以執行。

## <a name="install-powershell-module"></a>安裝 PowerShell 模組

從 PowerShell Gallery 安裝 Microsoft.AzureBlockchainService.ConsortiumManagement.PS 套件。

```powershell
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="establish-a-web3-connection"></a>建立 Web3 連線

若要管理協會成員，您需要建立您的 Azure 區塊鏈服務成員端點 Web3 連線。 您可以使用此指令碼來設定可以在呼叫協會管理 cmdlet 時使用的全域變數。

```powershell
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

取代\<成員的帳戶密碼\>建立成員時所使用的成員帳戶密碼。

在 Azure 入口網站中找到其他的值：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 瀏覽至您的預設 Azure 區塊鏈服務成員**概觀**頁面。

    ![成員概觀](./media/manage-consortium-powershell/member-overview.png)

    取代\<會員帳戶\>，並\<RootContract 位址\>入口網站中的值。

1. 端點位址中，選取**交易節點**並選取 [交易] 節點。
1. 選取 **連接字串**。

    ![連接字串](./media/manage-consortium-powershell/connection-strings.png)

    取代\<端點位址\>中的值取代**HTTPS （存取金鑰 1）** 或是**HTTPS （存取金鑰 2）**。

## <a name="network-and-smart-contract-management"></a>網路和智慧合約管理

若要連接到您的區塊鏈端點智能合約負責協會管理中使用的網路和智慧合約 cmdlet。

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

連接到協會管理智能合約，用來管理和強制執行內協會成員。

```powershell
Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>
```

| 參數 | 描述 | 必要項 |
|-----------|-------------|:--------:|
| RootContractAddress | 根合約的位址協會管理智能合約 | 是 |
| Web3Client | 取自新增 Web3Connection Web3Client 物件 | 是 |

**範例**

```powershell
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

您可以使用這個指令程式建立物件以存放資訊的遠端節點管理帳戶。

```powershell
Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>
```

| 參數 | 描述 | 必要項 |
|-----------|-------------|:--------:|
| ManagedAccountAddress | 區塊鏈成員帳戶地址 | 是 |
| ManagedAccountPassword | 帳戶地址密碼 | 是 |

**範例**

```powershell
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

建立到 RPC 結束點的異動節點的連線。

```powershell
New-Web3Connection [-RemoteRPCEndpoint <String>]
```

| 參數 | 描述 | 必要項 |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | 區塊鏈成員端點位址 | 是 |


**範例**

```powershell
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="consortium-member-management"></a>協會成員管理

您可以使用 協會成員管理 cmdlet 來管理內協會成員。 可用的動作取決於您協會的角色。

### <a name="get-blockchainmember"></a>Get-BlockchainMember

取得成員的詳細資訊或列出協會的成員。

```powershell
Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>
```

| 參數 | 描述 | 必要項 |
|-----------|-------------|:--------:|
| 名稱 | 您想要在擷取詳細資料的 Azure 區塊鏈服務成員的名稱。 如果您提供成員名稱，則會傳回成員的詳細資料。 如果省略名稱，就會傳回所有協會成員的清單。 | 否 |
| 成員 | 從匯入 ConsortiumManagementContracts 取得的成員物件 | 是 |
| Web3Client | 取自新增 Web3Connection Web3Client 物件 | 是 |

**範例**

```powershell
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

**範例輸出**

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

將區塊鏈成員中移除。

```powershell
Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| 參數 | 描述 | 必要項 |
|-----------|-------------|:--------:|
| 名稱 | 若要移除的成員名稱 | 是 |
| 成員 | 從匯入 ConsortiumManagementContracts 取得的成員物件 | 是 |
| Web3Account | 取自匯入 Web3Account Web3Account 物件 | 是 |
| Web3Client | 取自新增 Web3Connection Web3Client 物件 | 是 |

**範例**

```powershell
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

設定區塊鏈包括顯示名稱和協會角色的成員屬性。

協會系統管理員可以設定**DisplayName**並**角色**所有成員。 協會與使用者角色的成員才可以變更自己成員的顯示名稱。

```powershell
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| 參數 | 描述 | 必要項 |
|-----------|-------------|:--------:|
| 名稱 | 區塊鏈成員名稱 | 是 |
| DisplayName | 新的顯示名稱 | 否 |
| AccountAddress | 帳戶地址 | 否 |
| 成員 | 從匯入 ConsortiumManagementContracts 取得的成員物件 | 是 |
| Web3Account | 取自匯入 Web3Account Web3Account 物件 | 是 |
| Web3Client |  取自新增 Web3Connection Web3Client 物件| 是 |

**範例**

```powershell
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="consortium-member-invitation-management"></a>協會成員邀請管理

您可以使用 協會成員邀請管理 cmdlet 來管理協會成員邀請。 可用的動作取決於您協會的角色。

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

邀請協會的新成員。

```powershell
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| 參數 | 描述 | 必要項 |
|-----------|-------------|:--------:|
| SubscriptionId | 受邀成員的 azure 訂用帳戶識別碼 | 是 |
| 角色 | 協會角色。 值可以是系統管理員或使用者。 系統管理員是協會系統管理員角色。 使用者是協會成員角色。 | 是 |
| 成員 | 從匯入 ConsortiumManagementContracts 取得的成員物件 | 是 |
| Web3Account | 取自匯入 Web3Account Web3Account 物件 | 是 |
| Web3Client | 取自新增 Web3Connection Web3Client 物件 | 是 |

**範例**

```powershell
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

擷取或列出協會成員邀請狀態。

```powershell
Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>
```

| 參數 | 描述 | 必要項 |
|-----------|-------------|:--------:|
| SubscriptionId | 受邀成員的 azure 訂用帳戶識別碼。 如果提供 SubscriptionID，則會傳回訂用帳戶 id 的邀請詳細資料。 如果省略 SubscriptionID，則會傳回一份所有成員邀請。 | 否 |
| 成員 | 從匯入 ConsortiumManagementContracts 取得的成員物件 | 是 |
| Web3Client | 取自新增 Web3Connection Web3Client 物件 | 是 |

**範例**

```powershell
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

**範例輸出**

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

撤銷協會成員邀請。

```powershell
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| 參數 | 描述 | 必要項 |
|-----------|-------------|:--------:|
| SubscriptionId | 成員，才能撤銷的 azure 訂用帳戶識別碼 | 是 |
| 成員 | 從匯入 ConsortiumManagementContracts 取得的成員物件 | 是 |
| Web3Account | 取自匯入 Web3Account Web3Account 物件 | 是 |
| Web3Client | 取自新增 Web3Connection Web3Client 物件 | 是 |

**範例**

```powershell
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

設定組**角色**現有邀請。 只有協會的系統管理員可以變更的邀請。

```powershell
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| 參數 | 描述 | 必要項 |
|-----------|-------------|:--------:|
| SubscriptionId | 受邀成員的 azure 訂用帳戶識別碼 | 是 |
| 角色 | 新邀請的協會角色。 值可以是**使用者**或**系統管理員** | 是 |
| 成員 |  從匯入 ConsortiumManagementContracts 取得的成員物件 | 是 |
| Web3Account | 取自匯入 Web3Account Web3Account 物件 | 是 |
| Web3Client | 取自新增 Web3Connection Web3Client 物件 | 是 |

**範例**

```powershell
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>後續步驟

如需有關 consortia、 成員和節點的詳細資訊，請參閱：

> [!div class="nextstepaction"]
> [Azure 區塊鏈服務協會](consortium.md)