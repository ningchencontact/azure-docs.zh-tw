---
title: "以視覺化方式撰寫 Azure Data Factory | Microsoft Docs"
description: "了解如何以視覺化方式撰寫 Azure Data Factory"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/9/2018
ms.author: shlo
ms.openlocfilehash: 3e67665facba78c4ca8e2317f0323b4c5c02a49c
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2018
---
# <a name="visually-author-data-factories"></a>以視覺化方式撰寫資料處理站
在使用 Azure Data Factory UX 體驗的情況下，使用者可以透過視覺化方式在其資料處理站中撰寫及部署資源，而無須撰寫任何一行程式碼。 這個無程式碼介面可讓您將活動拖放到管線畫布上、執行測試回合、反覆進行偵錯，以及部署和監控管線回合。 您可以選擇以兩種方式使用 ADF UX 工具：

1. 直接與 Data Factory 服務搭配運作
2. 設定「VSTS Git 整合」來進行共同作業、來源控制或版本設定

## <a name="authoring-with-data-factory"></a>藉由 Data Factory 進行撰寫
第一個選項是直接藉由 Data Factory 進行撰寫模式。 這個方法與透過「VSTS 程式碼存放庫」進行撰寫有所不同，就是它既沒有任何儲儲存您所做變更之 JSON 實體的存放庫，也未針對共同作業或版本控制進行最佳化。

![設定 Data Factory](media/author-visually/configure-data-factory.png)

在 Data Factory 模式下，只有「發佈」模式。 您所做的任何變更都會直接發佈至 Data Factory 服務。

![Data Factory 發佈](media/author-visually/data-factory-publish.png)

## <a name="authoring-with-vsts-git-integration"></a>藉由 VSTS Git 整合進行撰寫
藉由 VSTS Git 整合進行撰寫可讓您在撰寫資料處理站管線時，進行來源控制和共同作業。 使用者可以選擇將資料處理站與「VSTS Git 帳戶」存放庫建立關聯，以進行來源控制、共同作業及版本設定等。一個 VSTS GIT 帳戶可以有多個存放庫。 不過，一個 VSTS Git 存放庫只能與一個資料處理站建立關聯。 如果您還沒有 VSTS 帳戶和存放庫，請從[這裡](https://docs.microsoft.com/en-us/vsts/accounts/create-account-msa-or-work-student)建立一個。

### <a name="configure-vsts-git-repo-with-azure-data-factory"></a>設定搭配 Azure Data Factory 的 VSTS Git 存放庫
使用者可以透過兩種方法設定搭配資料處理站的 VSTS GIT 存放庫。

#### <a name="method-1-lets-get-started-page"></a>方法 1：[讓我們開始吧] 頁面

移至 [讓我們開始吧] 頁面，然後按一下 [設定程式碼存放庫]

![設定程式碼存放庫](media/author-visually/configure-repo.png)

從該處會出現一個側邊面板，用於設定存放庫設定。

![設定存放庫設定](media/author-visually/repo-settings.png)
* **存放庫類型**：Visual Studio Team Services Git (目前不支援 Github)。
* **Visual Studio Team Services 帳戶**：可以從 https://{account name}.visualstudio.com 找出帳戶名稱。從[這裡](https://www.visualstudio.com/team-services/git/)登入您的 VSTS 帳戶，然後存取 Visual Studio 設定檔來查看您的存放庫和專案
* **專案名稱**：可以從 https://{account name}.visualstudio.com/{project name} 找出專案名稱
* **存放庫名稱**：存放庫名稱。 VSTS 專案包含 Git 存放庫，可隨著您的專案成長管理原始程式碼。 請建立新的存放庫，或使用專案中既有的存放庫。
* **將現有的資料處理站資源匯入至存放庫**：藉由選取此方塊，您便可以將在 UX 畫布上撰寫的目前資料處理站資源，以 JSON 格式匯入至相關的 VSTS GIT 存放庫。 此動作會將每個資源個別匯出 (亦即，已連結的服務和資料集會匯出至個別的 JSON)。    如果將此核取方塊取消選取，現有的資源就不會匯入至 Git 存放庫。

#### <a name="method-2-from-authoring-canvas"></a>方法 2：從撰寫畫布

在「撰寫畫布」中，按一下您資料處理站名稱底下的 [資料處理站] 下拉式功能表。 然後，按一下 [設定程式碼存放庫]。 與**方法 1** 類似，會出現一個側邊面板，用於設定存放庫設定。 如需有關這些設定的資訊，請參閱先前的小節。

![設定程式碼存放庫 2](media/author-visually/configure-repo-2.png)

### <a name="version-control"></a>版本控制
版本控制 (也稱為來源控制) 系統可讓開發人員在程式碼上共同作業，並追蹤對程式碼基底進行的變更。 來源控制是多開發人員專案的必要工具。

每個 VSTS Git 存放庫在與一個資料處理站建立關聯之後，都會有一個主要分支。 從該處，每個能夠存取 VSTS Git 存放庫的使用者在進行變更時都會有兩個選項：同步和發佈。

![同步和發佈](media/author-visually/sync-publish.png)

#### <a name="sync"></a>同步

按一下 [同步] 之後，您便可以從主要分支將變更提取至本機分支，或從本機分支將變更推送到主要分支。

![同步變更](media/author-visually/sync-change.png)

#### <a name="publish"></a>發佈
 將主要分支中的變更發佈至 Data Factory 服務。

> [!NOTE]
> **主要分支不代表在 Data Factory 服務中部署的內容。** 「必須」以手動方式將主要分支主發佈至 Data Factory 服務。




## <a name="expression-language"></a>運算式語言

使用者可以藉由使用 Azure Data Factory 所支援的運算式語言，指定運算式來定義屬性值。 如需有關支援哪些運算式的詳細資訊，請參閱 [Azure Data Factory 中的運算式和函式](control-flow-expression-language-functions.md)。

在屬性值中指定運算式，如以下的 UX 所示。

![運算式語言](media/author-visually/expression-language.png)

## <a name="parameters"></a>參數
使用者可以在 [參數] 索引標籤中，指定 [管線] 和 [資料集] 的參數。此外，只要按 [新增動態內容]，即可輕鬆在屬性中使用參數。

![動態內容](media/author-visually/dynamic-content.png)

從該處，您可以使用現有的參數，也可以在屬性值中指定新參數。

![參數](media/author-visually/parameters.png)

## <a name="feedback"></a>意見反應
按一下 [意見反應] 圖示，即可針對各種功能或您可能遇到的任何問題，向我們 (Microsoft) 提供意見反應。

![意見反應](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>後續步驟

若要了解如何監視和管理管線，請參閱[以程式設計方式監視和管理管線](monitor-programmatically.md)一文
