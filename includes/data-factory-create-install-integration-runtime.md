---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: a2858ac73838b50c21a76db5860675171a306192
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66168201"
---
## <a name="create-a-self-hosted-integration-runtime"></a>建立自我裝載整合執行階段

在本節中，您可以建立自我裝載整合執行階段，並使用 SQL Server 資料庫將它與內部部署電腦產生關聯。 自我裝載的整合執行階段是一項元件，可將資料從您電腦上的 SQL Server 複製到 Azure blob 儲存體。 

1. 建立整合執行階段名稱的變數。 使用唯一的名稱，並記下此名稱。 您將在本教學課程稍後使用它。 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
2. 建立自我裝載整合執行階段。 

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   以下是範例輸出：

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
  
3. 若要擷取所建立整合執行階段的狀態，請執行下列命令。 確認 **State** 屬性的值已設定為 **NeedRegistration**。 

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   以下是範例輸出：

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption :
   Version                   :
   Capabilities              : {}
   ScheduledUpdateDate       :
   UpdateDelayOffset         :
   LocalTimeZoneOffset       :
   AutoUpdate                :
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

4. 若要擷取驗證金鑰，用來向雲端中的 Azure Data Factory 服務註冊自我裝載的整合執行階段，請執行下列命令： 

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   以下是範例輸出：

   ```json
   {
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    

5. 複製其中一個金鑰 (不含雙引號)，該金鑰用來註冊您在下列步驟中安裝於電腦上的自我裝載整合執行階段。  

## <a name="install-the-integration-runtime"></a>安裝整合執行階段
1. 如果您的機器上已經有整合執行階段，請使用 [新增或移除程式] 將其解除安裝。 

2. 在本機 Windows 電腦上[下載](https://www.microsoft.com/download/details.aspx?id=39717)自我裝載的整合執行階段。 執行安裝。

3. 在 [歡迎使用 Microsoft Integration Runtime 設定] 頁面上，選取 [下一步]。

4. 在 [使用者授權合約] 頁面上，接受條款和授權合約，然後選取 [下一步]。

5. 在 [目的地資料夾] 頁面上，選取 [下一步]。

6. 在 [準備好要安裝 Microsoft Integration Runtime] 頁面上，選取 [安裝]。

7. 如果您看到一則有關將電腦設定為不使用時進入睡眠或休眠模式的警告訊息，請選取 [確定]。

8. 如果您看到 [電源選項] 頁面，請將它關閉，然後移至設定頁面。

9. 在 [完成 Microsoft Integration Runtime 設定] 頁面上，選取 [完成]。

10. 在 [註冊 Integration Runtime (自我裝載)] 頁面上，貼上您在上一節中儲存的金鑰，然後選取 [註冊]。 

    ![註冊整合執行階段](media/data-factory-create-install-integration-runtime/register-integration-runtime.png)

11. 當自我裝載的整合執行階段註冊成功時，您會看到下列訊息：

    ![已成功註冊](media/data-factory-create-install-integration-runtime/registered-successfully.png)

12. 在 [新增整合執行階段 (自我裝載) 節點] 頁面上，選取 [下一步]。 

    ![新增 Integration Runtime 節點頁面](media/data-factory-create-install-integration-runtime/new-integration-runtime-node-page.png)

13. 在 [內部網路通訊通道] 頁面上，選取 [略過]。 選取 TLS/SSL 憑證，以保護在多節點整合執行階段環境中的內部節點通訊。 

    ![內部網路通訊通道頁面](media/data-factory-create-install-integration-runtime/intranet-communication-channel-page.png)

14. 在 [註冊整合執行階段 (自我裝載)] 頁面上，選取 [啟動組態管理員]。

15. 當節點已連線至雲端服務時，您會看到下列頁面：

    ![節點已連線頁面](media/data-factory-create-install-integration-runtime/node-is-connected.png)

16. 現在，測試您 SQL Server 資料庫的連線。

    ![[診斷] 索引標籤](media/data-factory-create-install-integration-runtime/config-manager-diagnostics-tab.png)   

    a. 在 [組態管理員] 頁面上，移至 [診斷] 索引標籤。

    b. 選取 [SqlServer] 作為資料來源類型。

    c. 輸入伺服器名稱。

    d. 輸入資料庫名稱。

    e. 選取驗證模式。

    f. 輸入使用者名稱。

    g. 輸入使用者名稱的密碼。

    h. 請選取 [測試]，以確認整合執行階段可以連線到 SQL Server。 如果連線成功，您會看到綠色的核取記號。 如果連線不成功，您會看到一則錯誤訊息。 修正所有問題，並確定整合執行階段可連線到 SQL Server。    

    > [!NOTE]
    > 請記下驗證類型、伺服器、資料庫、使用者和密碼的值。 您稍後會在本教學課程中用到。 
    
