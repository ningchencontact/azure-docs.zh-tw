---
title: 使用 Azure Kubernetes Service (AKS) 建立 Kubernetes 叢集並以應用程式閘道作為輸入控制器
description: 教學課程說明如何使用 Azure Kubernetes Service 建立 Kubernetes 叢集並以應用程式閘道作為輸入控制器
services: terraform
ms.service: azure
keywords: terraform, devops, 虛擬機器, azure, kubernetes, 輸入, 應用程式閘道
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 0373b254a900fd34232bb6863c93802fa7b51aab
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169960"
---
# <a name="create-a-kubernetes-cluster-with-application-gateway-ingress-controller-using-azure-kubernetes-service-and-terraform"></a>使用 Azure Kubernetes Service 和 Terraform 建立 Kubernetes 叢集並以應用程式閘道作為輸入控制器
[Azure Kubernetes Service (AKS)](/azure/aks/) 會管理裝載的 Kubernetes 環境。 AKS 可快速且輕鬆地部署和管理容器化的應用程式，而不需要容器協調流程專業知識。 也可透過佈建、升級與依需求調整資源，以無需讓應用程式離線的方式來消除進行中作業及維護之間的界線。

輸入控制器是一項可為 Kubernetes 服務提供反向 Proxy、可設定的流量路由和 TLS 終止的軟體。 Kubernetes 輸入資源可用來設定個別 Kubernetes 服務的輸入規則和路由。 透過輸入控制器和輸入規則，您可以使用單一 IP 位址將流量路由至 Kubernetes 叢集中的多個服務。 上述所有功能都由 Azure [應用程式閘道](/azure/Application-Gateway/)所提供，因此成為 Azure 上的 Kubernetes 的理想輸入控制器。 

在本教學課程中，您將了解如何執行下列工作，以使用 AKS 建立 [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) 叢集並以應用程式閘道作為輸入控制器：

> [!div class="checklist"]
> * 使用 HCL (HashiCorp 語言) 定義 Kubernetes 叢集
> * 使用 Terraform 建立應用程式閘道資源
> * 使用 Terraform 和 AKS 建立 Kubernetes 叢集
> * 使用 kubectl 工具測試 Kubernetes 叢集的可用性

## <a name="prerequisites"></a>必要條件

- **Azure 訂用帳戶**：如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 。

- **設定 Terraform**：請遵循 [Terraform 和設定 Azure 的存取](/azure/virtual-machines/linux/terraform-install-configure)一文中的指示

- **Azure 服務主體**：請遵循[使用 Azure CLI 建立 Azure 服務主體](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)一文中**建立服務主體**一節的指示。 記下 appId、displayName 和密碼的值。
  - 請執行下列命令，以記下服務主體的物件識別碼

    ```azurecli
    az ad sp list --display-name <displayName>
    ```

## <a name="create-the-directory-structure"></a>建立目錄結構
第一個步驟是先建立目錄，用來存放供練習使用的 Terraform 組態檔。

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。

1. 開啟 [Azure Cloud Shell](/azure/cloud-shell/overview)。 如果您先前未選取環境，請選取 **Bash** 作為您的環境。

    ![Cloud Shell 提示](./media/terraform-k8s-cluster-appgw-with-tf-aks/azure-portal-cloud-shell-button-min.png)

1. 切換至 `clouddrive` 目錄。

    ```bash
    cd clouddrive
    ```

1. 建立名為 `terraform-aks-k8s` 的目錄。

    ```bash
    mkdir terraform-aks-appgw-ingress
    ```

1. 將目錄變更為新的目錄：

    ```bash
    cd terraform-aks-appgw-ingress
    ```

## <a name="declare-the-azure-provider"></a>宣告 Azure 提供者
建立宣告 Azure 提供者的 Terraform 組態檔。

1. 在 Cloud Shell 中建立名稱為 `main.tf` 的檔案。

    ```bash
    vi main.tf
    ```

1. 選取 I 鍵輸入插入模式。

