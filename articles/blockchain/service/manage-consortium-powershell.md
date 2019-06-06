---
title: 使用 Azure PowerShell 管理 Azure 區塊鏈 Service 協會中的成員
description: 了解如何使用 Azure PowerShell 管理 Azure 區塊鏈 Service 協會成員。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/10/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 4bb72bc3fe8b85a8d2aed88e02f5f3150abb6899
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66493645"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-by-using-powershell"></a>使用 PowerShell 管理 Azure 區塊鏈 Service 中的協會成員

您可以使用 PowerShell 來管理您的 Azure 區塊鏈服務區塊鏈協會成員。 具有系統管理員權限的成員可以邀請、 新增、 移除及變更的區塊鏈協會中的所有參與者的角色。 具有使用者權限的成員可以檢視中的區塊鏈協會的所有參與者，並變更其成員的顯示名稱。

## <a name="prerequisites"></a>必要條件

* 使用建立區塊鏈成員[Azure 入口網站](create-member.md)。
* 如需有關 consortia、 成員和節點的詳細資訊，請參閱[Azure 區塊鏈 Service 協會](consortium.md)。

## <a name="open-azure-cloud-shell"></a>開啟 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。

您也可以開啟另一個瀏覽器索引標籤在 Cloud Shell 中，移至[shell.azure.com/powershell](https://shell.azure.com/powershell)。 選取 **複製**若要複製的程式碼區塊，將它貼到 Cloud Shell 中，然後選取**Enter**執行它。

## <a name="install-the-powershell-module"></a>安裝 PowerShell 模組

從 PowerShell Gallery 安裝 Microsoft.AzureBlockchainService.ConsortiumManagement.PS 套件。

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>將資訊喜好設定

設定資訊喜好設定變數來執行 cmdlet 時，您可以取得更多資訊。 根據預設， *$InformationPreference*設為*SilentlyContinue*。

如需更詳細的 cmdlet 的詳細資訊，請在 PowerShell 中，如下所示將喜好設定：

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>建立 Web3 連線

若要管理協會成員，建立您的區塊鏈服務成員端點 Web3 連接。 您可以使用此指令碼來設定全域變數來呼叫協會管理 cmdlet。

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

取代 *\<成員的帳戶密碼\>* 取代為您建立成員時，使用的成員帳戶密碼。

在 Azure 入口網站中找到其他的值：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 移至您的預設區塊鏈服務成員**概觀**頁面。

    ![成員概觀](./media/manage-consortium-powershell/member-overview.png)

    取代 *\<會員帳戶\>* 並 *\<RootContract 位址\>* 入口網站中的值。

1. 端點位址中，選取**交易節點**，然後選取**預設交易節點**。 預設的節點具有相同的區塊鏈成員名稱。
1. 選取 [連接字串]  。

    ![連接字串](./media/manage-consortium-powershell/connection-strings.png)

    取代 *\<端點位址\>* 中的值取代**HTTPS （存取金鑰 1）** 或是**HTTPS （存取金鑰 2）** 。

## <a name="manage-the-network-and-smart-contracts"></a>管理網路和智能合約

若要連接到區塊鏈端點的智能合約負責協會管理中使用的網路和智慧合約 cmdlet。

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

您可以使用這個指令程式，連接到協會管理智能合約。 這些合約用來管理和強制執行內協會成員。

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| 參數 | 描述 | 必要項 |
|-----------|-------------|:--------:|
| RootContractAddress | 根的協會管理智能合約的合約位址 | 是 |
| Web3Client | 取自新增 Web3Connection Web3Client 物件 | 是 |

#### <a name="example"></a>範例

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

您可以使用這個指令程式建立物件以存放遠端節點的管理帳戶的資訊。

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| 參數 | 描述 | 必要項 |
|-----------|-------------|:--------:|
| ManagedAccountAddress | 區塊鏈成員帳戶地址 | 是 |
| ManagedAccountPassword | 帳戶地址密碼 | 是 |

#### <a name="example"></a>範例

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

您可以使用這個指令程式，連接到 RPC 結束點的異動節點。

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| 參數 | 描述 | 必要項 |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | 區塊鏈成員端點位址 | 是 |

#### <a name="example"></a>範例

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>管理協會成員

您可以使用 協會成員管理 cmdlet 來管理內協會成員。 可用的動作取決於您協會的角色。

### <a name="get-blockchainmember"></a>Get-BlockchainMember

若要取得成員的詳細資訊，或列出的協會成員使用這個指令程式。

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| 參數 | 描述 | 必要項 |
|-----------|-------------|:--------:|
| Name | 您想要擷取其相關詳細資料的區塊鏈服務成員的名稱。 當您輸入的名稱時，它會傳回成員的詳細資料。 當省略名稱時，它會傳回所有協會成員的清單。 | 否 |
| 成員 | 從匯入 ConsortiumManagementContracts 取得的成員物件 | 是 |
| Web3Client | 取自新增 Web3Connection Web3Client 物件 | 是 |

#### <a name="example"></a>範例

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

#### <a name="example-output"></a>範例輸出

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

您可以使用這個指令程式，將區塊鏈成員中移除。

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| 參數 | 描述 | 必要項 |
|-----------|-------------|:--------:|
| Name | 若要移除的成員名稱 | 是 |
| 成員 | 從匯入 ConsortiumManagementContracts 取得的成員物件 | 是 |
| Web3Account | 取自匯入 Web3Account Web3Account 物件 | 是 |
| Web3Client | 取自新增 Web3Connection Web3Client 物件 | 是 |

#### <a name="example"></a>範例

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

您可以使用這個指令程式，將區塊鏈成員屬性，包括顯示名稱和協會角色。

協會系統管理員可以設定**DisplayName**並**角色**所有成員。 協會成員與使用者角色可以變更自己成員的顯示名稱。

`Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| 參數 | 描述 | 必要項 |
|-----------|-------------|:--------:|
| Name | 區塊鏈成員名稱 | 是 |
| DisplayName | 新的顯示名稱 | 否 |
| AccountAddress | 帳戶地址 | 否 |
| 成員 | 從匯入 ConsortiumManagementContracts 取得的成員物件 | 是 |
| Web3Account | 取自匯入 Web3Account Web3Account 物件 | 是 |
| Web3Client |  取自新增 Web3Connection Web3Client 物件| 是 |

#### <a name="example"></a>範例

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>管理協會成員的邀請

您可以使用 協會成員邀請管理 cmdlet 來管理協會成員的邀請。 可用的動作取決於您協會的角色。

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

您可以使用這個指令程式，邀請協會的新成員。

`New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| 參數 | 描述 | 必要項 |
|-----------|-------------|:--------:|
| SubscriptionId | 要邀請之成員的 azure 訂用帳戶識別碼 | 是 |
| 角色 | 協會的角色。 值可以是系統管理員或使用者。 系統管理員是協會系統管理員角色。 使用者是協會成員角色。 | 是 |
| 成員 | 從匯入 ConsortiumManagementContracts 取得的成員物件 | 是 |
| Web3Account | 取自匯入 Web3Account Web3Account 物件 | 是 |
| Web3Client | 取自新增 Web3Connection Web3Client 物件 | 是 |

#### <a name="example"></a>範例

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

使用這個指令程式擷取，或列出協會成員的邀請狀態。

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| 參數 | 描述 | 必要項 |
|-----------|-------------|:--------:|
| SubscriptionId | 邀請成員的 Azure 訂用帳戶識別碼。 如果訂用帳戶識別碼是，它會傳回訂用帳戶 ID 的邀請詳細資料。 如果省略訂用帳戶識別碼，它會傳回一份所有成員邀請。 | 否 |
| 成員 | 從匯入 ConsortiumManagementContracts 取得的成員物件 | 是 |
| Web3Client | 取自新增 Web3Connection Web3Client 物件 | 是 |

#### <a name="example"></a>範例

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>範例輸出

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

您可以使用這個 cmdlet 來撤銷協會成員的邀請。

`Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>`

| 參數 | 描述 | 必要項 |
|-----------|-------------|:--------:|
| SubscriptionId | 要撤銷之成員的 azure 訂用帳戶識別碼 | 是 |
| 成員 | 從匯入 ConsortiumManagementContracts 取得的成員物件 | 是 |
| Web3Account | 取自匯入 Web3Account Web3Account 物件 | 是 |
| Web3Client | 取自新增 Web3Connection Web3Client 物件 | 是 |

#### <a name="example"></a>範例

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

使用此 cmdlet 來設定**角色**現有邀請。 只有協會的系統管理員可以變更的邀請。

`Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| 參數 | 描述 | 必要項 |
|-----------|-------------|:--------:|
| SubscriptionId | 要邀請之成員的 azure 訂用帳戶識別碼 | 是 |
| 角色 | 新邀請的協會角色。 值可以是**使用者**或是**管理員**。 | 是 |
| 成員 |  從匯入 ConsortiumManagementContracts 取得的成員物件 | 是 |
| Web3Account | 取自匯入 Web3Account Web3Account 物件 | 是 |
| Web3Client | 取自新增 Web3Connection Web3Client 物件 | 是 |

#### <a name="example"></a>範例

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>後續步驟

如需有關 consortia、 成員和節點的詳細資訊，請參閱：

> [!div class="nextstepaction"]
> [Azure 區塊鏈服務協會](consortium.md)
