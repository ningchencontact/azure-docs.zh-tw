---
title: 取得無人機影像
description: 說明如何從合作夥伴取得無人機的影像
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 6ee4391369744f966ce273697e5ba9e7f0590edd
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890987"
---
# <a name="get-drone-imagery-from-drone-partners"></a>從無人機合作夥伴取得無人機影像

本文說明如何從中的無人機影像合作夥伴，將 orthomosaic 資料帶入 Azure FarmBeats 資料中樞。 Orthomosaic 是從無人機所收集的資料進行幾何校正和拼接的鳥瞰圖/影像。

目前支援下列影像合作夥伴。

  ![專案伺服器陣列的節拍](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

將無人機影像資料與 Azure FarmBeats 整合，可協助您從在伺服器陣列上進行的無人機航班 orthomosaic 資料到資料中樞。 資料可供使用之後，您可以在 FarmBeats 加速器中加以查看，並可用於資料融合和 AI/ML 模型建立。

## <a name="before-you-begin"></a>開始之前

  - 請確定您已部署 Azure FarmBeats。 若要部署，請造訪[Deploy FarmBeats](prepare-for-deployment.md)。
  - 確定您的 FarmBeats 系統中已定義伺服器陣列（您想要無人機的影像）。

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>啟用無人機影像與 FarmBeats 的整合   

您必須將下列資訊提供給您的裝置提供者，以啟用與 FarmBeats 的整合：  
 - API 端點  
 - 租用戶識別碼  
 - 用戶端識別碼  
 - 用戶端密碼  

請使用下列步驟：

1. 下載此[腳本](https://aka.ms/farmbeatspartnerscript)，並在本機磁片磁碟機上將它解壓縮。 您會在此 ZIP 檔案中找到兩個檔案。  
2. 登入[Azure 入口網站](https://portal.azure.com/)並開啟 Cloud Shell （入口網站的右上方列提供此選項）。   

    ![專案伺服器陣列的節拍](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. 確定環境已設定為**PowerShell**

    ![專案伺服器陣列的節拍](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. 上傳您在 Cloud Shell 中所下載的兩個檔案（從上面的步驟1）。  

    ![專案伺服器陣列的節拍](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. 移至上傳檔案的目錄。 （根據預設，它會上傳到主目錄 > 使用者名稱）。  
6. 執行下列指令碼：

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```

7. 依照畫面上的指示來捕獲 API 端點、租使用者識別碼、用戶端識別碼、用戶端密碼和 EventHub 連接字串的值。

    當您將所需的認證輸入夥伴的無人機軟體系統之後，您將能夠從 FarmBeats 系統匯入所有伺服器陣列，並使用伺服器陣列詳細資料來進行航班路徑規劃和無人機映射集合。

    當無人機提供者的軟體處理原始影像之後，無人機軟體系統會將拼接 orthomosaic 和其他處理過的影像上傳到資料中樞。

## <a name="view-drone-imagery"></a>觀看無人機影像

將資料傳送至 FarmBeats 資料中樞後，您應該能夠使用 FarmBeats 資料中樞 Api 來查詢場景存放區。

或者，您應該能夠在 [**伺服器陣列詳細資料**] 頁面中，查看最新的無人機映射。 若要查看，請遵循下列步驟：  

1. 選取您的影像上傳到的伺服器陣列。 [**伺服器**陣列詳細資料] 頁面隨即顯示。
2. 向下卷到最新的 [**精確度對應**] 區段。
3. 您應該能夠在 [**無人機影像**] 區段中查看影像。

    ![專案伺服器陣列的節拍](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>下載無人機影像

當您選取 [無人機影像] 區段時，快顯視窗會隨即開啟，以顯示無人機 orthomosaic 的高解析度影像。

![專案伺服器陣列的節拍](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>查看所有無人機對應

無人機提供者所上傳的檔案和映射會顯示在 [對應] 區段中。 選取 [**對應**] 區段、[依**伺服器**陣列篩選]，然後選擇要查看和下載的適當檔案：

  ![專案伺服器陣列的節拍](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>後續步驟

瞭解如何使用 FarmBeats 資料中樞[api](references-for-farmbeats.md#rest-api)來取得您的無人機影像。
