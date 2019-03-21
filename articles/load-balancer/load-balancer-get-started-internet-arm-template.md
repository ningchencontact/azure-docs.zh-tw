---
title: 建立公用負載平衡器 - Azure 範本
titlesuffix: Azure Load Balancer
description: 了解如何使用範本在資源管理員中建立公用負載平衡器
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: af72eb0214f46c1e001f9e93934d38cb934503e4
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2019
ms.locfileid: "56592480"
---
# <a name="creating-a-public-load-balancer-using-a-template"></a>使用範本建立公用負載平衡器

> [!div class="op_single_selector"]
> * [入口網站](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [範本](../load-balancer/load-balancer-get-started-internet-arm-template.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>使用按一下即部署來部署範本

公用儲存機制中可用的範例範本會使用一個包含預設值的參數檔案，這些預設值可用來產生上述案例。 如果要通过单击部署的方式来部署此模板，请访问[此链接](https://go.microsoft.com/fwlink/?LinkId=544801)，单击“部署至 Azure”，如有必要，请替换默认参数值，并按照门户中的说明进行操作。

## <a name="deploy-the-template-by-using-powershell"></a>使用 PowerShell 部署範本

若要使用 PowerShell 部署您下載的範本，請依照下列步驟執行。

1. 如果您從未用過 Azure PowerShell，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/overview) ，並遵循其中的所有指示登入 Azure，然後選取您的訂用帳戶。
2. 运行 **New-AzResourceGroupDeployment** cmdlet 以使用模板创建资源组。

    ```powershell
    New-AzResourceGroupDeployment -Name TestRG -Location uswest `
        -TemplateFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' `
        -TemplateParameterFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.parameters.json'
    ```

## <a name="deploy-the-template-by-using-the-azure-cli"></a>使用 Azure CLI 部署範本

若要使用 Azure CLI 部署模板，请执行以下步骤。

1. 如果您從未使用過 Azure CLI，請參閱 [安裝和設定 Azure CLI](../cli-install-nodejs.md) ，並依照指示進行，直到選取您的 Azure 帳戶和訂用帳戶為止。
2. 執行 **azure config mode** 命令，以切換為資源管理員模式，如下所示。

    ```azurecli
    azure config mode arm
    ```

    此為上述命令的預期輸出內容：

        info:    New mode is arm

3. 從您的瀏覽器，瀏覽至[快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules)，複製 json 檔案的內容並貼上到您電腦中的新檔案。 在此案例中，您會將以下的值複製到名為 **c:\lb\azuredeploy.parameters.json** 的檔案。
4. 執行 **azure group deployment create** Cmdlet，使用先前下載並修改的範本和參數檔案來部署新的負載平衡器。 輸出後顯示的清單可說明所使用的參數。

    ```azurecli
    azure group create --name TestRG --location westus --template-file 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' --parameters-file 'c:\lb\azuredeploy.parameters.json'
    ```

## <a name="next-steps"></a>後續步驟

[開始設定內部負載平衡器](load-balancer-get-started-ilb-arm-ps.md)

[配置负载均衡器分发模式](load-balancer-distribution-mode.md)

[設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)

如需範本中負載平衡器的 JSON 語法和屬性，請參閱 [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)。
