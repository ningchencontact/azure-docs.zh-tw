---
title: 混合式環境中啟用 Azure 監視器 （預覽） |Microsoft Docs
description: 本文說明如何啟用 Azure 監視 Vm 的混合式雲端環境，其中包含一或多個虛擬機器。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/07/2019
ms.author: magoedte
ms.openlocfilehash: bc26cc0654aac9416bf31ffccf426648e3a8b8d2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122557"
---
# <a name="enable-azure-monitor-for-vms-preview-for-a-hybrid-environment"></a>Azure 監視器啟用混合式環境的虛擬機器 （預覽）

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

這篇文章說明如何啟用 Azure 監視器的 Vm （預覽） 的虛擬機器或在您的資料中心或其他雲端環境中裝載的實體電腦。 在此程序結束時，您將已成功開始監視您的環境中的虛擬機器，並了解是否它們發生效能或可用性問題。 

開始之前，請務必檢閱[必要條件](vminsights-enable-overview.md)並確認您的訂用帳戶和資源都符合需求。 檢閱適用於 [Log Analytics Linux 和 Windows 代理程式](../../log-analytics/log-analytics-agent-overview.md)的需求和部署方法。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>適用於 VM 的 Azure 監視器對應 Dependency Agent 本身不會傳輸任何資料，因此不需變更防火牆或連接埠。 地圖資料一律會由 Log Analytics 代理程式至 Azure 監視器服務，直接或透過傳輸[Operations Management Suite 閘道](../../azure-monitor/platform/gateway.md)如果 IT 安全性原則不允許網路上的電腦連線到網際網路。

若要完成這項工作的步驟摘要如下：

1. 安裝適用於 Windows 或 Linux 的 Log Analytics 代理程式。 安裝代理程式之前，請先檢閱[Log Analytics 代理程式概觀](../platform/log-analytics-agent.md)文章，以了解先決條件和部署方法。

