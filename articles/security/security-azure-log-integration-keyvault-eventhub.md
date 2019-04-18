---
title: 透過使用事件中樞整合來自 Azure Key Vault 的記錄 | Microsoft Docs
description: 提供使用 Azure 記錄整合將金鑰保存庫記錄提供給 SIEM 之必要步驟的教學課程
services: security
author: barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.topic: article
ms.date: 01/14/2019
ms.author: Barclayn
ms.custom: AzLog
ms.openlocfilehash: 7e70920e806b3d9838d693ff1fc74a3e9371319d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58883905"
---
# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Azure 記錄整合教學課程：使用事件中樞處理 Azure Key Vault 事件

>[!IMPORTANT]
> Azure 記錄整合功能將於 2019/06/01 淘汰。 AzLog 下載已在 2018 年 6 月 27 日停用。 如需繼續進行的指導，請檢閱 [Use Azure monitor to integrate with SIEM tools](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) (使用 Azure 監視器與 SIEM 工具整合) 一文 

您可以使用 Azure 記錄整合擷取記錄的事件，並將其提供給安全性資訊和事件管理 (SIEM) 系統。 本教學課程示範如何使用 Azure 記錄整合，來處理透過 Azure 事件中樞所取得的記錄。

整合 Azure 記錄的慣用方法是使用 SIEM 廠商的 Azure 監視器連接器，並遵循這些[指示](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)。 不過，如果您的 SIEM 廠商沒有提供 Azure 監視器連接器，在這類連接器可供使用之前，您可以使用 Azure 記錄整合作為暫時解決方案 (如果 Azure 記錄整合支援您的 SIEM)。

 
您可以使用本教學課程來熟悉 Azure 記錄整合如何與事件中樞搭配運作。請遵循範例步驟，並了解每個步驟如何支援解決方案。 然後您可以利用這裡學到的內容來建立自己的步驟，以支援您公司的獨特需求。

> [!WARNING]
> 本教學課程中的步驟和命令不適合複製並貼上。 這些步驟和命令僅供範例之用。 請勿在您的即時環境中以「現況」使用 PowerShell 命令。 您必須根據自己專屬的環境自訂這些命令。


本教學課程將逐步引導您將 Azure Key Vault 活動記錄至事件中樞，並以 JSON 檔案的形式提供給 SIEM 系統的程序。 然後您可以設定 SIEM 系統以處理 JSON 檔案。

>[!NOTE]
>本教學課程中大部分的步驟都涉及設定金鑰保存庫、儲存體帳戶和事件中樞。 Azure 記錄整合的特定步驟位於本教學課程的結尾。 請勿在生產環境中執行這些步驟。 這些步驟僅供實驗室環境之用。 您必須自訂這些步驟，才能在生產環境中使用。

在過程中提供的資訊可協助您了解每個步驟背後的原因。 其他文章的連結可為您提供特定主題的詳細資訊。

如需本教學課程提及之服務的詳細資訊，請參閱： 

- [Azure 金鑰保存庫](../key-vault/key-vault-whatis.md)
- [Azure 事件中樞](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Azure 記錄整合](security-azure-log-integration-overview.md)


## <a name="initial-setup"></a>初始設定

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

完成本文中的步驟之前，您需要下列項目：

