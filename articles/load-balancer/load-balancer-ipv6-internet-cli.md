---
title: "IPv6-Azure CLI 建立公用負載平衡器 |Microsoft 文件"
description: "了解如何使用 Azure CLI ipv6 的 Azure 資源管理員中建立公用負載平衡器。"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
keywords: "ipv6, azure load balancer, 雙重堆疊, 公用 ip, 原生 ipv6, 行動, iot"
ms.assetid: a1957c9c-9c1d-423e-9d5c-d71449bc1f37
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 3abd47460999f7b059469a58a59a3e297e88effb
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2017
---
# <a name="create-a-public-load-balancer-with-ipv6-in-azure-resource-manager-by-using-azure-cli"></a>建立公用負載平衡器使用 IPv6 Azure 資源管理員中，使用 Azure CLI

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [範本](load-balancer-ipv6-internet-template.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Azure 負載平衡器是第 4 層 (TCP、UDP) 負載平衡器。 負載平衡器提供高可用性，分散在雲端服務中的狀況良好的服務執行個體或虛擬機器中負載平衡器集之間的連入流量。 負載平衡器也會有多個連接埠或多個 IP 位址或兩者上的這些服務。

## <a name="example-deployment-scenario"></a>範例部署案例

下圖說明負載平衡解決方案，您可以使用本文中所述的範例範本部署。

![負載平衡器案例](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

在此案例中，您可以建立下列 Azure 資源：

* 兩部虛擬機器 (Vm)
* 每個 VM 指派的 IPv4 和 IPv6 位址在虛擬網路介面
* IPv4 和 IPv6 公用 IP 位址與公用負載平衡器
* 包含兩個 Vm 的可用性設定組
* 兩個負載平衡規則，以對應至私用端點的公用 Vip

## <a name="deploy-the-solution-by-using-azure-cli"></a>使用 Azure CLI 部署方案

下列步驟示範如何使用 Azure CLI Azure Resource Manager 建立公用負載平衡器。 使用 Azure 資源管理員中，您建立，並個別設定每個物件，然後將它們以建立資源。

若要部署負載平衡器，建立並設定下列物件：

* **前端 IP 組態**： 包含公用 IP 位址的連入網路流量。
* **後端位址集區**： 接收從負載平衡器的網路流量的虛擬機器包含網路介面 (Nic)。
* **負載平衡規則**： 包含規則，將負載平衡器上的公用連接埠對應至後端位址集區中的連接埠。
* **輸入 NAT 規則**： 包含將負載平衡器上的公用連接埠對應到特定的虛擬機器中的後端位址集區的連接埠的網路位址轉譯 (NAT) 規則。
* **探查**： 包含用來檢查虛擬機器執行個體中的後端位址集區可用性的健全狀態探查。

如需詳細資料，請參閱 [Azure Resource Manager 的負載平衡器支援](load-balancer-arm.md)。

## <a name="set-up-your-azure-cli-environment-to-use-azure-resource-manager"></a>若要使用 Azure 資源管理員設定 Azure CLI 環境

在此範例中，您可以執行 Azure CLI 工具在 PowerShell 命令視窗中。 若要改善可讀性和重複使用，您可以使用 PowerShell 的指令碼的功能，Azure PowerShell cmdlet。

1. 如果您從未使用過 Azure CLI，請參閱[安裝及設定 Azure CLI](../cli-install-nodejs.md)並遵循指示，選取您的 Azure 帳戶和訂用帳戶為止。

2. 若要切換至 Resource Manager 模式，請執行**azure 組態模式**命令：

    ```azurecli
    azure config mode arm
    ```

    預期的輸出：

        info:    New mode is arm

3. 登入 Azure，並取得訂閱的清單：

    ```azurecli
    azure login
    ```

4. 在提示中，輸入您的 Azure 認證：

    ```azurecli
    azure account list
    ```

5. 選取您要使用的訂用帳戶，並記下一個步驟中所使用的訂用帳戶識別碼。

6. 設定 Azure CLI 命令搭配使用的 PowerShell 變數：

    ```powershell
    $subscriptionid = "########-####-####-####-############"  # enter subscription id
    $location = "southcentralus"
    $rgName = "pscontosorg1southctrlus09152016"
    $vnetName = "contosoIPv4Vnet"
    $vnetPrefix = "10.0.0.0/16"
    $subnet1Name = "clicontosoIPv4Subnet1"
    $subnet1Prefix = "10.0.0.0/24"
    $subnet2Name = "clicontosoIPv4Subnet2"
    $subnet2Prefix = "10.0.1.0/24"
    $dnsLabel = "contoso09152016"
    $lbName = "myIPv4IPv6Lb"
    ```

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>建立資源群組、負載平衡器、虛擬網路和子網路

1. 建立資源群組：

    ```azurecli
    azure group create $rgName $location
    ```

2. 建立負載平衡器：

    ```azurecli
    $lb = azure network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. 建立虛擬網路：

    ```azurecli
    $vnet = azure network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

4. 在此虛擬網路中，建立兩個子網路：

    ```azurecli
    $subnet1 = azure network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = azure network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>建立前端集區的公用 IP 位址

1. 設定的 PowerShell 變數：

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. 建立公用 IP 位址為前端的 IP 集區：

    ```azurecli
    $publicipV4 = azure network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --ip-version IPv4 --allocation-method Dynamic --domain-name-label $dnsLabel
    $publicipV6 = azure network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --ip-version IPv6 --allocation-method Dynamic --domain-name-label $dnsLabel
    ```

    > [!IMPORTANT]
    > 負載平衡器使用公用 IP 的網域的標籤做為其完整的網域名稱 (FQDN)。 這是一項來自傳統部署的變更，該部署使用雲端服務名稱作為負載平衡器 FQDN。
    >
    > 在此範例中，FQDN 是 *contoso09152016.southcentralus.cloudapp.azure.com*。

## <a name="create-front-end-and-back-end-pools"></a>建立前端和後端集區

在本節中，您可以建立下列的 IP 集區：
* 前端 IP 集區會接收連入網路流量負載平衡器上。
* 後端 IP 集區，讓前端集區傳送的負載平衡網路流量。

1. 設定的 PowerShell 變數：

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. 建立前端的 IP 集區，並且讓它與您在上一個步驟和負載平衡器中建立的公用 IP。

    ```azurecli
    $frontendV4 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-name $publicIpv4Name --lb-name $lbName
    $frontendV6 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-name $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>建立探查、 NAT 規則和負載平衡器規則

此範例會建立下列項目：

* 要檢查連線到 TCP 連接埠 80 的探查規則。
* NAT 規則來轉譯 rdp 的連接埠 3389 至連接埠 3389 上的所有連入流量。\*
* NAT 規則來轉譯至遠端桌面通訊協定 (RDP) 連接埠 3389 的 3391 連接埠上的所有連入流量。\*
* 負載平衡器規則以平衡連接埠 80 的後端集區中的位址的連接埠 80 上的所有連入流量。

\*負載平衡器後方的特定虛擬機器執行個體關聯的 NAT 規則。 到達連接埠 3389 的網路流量會傳送到的特定虛擬機器和與該 NAT 規則相關聯的連接埠。 您必須為 NAT 規則指定通訊協定 (UDP 或 TCP)。 您無法在相同的通訊埠指派兩個通訊協定。

1. 設定的 PowerShell 變數：

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. 建立探查。

    下列範例會建立一個 TCP 探查，檢查連線到後端 TCP 連接埠 80 每 15 秒。 在兩個連續失敗次數之後, 會將標示為無法使用的後端資源。

    ```azurecli
    $probeV4V6 = azure network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --count 2 --lb-name $lbName
    ```

3. 建立允許 RDP 連線的後端資源的輸入的 NAT 規則：

    ```azurecli
    $inboundNatRuleRdp1 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. 建立負載平衡器規則，將流量傳送至不同的後端連接埠，根據接收到要求的前端。

    ```azurecli
    $lbruleIPv4 = azure network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-address-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
    $lbruleIPv6 = azure network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-address-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
    ```

5. 請檢查您的設定：

    ```azurecli
    azure network lb show --resource-group $rgName --name $lbName
    ```

    預期的輸出：

        info:    Executing command network lb show
        info:    Looking up the load balancer "myIPv4IPv6Lb"
        data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/pscontosorg1southctrlus09152016/providers/Microsoft.Network/loadBalancers/myIPv4IPv6Lb
        data:    Name                            : myIPv4IPv6Lb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : southcentralus
        data:    Provisioning state              : Succeeded
        data:
        data:    Frontend IP configurations:
        data:    Name             Provisioning state  Private IP allocation  Private IP   Subnet  Public IP
        data:    ---------------  ------------------  ---------------------  -----------  ------  ---------
        data:    FrontendVipIPv4  Succeeded           Dynamic                                     myIPv4Vip
        data:    FrontendVipIPv6  Succeeded           Dynamic                                     myIPv6Vip
        data:
        data:    Probes:
        data:    Name                 Provisioning state  Protocol  Port  Path  Interval  Count
        data:    -------------------  ------------------  --------  ----  ----  --------  -----
        data:    ProbeForIPv4AndIPv6  Succeeded           Tcp       80          15        2
        data:
        data:    Backend Address Pools:
        data:    Name             Provisioning state
        data:    ---------------  ------------------
        data:    BackendPoolIPv4  Succeeded
        data:    BackendPoolIPv6  Succeeded
        data:
        data:    Load Balancing Rules:
        data:    Name                  Provisioning state  Load distribution  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    --------------------  ------------------  -----------------  --------  -------------  ------------  ------------------  -----------------------
        data:    LBRuleForIPv4-Port80  Succeeded           Default            Tcp       80             80            false               4
        data:    LBRuleForIPv6-Port80  Succeeded           Default            Tcp       80             8080          false               4
        data:
        data:    Inbound NAT Rules:
        data:    Name                 Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    -------------------  ------------------  --------  -------------  ------------  ------------------  -----------------------
        data:    NatRule-For-Rdp-VM1  Succeeded           Tcp       3389           3389          false               4
        data:    NatRule-For-Rdp-VM2  Succeeded           Tcp       3391           3389          false               4
        info:    network lb show

## <a name="create-nics"></a>建立 NIC

建立 Nic，並將其與 NAT 規則、 負載平衡器規則和探查關聯。

1. 設定的 PowerShell 變數：

    ```powershell
    $nic1Name = "myIPv4IPv6Nic1"
    $nic2Name = "myIPv4IPv6Nic2"
    $subnet1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet1Name"
    $subnet2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet2Name"
    $backendAddressPoolV4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV4Name"
    $backendAddressPoolV6Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV6Name"
    $natRule1V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule1V4Name"
    $natRule2V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule2V4Name"
    ```

2. 建立的每個後端 NIC，並新增 IPv6 設定：

    ```azurecli
    $nic1 = azure network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-version "IPv4" --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule1V4Id
    $nic1IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = azure network nic create --name $nic2Name --resource-group $rgname --location $location --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule2V4Id
    $nic2IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>建立後端 VM 資源，並附加每個 NIC

若要建立 VM，您必須有儲存體帳戶。 為了負載平衡，VM 必須是可用性設定組的成員。 如需建立 Vm 的詳細資訊，請參閱[使用 PowerShell 建立 Azure VM](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)。

1. 設定的 PowerShell 變數：

    ```powershell
    $storageAccountName = "ps08092016v6sa0"
    $availabilitySetName = "myIPv4IPv6AvailabilitySet"
    $vm1Name = "myIPv4IPv6VM1"
    $vm2Name = "myIPv4IPv6VM2"
    $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
    $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
    $disk1Name = "WindowsVMosDisk1"
    $disk2Name = "WindowsVMosDisk2"
    $osDisk1Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk1Name.vhd"
    $osDisk2Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk2Name.vhd"
    $imageurn "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
    $vmUserName = "vmUser"
    $mySecurePassword = "PlainTextPassword*1"
    ```

    > [!WARNING]
    > 此範例會使用使用者名稱和密碼以純文字的 vm。 當您使用這些認證以純文字時，會謹慎。 處理在 PowerShell 中的認證以更安全方法，請參閱[ `Get-Credential` ](https://technet.microsoft.com/library/hh849815.aspx) cmdlet。

2. 建立儲存體帳戶和可用性集。

    當您建立 Vm 時，您可以使用現有的儲存體帳戶。 您可以使用下列命令，以建立新的儲存體帳戶：

    ```azurecli
    $storageAcc = azure storage account create $storageAccountName --resource-group $rgName --location $location --sku-name "LRS" --kind "Storage"
    ```

3. 建立可用性設定組：

    ```azurecli
    $availabilitySet = azure availset create --name $availabilitySetName --resource-group $rgName --location $location
    ```

4. 建立虛擬機器有相關聯的 Nic:

    ```azurecli
    $vm1 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm1Name --nic-id $nic1Id --os-disk-vhd $osDisk1Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension

    $vm2 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm2Name --nic-id $nic2Id --os-disk-vhd $osDisk2Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension
    ```

## <a name="next-steps"></a>後續步驟

[開始設定內部負載平衡器](load-balancer-get-started-ilb-arm-cli.md)  
[設定負載平衡器分配模式](load-balancer-distribution-mode.md)  
[設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)
