---
title: Azure Data Factory 中的視覺化撰寫 | Microsoft Docs
description: 了解如何使用 Azure Data Factory 中的視覺化撰寫
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/01/2018
ms.author: shlo
ms.openlocfilehash: 655a6ab2960047cde50bec2953015283ca8577f0
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214845"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Azure Data Factory 中的視覺化撰寫
Azure Data Factory 使用者介面體驗 (UX) 可讓您透過視覺化方式撰寫及部署資料處理站的資源，而不必編寫任何程式碼。 您可以將活動拖放到管線畫布上、執行測試回合、反覆進行偵錯，以及部署和監視管線回合。 使用 UX 來執行視覺化撰寫的方法有兩種：

- 直接使用 Data Factory 服務來撰寫。
- 使用 Visual Studio Team Services (VSTS) Git 整合來撰寫，以便進行共同作業、原始檔控制或版本設定。

## <a name="author-directly-with-the-data-factory-service"></a>直接使用 Data Factory 服務來撰寫
使用 Data Factory 服務的視覺化撰寫和使用 VSTS 的視覺化撰寫有兩個不同之處：

- Data Factory 服務未包含存放庫，此存放庫會用於儲存變更的 JSON 實體。
- Data Factory 服務並未針對共同作業或版本控制進行最佳化。

![設定 Data Factory 服務 ](media/author-visually/configure-data-factory.png)

當您使用 UX **撰寫畫布**來使用 Data Factory 服務來直接撰寫時，只能使用 [全部發行] 模式。 您所做的任何變更都會直接發佈至 Data Factory 服務。

![發佈模式](media/author-visually/data-factory-publish.png)

## <a name="author-with-vsts-git-integration"></a>使用 VSTS Git 整合來撰寫
使用 VSTS Git 整合所進行的視覺化撰寫，會對資料處理站管線上的工作支援原始檔控制和共同作業功能。 您可以將資料處理站與 VSTS Git 帳戶存放庫建立關聯，以進行原始檔控制、共同作業及版本設定等工作。 一個 VSTS Git 帳戶可以有多個存放庫，但一個 VSTS Git 存放庫只能與一個資料處理站建立關聯。 如果您沒有 VSTS 帳戶或存放庫，請遵循[這些指示](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student)來建立您的資源。

> [!NOTE]
> 您可以將指令碼和資料檔案儲存在 VSTS GIT 存放庫。 不過，您必須手動將檔案上載至 Azure 儲存體。 Data Factory 管線不會自動將 VSTS GIT 存放庫中儲存的指令碼或資料檔案上傳至 Azure 儲存體。

### <a name="configure-a-vsts-git-repository-with-azure-data-factory"></a>設定搭配 Azure Data Factory 的 VSTS Git 存放庫
您可以透過兩種方法設定搭配資料處理站的 VSTS GIT 存放庫。

#### <a name="method1"></a>設定方法 1：[現在就開始吧] 頁面

在 Azure Data Factory 中，移至 [現在就開始吧] 頁面。 選取 [設定程式碼存放庫]：

![設定 VSTS 程式碼存放庫](media/author-visually/configure-repo.png)

[存放庫設定] 設定窗格會隨即出現：

![設定程式碼存放庫設定](media/author-visually/repo-settings.png)

此窗格會顯示下列 VSTS 程式碼存放庫設定：

