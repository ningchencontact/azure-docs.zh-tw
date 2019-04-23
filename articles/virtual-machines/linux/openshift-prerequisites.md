---
title: Azure 中的 OpenShift 必要條件 | Microsoft Docs
description: 在 Azure 中部署 OpenShift 的必要條件。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/19/2019
ms.author: haroldw
ms.openlocfilehash: d8a9b82e51c837af6343ddf851545d02299aa527
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001640"
---
# <a name="common-prerequisites-for-deploying-openshift-in-azure"></a>在 Azure 中開發 OpenShift 的一般必要條件

本文說明在 Azure 中部署 OpenShift 容器平台或 OKD 的一般必要條件。

OpenShift 安裝是使用 Ansible 腳本。 Ansible 使用安全殼層 (SSH) 連接到所有叢集主機，以完成安裝步驟。

當 ansible 建立 SSH 連線至遠端主機時，它不能輸入密碼。 因此，私密金鑰不能有相關聯的密碼 (複雜密碼)，否則部署會失敗。

由於所有虛擬機器 (VM) 是透過 Azure Resource Manager 範本部署的，因此會使用相同的公開金鑰存取所有 VM。 對應的私密金鑰必須執行所有腳本以及在 VM 上。 若要安全地執行此動作，Azure 金鑰保存庫用來將私密金鑰傳遞至 VM。

如果容器有永續性儲存體的需求，則需要永久性磁碟區。 OpenShift 支援 Azure 虛擬硬碟 (Vhd) 的永續性磁碟區，但必須先將 Azure 設定為雲端提供者。

在此模型中，OpenShift 將會：

- 在 Azure 儲存體帳戶或受控的磁碟建立 VHD 物件。
- 將 VHD 掛接到 VM 並將磁碟區格式化。
- 將磁碟區掛接到 Pod。

為了使這個設定運作，OpenShift 需要在 Azure 中執行上述工作的權限。 服務主要用於此目的。 服務主體是 Azure Active Directory 中，獲授與資源權限的安全性帳戶。

服務主體必須具備儲存體帳戶和構成叢集之 VM 的存取權。 如果將所有 OpenShift 叢集資源都部署到單一資源群組中，則可以授與服務主體該資源群組的權限。

本指南描述如何建立與必要條件相關聯的成品。

> [!div class="checklist"]
> * 建立金鑰保存庫來管理 OpenShift 叢集的 SSH 金鑰。
> * 建立可供 Azure 雲端提供者使用的服務主體。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="sign-in-to-azure"></a>登入 Azure 
使用 [az login](/cli/azure/reference-index) 命令來登入 Azure 訂用帳戶並遵循畫面上的指示進行，或按一下 [試用] 來使用 Cloud Shell。

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group) 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 您應該使用專用的資源群組來裝載金鑰保存庫。 此群組與 OpenShift 叢集資源部署所在的資源群組分開。

下列範例會在 *eastus* 位置，建立名為 *keyvaultrg* 的資源群組：

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>建立金鑰保存庫
使用 [az keyvault create](/cli/azure/keyvault) 命令來建立要儲存叢集之 SSH 金鑰的金鑰保存庫。 金鑰保存庫名稱必須是全域唯一的。

下列範例會在 *keyvaultrg* 資源群組中，建立名為 *keyvault* 的金鑰保存庫：

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>建立 SSH 金鑰 
需要 SSH 金鑰才能安全存取 OpenShift 叢集。 使用 `ssh-keygen` 命令建立 SSH 金鑰組 (在 Linux 或 macOS 上)：
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> 您的 SSH 金鑰組不能有密碼 / 複雜密碼。

