---
title: "適用於 Windows 的 Azure 效能診斷 VM 擴充功能 |Microsoft Docs"
description: "介紹適用於 Windows 的 Azure 效能診斷 VM 擴充功能。"
services: virtual-machines-windows'
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/29/2017
ms.author: genli
ms.openlocfilehash: 5a7dc313f1d6453562e4d5a11ceca03e4459b043
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2017
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>適用於 Windows 的 Azure 效能診斷 VM 擴充功能

Azure 效能診斷 VM 延伸模組可協助您收集來自 Windows Vm 效能診斷資料。 該擴充功能執行分析，並提供一份結果和建議，可找出並解決虛擬機器上的效能問題。 此擴充功能會安裝名為 [PerfInsights](http://aka.ms/perfinsights) 的疑難排解工具。

## <a name="prerequisites"></a>必要條件

可以在 Windows Server 2008 R2、 Windows Server 2012、 Windows Server 2012 R2 和 Windows Server 2016 上安裝此擴充功能。 它也可以安裝在 Windows 8.1 和 Windows 10。

## <a name="extension-schema"></a>擴充功能結構描述
下列 JSON Azure 效能診斷 VM 延伸模組會顯示結構描述。 此延伸模組需要的名稱和儲存的診斷輸出和報表的儲存體帳戶金鑰。 這些值會寫，而且應該儲存在受保護的設定。 Azure VM 延伸模組保護的設定資料已加密，且它才會解密目標虛擬機器上。 請注意， **storageAccountName**和**storageAccountKey**會區分大小寫。 其他必要的參數會列在下一節中。

```JSON
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "performanceScenario": "[parameters('performanceScenario')]",
                  "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
                  "perfCounterTrace": "[parameters('perfCounterTrace')]",
                  "networkTrace": "[parameters('networkTrace')]",
                  "xperfTrace": "[parameters('xperfTrace')]",
                  "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
          "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]"        
            }
      }
    }
```

### <a name="property-values"></a>屬性值

|   **名稱**   |**值 / 範例**|       **說明**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|API 版本。
|publisher|Microsoft.Azure.Performance.Diagnostics|擴充功能的發行者命名空間。
|type|AzurePerformanceDiagnostics|VM 擴充功能類型。
|typeHandlerVersion|1.0|延伸模組處理常式的版本。
|performanceScenario|basic|這是要擷取資料的效能案例。 有效值為：**basic**、**vmslow**、**azurefiles** 及 **custom**。
|traceDurationInSeconds|300|如果任何追蹤選項選取的追蹤持續時間。
|perfCounterTrace|p|啟用效能計數器追蹤的選項。 有效值為 **p** 或空值。 如果您不想要擷取此追蹤，將值保留為空白。
|networkTrace|n|若要啟用網路追蹤的選項。 有效值為 **n** 或空值。 如果您不想要擷取此追蹤，將值保留為空白。
|xperfTrace|x|啟用 XPerf 追蹤的選項。 有效值為 **x** 或空值。 如果您不想要擷取此追蹤，將值保留為空白。
|storPortTrace|s|啟用 StorPort 追蹤的選項。 有效值為**s**值或空值。 如果您不想要擷取此追蹤，將值保留為空白。
|srNumber|123452016365929|支援票證數目，如果有的話。 將值保留為空白，如果您不需要它。
|storageAccountName|mystorageaccount|若要儲存的診斷記錄檔和結果的儲存體帳戶名稱。
|storageAccountKey|lDuVvxuZB28NNP…hAiRF3voADxLBTcc==|儲存體帳戶金鑰。

## <a name="install-the-extension"></a>安裝擴充功能

請遵循下列步驟，Windows 虛擬機器上安裝擴充功能：

1. 登入 [Azure 入口網站](http://portal.azure.com)。
2. 選取您要安裝此擴充功能的虛擬機器。

    ![具有反白顯示的虛擬機器的螢幕擷取畫面的 Azure 入口網站，](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. 選取**延伸**刀鋒視窗中，然後選取**新增**。

    ![螢幕擷取畫面的延伸模組的刀鋒視窗中，以反白顯示新增](media/performance-diagnostics-vm-extension/select-extensions.png)
4. 選取**Azure 效能診斷**，檢閱這些條款和條件，並選取**建立**。

    ![新螢幕擷取畫面的資源螢幕，以反白顯示的 Azure 效能診斷](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. 提供的參數值來安裝，並選取**確定**安裝擴充功能。 如需支援的案例的詳細資訊，請參閱[如何使用 PerfInsights](how-to-use-perfInsights.md#supported-troubleshooting-scenarios)。 

    ![螢幕擷取畫面的安裝延伸模組對話方塊](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. 安裝成功時，您會看到訊息，指出此狀態。

    ![螢幕擷取畫面的佈建成功訊息](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > 已成功佈建時，就會執行擴充功能。 只需要兩分鐘或更少的基本案例完成。 對於其他案例，它會在於安裝期間所指定的持續時間內持續執行。

## <a name="remove-the-extension"></a>移除擴充功能
若要從虛擬機器移除擴充功能，請遵循下列步驟：

1. 登入[Azure 入口網站](http://portal.azure.com)，選取您想要移除這個延伸模組，然後選取的虛擬機器**延伸**刀鋒視窗。 
2. 選取 (**...**) 效能診斷延伸模組項目從清單中，然後選取**解除安裝**。

    ![螢幕擷取畫面的延伸模組的刀鋒視窗中，反白顯示的解除安裝](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > 您也可以選取延伸模組項目，並選取**解除安裝**選項。

## <a name="template-deployment"></a>範本部署
Azure 虛擬機器擴充功能可以搭配 Azure Resource Manager 範本部署。 上一節中詳細的 JSON 結構描述可用於 Azure Resource Manager 範本。 這會在 Azure 資源管理員範本部署期間執行的 Azure 效能診斷 VM 延伸模組。 以下是範例範本：

````
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "defaultValue": "yourVMName"
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus"
    },
    "storageAccountName": {
      "type": "securestring"
      "defaultValue": "yourStorageAccount"
    },
    "storageAccountKey": {
      "type": "securestring"
      "defaultValue": "yourStorageAccountKey"
    },
    "performanceScenario": {
      "type": "string",
      "defaultValue": "basic"
    },
    "srNumber": {
      "type": "string",
      "defaultValue": ""
    },
    "traceDurationInSeconds": {
      "type": "int",
    "defaultValue": 300
    },
    "perfCounterTrace": {
      "type": "string",
      "defaultValue": "p"
    },
    "networkTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "xperfTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "storPortTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "requestTimeUtc": {
      "type": "string",
      "defaultValue": "10/2/2017 11:06:00 PM"
    }       
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "performanceScenario": "[parameters('performanceScenario')]",
                  "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
                  "perfCounterTrace": "[parameters('perfCounterTrace')]",
                  "networkTrace": "[parameters('networkTrace')]",
                  "xperfTrace": "[parameters('xperfTrace')]",
                  "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
          "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]"        
            }
      }
    }
  ]
}
````

## <a name="powershell-deployment"></a>PowerShell 部署
`Set-AzureRmVMExtension`命令可以用來將 Azure 效能診斷 VM 延伸模組部署到現有的虛擬機器。 才能執行此命令，將儲存 PowerShell 雜湊表中的公用和私用組態。

PowerShell

````
$PublicSettings = @{ "performanceScenario":"basic","traceDurationInSeconds":300,"perfCounterTrace":"p","networkTrace":"","xperfTrace":"","storPortTrace":"","srNumber":"","requestTimeUtc":"2017-09-28T22:08:53.736Z" }
$ProtectedSettings = @{"storageAccountName":"mystorageaccount","storageAccountKey":"mystoragekey"}

Set-AzureRmVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
````

## <a name="information-on-the-data-captured"></a>所擷取資料的詳細資訊
PerfInsights 工具會收集各種記錄檔、 組態和診斷資料，根據所選取的案例。 如需詳細資訊，請參閱[PerfInsights 文件](http://aka.ms/perfinsights)。

## <a name="view-and-share-the-results"></a>檢視，以及分享結果

從延伸模組的輸出會儲存在資料夾中。 資料夾名為 log_collection，並可以找到暫存磁碟機 (通常 D:\log_collection) 下，根據預設。 在這個資料夾中，您可以看到包含診斷記錄檔，以及報表結果及建議的 zip 檔案。

您也可以在安裝期間提供的儲存體帳戶中找到的 zip 檔案。 它藉由使用 30 天內共用[共用存取簽章 (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md)。 名為 *zipfilename*_saslink.txt 的文字檔也會在 log_collection 資料夾中建立。 此檔案包含建立用來下載 zip 檔案的 SAS 連結。 任何具有此連結的人員都能夠下載該 zip 檔案。

為了協助支援工程師努力支援票證，Microsoft 可能會使用此 SAS 連結來下載診斷資料。

若要檢視報表，將 zip 檔案解壓縮，並開啟**PerfInsights Report.html**檔案。

您也可以直接從入口網站下載之 zip 檔案，選取擴充功能。

![螢幕擷取畫面的效能診斷詳細狀態](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> 顯示在入口網站中的 SAS 連結可能無法運作。 這可以在編碼和解碼作業期間造成錯誤的 url。 相反地，您就可以直接從 *_saslink.txt 檔案從 VM 取得連結。

## <a name="troubleshoot-and-support"></a>疑難排解與支援

- 擴充功能 （在通知區域） 的部署狀態可能會顯示 「 部署正在進行中的 」，即使已成功佈建擴充功能。

    這個問題可以放心忽略，只要擴充功能狀態，則表示已成功佈建擴充功能。
- 您可以使用擴充功能記錄，在安裝期間解決一些問題。 擴充功能執行輸出會記錄至下列目錄中的檔案︰

        C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。 或者，您可以提出 Azure 支援事件。 移至[Azure 支援服務網站](https://azure.microsoft.com/support/options/)，然後選取**取得支援**。 使用 Azure 支援的相關資訊，請參閱[Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。
