---
title: 部署 Azure FarmBeats
description: 說明如何部署 FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: c609285b727414b4849c9ef6654406a035005bb1
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797721"
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
|5  |    Time Series Insights       |     資料中樞      |
|6 |      EventHub 命名空間    |  資料中樞       |
|7  |    Azure Data Factory V2       |     資料中樞/加速器      |
|8  |批次帳戶    |資料中樞   |
|9  |       儲存體帳戶     |  資料中樞/加速器        |
|10  |    邏輯應用程式        |     資料中樞      |
|11  |    API 連接        |     資料中樞      |
|12|      App Service      |  資料中樞/加速器       |
|13 |    App Service 方案        |     資料中樞/加速器      |
|14 |Azure 地圖服務帳戶     |加速器    |
|15 |       Time Series Insights      |  資料中樞     |

Azure FarmBeats 可讓您從 Azure Marketplace 下載。 您可以直接從 Azure 入口網站存取它。  

## <a name="prepare"></a>準備

您需要下列部署 Azure FarmBeats 的許可權：

- 租使用者：讀取存取權
- 訂用帳戶：擁有者的參與者
- 資源群組：擁有者

## <a name="before-you-begin"></a>開始之前

在起始部署之前，請確定您具有下列各項：

- Sentinel 帳戶
- Azure Active Directory （應用程式註冊）
- Azure FarmBeats

## <a name="create-a-sentinel-account"></a>建立 sentinel 帳戶    

具有 sentinel 的帳戶可協助您將 sentinel 衛星影像從其官方網站下載至您的裝置。 請遵循下列步驟來建立免費帳戶：

1. 移至 https://scihub.copernicus.eu/dhus/#/self-registration。 在 [註冊] 頁面中，提供名字、姓氏、使用者名稱、密碼和電子郵件。
2. 在 [**選取網域**] 下拉式功能表中，選取 [**土地**] 選項。
3. 在 [**選取使用**方式] 下拉式功能表中，選取 [**教育**] 選項。
4. 在 [**選取國家/地區**] 下拉式功能表中，選取您的國家/地區。
5. 選取 [**註冊**] 以完成註冊程式。

驗證電子郵件將會傳送至已註冊的電子郵件地址以進行確認。 選取連結並確認。 您的註冊程式已完成。

## <a name="create-azure-ad-app-registration"></a>建立 Azure AD 應用程式註冊

若要在 Azure FarmBeats 上進行驗證和授權，您必須擁有 Azure active directory 應用程式註冊，其：

- 案例1：安裝程式可以自動建立（前提是您擁有必要的租使用者、訂用帳戶和資源群組存取權限）。
- 案例2：您可以在部署 Azure FarmBeats 之前建立和設定（需要手動步驟）。

**案例 1**：安裝程式假設您有權在所需的訂用帳戶中建立 Azure active directory 應用程式註冊。 若要註冊，請登入入口網站，移至**Azure active directory** > **應用程式註冊** > **新增註冊**。

如果您已經有訂用帳戶，您可以直接移至下一個程式。

