---
title: 管理 Azure 區塊鏈服務使用 Azure CLI
description: 如何建立和管理 Azure 區塊鏈 Service 中使用 Azure CLI
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: d078ca181b2eed4b80d4f12f1c03b42f4e242194
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154445"
---
# <a name="manage-azure-blockchain-service-with-azure-cli"></a>管理 Azure 區塊鏈 Service 中使用 Azure CLI

除了 Azure 入口網站中，您可以使用 Azure CLI 來快速建立和管理您的 Azure 區塊鏈服務區塊鏈成員和交易的節點。

請確定您已安裝最新[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)並登入 Azure 帳戶中使用`az login`。

在下列範例中，取代範例`<parameter names>`以您自己的值。

## <a name="create-blockchain-member"></a>建立區塊鏈成員

範例會執行新的協會仲裁分類帳通訊協定的 Azure 區塊鏈 Service 中建立區塊鏈成員。

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"<myBlockchainLocation>\", \"properties\": {\"password\": \"<myStrongPassword>\", \"protocol\": \"Quorum\", \"consortium\": \"<myConsortiumName>\", \"consortiumManagementAccountPassword\": \"<myConsortiumManagementAccountPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] }, \"sku\": { \"name\": \"<skuName>\" } }"
```

| 參數 | 描述 |
|---------|-------------|
| **resource-group** | Azure 區塊鏈服務資源的建立資源群組名稱。 |
| **name** | 識別您的 Azure 區塊鏈 Service 區塊鏈成員唯一名稱。 公開的端點位址會使用的名稱。 例如： `myblockchainmember.blockchain.azure.com`。 |
| **位置** | 區塊鏈成員建立所在的 azure 區域。 例如： `eastus`。 選擇最靠近您的使用者或其他 Azure 應用程式的位置。 |
| **password** | 成員的帳戶密碼。 成員的帳戶密碼用來驗證使用基本驗證的區塊鏈成員的公用端點。 密碼必須符合下列四個需求的三種： 長度必須介於 12 和 72 個字元、 1 個小寫字元、 1 個大寫字元、 1 個數字和 1 個特殊字元不是數字 sign(#)、 百分比 （%）、 逗號 （，）、 star(*)，反引號(\`)，按兩下 quote(")、 單引號、 破折號 （-） 和 semicolumn(;)|
| **protocol** | 公開預覽支援仲裁。 |
| **consortium** | 若要加入或建立協會的名稱。 |
| **consortiumManagementAccountPassword** | 協會管理密碼。 此密碼用於加入協會。 |
| **ruleName** | 列入白名單 IP 位址範圍的規則名稱。 防火牆規則的選擇性參數。|
| **startIpAddress** | 列入白名單 IP 位址範圍的開頭。 防火牆規則的選擇性參數。 |
| **endIpAddress** | 列入白名單 IP 位址範圍的結尾。 防火牆規則的選擇性參數。 |
| **skuName** | 階層類型。 Basic 中使用標準和 B0 S0。 |

## <a name="change-blockchain-member-password"></a>變更的區塊鏈成員密碼

範例會變更的區塊鏈成員的密碼。

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.password="<myStrongPassword>" --remove properties.consortiumManagementAccountAddress
```
| 參數 | 描述 |
|---------|-------------|
| **resource-group** | Azure 區塊鏈服務資源的建立資源群組名稱。 |
| **name** | 識別您的 Azure 區塊鏈服務成員的名稱。 |
| **password** | 成員的帳戶密碼。 密碼必須符合下列四個需求的三種： 長度必須介於 12 和 72 個字元、 1 個小寫字元、 1 個大寫字元、 1 個數字和 1 個特殊字元不是數字 sign(#)、 百分比 （%）、 逗號 （，）、 star(*)，反引號(\`)，按兩下 quote(")、 單引號、 破折號 （-） 和分號 （;）。 |


## <a name="create-transaction-node"></a>建立交易節點

建立現有的區塊鏈成員內的交易 節點。 藉由新增交易節點，您可以增加安全性隔離，並分散負載。 例如，您可能有不同的用戶端應用程式的交易節點端點。

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --is-full-object --properties "{ \"location\": \"<myRegion>\", \"properties\": { \"password\": \"<myStrongPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] } }"
```

| 參數 | 描述 |
|---------|-------------|
| **resource-group** | Azure 區塊鏈服務資源的建立資源群組名稱。 |
| **name** | 也包含新的交易節點名稱的 Azure 區塊鏈 Service 區塊鏈成員名稱。 |
| **位置** | 區塊鏈成員建立所在的 azure 區域。 例如： `eastus`。 選擇最靠近您的使用者或其他 Azure 應用程式的位置。 |
| **password** | 異動節點的密碼。 密碼必須符合下列四個需求的三種： 長度必須介於 12 和 72 個字元、 1 個小寫字元、 1 個大寫字元、 1 個數字和 1 個特殊字元不是數字 sign(#)、 百分比 （%）、 逗號 （，）、 star(*)，反引號(\`)，按兩下 quote(")、 單引號、 破折號 （-） 和分號 （;）。 |
| **ruleName** | 列入白名單 IP 位址範圍的規則名稱。 防火牆規則的選擇性參數。 |
| **startIpAddress** | 列入白名單 IP 位址範圍的開頭。 防火牆規則的選擇性參數。 |
| **endIpAddress** | 列入白名單 IP 位址範圍的結尾。 防火牆規則的選擇性參數。|

## <a name="change-transaction-node-password"></a>變更交易節點密碼

範例會變更交易節點密碼。

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --set properties.password="<myStrongPassword>"
```

| 參數 | 描述 |
|---------|-------------|
| **resource-group** | Azure 區塊鏈服務資源所在的資源群組名稱。 |
| **name** | 也包含新的交易節點名稱的 Azure 區塊鏈 Service 區塊鏈成員名稱。 |
| **password** | 異動節點的密碼。 密碼必須符合下列四個需求的三種： 長度必須介於 12 和 72 個字元、 1 個小寫字元、 1 個大寫字元、 1 個數字和 1 個特殊字元不是數字 sign(#)、 百分比 （%）、 逗號 （，）、 star(*)，反引號(\`)，按兩下 quote(")、 單引號、 破折號 （-） 和分號 （;）。 |

## <a name="change-consortium-management-account-password"></a>變更協會管理帳戶密碼

協會管理帳戶用於協會成員資格管理。 每個成員會識別協會管理帳戶，而且您可以變更使用下列命令，此帳戶的密碼。

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.consortiumManagementAccountPassword="<myConsortiumManagementAccountPassword>" --remove properties.consortiumManagementAccountAddress
```

| 參數 | 描述 |
|---------|-------------|
| **resource-group** | Azure 區塊鏈服務資源的建立資源群組名稱。 |
| **name** | 識別您的 Azure 區塊鏈服務成員的名稱。 |
| **consortiumManagementAccountPassword** | 協會管理帳戶密碼。 密碼必須符合下列四個需求的三種： 長度必須介於 12 和 72 個字元、 1 個小寫字元、 1 個大寫字元、 1 個數字和 1 個特殊字元不是數字 sign(#)、 百分比 （%）、 逗號 （，）、 star(*)，反引號(\`)，按兩下 quote(")、 單引號、 破折號 （-） 和分號 （;）。 |
  
## <a name="update-firewall-rules"></a>更新防火牆規則

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.firewallRules="[ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ]" --remove properties.consortiumManagementAccountAddress
```

| 參數 | 描述 |
|---------|-------------|
| **resource-group** | Azure 區塊鏈服務資源所在的資源群組名稱。 |
| **name** | Azure 區塊鏈 Service 區塊鏈成員的名稱。 |
| **ruleName** | 列入白名單 IP 位址範圍的規則名稱。 防火牆規則的選擇性參數。|
| **startIpAddress** | 列入白名單 IP 位址範圍的開頭。 防火牆規則的選擇性參數。|
| **endIpAddress** | 列入白名單 IP 位址範圍的結尾。 防火牆規則的選擇性參數。|

## <a name="list-api-keys"></a>列出 API 金鑰

API 金鑰可以用於節點存取，類似於使用者名稱和密碼。 有兩個 API 金鑰，以支援金鑰輪替。 使用下列命令列出您的 API 金鑰。

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "listApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers
```

| 參數 | 描述 |
|---------|-------------|
| **resource-group** | Azure 區塊鏈服務資源所在的資源群組名稱。 |
| **name** | 也包含新的交易節點名稱的 Azure 區塊鏈 Service 區塊鏈成員名稱。 |

## <a name="regenerate-api-keys"></a>重新產生 API 金鑰

您可以使用下列命令來重新產生您的 API 金鑰。

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "regenerateApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers --request-body '{"keyName":"<keyValue>"}'
```


| 參數 | 描述 |
|---------|-------------|
| **resource-group** | Azure 區塊鏈服務資源所在的資源群組名稱。 |
| **name** | 也包含新的交易節點名稱的 Azure 區塊鏈 Service 區塊鏈成員名稱。 |
| **keyName** | 取代\<keyValue\> key1 或 key2。 |

## <a name="delete-a-transaction-node"></a>刪除交易節點

範例會刪除的區塊鏈成員交易節點。

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers
```

| 參數 | 描述 |
|---------|-------------|
| **resource-group** | Azure 區塊鏈服務資源所在的資源群組名稱。 |
| **name** | 也包含新的交易節點名稱，要刪除的 Azure 區塊鏈 Service 區塊鏈成員名稱。 |

## <a name="delete-a-blockchain-member"></a>刪除的區塊鏈成員

範例會刪除區塊鏈成員。

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers
```

| 參數 | 描述 |
|---------|-------------|
| **resource-group** | Azure 區塊鏈服務資源所在的資源群組名稱。 |
| **name** | 要刪除的 Azure 區塊鏈 Service 區塊鏈成員名稱。 |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Azure AD 使用者的存取權授與

```azurecli
az role assignment create --role <role> --assignee <assignee> --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| 參數 | 描述 |
|---------|-------------|
| **role** | Azure AD 角色的名稱。 |
| **assignee** | Azure AD 使用者識別碼。 例如， `user@contoso.com` |
| scope | 角色指派的範圍。 可以是區塊鏈成員交易 節點。 |

**範例：**

節點到區塊鏈的 Azure AD 使用者的存取權授與**成員**:

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**範例：**

節點到區塊鏈的 Azure AD 使用者的存取權授與**異動節點**:

```azurecli
az role assignment create \
  --role "MyRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Azure AD 群組或應用程式的節點存取權授與角色

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id>
```
| 參數 | 描述 |
|---------|-------------|
| **role** | Azure AD 角色的名稱。 |
| **assignee-object-id** | Azure AD 群組識別碼或應用程式識別碼。 |
| scope | 角色指派的範圍。 可以是區塊鏈成員交易 節點。 |

**範例：**

節點存取權授與**應用程式角色**

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee-object-id 22222222-2222-2222-2222-222222222222 \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>移除 Azure AD 節點存取

```azurecli
az role assignment delete --role <myRole> --assignee <assignee> --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| 參數 | 描述 |
|---------|-------------|
| **role** | Azure AD 角色的名稱。 |
| **assignee** | Azure AD 使用者識別碼。 例如， `user@contoso.com` |
| scope | 角色指派的範圍。 可以是區塊鏈成員交易 節點。 |

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Azure 入口網站設定 Azure 區塊鏈 Service 交易節點](configure-transaction-nodes.md)
