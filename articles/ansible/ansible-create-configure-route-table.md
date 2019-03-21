---
title: 使用 Ansible 建立、變更或刪除 Azure 路由表
description: 了解如何使用 Ansible 建立、變更或刪除路由表
ms.service: azure
keywords: ansible, azure, devops, bash, 劇本, 網路, 路由, 路由表
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/17/2018
ms.openlocfilehash: 025a8182d32a7d0d00a48795c848d356eb1c3d4e
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792441"
---
# <a name="create-change-or-delete-an-azure-route-table-using-ansible"></a>使用 Ansible 建立、變更或刪除 Azure 路由表
Azure 會自動路由傳送 Azure 子網路、虛擬網路及內部部署網路之間的流量。 如果您想要在 Azure 上變更任何預設路由，可藉由建立[路由表](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)來執行此動作。

Ansible 可讓您將環境中的資源部署和設定自動化。 本文示範如何建立、變更或刪除 Azure 路由表，以及如何將路由表附加至子網路。 

## <a name="prerequisites"></a>必要條件
- **Azure 訂用帳戶** - 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> 必須使用 Ansible 2.7，才能執行本教學課程中的下列範例劇本。

## <a name="create-a-route-table"></a>建立路由表
本節提供範例 Ansible 劇本來建立路由表。 每個 Azure 位置和訂用帳戶可以建立的路由表數目有所限制。 如需詳細資訊，請參閱 [Azure 限制](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
```

將此劇本儲存為 `route_table_create.yml`。 若要執行劇本，請使用 **ansible-playbook** 命令，如下所示：

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>建立路由表與子網路的關聯
子網路可以有零個或一個與其相關聯的路由表。 路由表可以與零個或多個子網路建立關聯。 由於路由表並未與虛擬網路建立關聯，所以您必須和想要路由表與之相關聯的每個子網路建立關聯。 如果虛擬網路連線到 Azure 虛擬網路閘道 (ExpressRoute 或在利用 VPN 閘道使用 BGP 時為 VPN)，離開子網路的所有流量都會根據您在路由表內建立的路由、[預設路由](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#default)以及從內部部署網路傳播的路由來路由傳送。 您只能在以路由表形式存在於相同的 Azure 位置和訂用帳戶的虛擬網路中，將路由表與子網路建立關聯。

本節說明如何使用範例 Ansible 劇本來建立虛擬網路和子網路，然後將路由表與子網路產生關聯。

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create virtual network
      azure_rm_virtualnetwork:
        name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
        - 10.1.0.0/16
        - 172.100.0.0/16
        dns_servers:
        - 127.0.0.1
        - 127.0.0.3
    - name: Create a subnet with route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
        route_table: "{ route_table_name }"
```

將此劇本儲存為 `route_table_associate.yml`。 若要執行 Ansible 腳本，請使用 **ansible-playbook** 命令，如下所示：

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>從子網路中斷與路由表的關聯
當您中斷路由表與子網路的關聯時，只需將子網路中的 `route_table` 設為 `None`。 以下是範例 Ansible 劇本。 

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    resource_group: myResourceGroup
  tasks:
    - name: Dissociate a route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
```

將此劇本儲存為 `route_table_dissociate.yml`。 若要執行 Ansible 腳本，請使用 **ansible-playbook** 命令，如下所示：

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>建立路由
本節提供範例 Ansible 劇本，可在路由表下方建立路由。 它會將 `virtual_network_gateway` 定義為 `next_hop_type`，並將 `10.1.0.0/16` 定義為 `address_prefix`。 雖然首碼可以在另一個首碼內，但不可在路由表內的多個路由中重複。 若要深入了解 Azure 選取路由的方式及所有後續躍點類型的詳細說明，請參閱[路由概觀](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)。

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        next_hop_type: virtual_network_gateway
        address_prefix: "10.1.0.0/16"
        route_table_name: "{{ route_table_name }}"
```
將此劇本儲存為 `route_create.yml`。 若要執行 Ansible 腳本，請使用 **ansible-playbook** 命令，如下所示：

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>刪除路由
本節提供範例 Ansible 劇本，可從路由表刪除路由。

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Remove route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        route_table_name: "{{ route_table_name }}"
        state: absent
```

將此劇本儲存為 `route_delete.yml`。 若要執行 Ansible 腳本，請使用 **ansible-playbook** 命令，如下所示：

```bash
ansible-playbook route_delete.yml
```

## <a name="get-information-of-a-route-table"></a>取得路由表的資訊
您可以透過名為 `azure_rm_routetable_facts` 的 Ansible 模組來檢視 route_table 的詳細資料。 事實模組將會傳回路由表的資訊以及所有已附加至該路由表的路由。
以下是範例 Ansible 劇本。 

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks: 
    - name: Get route table information
      azure_rm_routetable_facts:
         resource_group: "{{ resource_group }}"
         name: "{{ route_table_name }}"
      register: query
    
    - debug:
         var: query.route_tables[0]
```

將此劇本儲存為 `route_table_facts.yml`。 若要執行 Ansible 腳本，請使用 **ansible-playbook** 命令，如下所示：

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>刪除路由表
若有任何子網路與路由表相關聯，則無法刪除該路由表。 請從所有子網路與路由表[中斷關聯](#dissociate-a-route-table-from-a-subnet)之後，再嘗試予以刪除。

您可以一併刪除路由表及所有路由。 以下是範例 Ansible 劇本。 

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
```

將此劇本儲存為 `route_table_delete.yml`。 若要執行 Ansible 腳本，請使用 **ansible-playbook** 命令，如下所示：

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"] 
> [Ansible on Azure](https://docs.microsoft.com/azure/ansible/)