**案例 2**：當您沒有足夠的許可權可以在訂用帳戶內建立和設定 Azure AD 應用程式註冊時，這個方法是慣用的步驟。 要求您的系統管理員使用[自訂腳本](https://aka.ms/FarmBeatsAADScript)，以協助 IT 系統管理員在 Azure 入口網站上自動產生和設定 Azure AD 應用程式註冊。 若要使用 PowerShell 環境執行此自訂腳本的輸出，IT 系統管理員必須與您共用 Azure Active Directory 應用程式用戶端識別碼和密碼秘密。 記下這些值。

請使用下列步驟來執行 Azure AD 應用程式註冊腳本：

1. 取得註冊[腳本](https://aka.ms/FarmBeatsAADScript)。
2. 登入以 Azure 入口網站，然後選取您的訂用帳戶和 AD 租使用者。
3. 從 Azure 入口網站的頂端導覽啟動 Cloud Shell。

    ![專案伺服器陣列的節拍](./media/prepare-for-deployment/navigation-bar-1.png)


4. 系統會提示第一次使用者選取訂用帳戶，以建立儲存體帳戶和 Microsoft Azure 檔案共用。 選取 [建立儲存體]。
5. 第一次會提示使用者選擇慣用的 shell 體驗-Bash 或 PowerShell。 選擇 [PowerShell]。
6. 在 Cloud Shell 輸入下列命令以執行腳本。

    ```powershell
    ./create_aad_script.ps1
    ```
7. 記下 [Azure AD 應用程式識別碼] 和 [用戶端密碼]，以與部署 Azure FarmBeats 的人員共用。

## <a name="create-azure-farmbeats-offer-on-marketplace"></a>在 marketplace 上建立 Azure FarmBeats 供應專案

使用下列步驟在 marketplace 中建立 Azure FarmBeats 供應專案：

1. 登入**Azure 入口網站**，並在右上角選取您的帳戶，然後切換至您要部署 Microsoft Azure FarmBeats 的 Azure AD 租使用者。
2. 選取 [**搜尋/Marketplace** ] 或 [**建立資源**]。 輸入**FarmBeats**以查看供應專案詳細資料。 請在此頁面上下載可透過 aka.ms 超連結取得的指南，再繼續進行後續步驟。
3. 選取 [**建立**] 並輸入下列資訊：

   - 輸入訂用帳戶名稱。
   - 輸入現有的資源組名（僅限空的資源群組），或建立新的資源群組來部署 Azure FarmBeats。 請記下此資源群組，以便在稍後的階段輸入 json 檔案中。
   - 輸入您想要安裝 Azure FarmBeats 的區域。 FarmBeats 目前可以安裝在下欄區域：美國中部、西歐、美國東部2、北歐、美國西部、東南亞、美國東部、澳大利亞東部、美國西部2。
4. 選取 **[確定]** ，這會將您重新導向至 [使用規定] 頁面。 請參閱標準 marketplace 條款，或選取超連結以檢查使用條款。
5. 依序選取 [**關閉**] 和 [我同意] 核取方塊，然後選取 [**建立**]。
6. 您現在已成功簽署 marketplace 上的 Azure FarmBeats 使用者授權合約（EULA）。 若要繼續進行部署，請遵循本指南的後續步驟。

### <a name="prepare-inputjson-file"></a>準備輸入 Json 檔案

這個檔案是您要 Azure Cloud Shell 的輸入檔，以及在部署期間不會提示上傳之前于此檔案中指定其值的參數，因此可節省您一些時間。  

> [!NOTE]
> 此檔案會輸入要 Azure Cloud Shell 的值。  為了節省時間，在部署期間，系統不會提示您新增到此檔案中的參數。 系統會提示您輸入遺漏的參數。

請先檢查參數，然後再準備檔案。

|命令 | 說明|
|--- | ---|
|限量  | 提供選擇以下載 Azure FarmBeats 的其中一個或兩個元件。 指定要下載的元件。 若只要安裝資料中樞，請使用 "onlydatabhub"。 若要安裝資料中樞和加速器，請使用 "both"。|
|訂閱  | 指定安裝 FarmBeats 的訂用帳戶|
|"datahubResourceGroup"  | 資料中樞資源的資源組名。|
|"datahubLocation" | 您想要儲存資料中樞資源的位置。|
|"acceleratorWebsiteName"  |將資料中樞命名為唯一的 URL 前置詞
Swagger 網站。 預設值為 [資料中樞資源組名]。 按 enter 鍵以繼續使用預設值。|
|"acceleratorResourceGroup"  | 資料中樞資源的資源組名。 |
|"acceleratorLocation"  | 儲存資料中樞資源的位置
"acceleratorWebsiteName"  | 命名您的加速器網站的唯一 URL 前置詞。 預設值是快速鍵。 按 enter 鍵以繼續使用預設值。|
|''sentinelUsername'' | 用來登入的使用者名稱： https://scihub.copernicus.eu/dhus/#/self-registration。|
|"sentinelPassword"  | 用來登入的密碼： https://scihub.copernicus.eu/dhus/#/self-registration。|
|"farmbeatsAppId"  | 要由小組 Azure FarmBeats 共用的值。  |
|"farmbeatsPassword"  | 要由小組 Azure FarmBeats 共用的值。|
|"notificationEmailAddress"  | 用來接收您在資料中樞內設定之任何警示通知的電子郵件地址。|
|"按照" aadAppClientId ""  |[**選用**]只有在 Azure AD 應用程式已存在時，才會將參數包含在輸入 Json 中。  -True/False。 若為第一次安裝，則為 False，若為升級案例則為 True。|
|"aadAppClientId"  | [**選用**]只有在 Azure AD 應用程式已存在時，才會將參數包含在輸入 Json 中。  |
|"aadAppClientSecret"   | [**選用**]只有在 Azure AD 應用程式已存在時，才會將參數包含在輸入 Json 中。|


範例 JSON 輸入：

```json
{  
   "sku":"both", 
   "subscriptionId": "da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx", 
   "datahubResourceGroup": "dummy-test-dh1", 
   "datahubLocation": "westus2", 
   "datahubWebsiteName": "dummy-test-dh1", 
   "acceleratorResourceGroup": "dummy-test-acc1", 
   "acceleratorLocation": "westus2", 
   "acceleratorWebsiteName": "dummy-test-acc1", 
   "sentinelUsername": "dummy-dev", 
   "farmbeatsAppId": "c3cb3xxx-27xx-4xxb-8xx6-3xxx2xxdxxx5c", 
   "notificationEmailAddress": "dummy@microsoft.com", 
   "updateIfExists": true
}
```
## <a name="deploy-within-cloud-shell-browser-based-command-line"></a>在 Cloud Shell 以瀏覽器為基礎的命令列內部署

在 marketplace 工作流程中，您已建立一個資源群組，並簽署了使用者授權合約，這可以在實際部署中再次檢查一次。 部署作業會透過使用 Bash 環境 Azure Cloud Shell （以瀏覽器為基礎的命令列）來完成。  

> [!NOTE]
> 非使用中 Cloud Shell 會話會在20分鐘後到期。 請嘗試在這段時間內完成部署。

1. 登入**Azure**入口網站，然後選取所需的訂用帳戶和 AD 租使用者。
2. 從**Azure**入口網站的頂端導覽啟動**Cloud Shell** 。

   ![專案伺服器陣列的節拍](./media/prepare-for-deployment/navigation-bar-1.png)

3. 選取用來建立儲存體帳戶和 Microsoft Azure 檔案共用的訂用帳戶。
4. 選取 [建立儲存體]。
5. 從 shell 視窗的左邊選取 [環境] 下拉式（Bash）。

   ![專案伺服器陣列的節拍](./media/prepare-for-deployment/bash-1-1.png)

### <a name="deployment-scenario-1"></a>部署案例1

安裝程式會建立 Azure AD （您擁有 AD 租使用者讀取權限）。  

1. 下載[輸入 json](https://aka.ms/PPInputJsonTemplate)範本。 在輸入 json 檔案中包含 Azure 應用程式用戶端識別碼和密碼，並加以儲存。
2. 在記事本中開啟下載的檔案，然後輸入值來填入檔案。

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
    "updateIfExists":true
    }

    ```

    > [!NOTE]
    > 您可以略過這個步驟，輸入將會在執行時間提示。

3. 儲存檔案，並記下路徑（在您的本機電腦上）。  
4. 移至 Azure Cloud Shell，並在驗證成功後，選取 [上傳] （請參閱下方影像中反白顯示的圖示），然後將輸入 json 檔案上傳至 Cloud Shell 儲存體。 您不需要在 json 內傳遞 Azure AD 參數，因為安裝程式將會為您建立和設定 Azure AD 應用程式註冊。

   ![專案伺服器陣列的節拍](./media/prepare-for-deployment/bash-2-1.png)

5. 在 Cloud Shell 中輸入或貼上「部署命令」。 請務必修改輸入的路徑。 Json 檔案，然後按 enter 鍵。  

    ```
    wget -N -O farmbeats-installer.sh https://aka.ms/FB_1.2.0 && bash farmbeats-installer.sh> /home/dummyuser/input.json

    ```
   腳本會自動下載所有相依性，並建立部署器。 然後，系統會提示您同意 Azure FarmBeats 使用者授權合約（EULA）。

   - 如果您同意，請輸入 ' Y '，您會繼續進行下一個步驟。
   - 如果您不同意條款，且部署將會終止，請輸入 ' N '。

   然後，系統會提示您輸入部署的存取權杖。 複製產生的程式碼，並使用您的 Azure 認證登入 https://microsoft.com/devicelogin。

   > [!NOTE]
   > 程式碼會在60分鐘後到期。 當它到期時，您可以重新開機，方法是再次輸入部署命令。

6. 在下一個提示中，輸入 sentinel 帳戶密碼。
7. 安裝程式現在會驗證並開始部署，這可能需要大約20分鐘的時間。
8. 部署成功之後，您將會收到下列輸出連結：
    - **資料中樞 URL**：用來試用 Azure FarmBeats api 的 Swagger 連結。
    - **快速鍵 URL**：用來探索 Azure FarmBeats 智慧型伺服器陣列加速器的使用者介面。
    - 部署期間所建立的部署器**記錄**檔記錄檔。 它可以用來進行疑難排解。

    - 如果您同意，請輸入 ' Y '，並繼續進行下一個步驟。
    - 如果您不同意條款，且部署將會終止，請輸入 ' N '。

   然後，系統會提示您輸入部署的存取權杖。 複製所產生的程式碼，並使用您的 Azure 認證登入 https://microsoft.com/devicelogin。

   > [!NOTE]
    > 請記下這些檔案，並讓部署記錄檔路徑方便日後使用。


### <a name="deployment-scenario-2"></a>部署案例2

現有的 Azure Active Directory 應用程式註冊會用來部署。

1. 下載[輸入 json](https://aka.ms/PPInputJsonTemplate)會在輸入 json 中包含 Azure 應用程式用戶端識別碼和密碼，並加以儲存。

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

4. 在 Cloud Shell 中輸入或貼上 *部署 命令*。 請務必修改輸入的路徑。 Json 檔案，然後按 enter 鍵。

    ```
    wget -N -O farmbeats-installer.sh https://aka.ms/FB_1.2.0 && bash farmbeats-installer.sh> /home/dummyuser/input.json

    ```
   遵循其餘的步驟：

5. 腳本會自動下載所有相依性，並建立部署器。
6. 系統會提示您閱讀並同意 Azure FarmBeats 使用者授權合約（EULA）。

   - 如果您同意，請輸入 ' Y '，並繼續進行下一個步驟。
   - 如果您不同意條款，且部署將會終止，請輸入 ' N '。

7. 系統會提示您輸入部署的存取權杖。 複製所產生的程式碼，並使用您的 Azure 認證登入 https://microsoft.com/devicelogin。
8. 安裝程式現在會驗證並開始建立資源，這可能需要大約20分鐘的時間。 在這段期間 Cloud Shell 讓會話保持作用中狀態。
9. 一旦部署成功完成，您將會收到下列輸出連結：
   - **資料中樞 URL**：用於嘗試 FarmBeats api 的 Swagger 連結。
   - **快速鍵 URL**：用來探索 FarmBeats 智慧型伺服器陣列加速器的使用者介面。
   - 部署器**記錄**檔：在部署期間儲存記錄檔，並可用於進行疑難排解。

如果您遇到任何問題，請參閱[疑難排解](troubleshoot-project-farmbeats.md)。


## <a name="validate-deployment"></a>驗證部署

### <a name="data-hub"></a>資料中樞

資料中樞安裝完成後，您會收到 URL，以透過 Swagger 介面存取 Azure FarmBeats Api，格式為： HTTPs://\<yourdatahub-網站名稱 >. azurewebsites. net

1. 若要透過 Swagger 登入，請在瀏覽器中複製並貼上 URL。
2. 使用 Azure 入口網站認證登入。
3. 健全測試（選擇性）

     - 能夠使用「資料中樞」連結成功登入 Swagger 入口網站，而您會收到該連結作為成功部署的輸出。
     - 擴充類型取得 API-選取 [立即試用]/Execute
     - 您應該會收到伺服器回應碼200，而不是例外狀況，例如403「未經授權的使用者」。

### <a name="accelerator"></a>加速器

加速器安裝完成後，您會收到 URL 以存取 FarmBeats 使用者介面，格式如下： HTTPs://\<快速鍵-網站名稱 >. azurewebsites. net

1. 若要從加速器登入，請在瀏覽器中複製並貼上 URL。
2. 使用 Azure 入口網站認證登入。
3. 執行選擇性的健全測試。

    - 檢查您是否能夠使用您收到作為成功部署輸出的加速器連結，成功登入加速器入口網站。
    - 選取 [**建立伺服器**陣列]。
    - 在 [？] 圖示下，使用 [**開始**使用] 按鈕開啟 FarmBeats 指南。

## <a name="upgrade"></a>升級

升級步驟類似于第一次安裝。 請遵循下列步驟：

1. 登入以 Azure 入口網站，然後選取您想要的訂用帳戶和 AD 租使用者。
2. 從 Azure 入口網站的頂端導覽啟動 Cloud Shell。

   ![專案伺服器陣列的節拍](./media/prepare-for-deployment/navigation-bar-1.png)

3. 選取要建立儲存體帳戶的訂用帳戶，以及 Azure 檔案儲存體的檔案共用。
4. 選取 [建立儲存體]。
5. 從 shell （Bash）左邊的下拉式選單中選取環境。
6. 只有在需要時才對您的輸入 json 檔案進行變更，並上傳至 Azure Cloud Shell。 例如，您可以更新您想要接收之通知的電子郵件地址。
7. 記下輸入 json 檔案路徑（在您的本機電腦上）。
8. 移至 Azure Cloud Shell。 成功驗證之後，請選取 [上傳] 按鈕（請參閱下方影像中反白顯示的圖示），然後將輸入 json 檔案上傳至 Cloud Shell 儲存體。

   ![專案伺服器陣列的節拍](./media/prepare-for-deployment/bash-2-1.png)

9. 在 Cloud Shell 中輸入或貼上 **部署 命令**。 請務必修改輸入的路徑。 Json 檔案，然後按 enter 鍵。

    ```
    wget -N -O farmbeats-installer.sh https://aka.ms/FB_1.2.0 && bash farmbeats-installer.sh> /home/dummyuser/input.json

    ```
    遵循其餘的步驟：

10. 安裝程式會在執行時間自動提示所需的輸入：

    - 輸入用於部署的存取權杖。 複製所產生的程式碼，並使用您的 Azure 認證登入 https://microsoft.com/devicelogin。

     > [!NOTE]
     > 程式碼會在60分鐘後到期。 當它到期時，您可以重新開機，方法是再次輸入部署命令。

     - Sentinel 密碼

11. 安裝程式現在會驗證並開始建立資源，這可能需要大約20分鐘的時間。
12. 部署成功之後，您將會收到下列輸出連結：

    - **資料中樞 URL**：用於嘗試 FarmBeats api 的 Swagger 連結。
    - **快速鍵 URL**：用來探索 FarmBeats 智慧型伺服器陣列加速器的使用者介面。
    - 部署器**記錄**檔：在部署期間儲存記錄。 它可以用來進行疑難排解。

    > [!NOTE]
    > 請記下上述連結，並讓部署記錄檔路徑方便日後使用。

## <a name="uninstall"></a>解除安裝

目前不支援使用安裝程式自動卸載 FarmBeats。 若要移除資料中樞或加速器，請在 Azure 入口網站中，刪除已安裝這些元件的資源群組，或手動刪除資源。

例如，如果您將資料中樞和加速器部署在兩個不同的資源群組中，您會刪除這些資源群組，如下所示：

1. 登入 Azure 入口網站。
2. 在右上角選取您的帳戶，然後切換至您想要部署 Microsoft FarmBeats 的所需 Azure AD 租使用者。

   > [!NOTE]
   > 需要資料中樞，加速器才能正常運作。 我們不建議您卸載資料中樞，而不需要卸載加速器。

3. 選取 [資源群組]，然後輸入您想要刪除的資料中樞或加速器資源群組的名稱。
4. 選取資源群組名稱。 再次輸入名稱以再次檢查，然後按一下 [刪除] 來移除資源群組及其所有基礎資源。
5. 或者，您可以手動刪除每個資源，這不是建議的做法。
7. 若要刪除/卸載資料中樞，請直接移至 Azure 上的資源群組，並從該處刪除資源群組。

## <a name="next-steps"></a>後續步驟

您已部署 Azure FarmBeats。 現在，請瞭解如何[建立伺服器](manage-farms.md#create-farms)陣列。
