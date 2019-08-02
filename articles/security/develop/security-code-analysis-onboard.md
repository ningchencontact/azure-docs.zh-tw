---
title: Microsoft Azure 安全性程式碼分析上架指南
description: 本文是關於安裝安全性程式碼分析延伸模組
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
ms.openlocfilehash: 8a8eca73205d4f8f33d4d069fda72638af61d355
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718297"
---
# <a name="how-to-onboarding-and-installing"></a>如何：上架和安裝

開始使用 Microsoft 安全性程式碼分析的必要條件
  - 合格的 Microsoft 統一支援服務供應專案 (以下詳細資料)
  - Azure DevOps 組織
  - 將擴充功能安裝到 Azure DevOps 組織的許可權
  - 可同步處理至雲端託管 Azure DevOps 管線的原始程式碼


## <a name="onboarding-microsoft-security-code-analysis-extension"></a>將 Microsoft 安全性程式碼分析延伸模組上線

- 如果您已經有下列其中一項支援供應專案, 只要洽詢您的技術客戶經理並 purchase\swap 現有的時數, 即可取得擴充功能的存取權。
   - 統一支援–先進和效能層級、適用于開發人員的頂級支援、合作夥伴的頂級支援, 或企業的頂級支援。
- 如果您有下列其中一項支援服務, 或沒有任何 Microsoft 支援方案, 您將需要升級至合格的支援供應專案:
   - Azure 對合作夥伴、azure 基本、Azure 開發人員、Azure 標準、Azure 專業指導或整合支援-核心層的支援
- 若要購買合格的支援供應專案, 請造訪我們的[支援服務首頁](https://www.microsoft.com/enterprise/services/support)
- 準備好支援合約之後, 請洽詢您的技術客戶經理, 協助您開始使用, 並收集所有必要的詳細資料。
 
>[!NOTE]
> 只有 Microsoft 合作夥伴網路中的已註冊合作夥伴, 才符合購買合作夥伴頂級支援的資格, 否則請購買先前所述的其中一個合格支援供應專案

## <a name="installing-microsoft-security-code-analysis-extension"></a>安裝 Microsoft 安全性程式碼分析延伸模組

1. 當擴充功能與您的 Azure DevOps 組織共用之後, 請流覽至您的 Azure DevOps 組織頁面 (例如 http://dev.azure.com/contoso)
2. 按一下您名稱旁邊右上角的 [購物包] 圖示, 然後按一下 [管理延伸模組]。 
3. 按一下 [Microsoft 安全性程式碼分析] 延伸模組, 然後啟動 Azure DevOps UI wizard 以開始安裝。
4. 從下拉式清單中選擇要安裝擴充功能的 Azure DevOps 組織
5. 按一下 [安裝]。 完成後, 您可以繼續使用延伸模組

>[!NOTE]
> 即使您沒有存取權, 仍繼續進行安裝步驟, 而且您將能夠在此程式期間, 要求您的 Azure DevOps 組織系統管理員存取。
>
安裝延伸模組之後, 將會顯示安全的開發組建工作, 並可供新增至您的 Azure Pipelines。

## <a name="adding-specific-build-tasks-to-your-devops-pipeline"></a>將特定的組建工作新增至您的 DevOps 管線

1. 從您的 Azure DevOps 組織開啟 team 專案。
2. 流覽至 [**管線**] 底下的 [**組建**] 索引標籤 
3. 選取您要在其中新增擴充功能組建工作的管線。 
   - [新增]-按一下 [**新增**], 並遵循詳細的步驟來建立新的管線。
   - 編輯-選取管線, 然後按一下 [**編輯**] 開始編輯現有的管線。
4. 按一下 [+] 以流覽至 [**新增**工作] 窗格。
5. 從清單中尋找您想要新增的組建工作, 或使用 [搜尋] 方塊, 然後按一下 [**新增**]。 
6. 您現在可以繼續指定工作所需的參數。
>[!NOTE]
>檔案或目錄路徑相對於來源存放庫的根目錄, 而指定輸出檔案夾/檔案的參數將會取代為我們在組建代理程式上定義的通用位置。

7. 將新組建排入佇列。
> [!TIP]
>  - 若要在組建之後執行分析, 請在組建的 [發行組建成品] 步驟之後放置 Microsoft 安全性程式碼分析組建工作。 如此一來, 您的組建就可以在執行靜態分析工具之前完成並張貼結果。
>  - 請一律檢查安全開發組建工作的 [**錯誤時仍繼續**] 選項。 即使其中一個工具失敗, 其他人也可以執行。 沒有相互相關性。
>  - 只有當工具無法順利執行時, Microsoft 安全性程式碼分析組建工作才會失敗, 但如果和工具識別程式碼中的問題, 則不會失敗。 您可以使用**後置分析**組建工作, 將組建設定成在工具識別程式碼中的問題時中斷。
>  - 透過「發行」管線執行時, 不支援某些 Azure DevOps 組建工作。 這是 Azure DevOps 的限制。 它們不支援從發行管線內發行成品的工作。
>  - 如需 Azure DevOps Team Build 中預先定義的變數清單, 您可以將其指定為參數, 請參閱[Azure DevOps 組建變數](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts)

## <a name="next-steps"></a>後續步驟

如需設定組建工作的詳細資訊, 請參閱我們的設定[指南](security-code-analysis-customize.md)

如果您有關于擴充功能和所提供工具的進一步問題, 請[查看我們的常見問題頁面。](security-code-analysis-faq.md)


