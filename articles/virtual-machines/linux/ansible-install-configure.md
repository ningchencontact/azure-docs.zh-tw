---
title: 安裝及設定 Ansible 以搭配 Azure 虛擬機器使用 | Microsoft Docs
description: 了解如何安裝及設定 Ansible，以便管理 Ubuntu、CentOS 和 SLES 上的 Azure 資源
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/04/2018
ms.author: cynthn
ms.openlocfilehash: e7d57ead2caff87db07380582b9085b831844f1e
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37930064"
---
# <a name="install-and-configure-ansible-to-manage-virtual-machines-in-azure"></a>安裝及設定 Ansible 來管理 Azure 中的虛擬機器

Ansible 可讓您將環境中的資源部署和設定自動化。 您可以使用 Ansible 在 Azure 中管理虛擬機器 (VM)，就像是任何其他資源一樣。 本文詳細說明如何針對某些最常見的 Linux 發行版，安裝 Ansible 和必要的 Azure Python SDK 模組。 您可以配合特定的平台調整安裝的套件，來將 Ansible 安裝在其他發行版上。 為了以安全的方式建立 Azure 資源，您也將了解如何建立及定義 Ansible 所要使用的認證。

如需其他平台的更多安裝選項和步驟，請參閱 [Ansible 安裝指南](https://docs.ansible.com/ansible/intro_installation.html)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 2.0.30 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

## <a name="install-ansible"></a>安裝 Ansible

搭配 Azure 使用 Ansible 的其中一個最簡單方式是使用 Azure Cloud Shell 這個瀏覽器型的殼層經驗，其可用於管理和開發 Azure 資源。 Ansible 會預先安裝在 Cloud Shell 中，因此您可以跳過如何安裝 Ansible 的指示，並移至[建立 Azure 認證](#create-azure-credentials)。 如需也可在 Cloud Shell 中使用的其他工具清單，請參閱 [Azure Cloud Shell 中的 Bash 功能和工具](../../cloud-shell/features.md#tools)。

下列指示說明如何針對各種散發版本建立 Linux VM，然後再安裝 Ansible。 如果您不需要建立 Linux VM，請略過這第一個用來建立 Azure 資源群組的步驟。 如果您需要建立 VM，則請先使用 [az group create](/cli/azure/group#az_group_create) 建立資源群組。 下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組：

```azurecli
az group create --name myResourceGroup --location eastus
```

現在，選取下列其中一個散發版本，以了解如何在有需要時建立 VM，然後再安裝 Ansible：

- [CentOS 7.4](#centos-74)
- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

如有需要，請使用 [az vm create](/cli/azure/vm#az_vm_create) 建立 VM。 下列範例會建立名為 *myVMAnsible* 的 VM：

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image OpenLogic:CentOS:7.4:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

使用 VM 建立作業中的輸出所記錄的 `publicIpAddress` SSH 到您的 VM：

```bash
ssh azureuser@<publicIpAddress>
```

在您的 VM 上，安裝 Azure Python SDK 模組和 Ansible 所需的套件，如下所示：

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

現在繼續前往[建立 Azure 認證](#create-azure-credentials)。

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

如有需要，請使用 [az vm create](/cli/azure/vm#az_vm_create) 建立 VM。 下列範例會建立名為 *myVMAnsible* 的 VM：

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

使用 VM 建立作業中的輸出所記錄的 `publicIpAddress` SSH 到您的 VM：

```bash
ssh azureuser@<publicIpAddress>
```

在您的 VM 上，安裝 Azure Python SDK 模組和 Ansible 所需的套件，如下所示：

```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

現在繼續前往[建立 Azure 認證](#create-azure-credentials)。

### <a name="sles-12-sp2"></a>SLES 12 SP2

如有需要，請使用 [az vm create](/cli/azure/vm#az_vm_create) 建立 VM。 下列範例會建立名為 *myVMAnsible* 的 VM：

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image SUSE:SLES:12-SP2:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

使用 VM 建立作業中的輸出所記錄的 `publicIpAddress` SSH 到您的 VM：

```bash
ssh azureuser@<publicIpAddress>
```

在您的 VM 上，安裝 Azure Python SDK 模組和 Ansible 所需的套件，如下所示：

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
    python-devel libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]

# Remove conflicting Python cryptography package
sudo pip uninstall -y cryptography
```

現在繼續前往[建立 Azure 認證](#create-azure-credentials)。

## <a name="create-azure-credentials"></a>建立 Azure 認證

Ansible 會使用使用者名稱與密碼或服務主體與 Azure 進行通訊。 Azure 服務主體是安全性識別，可供您與應用程式、服務及諸如 Ansible 等自動化工具搭配使用。 您可以控制和定義對於服務主體可以在 Azure 中執行哪些作業的權限。 為了提高只提供使用者名稱和密碼的安全性，此範例會建立基本的服務主體。

在主機電腦上或在 Azure Cloud Shell 中，使用 [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) 建立服務主體。 Ansible 所需的認證會輸出到螢幕上：

```azurecli-interactive
az ad sp create-for-rbac --query '{"client_id": appId, "secret": password, "tenant": tenant}'
```

上述命令的輸出範例如下所示：

```json
{
  "client_id": "eec5624a-90f8-4386-8a87-02730b5410d5",
  "secret": "531dcffa-3aff-4488-99bb-4816c395ea3f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

若要向 Azure 驗證，您也需要使用 [az account show](/cli/azure/account#az-account-show) 取得 Azure 訂用帳戶識別碼：

```azurecli-interactive
az account show --query "{ subscription_id: id }"
```

您將在下一個步驟中使用這兩個命令的輸出。

## <a name="create-ansible-credentials-file"></a>建立 Ansible 認證檔案

若要提供認證給 Ansible，您可以定義環境變數或建立本機認證檔案。 如需如何定義 Ansible 認證的詳細資訊，請參閱 [Providing Credentials to Azure Modules](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules) (提供認證給 Azure 模組)。

針對開發環境，在您的主機 VM 上建立 Ansible 的「認證」檔案。 在上一個步驟的 Ansible 安裝所在 VM 上建立認證檔案：

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

「認證」檔案本身結合了訂用帳戶識別碼與建立服務主體的輸出。 為了符合 client_id、secret 和 tenant 所需，先前 [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) 命令的輸出內容必須相同。 下列範例*認證*檔案顯示符合上述輸出的值。 輸入您自己的值，如下所示︰

```bash
[default]
subscription_id=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
client_id=eec5624a-90f8-4386-8a87-02730b5410d5
secret=531dcffa-3aff-4488-99bb-4816c395ea3f
tenant=72f988bf-86f1-41af-91ab-2d7cd011db47
```

儲存並關閉檔案。

## <a name="use-ansible-environment-variables"></a>使用 Ansible 環境變數

如果您想要使用 Ansible Tower 或 Jenkins 等工具，則需要定義環境變數。 如果您就只要使用 Ansible 用戶端和上一個步驟所建立的 Azure 認證檔案，則可以略過此步驟。 環境變數會定義和 Azure 認證檔案相同的資訊：

```bash
export AZURE_SUBSCRIPTION_ID=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
export AZURE_CLIENT_ID=eec5624a-90f8-4386-8a87-02730b5410d5
export AZURE_SECRET=531dcffa-3aff-4488-99bb-4816c395ea3f
export AZURE_TENANT=72f988bf-86f1-41af-91ab-2d7cd011db47
```

## <a name="next-steps"></a>後續步驟

您現在已安裝 Ansible 和必要的 Azure Python SDK 模組，並已定義 Ansible 所要使用的認證。 了解如何[使用 Ansible 建立 VM](ansible-create-vm.md)。 您也可以了解如何[使用 Ansible 建立完整的 Azure VM 和支援資源](ansible-create-complete-vm.md)。
