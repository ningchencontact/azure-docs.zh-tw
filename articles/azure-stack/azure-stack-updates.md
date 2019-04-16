---
title: 管理 Azure Stack 中的更新概觀 | Microsoft Docs
description: 深入了解 Azure Stack 整合系統的更新管理。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: mabrigg
ms.lastreviewed: 04/04/2019
ms.reviewer: justini
ms.openlocfilehash: bf797404b173d6febe133eacbb9d36310160aff6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281568"
---
# <a name="manage-updates-in-azure-stack-overview"></a>在 Azure Stack 中管理更新概觀

*適用於：Azure Stack 整合系統*

適用於 Azure Stack 整合式系統的 Microsoft 更新套件，通常大約會在每個月的第四個星期二發行。 請向您的原始設備製造商 (OEM) 詢問其特定通知程序，以確定更新通知會送達您的組織。 如需尚在有效支援期間之版本的相關資訊，您也可以在 [概觀] > [版本資訊] 底下查看此文件庫。 

每次發行的 Microsoft 軟體更新均以單一更新封裝的形式提供。 身為 Azure Stack 操作員，您可以從管理員入口網站匯入及安裝這些更新套件，並監視這些更新套件的安裝進度。 

您的 OEM 供應商也會發行更新，例如驅動程式和韌體更新。 雖然廠商會以個別套件的形式提供這些更新，但它們會使用與 Microsoft 更新套件中相同的方式來匯入、安裝及管理。

為了讓系統獲得支援，您必須將 Azure Stack 更新為特定版本等級。 務必檢閱 [Azure Stack 服務原則](azure-stack-servicing-policy.md)。

> [!NOTE]
> 您無法將 Azure Stack 更新封裝套用於 Azure Stack 開發套件。 更新封裝專為整合系統所設計。 如需資訊，請參閱[重新部署 ASDK](https://docs.microsoft.com/azure/azure-stack/asdk)。

## <a name="the-update-resource-provider"></a>更新資源提供者

Azure Stack 包含處理 Microsoft 軟體應用程式更新的更新資源提供者。 對於有實體主機、Service Fabric 應用程式和執行階段，以及所有基礎結構虛擬機器和其相關聯服務，這個提供者可檢查均已套用更新。

在更新安裝中，更新程序以 Azure Stack 中的各種子系統 (例如，實體主機和基礎結構虛擬機器) 為目標時，您可以檢視高階狀態。

## <a name="plan-for-updates"></a>規劃更新

強烈建議您向使用者通知任何維護工作，並且盡可能將一般的維護期間安排在非上班時間。 維護作業可能會影響租用戶工作負載和入口網站作業。

- 開始安裝此更新之前，請先執行 [Test-AzureStack](azure-stack-diagnostic-test.md) 與下列參數來驗證 Azure Stack 的狀態，並解決所發現的一切運作問題，包括所有警告和失敗。 也請檢閱作用中警示，並將所有需要採取動作的警示解決。  

  ```powershell
  Test-AzureStack -Group UpdateReadiness
  ``` 

## <a name="using-the-update-tile-to-manage-updates"></a>[更新] 圖格可用來管理更新

您要從系統管理員入口網站管理更新。 身為 Azure Stack 操作員，您可以使用儀表板中的 [更新] 圖格進行下列操作：

- 檢視重要資訊，例如目前的版本。
- 安裝更新並監視進度。
- 檢閱先前安裝之更新本身的更新歷程記錄。
- 檢視雲端的目前 OEM 套件版本
 
## <a name="determine-the-current-version"></a>判斷目前版本

您可以在 [更新] 圖格中檢視目前的 Azure Stack 版本。 若要開啟圖格：

1. 開啟 Azure Stack 系統管理入口網站。
2. 選取 [儀表板]。 在 [更新] 圖格中，會列出目前的版本。 

    ![預設儀表板上的 [更新] 圖格](./media/azure-stack-updates/image1.png)

    例如，在畫面中的版本是 1.1903.0.35。

## <a name="install-updates-and-monitor-progress"></a>安裝更新並監視進度


1. 開啟 Azure Stack 系統管理入口網站。
2. 選取 [儀表板]。 選取 [更新] 圖格。
3. 選取 [立即更新]。

    ![Azure Stack 更新執行詳細資料](media/azure-stack-updates/azure-stack-update-button.png)

4.  您可以檢視高階狀態，因為更新流程會逐一查看 Azure Stack 中的各個子系統。 範例子系統包括實體主機、Service Fabric、基礎結構虛擬機器，以及提供系統管理員和使用者入口網站的服務。 在整個更新程序中，更新資源提供者會報告有關更新的其他詳細資料，例如已成功執行的步驟數目以及正在進行中的數目。

5. 從 [更新執行詳細資料] 刀鋒視窗中，選取 [下載完整記錄] 以下載完整記錄。

    ![Azure Stack 更新執行詳細資料](media/azure-stack-updates/update-run-details.png)

6. 完成後，更新資源提供者會提供 [成功] 確認，以通知您更新程序已完成，以及所花費的時間。 從該處，您可以使用篩選器檢視有關所有更新、可用更新或已安裝更新的資訊。

    ![Azure Stack 更新執行詳細資料成功](media/azure-stack-updates/update-success.png)

   如果更新失敗，則更新圖格報告 [需要注意]。 使用 [下載完整記錄] 以取得更新可能已失敗的高階狀態。 Azure Stack 記錄檔有助於診斷和疑難排解。

## <a name="next-steps"></a>後續步驟

- [Azure Stack 服務原則](azure-stack-servicing-policy.md) 
- [Azure Stack 中的區域管理](azure-stack-region-management.md)
