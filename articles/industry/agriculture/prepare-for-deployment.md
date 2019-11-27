---
title: 部署 Azure FarmBeats
description: 說明如何部署 FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: ccd8fc293d284150d7318242b71a46a99fec12fa
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406313"
---
# <a name="deploy-farmbeats"></a>部署 FarmBeats

本文說明如何設定 Azure FarmBeats。

Azure FarmBeats 是適用于資料驅動農業的業界專屬可擴充解決方案，可讓您順暢地布建和感應器裝置連線到 Azure 雲端、遙測資料收集和匯總。 Azure FarmBeats 具有各種感應器（例如相機、無人機、泥土感應器，以及從雲端管理裝置），其中包括 Azure 中的基礎結構和服務，可供 IoT 準備（物聯網）裝置用於擴充 web 和行動應用程式，以提供視覺效果、警示和深入解析。

> [!NOTE]
> 只有在公用雲端環境中才支援 Azure FarmBeats。 如需雲端環境的詳細資訊，請參閱[Azure](https://azure.microsoft.com/overview/what-is-a-public-cloud/)。

Azure FarmBeats 有下列兩個元件：

- **資料中樞**-資料中樞是 Azure FarmBeats 的平台層，可讓您建立、儲存、處理資料，以及從現有或新的資料管線繪製深入解析。 此平台層適用于執行及建立您的農業資料管線和模型。

- **加速器**-加速器是 Azure FarmBeats 的解決方案層，其具有內建應用程式，可使用預先建立的農業模型來說明 Azure FarmBeats 的功能。 此解決方案層可讓您建立伺服器陣列界限，並從伺服器陣列界限內容中的農業資料中繪製深入解析。

Azure FarmBeats 的快速部署應該不到一小時的時間。 資料中樞和加速器的成本會根據使用量而有所不同。

## <a name="deployed-resources"></a>已部署的資源

Azure FarmBeats 部署會在您的訂用帳戶內建立下列列出的資源：

|S.No  |資源名稱  |Azure FarmBeats 元件  |
|---------|---------|---------|
|1  |       Azure Cosmos DB   |  資料中樞       |
|2  |    Application Insights      |     資料中樞/加速器     |
|3  |Azure Cache for Redis   |資料中樞   |
|4  |       Azure 金鑰保存庫    |  資料中樞/加速器        |
|5  |    時間序列深入解析       |     資料中樞      |
|6 |      EventHub 命名空間    |  資料中樞       |
|7  |    Azure Data Factory V2       |     資料中樞/加速器      |
|8  |批次帳戶    |資料中樞   |
|9  |       儲存體帳戶     |  資料中樞/加速器        |
|10  |    邏輯應用程式        |     資料中樞      |
|11  |    API 連接        |     資料中樞      |
|12|      App Service      |  資料中樞/加速器       |
|13 |    App Service 方案        |     資料中樞/加速器      |
|14 |Azure 地圖服務帳戶     |加速器    |
|15 |       時間序列深入解析      |  資料中樞     |

Azure FarmBeats 可讓您從 Azure Marketplace 下載。 您可以直接從 Azure 入口網站存取它。  

## <a name="create-azure-farmbeats-offer-on-marketplace"></a>在 marketplace 上建立 Azure FarmBeats 供應專案

使用下列步驟在 marketplace 中建立 Azure FarmBeats 供應專案：

1. 登入 Azure 入口網站並在右上角選取您的帳戶，然後切換至您要部署 Microsoft Azure FarmBeats 的 Azure AD 租使用者。
2. Azure FarmBeats 可在 Azure Marketplace 取得。 在 [marketplace] 頁面上，選取 [立即取得]。
3. 選取 [建立] 並輸入下列資訊：
    - 訂用帳戶名稱。
    - 現有的資源組名（僅限空的資源群組），或建立新的資源群組來部署 Azure FarmBeats。 請在後續章節中記下此資源群組。
4. 您想要安裝 Azure FarmBeats 的區域。 目前在下欄區域中支援 Azure FarmBeats：美國中部、西歐、美國東部2、北歐、美國西部、東南亞、美國東部、澳大利亞東部、美國西部2。
5. 選取 [確定]。
[使用規定] 頁面隨即出現。 請參閱標準 marketplace 條款，或選取超連結以檢查使用條款。
6. 依序選取 [**關閉**] 和 [我同意] 核取方塊，然後選取 [**建立**]。
7. 您現在已成功簽署 marketplace 上的 Azure FarmBeats 使用者授權合約（EULA）。  
7. 若要繼續進行部署，請遵循本指南的後續步驟。

## <a name="prepare"></a>準備

您需要下列部署 Azure FarmBeats 的許可權：

- 租使用者：讀取存取權
- 訂用帳戶：參與者或擁有者
- 資源群組：擁有者

## <a name="before-you-begin"></a>開始之前

在起始部署之前，請確定您具有下列各項：

- Sentinel 帳戶
- Azure Active Directory （AD）應用程式註冊

## <a name="create-a-sentinel-account"></a>建立 sentinel 帳戶    

具有 sentinel 的帳戶可協助您將 sentinel 衛星影像從其官方網站下載至您的裝置。 請遵循下列步驟來建立免費帳戶：

移至 https://scihub.copernicus.eu/dhus/#/self-registration。 在 [註冊] 頁面中，提供名字、姓氏、使用者名稱、密碼和電子郵件。
驗證電子郵件將會傳送至已註冊的電子郵件地址以進行確認。 選取連結並確認。 您的註冊程式已完成。

## <a name="create-azure-ad-app-registration"></a>建立 Azure AD 應用程式註冊

若要在 Azure FarmBeats 上進行驗證和授權，您必須擁有 Azure active directory 應用程式註冊，其：

- 案例1：安裝程式可以自動建立（前提是您擁有必要的租使用者、訂用帳戶和資源群組存取權限）。
- 案例2：您可以在部署 Azure FarmBeats 之前建立和設定（需要手動步驟）。

**案例 1**：如果您有權建立 AAD 應用程式註冊，則可以略過此步驟，並讓安裝程式建立應用程式註冊。 請繼續進行下一節：[準備輸入 json](#prepare-input-json-file)檔案

如果您已經有訂用帳戶，您可以直接移至下一個程式。

**案例 2**：當您沒有足夠的許可權可以在訂用帳戶內建立和設定 Azure AD 應用程式註冊時，這個方法是慣用的步驟。 要求您的系統管理員使用[自訂腳本](https://aka.ms/FarmBeatsAADScript)，以協助 IT 系統管理員在 Azure 入口網站上自動產生和設定 Azure AD 應用程式註冊。 若要使用 PowerShell 環境執行此自訂腳本的輸出，IT 系統管理員必須與您共用 Azure Active Directory 應用程式用戶端識別碼和密碼秘密。 記下這些值。

請使用下列步驟來執行 Azure AD 應用程式註冊腳本：

1. 下載[腳本](https://aka.ms/FarmBeatsAADScript)。
2. 登入以 Azure 入口網站，然後選取您的訂用帳戶和 AD 租使用者。
3. 從 Azure 入口網站的頂端導覽啟動 Cloud Shell。

    ![專案伺服器陣列的節拍](./media/prepare-for-deployment/navigation-bar-1.png)


4. 系統會提示第一次使用者選取訂用帳戶，以建立儲存體帳戶和 Microsoft Azure 檔案共用。 選取 [建立儲存體]。
5. 第一次會提示使用者選擇慣用的 shell 體驗-Bash 或 PowerShell。 選擇 [PowerShell]。
6. 將腳本（來自步驟1）上傳至 Cloud Shell，並記下上傳檔案的位置。

    > [!NOTE]
    > 根據預設，它會上傳至您的主目錄。

    使用下列指令碼：

    ```azurepowershell-interactive
    ./create_aad_script.ps1
    ```
7. 記下 [Azure AD 應用程式識別碼] 和 [用戶端密碼]，以與部署 Azure FarmBeats 的人員共用。

### <a name="prepare-input-json-file"></a>準備輸入 Json 檔案

在安裝過程中，請建立一個輸入 json 檔案，如下所示：

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

此檔案是用來 Azure Cloud Shell 的輸入檔，以及在安裝期間使用其值的參數。 Json 中的所有參數都必須以適當的值取代或移除;若已移除，安裝程式會在安裝期間提示您


請先檢查參數，然後再準備檔案。

|命令 | 描述|
|--- | ---|
|sku  | 提供選擇以下載 Azure FarmBeats 的其中一個或兩個元件。 指定要下載的元件。 若只要安裝資料中樞，請使用 "onlydatabhub"。 若要安裝資料中樞和加速器，請使用 "both"|
|subscriptionId | 指定用來安裝 Azure FarmBeats 的訂用帳戶|
|datahubResourceGroup| 資料中樞資源的資源組名|
|location |您想要建立資源的位置|
|acceleratorWebsiteName |將資料中樞命名為唯一的 URL 前置詞|
|acceleratorResourceGroup  | 命名您的加速器網站的唯一 URL 前置詞。|
|datahubWebsiteName  | UUnique URL 前置詞以命名您的資料中樞網站。 |
|sentinelUsername | 用來登入的使用者名稱： https://scihub.copernicus.eu/dhus/#/self-registration。|
|notificationEmailAddress  | 用來接收您在資料中樞內設定之任何警示通知的電子郵件地址。|
|updateIfExists|選擇性只有在您想要升級現有的 Azure FarmBeats 實例時，才會將參數包含在輸入 Json 中。 如需升級，請查看其他詳細資料，例如 資源組名、位置等必須相同。|
|aadAppClientId | [**選用**]只有在 Azure AD 應用程式已存在時，才會將參數包含在輸入 Json 中。  |
|aadAppClientSecret  | [**選用**]只有在 Azure AD 應用程式已存在時，才會將參數包含在輸入 Json 中。|

## <a name="deploy-within-cloud-shell-browser-based-command-line"></a>在 Cloud Shell 以瀏覽器為基礎的命令列內部署

在上述 marketplace 工作流程中，您必須建立一個資源群組，並簽署使用者授權合約，以便在實際部署中再次檢查一次。 您可以使用 Bash 環境，透過 Azure Cloud Shell （以瀏覽器為基礎的命令列）來完成部署。 請繼續進行下一節，以透過 Cloud Shell 進行部署。

> [!NOTE]
> 非使用中 Cloud Shell 會話會在20分鐘後到期。 請嘗試在這段時間內完成部署。

1. 登入 Azure 入口網站，然後選取所需的訂用帳戶和 AD 租使用者。
2. 從 Azure 入口網站的頂端導覽啟動 Cloud Shell。
3. 如果您是第一次使用 Cloud Shell，系統會提示您選取訂用帳戶以建立儲存體帳戶和 Microsoft Azure 檔案共用。
4. 選取 [**建立儲存體**]。  

選取 [環境] 做為 Bash （而不是 PowerShell）。

## <a name="deployment-scenario-1"></a>部署案例1

安裝程式會建立 Azure AD App 註冊（上述案例1）

1. 複製下列範本，並將其命名為 input. json。  
範例 JSON 輸入：

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

2. 儲存檔案，並記下路徑（在您的本機電腦上）。
3. 移至 Azure Cloud Shell，並在驗證成功後，選取 [上傳] （請參閱下方影像中反白顯示的圖示），然後將輸入 json 檔案上傳至 Cloud Shell 儲存體。  

    ![專案伺服器陣列的節拍](./media/prepare-for-deployment/bash-2-1.png)

4. 在 cloud shell 中移至您的主目錄。 根據預設，它是/home/<username>
5. 在 Cloud Shell 中輸入或貼上下列命令。 請務必修改輸入的路徑。 Json 檔案，然後按 enter 鍵。

   ```bash
      wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
     安裝程式會自動下載所有相依性，並建立部署器。 系統會提示您同意 Azure FarmBeats 使用者授權合約（EULA）。

     - 如果您同意，請輸入 ' Y '，您會繼續進行下一個步驟。
     - 如果您不同意條款，且部署將會終止，請輸入 ' N '。

6. 然後，系統會提示您輸入部署的存取權杖。 複製產生的程式碼，並使用您的 Azure 認證登入 https://microsoft.com/devicelogin。

    > [!NOTE]
    > 權杖會在60分鐘後到期。 當它到期時，您可以重新開機，方法是再次輸入部署命令。

7. 出現提示時，輸入您的 Sentinel 帳戶密碼。
8. 安裝程式現在會驗證並開始部署，這可能需要大約20分鐘的時間。
9. 部署成功之後，您將會收到下列輸出連結：

 - **資料中樞 URL**：用來試用 Azure FarmBeats api 的 Swagger 連結。
 - **快速鍵 URL**：用來探索 Azure FarmBeats 智慧型伺服器陣列加速器的使用者介面。
 - 部署期間所建立的部署器**記錄**檔記錄檔。 如有需要，可用於進行疑難排解。

## <a name="deployment-scenario-2"></a>部署案例2

現有的 Azure Active Directory 應用程式註冊可用來部署（上述案例2）

1. 複製下列 JSON 檔案，其中包含在輸入 JSON 中的 Azure 應用程式用戶端識別碼和密碼，並加以儲存。

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

遵循其餘的步驟：

2. 記下您的輸入 json 檔案的路徑（在本機電腦上）。
3. 再次移至 Azure Cloud Shell 並成功驗證，選取 [上傳] 按鈕（請參閱下方影像中反白顯示的圖示），然後將輸入 json 檔案上傳至 Cloud Shell 儲存體。

    ![專案伺服器陣列的節拍](./media/prepare-for-deployment/bash-2-1.png)

4. 在 cloud shell 中移至您的主目錄。 根據預設，它是/home/<username>
5. 在 Cloud Shell 中輸入或貼上下列命令。 請務必修改輸入的路徑。 Json 檔案，然後按 enter 鍵。

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

遵循畫面上的指示。

6. 腳本會自動下載所有相依性，並建立部署器。
7. 系統會提示您閱讀並同意 Azure FarmBeats 使用者授權合約（EULA）。

    - 如果您同意，請輸入 ' Y '，您會繼續進行下一個步驟。
    - 如果您不同意條款，且部署將會終止，請輸入 ' N '。

8. 系統會提示您輸入部署的存取權杖。 複製所產生的程式碼，並使用您的 Azure 認證登入 https://microsoft.com/devicelogin。
9. 安裝程式現在會驗證並開始建立資源，這可能需要大約20分鐘的時間。 在這段期間 Cloud Shell 讓會話保持作用中狀態。
10. 一旦部署成功完成，您將會收到下列輸出連結：

 - **資料中樞 URL**：用來試用 Azure FarmBeats api 的 Swagger 連結。
 - **快速鍵 URL**：用來探索 Azure FarmBeats 加速器的使用者介面。
 - 部署器**記錄**檔：在部署期間建立的記錄檔。 如有需要，可用於進行疑難排解。

如果您遇到任何問題，請參閱[疑難排解](troubleshoot-project-farmbeats.md)。


## <a name="validate-deployment"></a>驗證部署

### <a name="data-hub"></a>資料中樞

資料中樞安裝完成後，您會收到 URL，以透過 Swagger 介面存取 Azure FarmBeats Api，格式為： HTTPs://\<yourdatahub-網站名稱 >. azurewebsites. net/Swagger

1. 若要透過 Swagger 登入，請在瀏覽器中複製並貼上 URL。
2. 使用 Azure 入口網站認證登入。
3. 健全測試（選擇性）

     - 能夠使用「資料中樞」連結成功登入 Swagger 入口網站，而您會收到該連結作為成功部署的輸出。
     - 擴充類型取得 API-選取 [立即試用]/Execute
     - 您應該會收到伺服器回應碼200，而不是例外狀況，例如403「未經授權的使用者」。

### <a name="accelerator"></a>加速器

加速器安裝完成後，您會收到 URL 來存取 Azure FarmBeats 使用者介面，其格式如下： HTTPs://\<快速鍵-網站名稱 >. azurewebsites. net

1. 若要從加速器登入，請在瀏覽器中複製並貼上 URL。
2. 使用 Azure 入口網站認證登入。

## <a name="upgrade"></a>升級

升級步驟類似于第一次安裝。 請遵循下列步驟：

1. 登入以 Azure 入口網站，然後選取您想要的訂用帳戶和 AD 租使用者。
2. 從 Azure 入口網站的頂端導覽啟動 Cloud Shell。

   ![專案伺服器陣列的節拍](./media/prepare-for-deployment/navigation-bar-1.png)

3. 從 shell 左邊的下拉式選單中，選取 [環境] 做為 [Bash]。
4. 只有在需要時才對您的輸入 json 檔案進行變更，並上傳至 Azure Cloud Shell。 例如，您可以更新您想要接收之通知的電子郵件地址。
5. 將輸入 json 檔案上傳至 Azure Cloud Shell。
6. 在 Cloud Shell 中輸入或貼上下列兩個命令。 請務必修改輸入 json 檔案的路徑，然後按 enter 鍵。

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
遵循畫面上的指示：

7. 安裝程式會在執行時間自動提示所需的輸入：
8. 輸入用於部署的存取權杖。 複製所產生的程式碼，並使用您的 Azure 認證登入 https://microsoft.com/devicelogin。
9. Sentinel 密碼
10. 安裝程式現在會驗證並開始建立資源，這可能需要大約20分鐘的時間。
11. 部署成功之後，您將會收到下列輸出連結：
 - **資料中樞 URL**：用來試用 Azure FarmBeats api 的 Swagger 連結。
 - **快速鍵 URL**：用來探索 Azure FarmBeats 加速器的使用者介面。
 - 部署器**記錄**檔：在部署期間儲存記錄。 它可以用來進行疑難排解。

> [!NOTE]
> 請記下上述值，以供日後使用。


## <a name="uninstall"></a>解除安裝

目前不支援使用安裝程式自動卸載 Azure FarmBeats。 若要移除資料中樞或加速器，請在 Azure 入口網站中，刪除已安裝這些元件的資源群組，或手動刪除資源。

例如，如果您將資料中樞和加速器部署在兩個不同的資源群組中，您會刪除這些資源群組，如下所示：

1. 登入 Azure 入口網站。
2. 在右上角選取您的帳戶，並切換至您想要部署 Azure FarmBeats 的所需 Azure AD 租使用者。

   > [!NOTE]
   > 需要資料中樞，加速器才能正常運作。 我們不建議您卸載資料中樞，而不需要卸載加速器。

3. 選取 [資源群組]，然後輸入您想要刪除的資料中樞或加速器資源群組的名稱。
4. 選取資源群組名稱。 再次輸入名稱以進行雙重檢查，然後選取 [刪除] 以移除資源群組及其所有基礎資源。
5. 或者，您可以手動刪除每個資源，這不是建議的做法。
7. 若要刪除/卸載資料中樞，請直接移至 Azure 上的資源群組，並從該處刪除資源群組。

## <a name="next-steps"></a>後續步驟

您已部署 Azure FarmBeats。 現在，請瞭解如何[建立伺服器](manage-farms.md#create-farms)陣列。