| 設定 | 說明 | 值 |
|:--- |:--- |:--- |
| **存放庫類型** | VSTS 程式碼存放庫的類型。<br/>**注意**：目前不支援 GitHub。 | Visual Studio Team Services Git |
| **Azure Active Directory** | 您的 Azure AD 租用戶名稱。 | <your tenant name> |
| **Visual Studio Team Services 帳戶** | 您的 VSTS 帳戶名稱。 您可以在 `https://{account name}.visualstudio.com` 找到您的 VSTS 帳戶名稱。 您可以[登入您的 VSTS 帳戶](https://www.visualstudio.com/team-services/git/)，以存取 Visual Studio 設定檔和查看您的存放庫與專案。 | <your account name> |
| **ProjectName** | VSTS 專案名稱。 您可以在 `https://{account name}.visualstudio.com/{project name}` 找到您的 VSTS 專案名稱。 | <your VSTS project name> |
| **RepositoryName** | 您的 VSTS 程式碼存放庫名稱。 VSTS 專案包含 Git 存放庫，可隨著您的專案成長管理原始程式碼。 您可以建立新的存放庫，或使用專案中既有的存放庫。 | <your VSTS code repository name> |
| **共同作業分支** | 將會用於發行的 VSTS 共同作業分支。 根據預設，它是 `master`。 如果您想要從其他分支發行資源，請變更此選項。 | <your collaboration branch name> |
| **根資料夾** | 在您 VSTS 共同作業分支中的根資料夾。 | <your root folder name> |
| **將現有的 Data Factory 資源匯入存放庫** | 指定是否要從 UX **撰寫畫布**將現有的資料處理站資源匯入到 VSTS Git 存放庫。 選取此方塊可將您的資料處理站資源以 JSON 格式匯入到相關聯的 Git 存放庫。 此動作會將每個資源個別匯出 (亦即，已連結的服務和資料集會匯出至個別的 JSON)。 若未選取此方塊，則不會匯入現有資源。 | 已選取 (預設值) |

#### <a name="configuration-method-2-ux-authoring-canvas"></a>設定方法 2：UX 撰寫畫布
在 Azure Data Factory UX **撰寫畫布**中，找到您的資料處理站。 選取 [Data Factory] 下拉式功能表，然後選取 [設定程式碼存放庫]。

隨即會出現設定窗格。 如需有關組態設定的詳細資訊，請參閱<a href="#method1">設定方法 1</a> 中的說明。

![設定用於 UX 撰寫的程式碼存放庫設定](media/author-visually/configure-repo-2.png)

#### <a name="switch-to-a-different-git-repo"></a>切換至不同的 Git 存放庫

若要切換至不同的 Git 存放庫，請找出位於 [Data Factory 概觀] 頁面右上角的圖示，如下列螢幕擷取畫面所示。 如果看不到該圖示，請清除您的本機瀏覽器快取。 選取適當圖示以移除與目前存放庫的關聯。

移除與目前存放庫的關聯之後，您可以設定 Git 設定以使用不同的存放庫。 接著，您可以將現有的 Data Factory 資源匯入至新的存放庫。

![移除與目前 Git 存放庫的關聯。](media/author-visually/remove-repo.png)

### <a name="use-version-control"></a>使用版本控制
版本控制系統 (也稱為_原始檔控制_) 可讓開發人員在程式碼上共同作業，並追蹤對程式碼基底所進行的變更。 來源控制是多開發人員專案的必要工具。

與資料處理站相關聯的每個 VSTS Git 存放庫都有共同作業分支。 (`master` 是預設的共同作業分支)。 使用者也可以按一下 [+ 新增分支] 來建立功能分支，並在功能分支中進行開發。

![藉由同步或發佈來變更程式碼](media/author-visually/sync-publish.png)

當在您的功能分支中的功能開發已經備妥時，您可以按一下 [建立提取要求]。 這會帶您到 VSTS GIT，您可以在其中發出提取要求、執行程式碼檢閱，以及將變更合併到您的共同作業分支。 (`master` 是預設值)。 您只被允許從您的共同作業分支發佈到 Data Factory 服務。 

![建立新的提取要求](media/author-visually/create-pull-request.png)

#### <a name="publish-code-changes"></a>發佈程式碼變更
將變更合併到共同作業分支之後 (`master` 是預設值)，請選取 [發佈] 來手動將 master 分支中的程式碼變更發佈到 Data Factory 服務。

![將變更發佈到 Data Factory 服務](media/author-visually/publish-changes.png)

> [!IMPORTANT]
> 主要分支不代表在 Data Factory 服務中部署的內容。 「必須」以手動方式將主要分支主發佈至 Data Factory 服務。

## <a name="use-the-expression-language"></a>使用運算式語言
您可以藉由使用 Azure Data Factory 所支援的運算式語言，來指定屬性值的運算式。 

選取 [新增動態內容] 來指定屬性值的運算式：

![使用運算式語言](media/author-visually/dynamic-content-1.png)

## <a name="use-functions-and-parameters"></a>使用函式和參數

您可以在 Data Factory **運算式建立幫手**中使用函式或指定管線和資料集的參數：

如需所支援運算式的相關資訊，請參閱 [Azure Data Factory 中的運算式和函式](control-flow-expression-language-functions.md)。

![新增動態內容](media/author-visually/dynamic-content-2.png)

## <a name="provide-feedback"></a>提供意見反應
選取 [意見反應] 可為功能加上註解，也可以向 Microsoft 通報工具問題：

![意見反應](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>後續步驟
若要深入了解如何監視和管理管線，請參閱[以程式設計方式監視和管理管線](monitor-programmatically.md)。