* Azure 訂用帳戶和該訂用帳戶上具有系統管理員權限的帳戶。 如果您沒有訂用帳戶，則可以建立[免費帳戶](https://azure.microsoft.com/free/)。
 
* 具有網際網路存取且符合安裝 Azure 記錄整合需求的系統。 系統可位於雲端服務或裝載於內部部署。

* 已安裝 Azure 記錄整合。 若要安裝該項目：

   a. 使用遠端桌面連線至步驟 2 中所述的系統。   
   b. 將 Azure 記錄整合安裝程式複製到系統。 c. 啟動安裝程式並接受 Microsoft 軟體授權條款。

* 如果您將提供遙測資訊，請將核取方塊保留為選取。 如果您不想要將使用情況資訊傳送給 Microsoft，請清除核取方塊。

   如需 Azure 記錄整合和安裝方式的詳細資訊，請參閱[使用 Azure 診斷記錄和 Windows 事件轉送進行 Azure 記錄整合](security-azure-log-integration-get-started.md)。

* 最新的 PowerShell 版本。

   如果您已安裝 Windows Server 2016，便至少擁有 PowerShell 5.0。 如果您正在使用任何其他版本的 Windows Server，則可能已安裝舊版 PowerShell。 您可以透過在 PowerShell 視窗中輸入 ```get-host``` 檢查版本。 如果您尚未安裝 PowerShell 5.0，則可以[進行下載](https://www.microsoft.com/download/details.aspx?id=50395)。

   之後您至少擁有 PowerShell 5.0 中，您可以繼續依照中的指示安裝最新版[安裝 Azure PowerShell](/powershell/azure/install-az-ps)。


## <a name="create-supporting-infrastructure-elements"></a>建立支援基礎結構元素

1. 開啟提升權限的 PowerShell 視窗並移至 **C:\Program Files\Microsoft Azure Log Integration**。
1. 透過執行指令碼 LoadAzLogModule.ps1，匯入 AzLog Cmdlet。 輸入 `.\LoadAzLogModule.ps1` 命令 (請注意該命令中的 ".\")。您應該會看到如下的結果：</br>

   ![載入的模組清單](./media/security-azure-log-integration-keyvault-eventhub/loaded-modules.png)

1. 輸入 `Connect-AzAccount` 命令 在登入視窗中，輸入將用於本教學課程的訂用帳戶認證資訊。

   >[!NOTE]
   >如果這是您第一次從此電腦登入 Azure，您會看到有關允許 Microsoft 收集 PowerShell 使用情況資料的訊息。 由於此資料收集會用於改進 Azure PowerShell，因此我們建議您將其啟用。

1. 驗證成功後，您已登入。 由於您在後續步驟中需要使用訂用帳戶識別碼和訂用帳戶名稱，因此請記下這些資訊。

1. 建立變數來儲存稍後將使用的值。 逐行下列每一行 PowerShell。 您可能需要調整該值以符合您的環境。
    - ```$subscriptionName = 'Visual Studio Ultimate with MSDN'``` (您的訂用帳戶名稱可能會不同。 您可以看到其顯示為先前命令輸出的一部分。)
    - ```$location = 'West US'``` (此變數會用於傳遞應該建立資源的位置。 您可以將此變數變更為您所選擇的任何位置。)
    - ```$random = Get-Random```
    - ```$name = 'azlogtest' + $random``` (名稱可以是任何項目，但必須只包含小寫字母和數字。)
    - ```$storageName = $name``` (此變數會作為儲存體帳戶名稱使用。)
    - ```$rgname = $name``` (此變數會作為資源群組名稱使用。)
    - ```$eventHubNameSpaceName = $name``` (此為事件中樞命名空間的名稱。)
1. 指定您要使用的訂用帳戶：
    
    ```Select-AzSubscription -SubscriptionName $subscriptionName```
1. 建立資源群組：
    
    ```$rg = New-AzResourceGroup -Name $rgname -Location $location```
    
   如果您在此時輸入 `$rg`，則應該會看到類似此螢幕擷取畫面的輸出：

   ![建立資源群組之後的輸出](./media/security-azure-log-integration-keyvault-eventhub/create-rg.png)
1. 建立將用於追蹤狀態資訊的儲存體帳戶：
    
    ```$storage = New-AzStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
1. 建立事件中樞命名空間。 如此才能建立事件中樞。
    
    ```$eventHubNameSpace = New-AzEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
1. 取得將與深入解析提供者搭配使用的規則識別碼：
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey'```
1. 取得所有可能的 Azure 位置，並將名稱新增至可用於後續步驟的變數：
    
    a. ```$locationObjects = Get-AzLocation```    
    b. ```$locations = @('global') + $locationobjects.location```
    
    如果您輸入`$locations`到目前為止，您看到的位置名稱含 Get AzLocation 所傳回之其他資訊。
1. 建立 Azure Resource Manager 記錄設定檔： 
    
    ```Add-AzLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    如需有關 Azure 記錄設定檔的詳細資訊，請參閱 [Azure 活動記錄概觀](../azure-monitor/platform/activity-logs-overview.md)。

> [!NOTE]
> 嘗試建立記錄設定檔時，您可能會收到錯誤訊息。 然後，您可以檢閱 Get AzLogProfile 和移除 AzLogProfile 的文件。 如果您執行 Get AzLogProfile 時，您會看到記錄設定檔的相關資訊。 您可以透過輸入 ```Remove-AzLogProfile -name 'Log Profile Name'``` 命令，刪除現有記錄設定檔。
>
>![資源管理員設定檔錯誤](./media/security-azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>建立金鑰保存庫

1. 建立金鑰保存庫：

   ```$kv = New-AzKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location```

1. 設定金鑰保存庫記錄：

   ```Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true```

## <a name="generate-log-activity"></a>產生記錄活動

您需要將要求傳送至金鑰保存庫才能產生記錄活動。 產生金鑰、儲存密碼或從金鑰保存庫讀取密碼等動作都會建立記錄項目。

1. 顯示目前的儲存體金鑰：
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. 產生新的 **key2**：
    
   ```New-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
1. 再次顯示金鑰，並看到 **key2** 保存不同的值：
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. 設定和讀取密碼以產生其他記錄項目：
    
   a. ```Set-AzKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)``` b. ```(Get-AzKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![傳回的密碼](./media/security-azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>設定 Azure 記錄整合

您現在已將所有必要元素設定為將金鑰保存庫記錄至事件中樞，然後您需要設定 Azure 記錄整合：

1. ```$storage = Get-AzStorageAccount -ResourceGroupName $rgname -Name $storagename```
1. ```$eventHubKey = Get-AzEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
1. ```$storagekeys = Get-AzStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
1. ```$storagekey = $storagekeys[0].Value```

對每個事件中樞執行 AzLog 命令：

1. ```$eventhubs = Get-AzEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
1. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

執行最後兩列命令約一分鐘之後，您應該會看到產生的 JSON 檔案。 您可以透過監視目錄 **C:\users\AzLog\EventHubJson** 進行確認。

## <a name="next-steps"></a>後續步驟

- [Azure 記錄整合常見問題集](security-azure-log-integration-faq.md)
- [開始使用 Azure 記錄整合](security-azure-log-integration-get-started.md)
- [將記錄從 Azure 資源整合到 SIEM 系統](security-azure-log-integration-overview.md)
