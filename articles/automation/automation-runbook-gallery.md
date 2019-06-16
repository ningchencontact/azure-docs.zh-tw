---
title: Azure 自動化的 Runbook 和模組資源庫
description: 來自 Microsoft 和社群的 Runbook 和模組可供您在 Azure 自動化環境中安裝及使用。  本文說明如何存取這些資源以及將您的 Runbook 貢獻至資源庫。
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 20aafc117ad8b6bd625894180fdfe79bd86192bd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60737344"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Azure 自動化的 Runbook 和模組資源庫

您可以存取已由 Microsoft 和社群建置的案例，而不是在 Azure 自動化中建立您自己的 Runbook 和模組。

您可以取得 PowerShell runbook 並[模組](#modules-in-powershell-gallery)從 PowerShell 資源庫並[Python runbook](#python-runbooks)從 Script Center 組件庫。 您也可以提供給社群共用您開發的案例將 runbook 新增至資源庫，請參閱

## <a name="runbooks-in-powershell-gallery"></a>PowerShell 資源庫中的 Runbook

[PowerShell 資源庫](https://www.powershellgallery.com/packages)來自 Microsoft 和社群，您可以匯入至 Azure 自動化提供各種不同的 runbook。 若要使用其中一個，從資源庫下載 runbook，或者可以從資源庫中，或從您的自動化帳戶，在 Azure 入口網站中直接匯入 runbook。

您可以只匯入直接從 PowerShell 資源庫使用 Azure 入口網站。 您無法執行此函式，使用 PowerShell。

> [!NOTE]
> 您應該驗證您從 PowerShell 資源庫取得，並小心中安裝和執行它們在生產環境中的任何 runbook 的內容。

### <a name="to-import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>從 Runbook 資源庫，使用 Azure 入口網站匯入的 PowerShell runbook

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 在 [程序自動化]  底下，按一下 [Runbook 資源庫] 
3. 選取**來源：PowerShell 資源庫**。
4. 找出您想要的資源庫項目，並且選取以檢視其詳細資料。 您可以在左邊輸入發行者和類型的其他搜尋參數。

   ![瀏覽資源庫](media/automation-runbook-gallery/browse-gallery.png)

5. 按一下 [檢視來源專案]  以檢視 [TechNet 指令碼中心](https://gallery.technet.microsoft.com/)中的項目。
6. 若要匯入項目，請按一下以檢視其詳細資料，然後按一下 [匯入]  按鈕。

   ![匯入按鈕](media/automation-runbook-gallery/gallery-item-detail.png)

7. 選擇性變更 Runbook 的名稱，然後按一下 [確定]  以匯入 Runbook。
8. Runbook 會出現在自動化帳戶的 [Runbook]  索引標籤上。

### <a name="adding-a-powershell-runbook-to-the-gallery"></a>加入資源庫中的 PowerShell runbook

Microsoft 鼓勵您將 runbook 新增至 「 PowerShell 資源庫，您認為可能有助於其他客戶。 PowerShell 資源庫 」 接受 PowerShell 模組和 PowerShell 指令碼。 您可以新增 runbook[將它上傳至 「 PowerShell 資源庫](/powershell/gallery/how-to/publishing-packages/publishing-a-package)。

> [!NOTE]
> PowerShell 資源庫中不支援圖形化 runbook。

## <a name="modules-in-powershell-gallery"></a>PowerShell 資源庫中的模組

PowerShell 模組包含您可以在 Runbook 中使用的 Cmdlet，您可以安裝在 Azure 自動化中的現有模組可於 [PowerShell 資源庫](https://www.powershellgallery.com)取得。 您可以從 Azure 入口網站啟動此資源庫，然後直接安裝至 Azure 自動化。 您也可以手動下載及安裝。  

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>透過 Azure 入口網站從自動化模組資源庫匯入模組

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 在 [共用資源]  下選取 [模組]  ，以開啟模組清單。
3. 從頁面頂端按一下 [瀏覽資源庫]  。

   ![模組資源庫](media/automation-runbook-gallery/modules-blade.png)

4. 在 [瀏覽資源庫]  頁面上，您可以依下列欄位來搜尋：

   * 模組名稱
   * 標記
   * 作者
   * Cmdlet/DSC 資源名稱

5. 尋找您感興趣的模組，並選取以檢視其詳細資料。  

   當您向下切入到特定的模組時，您可以檢視更多的資訊。 此資訊包括返回 PowerShell 資源庫的連結、任何必要的相依性，以及該模組包含的所有 Cmdlet 或 DSC 資源。

   ![PowerShell 模組的詳細資料](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. 若要將該模組直接安裝至 Azure 自動化，請按一下 [匯入]  按鈕。
7. 按一下 [匯入] 按鈕時，在 [匯入]  窗格上，您會看到將要匯入的模組名稱。 如果已安裝所有相依性，[確定]  按鈕會啟動。 如果缺少相依性，您必須先匯入這些相依性後，才能匯入此模組。
8. 在 [匯入]  分頁上按一下 [確定]  以匯入模組。 當 Azure 自動化將模組匯入至您的帳戶時，它會擷取有關模組和 Cmdlet 的中繼資料。 因為必須解壓縮每個活動，此動作可能需要幾分鐘的時間。
9. 您會收到初始通知，表示正在部署模組，而完成時還會收到另一個通知。
10. 匯入模組之後，您可以看到可用的活動。 您可以將其資源用於 Runbook 和「預期狀態設定」中。

> [!NOTE]
> Azure 自動化不支援僅支援 PowerShell 核心的模組，該模組無法匯入 Azure 入口網站中，或直接從 PowerShell 資源庫部署。

## <a name="python-runbooks"></a>Python Runbook

[指令碼中心資源庫](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=)中提供 Python Runbook。 您可以按一下參與至指令碼中心資源庫的 Python runbook**上傳投稿文章**。 當您這麼做時，請務必在上傳您的貢獻項目時新增標籤 **Python**。

> [!NOTE]
> 若要上傳到的內容[指令碼中心](https://gallery.technet.microsoft.com/scriptcenter)須有 100 個點的最小值。 

## <a name="requesting-a-runbook-or-module"></a>要求 Runbook 或模組

您可以將要求傳送至 [使用者心聲](https://feedback.azure.com/forums/246290-azure-automation/)。  如果您需要協助撰寫 Runbook 或有關於 PowerShell 的問題，請將問題張貼至我們的[論壇](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc) \(英文\)。

## <a name="next-steps"></a>後續步驟

* 若要開始使用 Runbook，請參閱[在 Azure 自動化中管理 Runbook](manage-runbooks.md)
* 若要了解含有 Runbook 的 PowerShell 和 PowerShell 工作流程之間的差異，請參閱 [了解 PowerShell 工作流程](automation-powershell-workflow.md)
