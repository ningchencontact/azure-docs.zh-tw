---
title: 使用专用前端 IP 地址配置 Azure 应用程序网关
description: 本文提供有关如何使用专用前端 IP 地址配置应用程序网关的信息
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/26/2019
ms.author: absha
ms.openlocfilehash: cfc63349e20aa6dbef4e0d31e81842d325bd3ec6
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905532"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>使用内部负载均衡器 (ILB) 终结点配置应用程序网关

可向 Azure 应用程序网关配置面向 Internet 的 VIP 或不向 Internet 公开的内部终结点（也称为内部负载均衡器 (ILB) 终结点（使用前端 IP 地址的专用 IP）。 对于不向 Internet 公开的内部业务线应用程序，使用前端专用 IP 地址配置网关的做法非常有效。 對於位在不會對網際網路公開的安全性界限中的多層式應用程式內的服務/階層也十分實用，但仍需要循環配置資源負載散發、工作階段綁定或安全通訊端層 (SSL) 終止。

本文逐步讲解如何在 Azure 门户中使用前端专用 IP 地址配置应用程序网关。

在本文中，您將了解如何：

- 为应用程序网关创建专用前端 IP 配置
- 使用专用前端 IP 配置创建应用程序网关


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>登入 Azure

在 Azure 入口網站登入 <https://portal.azure.com>

## <a name="create-an-application-gateway"></a>建立應用程式閘道

Azure 需要虛擬網路才能在您所建立的資源之間進行通訊。 您可以建立新的虛擬網路，或使用現有的虛擬網路。 在此範例中，我們會建立新的虛擬網路。 您建立應用程式閘道時，可以同時建立虛擬網路。 在不同的子網路中，建立應用程式閘道執行個體。 在此範例中您會建立兩個子網路：一個用於應用程式閘道，另一個用於後端伺服器。

1. 按一下 Azure 入口網站左上角的 [新增]。
2. 在「精選」清單中選取 [網路]，然後選取 [應用程式閘道]。
3. 輸入 myAppGateway 作為應用程式閘道的名稱，輸入 myResourceGroupAG 作為新的資源群組。
4. 接受其他設定的預設值，然後按一下 [確定]。
5. 按一下 [選擇虛擬網路]，按一下 [新建]，然後針對虛擬網路輸入這些值：
   - myVNet* - 虚拟网络的名称。
   - 10.0.0.0/16* - 虚拟网络地址空间。
   - myAGSubnet - 作為子網路名稱。
   - 10.0.0.0/24 - 作為子網路位址空間。  
     ![private-frontendip-1](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)
6. 按一下 [確定] 以建立虛擬網路和子網路。
7. 选择“专用”作为“前端 IP 配置”（默认为动态 IP 地址分配）。 所选子网的第一个可用地址将分配为前端 IP 地址。
8. 若要从子网地址范围中选择专用 IP（静态分配），请单击“选择特定的专用 IP 地址”框并指定 IP 地址。
   > [!NOTE]
   > IP 地址类型（静态或动态）一经分配，以后便不可更改。
9. 选择协议和端口的侦听器配置以及 WAF 配置（如果需要），然后单击“确定”。
    ![private-frontendip-2](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-2.png)
10. 檢閱 [摘要] 頁面上的設定，然後按一下 [確定] 以建立網路資源和應用程式閘道。 建立應用程式閘道可能需要幾分鐘的時間，請等候部署成功完成後，再繼續進行至下一節。

## <a name="add-backend-pool"></a>新增後端集區

後端集區可用來將要求路由至能為要求提供服務的後端伺服器。 后端可以包含 NIC、虚拟机规模集、公共 IP、内部 IP、完全限定的域名 (FQDN) 和多租户后端（例如 Azure 应用服务）。 本示例使用虚拟机作为目标后端。 我們可以使用現有的虛擬機器，或建立新的虛擬機器。 在此範例中，我們會建立兩個虛擬機器，供 Azure 作為應用程式閘道的後端伺服器。 为此，我们将会：

1. 创建两个新的 VM *myVM* 和 *myVM2*，用作后端服务器。
2. 在虛擬機器上安裝 IIS，以確認成功建立應用程式閘道。
3. 将后端服务器添加到后端池。

### <a name="create-a-virtual-machine"></a>建立虛擬機器

1. 按一下 [新增] 。
2. 按一下 [計算]，然後選取 [精選] 清單中的 [Windows Server 2016 Datacenter]。
3. 針對虛擬機器，請輸入這些值：
   - myVM - 作為虛擬機器的名稱。
   - azureuser - 作為系統管理員使用者名稱。
   - *Azure123456!* 作為密碼。
   - 選取 [使用現有的]，然後選取 [myResourceGroupAG]。
4. 按一下 [確定]。
5. 选择“DS1_V2”作为虚拟机的大小，然后单击“选择”。
6. 確定您已選取 [myVNet] 作為虛擬網路，而且子網路是 [myBackendSubnet]。
7. 按一下 [停用] 來停用開機診斷。
8. 按一下 [確定]，檢閱 [摘要] 頁面上的設定，然後按一下 [建立]。

### <a name="install-iis"></a>安裝 IIS

1. 開啟互動式殼層，並確定它是設定為 **PowerShell**。
    ![private-frontendip-3](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
2. 執行下列命令以在虛擬機器上安裝 IIS：

   ```azurepowershell
   Set-AzVMExtension `
   
     -ResourceGroupName myResourceGroupAG `
   
     -ExtensionName IIS `
   
     -VMName myVM `
   
     -Publisher Microsoft.Compute `
   
     -ExtensionType CustomScriptExtension `
   
     -TypeHandlerVersion 1.4 `
   
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' -Location EastUS  ```



3. Create a second virtual machine and install IIS using the steps that you just finished. Enter myVM2 for its name and for VMName in Set-AzVMExtension.

### Add backend servers to backend pool

1. Click **All resources**, and then click **myAppGateway**.
2. Click **Backend pools**. A default pool was automatically created with the application gateway. Click **appGatewayBackendPool**.
3. Click **Add target** to add each virtual machine that you created to the backend pool.
   ![private-frontendip-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
4. Click **Save.**

## Test the application gateway

1. Check your frontend IP that got assigned by clicking the **Frontend IP Configurations** blade in the portal.
    ![private-frontendip-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Copy the private IP address, and then paste it into the address bar of your browser of a VM in the same VNet or on-premises which has connectivity to this VNet and try to access the Application Gateway.

## Next steps

In this tutorial, you learned how to:

- Create a private frontend IP configuration for an Application Gateway
- Create an application gateway with private frontend IP configuration

If you want to monitor the health of your backend, see [Application Gateway Diagnostics](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