2. 針對 [Windows](https://aka.ms/dependencyagentwindows) 或 [Linux](https://aka.ms/dependencyagentlinux)，下載並安裝適用於 VM 的 Azure 監視器對應 Dependency Agent。

3. 啟用收集效能計數器。

4. 部署適用於 VM 的 Azure 監視器。

## <a name="install-the-dependency-agent-on-windows"></a>在 Windows 上安裝 Dependency Agent
您可以執行 `InstallDependencyAgent-Windows.exe` 以手動將 Dependency Agent 安裝在 Windows 電腦上。 如果您在執行此可執行檔時未使用任何選項，則會啟動以互動方式安裝代理程式的安裝精靈。

>[!NOTE]
>必須有*系統管理員*權限，才能安裝或解除安裝代理程式。

下表將強調說明命令列中適用於代理程式的安裝程式所支援的參數。

| 參數 | 描述 |
|:--|:--|
| /? | 傳回命令列選項的清單。 |
| /S | 執行無訊息安裝，而不需要與使用者互動。 |

例如，若要執行安裝程式`/?`參數，輸入**Windows.exe /？** 。

Windows Dependency Agent 的檔案預設安裝在 *C:\Program Files\Microsoft Dependency Agent* 中。 如果相依性代理程式無法啟動安裝程式完成之後，請檢查記錄檔取得詳細的錯誤資訊。 記錄目錄是 *%Programfiles%\Microsoft Dependency Agent\logs*。

## <a name="install-the-dependency-agent-on-linux"></a>在 Linux 上安裝 Dependency Agent
將 Dependency Agent 從 *InstallDependencyAgent-Linux64.bin* (具有自我解壓縮二進位檔的殼層指令碼) 安裝在 Linux 伺服器上。 您可以使用 `sh` 來執行檔案，或對檔案本身新增執行權限。

>[!NOTE]
> 必須有 root 權限，以便安裝或設定代理程式。
>

| 參數 | 描述 |
|:--|:--|
| -help | 取得命令列選項的清單。 |
| -s | 執行無訊息安裝，不會出現任何使用者提示。 |
| --check | 檢查權限和作業系統，但不會安裝代理程式。 |

例如，若要執行安裝程式`-help`參數，輸入**InstallDependencyAgent-Linux64.bin-協助**。

執行命令以 root 身分安裝 Linux 相依性代理程式`sh InstallDependencyAgent-Linux64.bin`。

如果 Dependency Agent 無法啟動，請檢查記錄以取得詳細的錯誤資訊。 在 Linux 代理程式上，記錄檔的目錄是 /var/opt/microsoft/dependency-agent/log  。

Dependency Agent 的檔案位於下列目錄：

| 檔案 | Location |
|:--|:--|
| 核心檔案 | /opt/microsoft/dependency-agent |
| 記錄檔 | /var/opt/microsoft/dependency-agent/log |
| 組態檔 | /etc/opt/microsoft/dependency-agent/config |
| 服務可執行檔 | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| 二進位儲存體檔案 | /var/opt/microsoft/dependency-agent/storage |

## <a name="enable-performance-counters"></a>啟用效能計數器
如果解決方案所參考的 Log Analytics 工作區還未設定為收集此解決方案所需的效能計數器，則必須啟用這些效能計數器。 您可以使用下列其中一種這樣：
* 手動，如 [Log Analytics 中的 Windows 和 Linux 效能資料來源](../../azure-monitor/platform/data-sources-performance-counters.md)中所述
* 下載並執行 PowerShell 指令碼所提供的[Azure PowerShell 資源庫](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

## <a name="deploy-azure-monitor-for-vms"></a>部署適用於 VM 的 Azure 監視器
此方法包含一個 JSON 範本，其會指定在 Log Analytics 工作區中啟用解決方案元件的設定。

如果您不知道如何使用範本部署資源，請參閱：
* [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../../azure-resource-manager/resource-group-template-deploy.md)
* [使用 Resource Manager 範本與 Azure CLI 部署資源](../../azure-resource-manager/resource-group-template-deploy-cli.md)

若要使用 Azure CLI，必須先安裝，並在本機使用 CLI。 您必須執行 Azure CLI 2.0.27 版或更新版本。 若要知道您使用的版本，請執行 `az --version`。 若要安裝或升級 Azure CLI，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

### <a name="create-and-execute-a-template"></a>建立並執行範本

1. 複製以下 JSON 語法並貼到您的檔案中：

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. 在本機資料夾中將此檔案儲存為 *installsolutionsforvminsights.json*。

1. 擷取 *WorkspaceName*、*ResourceGroupName* 和 *WorkspaceLocation* 的值。 *WorkspaceName* 的值是您 Log Analytics 工作區的名稱。 *WorkspaceLocation* 的值是定義工作區所在的區域。

1. 您已經準備好使用下列 PowerShell 命令部署此範本：

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    組態變更可能需要幾分鐘才能完成。 完成後，會顯示訊息，如下所示，而且包含結果：

    ```powershell
    provisioningState       : Succeeded
    ```
   啟用監視之後，可能需要約 10 分鐘的時間才能檢視混合式電腦的健康狀態和計量。

## <a name="next-steps"></a>後續步驟

現在您的虛擬機器啟用監視，這項資訊是使用適用於 Vm 的 Azure 監視器可用於分析。
 
- 若要了解如何使用健全狀況的功能，請參閱[Vm 的健全狀況檢視 Azure 監視器](vminsights-health.md)。
- 若要檢視探索到的應用程式相依性，請參閱[檢視適用於 VM 的 Azure 監視器對應](vminsights-maps.md)。
- 若要找出瓶頸並與您的 VM 效能的整體使用率，請參閱[檢視 Azure 虛擬機器效能](vminsights-performance.md)。
- 若要檢視探索到的應用程式相依性，請參閱[檢視適用於 VM 的 Azure 監視器對應](vminsights-maps.md)。