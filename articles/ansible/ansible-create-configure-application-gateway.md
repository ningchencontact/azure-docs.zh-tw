---
title: 使用 Ansible 以 Azure 應用程式閘道管理 Web 流量
description: 了解如何使用 Ansible 建立並設定 Azure 應用程式閘道以管理 Web 流量
ms.service: ansible
keywords: ansible, azure, devops, bash, 劇本, 應用程式閘道, 負載平衡器, web 流量
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2018
ms.openlocfilehash: 43e8681e5266f113d466a138abeeda77aff1c18b
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052252"
---
# <a name="manage-web-traffic-with-azure-application-gateway-by-using-ansible"></a>使用 Ansible 以 Azure 應用程式閘道管理 Web 流量

[Azure 應用程式閘道](https://docs.microsoft.com/azure/application-gateway/)是網路流量負載平衡器，可讓您管理 Web 應用程式的流量。

Ansible 有助於將環境中的資源部署和設定自動化。 本文說明如何使用 Ansible 建立應用程式閘道。 此外也說明如何使用閘道管理在 Azure 容器執行個體中執行的兩個 Web 伺服器的流量。

本教學課程說明如何：

> [!div class="checklist"]
> * 設定網路
> * 使用 HTTPD 映像建立兩個 Azure 容器執行個體
> * 在伺服器集區中建立與 Azure 容器執行個體搭配運作的應用程式閘道

## <a name="prerequisites"></a>必要條件

- **Azure 訂用帳戶** - 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> 必須使用 Ansible 2.7，才能執行此教學課程中的下列範例劇本。 

## <a name="create-a-resource-group"></a>建立資源群組

資源群組是在其中部署與管理 Azure 資源的邏輯容器。  

下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組。

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

將此劇本儲存為 *rg.yml*。 若要執行劇本，請使用 **ansible-playbook** 命令，如下所示：

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>建立網路資源

首先，請建立可讓應用程式閘道與其他資源進行通訊的虛擬網路。

下列範例會建立名為 **myVNet** 的虛擬網路、名為 **myAGSubnet** 的子網路，以及名為 **myAGPublicIPAddress** 的公用 IP 位址，且網域名稱為 **mydomain**。

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    vnet_name: myVNet 
    subnet_name: myAGSubnet 
    publicip_name: myAGPublicIPAddress
    publicip_domain: mydomain
  tasks:
    - name: Create a virtual network
      azure_rm_virtualnetwork:
        name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
            - 10.1.0.0/16
            - 172.100.0.0/16
        dns_servers:
            - 127.0.0.1
            - 127.0.0.2

    - name: Create a subnet
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: 10.1.0.0/24

    - name: Create a public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}" 
        allocation_method: Dynamic
        name: "{{ publicip_name }}"
        domain_name_label: "{{ publicip_domain }}"
```

請將此劇本儲存為 *vnet_create.yml*。 若要執行劇本，請使用 **ansible-playbook** 命令，如下所示：

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>建立伺服器

下列範例將說明如何使用 HTTPD 映像建立兩個 Azure 容器執行個體，作為應用程式閘道的 Web 伺服器。  

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Create a container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_1_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80

    - name: Create another container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_2_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80
```

請將此劇本儲存為 *aci_create.yml*。 若要執行劇本，請使用 **ansible-playbook** 命令，如下所示：

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>建立應用程式閘道

下列範例會使用後端、前端與 HTTP 的設定建立名為 **myAppGateway** 的應用程式閘道。  

* **appGatewayIP** 定義於 **gateway_ip_configurations** 區塊中。 閘道的 IP 設定需要子網路參考。
* **appGatewayBackendPool** 定義於 **backend_address_pools** 區塊中。 應用程式閘道必須至少有一個後端位址集區。
* **appGatewayBackendHttpSettings** 定義於 **backend_http_settings_collection** 區塊中。 它會指定以連接埠 80 和 HTTP 通訊協定來進行通訊。
* **appGatewayHttpListener** 定義於 **backend_http_settings_collection** 區塊中。 這是與 appGatewayBackendPool 相關聯的預設接聽程式。
* **appGatewayFrontendIP** 定義於 **frontend_ip_configurations** 區塊中。 它會將 myAGPublicIPAddress 指派給 appGatewayHttpListener。
* **rule1** 定義於 **request_routing_rules** 區塊中。 這是與 appGatewayHttpListener 相關聯的預設路由規則。

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    vnet_name: myVNet
    subnet_name: myAGSubnet
    location: eastus
    publicip_name: myAGPublicIPAddress
    appgw_name: myAppGateway
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Get info of Subnet
      azure_rm_resource_facts:
        api_version: '2018-08-01'
        resource_group: "{{ resource_group }}"
        provider: network
        resource_type: virtualnetworks
        resource_name: "{{ vnet_name }}"
        subresource:
          - type: subnets
            name: "{{ subnet_name }}"
      register: subnet

    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_1_name }}"
      register: aci_1_output
    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_2_name }}"
      register: aci_2_output

    - name: Create instance of Application Gateway
      azure_rm_appgateway:
        resource_group: "{{ resource_group }}"
        name: "{{ appgw_name }}"
        sku:
          name: standard_small
          tier: standard
          capacity: 2
        gateway_ip_configurations:
          - subnet:
              id: "{{ subnet.response[0].id }}"
            name: appGatewayIP
        frontend_ip_configurations:
          - public_ip_address: "{{ publicip_name }}"
            name: appGatewayFrontendIP
        frontend_ports:
          - port: 80
            name: appGatewayFrontendPort
        backend_address_pools:
          - backend_addresses:
              - ip_address: "{{ aci_1_output.response[0].properties.ipAddress.ip }}"
              - ip_address: "{{ aci_2_output.response[0].properties.ipAddress.ip }}"
            name: appGatewayBackendPool
        backend_http_settings_collection:
          - port: 80
            protocol: http
            cookie_based_affinity: enabled
            name: appGatewayBackendHttpSettings
        http_listeners:
          - frontend_ip_configuration: appGatewayFrontendIP
            frontend_port: appGatewayFrontendPort
            name: appGatewayHttpListener
        request_routing_rules:
          - rule_type: Basic
            backend_address_pool: appGatewayBackendPool
            backend_http_settings: appGatewayBackendHttpSettings
            http_listener: appGatewayHttpListener
            name: rule1
```

請將此劇本儲存為 *appgw_create.yml*。 若要執行劇本，請使用 **ansible-playbook** 命令，如下所示：

```bash
ansible-playbook appgw_create.yml
```

建立應用程式閘道可能需要幾分鐘的時間。

## <a name="test-the-application-gateway"></a>測試應用程式閘道

在網路資源的範例劇本中，您已在 **eastus** 中建立網域 **mydomain**。 請在瀏覽器中移至 `http://mydomain.eastus.cloudapp.azure.com`。 如果您看到下列頁面，表示應用程式閘道正常運作。

![運作中的應用程式閘道測試成功](media/ansible-create-configure-application-gateway/applicationgateway.PNG)

## <a name="clean-up-resources"></a>清除資源

如果您不需要這些資源，可以執行下列程式碼將其刪除。 它會刪除名為 **myResourceGroup** 的資源群組。

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

請將此劇本儲存為 *rg_delete*.yml。 若要執行劇本，請使用 **ansible-playbook** 命令，如下所示：

```bash
ansible-playbook rg_delete.yml
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Ansible on Azure](https://docs.microsoft.com/azure/ansible/)
