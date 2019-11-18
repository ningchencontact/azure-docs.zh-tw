---
title: 從合作夥伴取得感應器資料
description: 說明如何從合作夥伴取得感應器資料
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 33fd2221d3f3a0562abf83f014a7b84df972eebc
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2019
ms.locfileid: "74128837"
---
# <a name="get-sensor-data-from-sensor-partners"></a>從感應器合作夥伴取得感應器資料

Azure FarmBeats 可協助您將 IoT 裝置和感應器的串流資料帶入資料中樞。 目前支援下列感應器裝置合作夥伴：

  ![專案伺服器陣列的節拍](./media/get-sensor-data-from-sensor-partner/partner-information-1.png)

將裝置資料與 Azure FarmBeats 整合，可協助您從伺服器陣列中部署的 IoT 感應器，取得資料中樞的基礎資料。 您可以透過 FarmBeats 加速器來視覺化資料，並可用於使用 FarmBeats 的資料融合和 AI/ML 模型建立。

若要啟動感應器資料串流，請確定下列事項：

-  您已從 Azure Marketplace 安裝 FarmBeats。
-  您已決定要在伺服器陣列上安裝的感應器和裝置。
-  如果您打算使用土裡濕度感應器，您可以使用 FarmBeats 的土裡濕度感應器放置圖來取得感應器數目的建議，以及確切放置感應器的位置。 如需詳細資訊，請參閱[產生對應](generate-maps.md)。

- 從您的伺服器陣列中的裝置夥伴購買並部署裝置/感應器。 請確定您可以透過裝置合作夥伴的解決方案存取感應器資料。

### <a name="enable-device-integration-with-farmbeats"></a>啟用裝置與 FarmBeats 的整合   

開始串流處理感應器資料之後，您就可以開始將資料帶入 FarmBeats 系統的程式。 您必須將下列資訊提供給您的裝置提供者，以啟用與 FarmBeats 的整合：  

 - API 端點  
 - 租用戶識別碼  
 - 用戶端識別碼  
 - 用戶端密碼  
 - EventHub 連接字串

您的系統整合者會提供上述資訊給您。 針對啟用裝置整合的任何問題，請洽詢您的系統整合者。

或者，您也可以從 Azure Cloud Shell 執行此腳本來產生認證。 請遵循下列步驟：

1. 下載[ZIP](https://aka.ms/farmbeatspartnerscript)檔案，並解壓縮至您的本機磁片磁碟機。 您會在 ZIP 檔案中找到兩個檔案。
2. 登入 https://portal.azure.com/ 並開啟 Cloud Shell （入口網站右上方列有提供此選項）  

    ![專案伺服器陣列的節拍](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. 請確定環境設定為**PowerShell** -預設會設定為 Bash。

    ![專案伺服器陣列的節拍](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

4. 上傳 Cloud Shell 中的兩個檔案（來自上述的步驟1）。

    ![專案伺服器陣列的節拍](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

5. 移至上傳檔案的目錄（根據預設，它會上傳到主目錄 > 使用者名稱）。
6. 執行下列指令碼：

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```
7. 遵循畫面上的指示來捕捉值。 （API 端點、租使用者識別碼、用戶端識別碼、用戶端密碼和 EventHub 連接字串）。

**使用產生的認證來整合裝置資料**

流覽裝置合作夥伴入口網站，使用您在上一節中產生的一組認證來連結 FarmBeats。

 - API 端點  
 - EventHub 連接字串  
 - 用戶端識別碼  
 - 用戶端密碼  
 - 租用戶識別碼  

 裝置提供者會確認整合成功。 確認之後，您可以在 Azure FarmBeats 上查看所有裝置和感應器。

## <a name="view-devices-and-sensors"></a>查看裝置和感應器

請使用下一節來查看伺服器陣列中的裝置和感應器。

### <a name="view-devices"></a>檢視裝置

目前 FarmBeats 支援下列裝置：

- **節點**：一或多個感應器所連接的裝置。
- **閘道**：一或多個節點所連接的目標裝置。

請使用下列步驟：

1. 在 [首頁] 頁面上，從功能表中選取 [**裝置**]。
  [裝置] 頁面會顯示裝置類型、型號、狀態、其放置所在的伺服器陣列，以及中繼資料的上次更新日期。 根據預設，[伺服器陣列] 資料行會設定為 Null。 您可以選擇將裝置指派給伺服器陣列。 如需詳細資訊，請參閱[指派裝置](#assign-devices)。
2. 選取裝置以查看裝置屬性、遙測和連線到裝置的子裝置。  

    ![專案伺服器陣列的節拍](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>觀看感應器

請使用下列步驟：

1. 在 [首頁] 頁面上，從功能表中選取 [**感應器**]。
  [感應器] 頁面會顯示感應器類型、其連線的伺服器陣列、父裝置、埠名稱、埠類型和上次更新狀態的詳細資料。
2. 選取感應器以查看感應器內容、作用中警示和感應器的遙測。

    ![專案伺服器陣列的節拍](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>指派裝置  

一旦感應器資料流程入之後，您就可以將它指派給您已部署感應器的伺服器陣列。

1. 在首頁上，從功能表中選取 [**伺服器**陣列]，隨即會顯示 [**伺服器**陣列清單] 頁面。  
2. 選取您要指派裝置的伺服器陣列，然後選取 [**新增裝置**]。  
3. [**新增裝置**] 視窗隨即顯示。 選取您要指派給伺服器陣列的裝置。

    ![專案伺服器陣列的節拍](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. 選取 [**新增裝置**]。 或者，移至 [**裝置**] 功能表，選取您要指派給伺服器陣列的裝置，然後選取 [**關聯裝置**]。  
5. 在 [**關聯裝置**] 視窗中，從下拉式選單選取 [伺服器陣列]，然後選取 [**全部**套用]，讓伺服器陣列與所有選取的裝置產生關聯。

    ![專案伺服器陣列的節拍](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. 若要將每個裝置關聯到不同的伺服器陣列，請選取 [**指派給伺服器**陣列] 欄中的下拉式清單，然後針對每個裝置資料列選取一個伺服器陣列。  
7. 選取 [**指派**] 以完成裝置指派。

### <a name="visualize-sensor-data"></a>將感應器資料視覺化

請使用下列步驟：

1. 在 [首頁] 頁面上，從功能表中選取 [**伺服器**陣列] 以查看 [**伺服器**陣列] 頁面。  
2. 選取您要查看其感應器資料的**伺服器**陣列。  
3. 在 [**伺服器**陣列] 儀表板上，您可以查看遙測資料。 您可以選擇觀看即時遙測，或使用**自訂範圍**來觀看特定的日期範圍。

    ![專案伺服器陣列的節拍](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-sensor"></a>刪除感應器

請遵循下列步驟：

1. 在 [首頁] 頁面上，從功能表選取 [**感應器**] 以查看 [**感應器**] 頁面。  
2. 選取您要刪除的裝置，然後從 [確認] 視窗中選取 [**刪除**]。

    ![專案伺服器陣列的節拍](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

確認訊息會顯示已成功刪除感應器。  

## <a name="delete-devices"></a>刪除裝置

請遵循下列步驟：

1. 在 [首頁] 頁面上，從功能表中選取 [**裝置**] 以查看 [裝置] 頁面。  
2. 選取您要刪除的裝置，然後從確認視窗中選取 [**刪除**]。

    ![專案伺服器陣列的節拍](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>後續步驟

您現在已將感應器資料流程入您的 Azure FarmBeats 實例。 現在，請瞭解如何為您的伺服器陣列[產生對應](generate-maps.md#generate-maps)。
