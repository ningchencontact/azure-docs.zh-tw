---
title: Microsoft 安全性程式碼分析上架指南
description: 本文說明如何安裝 Microsoft 安全性程式碼分析延伸模組
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 8936bc21af951cbabb90188b6a31d610caf80395
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241767"
---
# <a name="onboarding-and-installing"></a>上架和安裝

開始使用 Microsoft 安全性程式碼分析的必要條件:

- 符合資格的 Microsoft 統一支援供應專案, 如下一節所述。
- Azure DevOps 組織。
- Azure DevOps 組織安裝延伸模組的許可權。
- 可同步處理至雲端託管 Azure DevOps 管線的原始程式碼。

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>將 Microsoft 安全性程式碼分析延伸模組上線

- 如果您有下列其中一項支援供應專案, 請洽詢您的技術客戶經理以購買或交換現有的時數, 以取得擴充功能的存取權:
   - 整合支援的先進層
   - 整合支援效能層級
   - 開發人員的頂級支援
   - 合作夥伴的頂級支援
   - 適用于企業的頂級支援
- 如果您有下列其中一項支援服務, 或沒有 Microsoft 支援方案, 您必須升級為符合資格的支援供應專案:
   - 適用於合作夥伴的 Azure 支援
   - Azure 基本支援
   - Azure 開發人員支援
   - Azure 標準支援
   - Azure 專業指導
   - 整合支援核心層
- 若要購買合格的支援供應專案, 請移至我們的[支援服務首頁](https://www.microsoft.com/enterprise/services/support)。
- 在支援合約就緒之後, 請洽詢您的技術客戶經理以協助您開始使用, 並協助您收集所有必要的詳細資料。

>[!NOTE]
> 如果您是在 Microsoft 合作夥伴網路中註冊的合作夥伴, 您就有資格購買合作夥伴的頂級支援。 否則, 您必須購買先前所述的其中一個合格支援供應專案。

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>安裝 Microsoft 安全性程式碼分析延伸模組

1. 將延伸模組與您的 Azure DevOps 組織共用之後, 請移至您的 Azure DevOps 組織頁面。 這類頁面的範例 URL 是 http://dev.azure.com/contoso 。
1. 選取您名稱旁邊的 [購物包] 圖示, 然後選取 [**管理延伸**模組]。
1. 選取 [Microsoft 安全性程式碼分析] 延伸模組, 然後開啟 Azure DevOps UI wizard 以開始安裝。
1. 從下拉式清單中, 選擇要在其中安裝擴充功能的 Azure DevOps 組織。
1. 選取 [安裝]。 安裝完成之後, 您就可以開始使用延伸模組。

>[!NOTE]
> 即使您沒有安裝延伸模組的存取權, 仍請繼續執行安裝步驟。 在安裝過程中, 您可以要求 Azure DevOps 組織系統管理員的存取權。

安裝擴充功能之後, 就可以看到安全的開發組建工作, 並可將其新增至您的 Azure 管線。

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>將特定的組建工作新增至您的 Azure DevOps 管線

1. 從您的 Azure DevOps 組織中, 開啟您的 team 專案。
1. 選取 [**管線** > **組建**]。
1. 選取您要在其中新增擴充功能組建工作的管線:
   - 新管線:選取 [**新增**], 並遵循詳細的步驟來建立新的管線。
   - 編輯管線:選取現有的管線, 然後選取 [**編輯**] 以開始編輯管線。
1. 選取 **+** 並移至 [**新增**工作] 窗格。
1. 從清單或使用 [搜尋] 方塊, 尋找您要新增的組建工作。 選取 [新增]。
1. 指定工作所需的參數。
1. 將新組建排入佇列。
   >[!NOTE]
   >檔案和資料夾路徑相對於來源存放庫的根目錄。 如果您將輸出檔案和資料夾指定為參數, 則會將它們取代為我們在組建代理程式上定義的通用位置。

> [!TIP]
>
> - 若要在您的組建之後執行分析, 請在組建的 [發行組建成品] 步驟之後放置 Microsoft 安全性程式碼分析組建工作。 如此一來, 您的組建就可以在執行靜態分析工具之前完成並張貼結果。
> - 針對安全開發組建工作, 請一律選取 [**發生錯誤時仍繼續**]。 即使其中一項工具失敗, 其他工具也可以執行。 工具之間沒有相互相關性。
> - 只有當工具無法順利執行時, Microsoft 安全性程式碼分析組建工作才會失敗。 但即使工具識別出程式碼中的問題, 它們也會成功。 藉由使用 [後期分析組建] 工作, 您可以在工具識別程式碼中的問題時, 將組建設定為失敗。
> - 透過發行管線執行時, 不支援某些 Azure DevOps 組建工作。 更具體來說, Azure DevOps 不支援從發行管線內發佈成品的工作。
> - 如需 Azure DevOps Team Build 中可指定為參數之預先定義的變數清單, 請參閱[Azure DevOps 組建變數](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts)。

## <a name="next-steps"></a>後續步驟

如需設定組建工作的詳細資訊, 請參閱我們的設定[指南](security-code-analysis-customize.md)。

如果您有更多關於擴充功能和所提供工具的問題, 請查看我們的[常見問題頁面](security-code-analysis-faq.md)。
