---
title: 在 Azure 的 LUIS 應用程式中管理版本 | Microsoft Docs
description: 了解如何在 Language Understanding (LUIS) 應用程式中管理版本。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/29/2017
ms.author: v-geberr
ms.openlocfilehash: 672f7991be0fc236e39daf7d1ce1d6080b31815b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368055"
---
# <a name="manage-versions"></a>管理版本

每次您使用模型工作時，都會建立不同的應用程式[版本](luis-concept-version.md)。 

## <a name="set-active-version"></a>設定使用中版本
若要使用版本，請在 [我的應用程式] 頁面上選取應用程式的名稱來開啟應用程式，然後選取頂端列中的 [設定]。

![[版本] 頁面](./media/luis-how-to-manage-versions/settings.png)

[設定] 頁面可讓您設定整個應用程式的設定，包括版本和共同作業者。 

## <a name="clone-a-version"></a>複製版本
1. 在 [設定] 頁面上，於 [應用程式設定] 和 [共同作業者] 區段之後，尋找有所要複製版本的資料列。 選取最右側的三個點 (...)。 

    ![版本資料列屬性](./media/luis-how-to-manage-versions/version-section.png)

2. 從清單中選取 [複製]。

    ![版本資料列屬性選項](./media/luis-how-to-manage-versions/version-three-dots-modal.png)

3. 在 [複製版本] 對話方塊中，輸入新版本的名稱，例如 "0.2"。

   ![[複製版本] 對話方塊](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
 > [!NOTE]
 > 版本識別碼只能包含字元、數字或 '.'，且長度不能超過 10 個字元。
 
 隨即會建立具有指定名稱的新版本，並會設定為使用中版本。
 
  ![已建立版本並新增至清單中](./media/luis-how-to-manage-versions/new-version.png)

 > [!NOTE]
 > 如上圖所示，已發行的版本會有相關聯的彩色標記，以指出其發行時所在位置的類型：生產 (綠色)、暫存 (紅色)、兼具 (黑色)。 每個已發行的版本都會顯示訓練和發行日期。

## <a name="set-active-version"></a>設定使用中版本
1. 在 [設定] 頁面上的 [版本] 清單中，選取最右側的三個點 (...)。

2. 從快顯清單中，選取 [設定為使用中]。

    ![設定使用中版本](./media/luis-how-to-manage-versions/set-active-version.png)

    使用中的版本會以淺藍色醒目提示，如下列螢幕擷取畫面所示：

    ![使用中的版本](./media/luis-how-to-manage-versions/set-active-version-done.png) 


## <a name="import-version"></a>匯入版本
您可以從 JSON 檔案匯入版本。 匯入版本之後，新版本就會變成使用中的版本。

**若要匯入版本：**

1. 在 [設定] 頁面上，選取 [匯入新版本] 按鈕。

    ![[匯入] 按鈕](./media/luis-how-to-manage-versions/import-version.png)

2. 選取 [瀏覽]，然後選擇 JSON 檔案。

    ![[匯入版本] 對話方塊](./media/luis-how-to-manage-versions/import-version-dialog.png)

如果應用程式中已有 JSON 檔案中的版本，您就只需要設定版本識別碼。

## <a name="export-version"></a>匯出版本
您可以將版本匯出到 JSON 檔案。

**若要匯出版本：**

1. 在 [設定] 頁面上的 [版本] 清單中，選取最右側的三個點 (...)。

2. 在動作的快顯清單中選取 [匯出]，然後選取檔案的儲存位置。

## <a name="delete-a-version"></a>刪除版本
您可以刪除版本，但至少必須保留一個應用程式版本。 您可以刪除使用中版本以外的所有版本。 

1. 在 [設定] 頁面上的 [版本] 清單中，選取最右側的三個點 (...)。

2. 在動作的快顯清單中選取 [刪除]，然後選取檔案的儲存位置。

    ![刪除版本確認](./media/luis-how-to-manage-versions/delete-menu.png) 


## <a name="rename-a-version"></a>重新命名版本
您可以重新命名版本 (只要該版本名稱還未使用)。  

1. 在 [設定] 頁面上的 [版本] 清單中，選取最右側的三個點 (...)。

2. 在動作的快顯清單中選取 [重新命名]。

3. 輸入新的應用程式名稱，然後選取 [完成]。

    ![重新命名版本確認](./media/luis-how-to-manage-versions/rename-popup.png) 