如需有關 Windows 上的 SSH 金鑰詳細資訊，請參閱[如何在 Windows 上建立 SSH 金鑰](/azure/virtual-machines/linux/ssh-from-windows)。 請務必以 OpenSSH 格式匯出私密金鑰。

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>將 SSH 私密金鑰儲存在 Azure Key Vault 中
OpenShift 部署會使用您建立的 SSH 金鑰來安全存取 OpenShift 主機。 若要啟用部署以安全地擷取 SSH 金鑰，請使用下列命令將金鑰儲存在金鑰保存庫中：

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>建立服務主體 
OpenShift 會使用使用者名稱與密碼或服務主體與 Azure 進行通訊。 Azure 服務主體是安全性識別，可供您與應用程式、服務及諸如 OpenShift 等自動化工具搭配使用。 您可以控制和定義關於服務主體可以在 Azure 中執行哪些作業的權限。 最好的方式是範圍的服務主體到特定的資源群組，而不是整個訂用帳戶的權限。

使用 [az ad sp create-for-rbac](/cli/azure/ad/sp) 建立服務主體，並輸出 OpenShift 所需的認證。

下列範例會建立服務主體，並將其參與者權限指派給名為 openshiftrg 的資源群組。

首先，建立名為 openshiftrg 的資源群組：

```azurecli
az group create -l eastus -n openshiftrg
```

建立服務主體：

```azurecli
scope=`az group show --name openshiftrg --query id`
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --password {Strong Password} \
      --scopes $scope
```
如果您使用 Windows，請執行 ```az group show --name openshiftrg --query id``` 並以輸出代替 $scope。

記下命令傳回的 appId 屬性：
```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```
 > [!WARNING] 
 > 請務必建立安全的密碼。 請依照 [Azure AD 密碼規則和限制](/azure/active-directory/active-directory-passwords-policy)指引。

如需有關服務主體的詳細資訊，請參閱[使用 Azure CLI 建立 Azure 服務主體](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)。

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>僅適用於 Resource Manager 範本的必要條件

祕密都要建立的 SSH 私密金鑰 (**sshPrivateKey**)，Azure AD 用戶端祕密 (**aadClientSecret**)，OpenShift 系統管理員密碼 (**openshiftPassword**)，和 Red Hat Subscription Manager 密碼或啟用金鑰 (**rhsmPasswordOrActivationKey**)。  此外，如果使用的自訂 SSL 憑證，則六個額外的密碼必須要建立- **routingcafile**， **routingcertfile**， **routingkeyfile**， **mastercafile**， **mastercertfile**，並**masterkeyfile**。  這些參數將會更詳細地說明。

範本參考特定的祕密名稱讓您**必須**使用粗體名稱上列 （區分大小寫）。

### <a name="custom-certificates"></a>自訂憑證

根據預設，範本會部署 OpenShift 叢集使用自我簽署的憑證的 OpenShift web 主控台和路由網域。 如果您想要使用的自訂 SSL 憑證，設定 'routingCertType' 到 'custom' 和 'masterCertType' 到 'custom'。  您必須將憑證的.pem 格式的 CA、 憑證，以及金鑰檔案。  您可使用一個，但沒有其他自訂的憑證。

您必須將這些檔案儲存在 Key Vault 祕密。  使用相同的金鑰保存庫所使用的私密金鑰。  而不是 6 的其他輸入所需的祕密的名稱，範本就會是硬式編碼為使用特定的祕密名稱，每個 SSL 憑證檔案。  儲存憑證資料，使用下表中的資訊。

| 祕密名稱      | 憑證檔案   |
|------------------|--------------------|
| mastercafile     | 主要的 CA 檔案     |
| mastercertfile   | 主要憑證檔案   |
| masterkeyfile    | 主要金鑰檔案    |
| routingcafile    | 路由的 CA 檔案    |
| routingcertfile  | 路由的憑證檔案  |
| routingkeyfile   | 路由的金鑰檔案   |

建立使用 Azure CLI 的祕密。 以下是範例。

```bash
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>後續步驟

本文涵蓋下列主題：
> [!div class="checklist"]
> * 建立金鑰保存庫來管理 OpenShift 叢集的 SSH 金鑰。
> * 建立可供 Azure 雲端解決方案提供者使用的服務主體。

接下來，部署 OpenShift 叢集：

- [部署 OpenShift 容器平台](./openshift-container-platform.md)
- [部署 OpenShift 容器平台自我管理的 Marketplace 供應項目](./openshift-marketplace-self-managed.md)