1. 將下列程式碼貼到編輯器中：

    ```hcl
    provider "azurerm" {
        version = "~>1.18"
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. 選取 **Esc** 鍵結束插入模式。

1. 輸入下列命令來儲存檔案及結束 vi 編輯器：

    ```bash
    :wq
    ```

## <a name="define-input-variables"></a>定義輸入變數
建立可列出此部署所需所有變數的 Terraform 組態檔。

1. 在 Cloud Shell 中建立名稱為 `variables.tf` 的檔案。

    ```bash
    vi variables.tf
    ```

1. 選取 I 鍵輸入插入模式。

1. 將下列程式碼貼到編輯器中：
    
    ```hcl
    variable "resource_group_name" {
      description = "Name of the resource group already created."
    }
    
    variable "location" {
      description = "Location of the cluster."
    }
    
    variable "aks_service_principal_app_id" {
      description = "Application ID/Client ID  of the service principal. Used by AKS to manage AKS related resources on Azure like vms, subnets."
    }
    
    variable "aks_service_principal_client_secret" {
      description = "Secret of the service principal. Used by AKS to manage Azure."
    }
    
    variable "aks_service_principal_object_id" {
      description = "Object ID of the service principal."
    }
    
    variable "virtual_network_name" {
      description = "Virtual network name"
      default     = "aksVirtualNetwork"
    }
    
    variable "virtual_network_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/8"
    }
    
    variable "aks_subnet_name" {
      description = "AKS Subnet Name."
      default     = "kubesubnet"
    }
    
    variable "aks_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/16"
    }
    
    variable "app_gateway_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.1.0.0/16"
    }
    
    variable "app_gateway_name" {
      description = "Name of the Application Gateway."
      default = "ApplicationGateway1"
    }
    
    variable "app_gateway_sku" {
      description = "Name of the Application Gateway SKU."
      default = "Standard_v2"
    }
    
    
    variable "app_gateway_tier" {
      description = "Tier of the Application Gateway SKU."
      default = "Standard_v2"
    }
    
    
    variable "aks_name" {
      description = "Name of the AKS cluster."
      default     = "aks-cluster1"
    }
    variable "aks_dns_prefix" {
      description = "Optional DNS prefix to use with hosted Kubernetes API server FQDN."
      default     = "aks"
    }
    
    
    variable "aks_agent_os_disk_size" {
      description = "Disk size (in GB) to provision for each of the agent pool nodes. This value ranges from 0 to 1023. Specifying 0 will apply the default disk size for that agentVMSize."
      default     = 40
    }
    
    variable "aks_agent_count" {
      description = "The number of agent nodes for the cluster."
      default     = 3
    }
    
    variable "aks_agent_vm_size" {
      description = "The size of the Virtual Machine."
      default     = "Standard_D3_v2"
    }
    
    variable "kubernetes_version" {
      description = "The version of Kubernetes."
      default     = "1.11.5"
    }
    
    variable "aks_service_cidr" {
      description = "A CIDR notation IP range from which to assign service cluster IPs."
      default     = "10.0.0.0/16"
    }
    
    variable "aks_dns_service_ip" {
      description = "Containers DNS server IP address."
      default     = "10.0.0.10"
    }
    
    variable "aks_docker_bridge_cidr" {
      description = "A CIDR notation IP for Docker bridge."
      default     = "172.17.0.1/16"
    }
    
    variable "aks_enable_rbac" {
      description = "Enable RBAC on the AKS cluster. Defaults to false."
      default     = "false"
    }
    
    variable "vm_user_name" {
      description = "User name for the VM"
      default     = "vmuser1"
    }
    
    variable "public_ssh_key_path" {
      description = "Public key path for SSH."
      default     = "~/.ssh/id_rsa.pub"
    }
    
    variable "tags" {
      type = "map"
    
      default = {
        source = "terraform"
      }
    }
    ```

## <a name="define-the-resources"></a>定義資源 
建立可建立所有資源的 Terraform 組態檔。 

1. 在 Cloud Shell 中建立名稱為 `resources.tf` 的檔案。

    ```bash
    vi resources.tf
    ```

1. 選取 I 鍵輸入插入模式。

1. 將下列程式碼區塊貼到編輯器中：

    a. 建立要重複使用之計算變數的區域變數區塊。

    ```hcl
    # # Locals block for hardcoded names. 
    locals {
        backend_address_pool_name      = "${azurerm_virtual_network.test.name}-beap"
        frontend_port_name             = "${azurerm_virtual_network.test.name}-feport"
        frontend_ip_configuration_name = "${azurerm_virtual_network.test.name}-feip"
        http_setting_name              = "${azurerm_virtual_network.test.name}-be-htst"
        listener_name                  = "${azurerm_virtual_network.test.name}-httplstn"
        request_routing_rule_name      = "${azurerm_virtual_network.test.name}-rqrt"
        app_gateway_subnet_name = "appgwsubnet"
    }
    ```

    b. 建立資源群組、新使用者身分識別的資料來源。

    ```hcl
    data "azurerm_resource_group" "rg" {
      name = "${var.resource_group_name}"
    }
    
    # User Assigned Idntities 
    resource "azurerm_user_assigned_identity" "testIdentity" {
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      location            = "${data.azurerm_resource_group.rg.location}"
    
      name = "identity1"
    
      tags = "${var.tags}"
    }
    ```

    c. 建立基礎網路資源。

    ```hcl
    resource "azurerm_virtual_network" "test" {
      name                = "${var.virtual_network_name}"
      location            = "${data.azurerm_resource_group.rg.location}"
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      address_space       = ["${var.virtual_network_address_prefix}"]
    
      subnet {
        name           = "${var.aks_subnet_name}"
        address_prefix = "${var.aks_subnet_address_prefix}" 
      }
    
      subnet {
        name           = "appgwsubnet"
        address_prefix = "${var.app_gateway_subnet_address_prefix}"
      }
    
      tags = "${var.tags}"
    }
    
    data "azurerm_subnet" "kubesubnet" {
      name                 = "${var.aks_subnet_name}"
      virtual_network_name = "${azurerm_virtual_network.test.name}"
      resource_group_name  = "${data.azurerm_resource_group.rg.name}"
    }
    
    data "azurerm_subnet" "appgwsubnet" {
      name                 = "appgwsubnet"
      virtual_network_name = "${azurerm_virtual_network.test.name}"
      resource_group_name  = "${data.azurerm_resource_group.rg.name}"
    }
    
    # Public Ip 
    resource "azurerm_public_ip" "test" {
      name                         = "publicIp1"
      location                     = "${data.azurerm_resource_group.rg.location}"
      resource_group_name          = "${data.azurerm_resource_group.rg.name}"
      public_ip_address_allocation = "static"
      sku                          = "Standard"
    
      tags = "${var.tags}"
    }
    ```

    d. 建立應用程式閘道資源。

    ```hcl
    resource "azurerm_application_gateway" "network" {
      name                = "${var.app_gateway_name}"
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      location            = "${data.azurerm_resource_group.rg.location}"
    
      sku {
        name     = "${var.app_gateway_sku}"
        tier     = "Standard_v2"
        capacity = 2
      }
    
      gateway_ip_configuration {
        name      = "appGatewayIpConfig"
        subnet_id = "${data.azurerm_subnet.appgwsubnet.id}"
      }
    
      frontend_port {
        name = "${local.frontend_port_name}"
        port = 80
      }
    
      frontend_port {
        name = "httpsPort"
        port = 443
      }
    
      frontend_ip_configuration {
        name                 = "${local.frontend_ip_configuration_name}"
        public_ip_address_id = "${azurerm_public_ip.test.id}"
      }
    
      backend_address_pool {
        name = "${local.backend_address_pool_name}"
      }
    
      backend_http_settings {
        name                  = "${local.http_setting_name}"
        cookie_based_affinity = "Disabled"
        port                  = 80
        protocol              = "Http"
        request_timeout       = 1
      }
    
      http_listener {
        name                           = "${local.listener_name}"
        frontend_ip_configuration_name = "${local.frontend_ip_configuration_name}"
        frontend_port_name             = "${local.frontend_port_name}"
        protocol                       = "Http"
      }
    
      request_routing_rule {
        name                       = "${local.request_routing_rule_name}"
        rule_type                  = "Basic"
        http_listener_name         = "${local.listener_name}"
        backend_address_pool_name  = "${local.backend_address_pool_name}"
        backend_http_settings_name = "${local.http_setting_name}"
      }
    
      tags = "${var.tags}"
    
      depends_on = ["azurerm_virtual_network.test", "azurerm_public_ip.test"]
    }
    ```

    e. 建立角色指派。

    ```hcl
    resource "azurerm_role_assignment" "ra1" {
      scope                = "${data.azurerm_subnet.kubesubnet.id}"
      role_definition_name = "Network Contributor"
      principal_id         = "${var.aks_service_principal_object_id }"
    
      depends_on = ["azurerm_virtual_network.test"]
    }
    
    resource "azurerm_role_assignment" "ra2" {
      scope                = "${azurerm_user_assigned_identity.testIdentity.id}"
      role_definition_name = "Managed Identity Operator"
      principal_id         = "${var.aks_service_principal_object_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity"]
    }
    
    resource "azurerm_role_assignment" "ra3" {
      scope                = "${azurerm_application_gateway.network.id}"
      role_definition_name = "Contributor"
      principal_id         = "${azurerm_user_assigned_identity.testIdentity.principal_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    
    resource "azurerm_role_assignment" "ra4" {
      scope                = "${data.azurerm_resource_group.rg.id}"
      role_definition_name = "Reader"
      principal_id         = "${azurerm_user_assigned_identity.testIdentity.principal_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    ```

    f. 建立 Kubernetes 叢集。

    ```hcl
    resource "azurerm_kubernetes_cluster" "k8s" {
      name       = "${var.aks_name}"
      location   = "${data.azurerm_resource_group.rg.location}"
      dns_prefix = "${var.aks_dns_prefix}"
    
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
    
      linux_profile {
        admin_username = "${var.vm_user_name}"
    
        ssh_key {
          key_data = "${file(var.public_ssh_key_path)}"
        }
      }
    
      addon_profile {
        http_application_routing {
          enabled = false
        }
      }
    
      agent_pool_profile {
        name            = "agentpool"
        count           = "${var.aks_agent_count}"
        vm_size         = "${var.aks_agent_vm_size}"
        os_type         = "Linux"
        os_disk_size_gb = "${var.aks_agent_os_disk_size}"
        vnet_subnet_id  = "${data.azurerm_subnet.kubesubnet.id}"
      }
    
      service_principal {
        client_id     = "${var.aks_service_principal_app_id}"
        client_secret = "${var.aks_service_principal_client_secret}"
      }
    
      network_profile {
        network_plugin     = "azure"
        dns_service_ip     = "${var.aks_dns_service_ip}"
        docker_bridge_cidr = "${var.aks_docker_bridge_cidr}"
        service_cidr       = "${var.aks_service_cidr}"
      }
    
      depends_on = ["azurerm_virtual_network.test", "azurerm_application_gateway.network"]
      tags       = "${var.tags}"
    }
    
    ```

    上述程式碼可設定叢集名稱、位置和 resource_group_name； 此外，也能設定屬於完整網域名稱 (FQDN) 一部分的 dns_prefix 值 (完整網域名稱可用來存取叢集)。

    **linux_profile** 記錄可讓您配置使用 SSH 登入背景工作角色節點的設定。

    透過 AKS，您只需要為背景工作角色節點付費。 **agent_pool_profile** 記錄可設定這些背景工作角色節點的詳細資料。 **agent_pool_profile 記錄**涵蓋要建立的背景工作角色節點數量，以及背景工作角色節點的類型。 若您日後需要擴充或縮減叢集規模，可修改此記錄中的 **count** 值。

1. 選取 **Esc** 鍵結束插入模式。

1. 輸入下列命令來儲存檔案及結束 vi 編輯器：

    ```bash
    :wq
    ```


## <a name="create-a-terraform-output-file"></a>建立 Terraform 輸出檔
[Terraform 輸出檔](https://www.terraform.io/docs/configuration/outputs.html)可讓您定義 Terraform 套用方案後要對使用者醒目提示的值，以及讓您能使用 `terraform output` 命令查詢此輸出檔。 在本節中，您可建立允許透過 [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) 存取叢集的輸出檔。

1. 在 Cloud Shell 中建立名稱為 `output.tf` 的檔案。

    ```bash
    vi output.tf
    ```

1. 選取 I 鍵輸入插入模式。

1. 將下列程式碼貼到編輯器中：

    ```hcl
    output "client_key" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_key}"
    }

    output "client_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate}"
    }

    output "cluster_ca_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate}"
    }

    output "cluster_username" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.username}"
    }

    output "cluster_password" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.password}"
    }

    output "kube_config" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config_raw}"
    }

    output "host" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.host}"
    }
    ```

1. 選取 **Esc** 鍵結束插入模式。

1. 輸入下列命令來儲存檔案及結束 vi 編輯器：

    ```bash
    :wq
    ```

## <a name="set-up-azure-storage-to-store-terraform-state"></a>設定 Azure 儲存體以儲存 Terraform 狀態
Terraform 可透過 `terraform.tfstate` 檔案在本機追蹤狀態。 此模式在單一人員環境中運作良好。 不過，在更常見的多人環境中，您必須追蹤使用 [Azure 儲存體](/azure/storage/)的伺服器的狀態。 在本節中，您可擷取必要的儲存體帳戶資訊 (帳戶名稱和帳戶金鑰)，以及建立儲存體容器，而系統會將 Terraform 狀態資訊儲存在該容器中。

1. 在 Azure 入口網站中，選取左側功能表中的 [所有服務]  。

1. 選取 [儲存體帳戶]  。

1. 在 [儲存體帳戶]  索引標籤中，選取要讓 Terraform 儲存狀態的帳戶名稱。 例如，您可以使用第一次開啟 Cloud Shell 時建立的儲存體帳戶。  Cloud Shell 建立的儲存體帳戶名稱通常會以 `cs` 開頭，其後加上由數字和字母組成的隨機字串。 **請記下您所選儲存體帳戶的名稱，我們稍後需要用到。**

1. 在 [儲存體帳戶] 索引標籤中，選取 [存取金鑰]  。

    ![[儲存體帳戶] 功能表](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account.png)

1. 請記下 **key1** 的**金鑰**值。 (選取金鑰右邊的圖示，將值複製到剪貼簿。)

    ![儲存體帳戶存取金鑰](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account-access-key.png)

1. 在 Cloud Shell 中，為您的 Azure 儲存體帳戶建立容器 (使用適用於 Azure 儲存體帳戶的適當值，取代 &lt;YourAzureStorageAccountName> 和 &lt;YourAzureStorageAccountAccessKey> 預留位置中的值)。

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>建立 Kubernetes 叢集
在本節中，您可了解如何使用 `terraform init` 命令建立資源，即您在前述小節中建立的組態檔所定義的資源。

1. 在 Cloud Shell 中初始化 Terraform (使用適用於 Azure 儲存體帳戶的適當值，取代 &lt;YourAzureStorageAccountName> 和 &lt;YourAzureStorageAccountAccessKey> 預留位置中的值)。

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    `terraform init` 命令會顯示後端和提供者外掛程式已成功初始化：

    ![「terraform 初始化」結果範例](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-init-complete.png)

1. 建立變數檔案，以提供輸入值。在 Cloud Shell 中，建立一個名為 `main.tf` 的檔案。

    ```bash
    vi terraform.tfvars
    ```

1. 選取 I 鍵輸入插入模式。

1. 將稍早建立的下列變數貼到編輯器中：

    ```hcl
      resource_group_name = <Name of the Resource Group already created>

      location = <Location of the Resource Group>
        
      aks_service_principal_app_id = <Service Principal AppId>
        
      aks_service_principal_client_secret = <Service Principal Client Secret>
        
      aks_service_principal_object_id = <Service Principal Object Id>
        
    ```

1. 選取 **Esc** 鍵結束插入模式。

1. 輸入下列命令來儲存檔案及結束 vi 編輯器：

    ```bash
    :wq
    ```

1. 執行 `terraform plan` 命令以建立用來定義基礎結構元素的 Terraform 方案。 

    ```bash
    terraform plan -out out.plan
    ```

    `terraform plan` 命令可顯示您執行 `terraform apply` 命令時將會建立的資源：

    ![「terraform 方案」結果範例](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-plan-complete.png)

1. 執行 `terraform apply` 命令以套用方案，並建立 Kubernetes 叢集。 建立 Kubernetes 叢集的程序可能需要數分鐘，而這會導致 Cloud Shell 工作階段逾時。如果 Cloud Shell 工作階段逾時，請遵循「從 Cloud Shell 逾時復原」一節中的步驟以完成教學課程。

    ```bash
    terraform apply out.plan
    ```

    `terraform apply` 命令可顯示建立資源 (即組態檔中定義的資源) 的結果：

    ![「terraform 套用」結果範例](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-apply-complete.png)

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]  以查看在選取的資源群組中，針對新 Kubernetese 叢集建立的資源。

    ![Cloud Shell 提示](./media/terraform-k8s-cluster-appgw-with-tf-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>從 Cloud Shell 逾時復原
如果 Cloud Shell 工作階段逾時，您可以使用下列步驟復原：

1. 開啟 Cloud Shell 工作階段。

1. 變更為包含 Terraform 組態檔的目錄。

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. 執行以下命令：

    ```bash
    export KUBECONFIG=./azurek8s
    ```
    
## <a name="test-the-kubernetes-cluster"></a>測試 Kubernetes 叢集
Kubernetes 工具可用來驗證新建立的叢集。

1. 從 Terraform 狀態取得 Kubernetes 組態，並將其儲存在 kubectl 可讀取的檔案中。

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. 設定環境變數，以便 kubectl 挑選正確的組態。

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. 驗證叢集的健康情況。

    ```bash
    kubectl get nodes
    ```

    您可查看背景工作角色節點的詳細資料，這些節點應該都處於 [就緒]  狀態，如下圖所示：

    ![kubectl 工具可讓您驗證 Kubernetes 叢集的健康情況](./media/terraform-k8s-cluster-appgw-with-tf-aks/kubectl-get-nodes.png)


## <a name="next-steps"></a>後續步驟
在本文中，您可了解如何使用 Terraform 和 AKS 建立 Kubernetes 叢集。 以下有一些額外的資源，可協助您深入了解 Azure 上的 Terraform。
 
 > [!div class="nextstepaction"] 
 > [Microsoft.com 中的 Terraform 中樞](https://docs.microsoft.com/azure/terraform/)
 
