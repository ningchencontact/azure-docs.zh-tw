---
title: 發佈 Azure 應用程式供應項目 - Azure Marketplace | Microsoft Docs
description: 說明在 Azure Marketplace 上發佈 Azure 應用程式供應項目的程序和步驟。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: pbutlerm
ms.openlocfilehash: ad62b306849b2291c81399cedc1634057e2eec9d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54828089"
---
# <a name="publish-azure-application-offer"></a>發佈 Azure 應用程式供應項目

在 [新增供應項目] 頁面提供資訊並建立供應項目後，即可發佈供應項目。 選取 [發佈] 即可啟動發佈程序。

下圖將展示供應項目「上線」發佈程序中主要的步驟。

![供應項目發佈步驟](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>發佈步驟的詳細說明

下表列出並描述每個發佈步驟，並提供完成每個步驟的時間估計。  以「天」為單位的時間估計，定義為工作天，這會排除週末及假日。

|  **發佈步驟**           | <bpt id="p1">**</bpt>Time<ept id="p1">**</ept>    | **說明**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| 檢查必要條件         | < 15 分鐘    | 檢查供應項目資訊和供應項目設定。                        |
| 驗證受影響的營收設定 | < 15 分鐘  | 已檢查供應項目的 Azure 資源使用狀況。             |
| 認證                  | < 1 天     | Azure 認證團隊會分析供應項目。 掃描供應項目中是否有病毒、惡意程式碼、安全合規性和安全性問題。 檢查供應項目以查看它是否符合所有資格條件。 如需詳細資訊，請參閱[必要條件](./cpp-prerequisites.md)。 若發現問題，將提供意見反應。 |
| 試用產品驗證          | < 2 小時   | (選擇性) 如果有試用產品，則 Microsoft 會驗證它是否可以部署及複寫。  |
| 封裝和潛在客戶開發註冊 | < 1 小時  | 供應項目的技術資產會封裝供客戶使用，潛在客戶系統也設定且部署完畢。 |
|  發佈者登出             |  manual    | 供應項目上線前發行者最終檢閱與確認。 現在供應項目可供預覽。  您可以在所選訂用帳戶部署供應項目 (供應項目資訊步驟)，確認是否符合您所有要求。  確認該供應項目之後，請選取 [Go Live]，您的供應項目就可以移至下一個步驟。 |
| Microsoft 檢閱                | 7 - 14 天 | Microsoft 會全面地檢閱您的 Azure 應用程式，如果發現問題就會傳送電子郵件給您。  此步驟的長度，取決於應用程式的複雜度、所發現的問題，以及您回應它們的速度。  |
| 即時                           | < 1 天 | 供應項目推出、複寫到指定的區域，並開放給大眾使用。 |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |
 
您可以在 Cloud Partner 入口網站內供應項目的 [狀態] 索引標籤中，監視發佈程序。

![Azure 應用程式供應項目的 [狀態] 索引標籤](./media/offer-status-tab.png)

完成發佈程序之後，您的供應項目將會列在 [Microsoft Azure Marketplace 應用程式類別](https://azuremarketplace.microsoft.com/marketplace/apps/)中。


## <a name="errors-and-review-feedback"></a>錯誤和檢閱意見

除了顯示供應項目的發佈狀態，[狀態] 索引標籤也會顯示來自 **Microsoft 審核**步驟的錯誤訊息和意見反應。  一般而言，檢閱問題會以提取要求 (PR) 參考。  每個 PR 都連結到線上 Visual Studio Team Services (VSTS，重新命名為 [Azure DevOps](https://azure.microsoft.com/services/devops/)) 項目，其中包含問題詳細資料。  下列影像顯示檢閱 PR 參考的範例。  對於更複雜的情況，檢閱與支援小組可能會透過電子郵件連絡您。 

![顯示檢閱意見的 [狀態] 索引標籤](./media/status-tab-ms-review.png)

您必須先處理每個回報的問題，供應項目才能繼續進行發佈程序。  下圖說明此意見反應程序如何與發佈程序相關。

![使用 VSTS 意見反應的發佈步驟](./media/pub-flow-vsts-access.png)


### <a name="vsts-access"></a>VSTS 存取

若要檢視檢閱意見中的 VSTS 項目參考，發行者必須被授與適當的授權。  否則，新的發行者會收到 `401 - Not Authorized` 回應頁面。  若要要求供應項目檢閱 VSTS 系統的存取權，請執行下列步驟：

1. 收集下列資訊：
    - 您的發行者名稱和識別碼
    - 供應項目類型 (Azure 應用程式)、提供名稱和 SKU 識別碼
    - 提取要求連結，例如：`https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`  此 URL 可以從通知訊息或 401 回應頁面的位址擷取。
    - 您的發行組織中您要授與存取權之個人的電子郵件地址。  其中應該包含在 Cloud Partner 入口網站上註冊為發行者時所提供的擁有者地址。
2. 建立支援事件。  在 Cloud Partner 入口網站的標題列中，選取 [說明] 按鈕，然後從功能表選擇 [支援]。  您預設網頁瀏覽器應該會啟動，並瀏覽至 Microsoft 新的支援事件頁面。  (您可能必須先登入。)
3. 指定 [問題類型] 為 [市集上架]，並指定 [類別] 為 [存取問題]，然後選取 [開始要求]。

    ![支援票證類別](./media/support-incident1.png)

4. 在 [步驟 2 之 1] 頁面中，提供您的連絡資訊並選取 [繼續]。
5. 在 [步驟 2 之 2] 頁面中，指定事件標題 (例如，`Request VSTS access`)，並提供您在第一步中收集的資訊 (上述)。  閱讀並接受合約，然後選取 [提交]。

如果事件建立成功，就會顯示確認頁面。  儲存確認資訊供您參考。  Microsoft 支援服務應該會在幾個工作天內回覆您的存取要求。


## <a name="next-steps"></a>後續步驟

Azure 應用程式發佈之後，您可以[更新現有供應項目](./cpp-update-existing-offer.md)，以反映變更的商務或技術需求。 
