---
title: 部署 Azure FarmBeats
description: 本文說明如何部署 Azure FarmBeats。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: c29bba81132df15fcea9ff0df7be6a8cea94c9a0
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851123"
---
# <a name="deploy-azure-farmbeats"></a>部署 Azure FarmBeats

本文說明如何設定 Azure FarmBeats。

Azure FarmBeats 是適用于資料驅動農業的業界專屬可擴充解決方案，可讓您順暢地布建和感應器裝置連線到 Azure 雲端、遙測資料收集和匯總。 Azure FarmBeats 包含各種感應器，例如相機、無人機和土裡部感應器。 透過 Azure FarmBeats，您可以從雲端管理裝置，包括在 Azure 中管理適用于 IoT 的裝置的基礎結構和服務，以及使用可擴充的 web 和行動應用程式來提供視覺效果、警示和深入解析。

> [!NOTE]
> 只有在公用雲端環境中才支援 Azure FarmBeats。 如需詳細資訊，請參閱[什麼是公用雲端？](https://azure.microsoft.com/overview/what-is-a-public-cloud/)。

Azure FarmBeats 有下列兩個元件：

- **Datahub**： Azure FarmBeats 的平台層，可讓您建立、儲存、處理資料，以及從現有或新的資料管線繪製深入解析。 此平台層適用于執行及建立您的農業資料管線和模型。

- **加速器**： Azure FarmBeats 的解決方案層，其具有內建應用程式，可使用預先建立的農業模型來說明 Azure FarmBeats 的功能。 此解決方案層可讓您建立伺服器陣列界限，並從伺服器陣列界限內容中的農業資料中繪製深入解析。

Azure FarmBeats 的快速部署應該不到一小時的時間。 視使用量而定，Datahub 和加速器的成本會有所不同。

## <a name="deployed-resources"></a>已部署的資源

Azure FarmBeats 部署會在您的訂用帳戶中建立下列資源：

| S. 編號  | 資源名稱  | FarmBeats 元件  |
|---------|---------|---------|
|1  |       Azure Cosmos DB   |  Datahub       |
|2  |    Application Insights      |     Datahub/加速器     |
|3  |Azure Cache for Redis   |Datahub   |
|4  |       Azure 金鑰保存庫    |  Datahub/加速器        |
|5  |    Azure 時間序列深入解析       |     Datahub      |
|6 |      Azure 事件中樞命名空間    |  Datahub       |
|7  |    Azure Data Factory V2       |     Datahub/加速器      |
|8  |批次帳戶    |Datahub   |
|9  |       儲存體帳戶     |  Datahub/加速器        |
|10  |    邏輯應用程式        |     Datahub      |
|11  |    API 連接        |     Datahub      |
|12|      Azure App Service      |  Datahub/加速器       |
|13 |    App Service 方案        |     Datahub/加速器      |
|14 |Azure 地圖服務帳戶     |加速器    |
|15 |       Azure 時間序列深入解析      |  Datahub     |

Azure FarmBeats 可在 Azure Marketplace 下載，您可以直接從 Azure 入口網站存取。  

## <a name="create-an-azure-farmbeats-offer-in-azure-marketplace"></a>在 Azure Marketplace 中建立 Azure FarmBeats 供應專案

若要在 Azure Marketplace 中建立 Azure FarmBeats 供應專案，請執行下列動作：

1. 登入 Azure 入口網站，在右上方選取您的帳戶，然後切換至您要部署 Azure FarmBeats 的 Azure Active Directory （Azure AD）租使用者。
1. Azure Marketplace 提供 Azure FarmBeats。 在 [ **Azure Marketplace** ] 頁面上，選取 [**立即取得**]。
1. 選取 [建立]。

> [!NOTE]
> 在 Azure Marketplace 中完成供應專案只是安裝程式的一部分。 若要在您的 Azure 訂用帳戶中完成 Azure FarmBeats 的部署，請遵循下一節中的指示。

## <a name="prepare"></a>準備

您必須具備下列許可權，才能部署 Azure FarmBeats：

- **租**使用者：讀取存取權
- **訂**用帳戶：參與者或擁有者
- **資源群組**：擁有者

## <a name="before-you-begin"></a>開始之前

開始部署之前，請確定已備妥下列必要條件：

- Sentinel 帳戶
- Azure AD 應用程式註冊

## <a name="create-a-sentinel-account"></a>建立 Sentinel 帳戶    

具有 Sentinel 的帳戶可協助您將 Sentinel 衛星影像從其官方網站下載至您的裝置。 若要建立免費帳戶，請執行下列動作：

1. 移至 [ [Sentinel 帳戶] 註冊頁面](https://scihub.copernicus.eu/dhus/#/self-registration)。 
1. 在註冊表單中，提供您的名字、姓氏、使用者名稱、密碼和電子郵件地址。

驗證電子郵件將會傳送至已註冊的電子郵件地址以進行確認。 選取連結以確認您的電子郵件地址。 您的註冊程式已完成。

## <a name="create-an-azure-ad-app-registration"></a>建立 Azure AD 應用程式註冊

若要在 Azure FarmBeats 上進行驗證和授權，您必須擁有 Azure AD 應用程式註冊。 您可以透過下列兩種方式之一來建立它：

* **選項 1**：如果您擁有必要的租使用者、訂用帳戶和資源群組存取權限，則安裝程式可以自動建立註冊。 如果這些許可權已就緒，請繼續進行「[準備輸入 json](#prepare-the-inputjson-file)檔案」一節。

* **選項 2**：您可以在部署 Azure FarmBeats 之前，手動建立和設定註冊。 當您沒有在訂用帳戶內建立和設定 Azure AD 應用程式註冊的必要許可權時，我們建議使用此方法。 請要求您的系統管理員使用[自訂腳本](https://aka.ms/FarmBeatsAADScript)，以協助 IT 系統管理員在 Azure 入口網站上自動產生和設定 Azure AD 應用程式註冊。 若要使用 PowerShell 環境執行此自訂腳本的輸出，IT 系統管理員必須與您共用 Azure AD 應用程式用戶端識別碼和密碼秘密。 記下這些值。

    若要執行 Azure AD 應用程式註冊腳本，請執行下列動作：

    1. 下載[腳本](https://aka.ms/FarmBeatsAADScript)。
    1. 登入 Azure 入口網站，然後選取您的訂用帳戶和 Azure AD 租使用者。
    1. 從 Azure 入口網站頂端的工具列啟動 Cloud Shell。

        ![專案 FarmBeats](./media/prepare-for-deployment/navigation-bar-1.png)

    1. 如果您是第一次的使用者，系統會提示您選取訂用帳戶來建立儲存體帳戶和 Microsoft Azure 檔案共用。 選取 [建立儲存體]。
    1. 第一次的使用者也會提供 shell 體驗、Bash 或 PowerShell 的選擇。 選擇 [PowerShell]。
    1. 將腳本（來自步驟1）上傳至 Cloud Shell，並記下上傳檔案的位置。

        > [!NOTE]
        > 根據預設，檔案會上傳到您的主目錄。

        使用下列指令碼：

        ```azurepowershell-interactive
        ./create_aad_script.ps1
        ```
    1. 記下 [Azure AD 應用程式識別碼] 和 [用戶端密碼]，以與部署 Azure FarmBeats 的人員共用。

### <a name="prepare-the-inputjson-file"></a>準備輸入 json 檔案

在安裝過程中，請建立一個*輸入 json*檔案，如下所示：

```json
{  
    "sku":"both",
    "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx",
    "datahubResourceGroup":"dummy-test-dh1",
    "location":"westus2",
    "datahubWebsiteName":"dummy-test-dh1",
    "acceleratorResourceGroup":" dummy-test-acc1",
    "acceleratorWebsiteName":" dummy-test-acc1",
    "sentinelUsername":"dummy-dev",
    "notificationEmailAddress":"dummy@yourorg.com",
    "updateIfExists":true
}
```

此檔案是您要 Azure Cloud Shell 的輸入檔案。 其中包含在安裝期間使用其值的參數。 JSON 檔案中的所有參數都必須以適當的值取代或移除。 它會被移除，安裝程式會在安裝期間提示您。

在準備檔案之前，請先檢查下表中的參數：

|命令 | 描述|
|--- | ---|
|sku  | 提供可供下載 Azure FarmBeats 的其中一個或兩個元件的選項。 它會指定要下載的元件。 若只要安裝 Datahub，請使用 "onlydatabhub"。 若要安裝 Datahub 和加速器，請使用 "both"。|
|subscriptionId | 指定用來安裝 Azure FarmBeats 的訂用帳戶。|
|datahubResourceGroup| Datahub 資源的資源組名。|
|location |您想要建立資源的位置。|
|acceleratorWebsiteName |用來命名 Datahub 實例的唯一 URL 前置詞。|
|acceleratorResourceGroup  | 命名您的加速器網站所用的唯一 URL 前置詞。|
|datahubWebsiteName  | 用來命名 Datahub 網站的唯一 URL 前置詞。 |
|sentinelUsername | 要用來登入的使用者名稱（例如 `https://scihub.copernicus.eu/dhus/#/self-registration`）。|
|notificationEmailAddress  | 用來接收您在 Datahub 實例中設定之任何警示通知的電子郵件地址。|
|updateIfExists| 選擇性只有在您想要升級現有的 Azure FarmBeats 實例時，才會包含在*輸入 json*檔案中的參數。 若要進行升級，其他詳細資料（例如資源組名和位置）必須相同。|
|aadAppClientId | 選擇性只有在 Azure AD 應用程式已存在時，才會包含在*輸入 json*檔案中的參數。  |
|aadAppClientSecret  | 選擇性只有在 Azure AD 應用程式已存在時，才會包含在*輸入 json*檔案中的參數。|

## <a name="deploy-the-app-within-cloud-shell"></a>在 Cloud Shell 中部署應用程式

在先前討論的 marketplace 工作流程中，您必須已建立一個資源群組，並簽署「授權條款」合約，您可以在實際部署過程中再次檢查。 您可以使用 Bash 環境，透過 Cloud Shell 以瀏覽器為基礎的命令列介面來部署應用程式。 若要透過 Cloud Shell 部署，請繼續進行下一節。

> [!NOTE]
> 非使用中 Cloud Shell 會話會在20分鐘後到期。 請嘗試在這段時間內完成部署。

1. 登入 Azure 入口網站，然後選取您想要使用的訂用帳戶和 Azure 租使用者。
1. 從 Azure 入口網站頂端的工具列啟動 Cloud Shell。
1. 如果您是第一次使用 Cloud Shell，系統就會提示您選取訂用帳戶來建立儲存體帳戶和 Azure 檔案儲存體共用。
1. 選取 [**建立儲存體**]。  
1. 針對環境，選取 [ **Bash** （不是 PowerShell）]。

## <a name="deployment-scenario-1"></a>部署案例1

在此案例中（稍早在「選項1」中會描述），安裝程式會自動建立 Azure AD 應用程式註冊。 您可以執行下列動作來部署 FarmBeats：

1. 複製下列範例 JSON 範本，並將其儲存為*輸入 JSON*。 請務必記下本機電腦上的檔案路徑。

    ```json
    {  
       "sku":"both", 
       "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx", 
       "datahubResourceGroup":"dummy-test-dh1", 
       "location":"eastus2", 
       "datahubWebsiteName":"dummy-test-dh1", 
       "acceleratorResourceGroup":" dummy-test-acc1",   
       "acceleratorWebsiteName":" dummy-test-acc1", 
       "sentinelUsername":"dummy-dev", 
       "notificationEmailAddress":" dummy@microsoft.com", 
       "updateIfExists":true 
    }
    ```

1. 開啟 Azure Cloud Shell。 成功驗證之後，請選取 [**上傳**] 按鈕（在下圖中反白顯示），然後將*輸入 json*檔案上傳至 Cloud Shell 儲存體。  

    ![專案 FarmBeats](./media/prepare-for-deployment/bash-2-1.png)

1. 在 Cloud Shell 中，移至您的主目錄。 根據預設，目錄名稱會是 */home/\<username >* 。
1. 在 Cloud Shell 中，輸入下列命令。 請務必修改*輸入 json*檔案的路徑，然後選取 Enter 鍵。

   ```bash
      wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
     安裝程式會自動下載所有相依性，並建立部署器。 系統會提示您同意 Azure FarmBeats 授權條款。

     - 如果您同意，請輸入**Y**以繼續進行下一個步驟。
     - 如果您不同意，請輸入**N**，部署將會終止。

1. 在提示中，輸入部署的存取權杖。 複製產生的程式碼，並使用您的 Azure 認證登入[裝置登入頁面](https://microsoft.com/devicelogin)。

    > [!NOTE]
    > 權杖會在60分鐘後到期。 若已過期，您可以重新輸入部署命令以重新開機。

1. 在提示字元中，輸入您的 Sentinel 帳戶密碼。
   
   安裝程式會驗證並啟動部署，這可能需要大約20分鐘的時間。

   成功完成部署之後，您會收到下列輸出連結：

    - **DATAHUB URL**：用於嘗試 Azure FarmBeats Api 的 Swagger 連結。
    - **快速鍵 URL**：用於探索 Azure FarmBeats 智慧型伺服器陣列加速器的使用者介面。
    - 部署程式**記錄**檔：在部署期間建立的記錄檔。 如有需要，您可以使用它來進行疑難排解。

## <a name="deployment-scenario-2"></a>部署案例2

在此案例中（稍早的「選項2」所述），您可以執行下列動作，使用現有的 Azure Active Directory 應用程式註冊來部署 FarmBeats：

1. 複製下列 JSON 檔案，其中包含在*輸入 JSON*檔案中的 Azure 應用程式用戶端識別碼和密碼，並加以儲存。 請務必記下本機電腦上的檔案路徑。

    ```json
   {

   "sku":"both",
   "subscriptionId":"daxx9xxx-d18f-4xxc-9c21-5xx3exxxxx45",
   "datahubResourceGroup":"dummy-test-dh1",   
   "location":"westus2",   
   "datahubWebsiteName":"dummy-test-dh1",   
   "acceleratorResourceGroup":"dummy-test-acc1",   
   "acceleratorWebsiteName":"dummy-test-acc1",   
   "sentinelUsername":"dummy-dev",
   "notificationEmailAddress":"dummyuser@org1.com",
   "updateIfExists": true,
   "aadAppClientId": "lmtlemlemylmylkmerkywmkm823",
   "aadAppClientSecret": "Kxxxqxxxxu*kxcxxx3Yxxu5xx/db[xxx"

   }
   ```

1. 再次前往 Azure Cloud Shell，而且您已成功驗證。
1. 選取 [**上傳**] 按鈕（在下圖中反白顯示），然後將*輸入 json*檔案上傳至 Cloud Shell 儲存體。

    ![專案 FarmBeats](./media/prepare-for-deployment/bash-2-1.png)

1. 在 Cloud Shell 中，移至您的主目錄。 根據預設，目錄名稱會是 */home/\<username >* 。
1. 在 Cloud Shell 中，輸入下列命令。 請務必修改*輸入 json*檔案的路徑，然後選取 Enter 鍵。

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

     安裝程式會自動下載所有相依性，並建立部署器。 系統會提示您同意 Azure FarmBeats 授權條款。

     - 如果您同意，請輸入**Y**以繼續進行下一個步驟。
     - 如果您不同意，請輸入**N**，部署將會終止。

1. 在提示中，輸入部署的存取權杖。 複製產生的程式碼，並使用您的 Azure 認證登入[裝置登入頁面](https://microsoft.com/devicelogin)。

   安裝程式會驗證並啟動部署，這可能需要大約20分鐘的時間。

   成功完成部署之後，您會收到下列輸出連結：

    - **DATAHUB URL**：用於嘗試 Azure FarmBeats Api 的 Swagger 連結。
    - **快速鍵 URL**：用於探索 Azure FarmBeats 智慧型伺服器陣列加速器的使用者介面。
    - 部署程式**記錄**檔：在部署期間建立的記錄檔。 如有需要，您可以使用它來進行疑難排解。

如果您遇到任何問題，請參閱[疑難排解](troubleshoot-project-farmbeats.md)。


## <a name="validate-the-deployment"></a>驗證部署

### <a name="datahub"></a>Datahub

Datahub 安裝完成之後，您會收到 URL，以透過 Swagger 介面存取 Azure FarmBeats Api，格式為 HTTPs://\<yourdatahub-網站名稱 >. azurewebsites. net/Swagger。

1. 若要透過 Swagger 登入，請在瀏覽器中複製並貼上 URL。
1. 使用您的 Azure 入口網站認證登入。
1. 選擇性嘗試此「健全測試」： 

     - 使用 Datahub 連結（您收到做為成功部署的輸出），成功登入 Swagger 入口網站。
     - 擴充類型取得 API-選取 [立即試用]/Execute
     - 您應該會收到伺服器回應碼200，而不是例外狀況，例如「403未經授權的使用者」。

### <a name="accelerator"></a>加速器

加速器安裝完成之後，您會收到 URL，以存取 Azure FarmBeats 使用者介面，格式為 HTTPs://\<快速鍵-網站名稱 >. azurewebsites。

1. 若要從加速器登入，請在瀏覽器中複製並貼上 URL。
1. 使用您的 Azure 入口網站認證登入。

## <a name="upgrade"></a>升級

升級的指示類似于第一次安裝。 執行下列動作：

1. 登入 Azure 入口網站，然後選取您想要的訂用帳戶和 Azure 租使用者。
1. 從 Azure 入口網站的頂端導覽中開啟 Cloud Shell。

   ![專案 FarmBeats](./media/prepare-for-deployment/navigation-bar-1.png)

1. 在左側的 [**環境**] 下拉式清單中，選取 [ **Bash**]。
1. 如有需要，請變更您的*輸入 json*檔案，然後將它上傳至 Cloud Shell。 例如，您可以更新您想要接收之通知的電子郵件地址。
1. 輸入下列兩個命令，或將其貼入 Cloud Shell。 請務必修改*輸入 json*檔案路徑，然後選取 Enter 鍵。

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

   安裝程式會在執行時間自動提示您輸入必要的輸入。

1. 輸入用於部署的存取權杖。 複製所產生的程式碼，並使用您的 Azure 認證[登入裝置登入頁面](https://microsoft.com/devicelogin)。
1. 輸入您的 Sentinel 密碼。

   安裝程式現在會驗證並開始建立資源，這可能需要大約20分鐘的時間。

   成功完成部署之後，您會收到下列輸出連結：
    - **DATAHUB URL**：用於嘗試 Azure FarmBeats Api 的 Swagger 連結。
    - **快速鍵 URL**：用於探索 Azure FarmBeats 智慧型伺服器陣列加速器的使用者介面。
    - 部署程式**記錄**檔：在部署期間建立的記錄檔。 如有需要，您可以使用它來進行疑難排解。

> [!NOTE]
> 請記下先前的值，以供日後使用。


## <a name="uninstall"></a>解除安裝

目前，我們不支援使用安裝程式自動卸載 Azure FarmBeats。 若要移除 Datahub 或加速器，請在 Azure 入口網站中，刪除已安裝這些元件的資源群組，或手動刪除資源。

例如，如果您將 Datahub 和加速器部署在兩個不同的資源群組中，您可以刪除這些資源群組，如下所示：

1. 登入 Azure 入口網站。
1. 在右上方選取您的帳戶，然後切換至您要部署 Azure FarmBeats 的 Azure AD 租使用者。

   > [!NOTE]
   > 需要 Datahub，加速器才能正常運作。 我們不建議您卸載 Datahub，也不要卸載加速器。

1. 選取 [**資源群組**]，然後輸入您想要刪除的 Datahub 或加速器資源群組的名稱。
1. 選取資源群組名稱。 重新輸入名稱以確認它，然後選取 [**刪除**] 來移除資源群組及其所有基礎資源。
   或者，您可以手動刪除每個資源，這是我們不建議的方法。
1. 若要刪除或卸載 Datahub，請直接移至 Azure 上的資源群組，然後從該處刪除資源群組。

## <a name="next-steps"></a>後續步驟

您已瞭解如何部署 Azure FarmBeats。 接下來，瞭解如何[建立 FarmBeats 伺服器](manage-farms.md#create-farms)陣列。
