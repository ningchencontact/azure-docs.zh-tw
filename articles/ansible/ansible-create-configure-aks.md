---
title: 使用 Ansible 在 Azure 中建立並設定 Azure Kubernetes Service 叢集
description: 了解如何使用 Ansible 在 Azure 中建立並管理 Azure Kubernetes Service 叢集
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, 劇本, aks, 容器, Kubernetes
author: tomarcher
manager: jpconnock
editor: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 07/11/2018
ms.author: tarcher
ms.openlocfilehash: 6d7c5f961256e0ae1831bd76353cadd761f4b8ac
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011986"
---
# <a name="create-and-configure-azure-kubernetes-service-clusters-in-azure-using-ansible"></a>使用 Ansible 在 Azure 中建立並設定 Azure Kubernetes Service 叢集
Ansible 可讓您將環境中的資源部署和設定自動化。 您可以使用 Ansible 來管理 Azure Kubernetes Service (AKS)。 本文說明如何使用 Ansible 建立並設定 Azure Kubernetes Service 叢集。

## <a name="prerequisites"></a>必要條件
- **Azure 訂用帳戶** - 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- **設定 Ansible** - [建立 Azure 認證和設定 Ansible](../virtual-machines/linux/ansible-install-configure.md#create-azure-credentials)
- **Ansible 和 Azure Python SDK 模組** 
  - [CentOS 7.4](../virtual-machines/linux/ansible-install-configure.md#centos-74)
  - [Ubuntu 16.04 LTS](../virtual-machines/linux/ansible-install-configure.md#ubuntu-1604-lts)
  - [SLES 12 SP2](../virtual-machines/linux/ansible-install-configure.md#sles-12-sp2)
- **Azure 服務主體** - 在[建立服務主體](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal)時，請記下下列值：**appId**、**displayName**、**password** 和 **tenant**。

> [!Note]
> 必須有 Ansible 2.6 才能執行本教學課程中的下列劇本範例。 

## <a name="create-a-managed-aks-cluster"></a>建立受控 AKS 叢集
下列 Ansible 劇本範例會建立一個資源群組，以及一個位於資源群組中的 AKS 叢集：

  ```yaml
  - name: Create Azure Kubernetes Service
    hosts: localhost
    connection: local
    vars:
      resource_group: myResourceGroup
      location: eastus
      aks_name: myAKSCluster
      username: azureuser
      ssh_key: "your_ssh_key"
      client_id: "your_client_id"
      client_secret: "your_client_secret"
    tasks:
    - name: Create resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    - name: Create a managed Azure Container Services (AKS) cluster
      azure_rm_aks:
        name: "{{ aks_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        dns_prefix: "{{ aks_name }}"
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 2
            vm_size: Standard_D2_v2
        tags:
          Environment: Production
  ```

下面幾點有助於解釋上述 Ansible 劇本的程式碼：
- **tasks** 內的第一個區段會定義名為 **myResourceGroup** 且位於 **eastus** 位置內的資源群組。 
- **tasks** 內的第二個區段會定義名為 **myAKSCluster** 且位於 **myResourceGroup** 資源群組內的 AKS 叢集。 

若要使用 Ansible 建立 AKS 叢集，請將上述劇本範例儲存為 `azure_create_aks.yml`，並使用下列命令執行劇本：

  ```bash
  ansible-playbook azure_create_aks.yml
  ```

*ansible-playbook 命令的輸出與下列顯示已成功建立 AKS 叢集的內容類似：

  ```bash
  PLAY [Create AKS] ****************************************************************************************

  TASK [Gathering Facts] ********************************************************************************************
  ok: [localhost]

  TASK [Create resource group] **************************************************************************************
  changed: [localhost]

  TASK [Create a Azure Container Services (AKS) cluster] ***************************************************
  changed: [localhost]

  PLAY RECAP *********************************************************************************************************
  localhost                  : ok=3    changed=2    unreachable=0    failed=0
  ```

## <a name="scale-aks-nodes"></a>調整 AKS 節點

上一節中的劇本範例會定義兩個節點。 如果叢集上需要較少或較多的容器工作負載，您可以輕鬆地調整節點數目。 本節中的劇本範例會將節點數從兩個增加為三個。 變更 **agent_pool_profiles** 區塊中的 **count** 值，即可修改節點計數。 

在 **service_principal** 區塊中輸入您自己的 `ssh_key`、`client_id` 和 `client_secret`：

```yaml
- name: Scale AKS cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
  tasks:
  - name: Scaling an existed AKS cluster
    azure_rm_aks:
        name: "{{ aks_name }}"    
        location: "{{ location }}"
        resource_group: "{{ resource_group }}" 
        dns_prefix: "{{ aks_name }}" 
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 3
            vm_size: Standard_D2_v2
```

若要使用 Ansible 調整 Azure Kubernetes Service 叢集，請將上述劇本儲存為 azure_configure_aks.yml，並如下所示執行劇本：

  ```bash
  ansible-playbook azure_configure_aks.yml
  ```

下列輸出顯示您已成功建立 AKS 叢集：

  ```bash
  PLAY [Scale AKS cluster] ***************************************************************

  TASK [Gathering Facts] ******************************************************************
  ok: [localhost]

  TASK [Scaling an existed AKS cluster] **************************************************
  changed: [localhost]

  PLAY RECAP ******************************************************************************
  localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"] 
> [教學課程：調整 Azure Kubernetes Service (AKS) 中的應用程式](https://docs.microsoft.com/en-us/azure/aks/tutorial-kubernetes-scale)