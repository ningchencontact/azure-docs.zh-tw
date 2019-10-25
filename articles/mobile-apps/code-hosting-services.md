---
title: 使用 GitHub 和 Azure DevOps 在雲端裝載您的行動應用程式原始程式碼
description: 瞭解使用 Microsoft 服務在雲端中裝載行動應用程式程式碼的服務。
author: elamalani
ms.assetid: 12a8a079-9b3c-4faf-2222-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: f0af2ee5e62bbdbfb5d18cffc3e3ed62edec81f3
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795623"
---
# <a name="cloud-hosted-source-code-management-services"></a>雲端託管的原始程式碼管理服務
如果您的開發小組有多個小組成員使用相同的程式碼基底，您需要尋找適當的位置來裝載您的程式碼。 將資料儲存在雲端並擁有集中式存放庫，可讓每個人都能上傳、編輯和管理程式碼檔案、與其他開發人員互動，並提供程式碼隨時可供存取的功能，只要您具有網際網路連接。

雲端裝載比內部部署選項簡單許多，因為它需要較少的硬體設定，並可讓組織以更靈活的方式完成執行程式。

## <a name="benefits-of-hosting-source-code-in-the-cloud"></a>在雲端裝載原始程式碼的優點
- **集中式雲端儲存體**，從任何地方都能查看及管理您的資料。
- 在小組中提供更好的共同作業**和更清楚**的程式碼，以追蹤程式碼並管理專案，以確保持續交付絕佳的軟體。
- **參與**並讓您**更輕鬆地參與**您的專案。
- **更快速的發行週期**，因為小組可以更快速且**輕鬆地參與**您的專案。
- **降低成本**，而不必擔心維護自己的硬體、伺服器、VPN 等等。

使用下列服務在雲端裝載您的應用程式資料。

## <a name="github"></a>GitHub
[GitHub](https://github.com/)是一個開放原始碼存放庫裝載服務，它會以各種不同的程式設計語言裝載原始程式碼專案，並追蹤對每個反復專案所做的各種變更。

**主要功能**
- 將所有程式碼**裝載**在同一個位置。 私用、公用或開放原始碼，所有存放庫都有提供工具，可協助您裝載、版本和發行程式碼。
- 順暢的程式**代碼審查**和內建的審核工具，讓程式碼在任何小組流程中都能有重要的部分。
    - 保護分支、建議變更和要求審查。
    - 查看內容中的差異和批註，並取得清楚的意見反應。
- 使用 GitHub 的**專案管理工具**，及早協調、保持一致，並完成更多工作。
    - 請參閱專案的大圖片。
    - GitHub 中您的程式碼**旁邊的工作**面板。
    - **拖放卡**，讓您可以將問題或提取要求指派給小組成員。
    - 用來組織和追蹤進度的**里程碑**。
    - 請**注意**，用來捕捉可能有用但不屬於特定問題或提取要求的想法。
- 藉由從 **[GitHub Marketplace](https://github.com/marketplace)** 購買應用程式，輕鬆探索及選擇適當的工具以獲得更好的溝通和自動化工作。
- **小組管理**可輕鬆管理和拓展小組、**使用者角色**，以協助組織小組和存取權限、**討論執行緒**工具來持續交談主題和小組的焦點，以及快速設定的**社區指導方針**具有帳戶的新小組成員。
- **流覽**和**星**狀熱門專案以遵循。
- **網路，並**向產業中的其他人學習。

**參考**
- [GitHub](https://github.com/)
- [GitHub 指南](https://guides.github.com/)
- [GitHub 社區論壇](https://github.community/)
- [GitHub Marketplace](https://github.com/marketplace)

## <a name="azure-devops"></a>Azure DevOps
[Azure DevOps](https://azure.microsoft.com/services/devops/)支援[Azure Repos](https://azure.microsoft.com/services/devops/repos/)和[Team Foundation 版本控制（TFVC）](https://docs.microsoft.com/azure/devops/repos/tfvc/index?view=azure-devops)做為原始檔控制選項。 它有無限制的免費私人存放庫，具有共同作業程式碼審查、先進的檔案管理、程式碼搜尋和分支原則，以確保高品質的程式碼。 Azure Repos 很適合用於小型專案，以及需要原生 AAD 支援和 advanced 原則的大型組織。
    
**主要功能**
- **無限制的雲端裝載 Git 原始程式碼存放庫**，適用于您的公用和私用存放庫
    - 支援任何 Git 用戶端。
    - Web 勾點和 API 整合。
- 從存放庫提取要求開始**您的下一個組建**
    - 使用執行緒討論和每項變更的持續整合，共同作業以建立更好的程式碼。
    - 使用 Azure Pipelines 或您的工具，設定持續整合/持續傳遞 (CI/CD)，以自動透過每個已完成的提取要求來觸發組建、測試和部署。
    - 使用分支原則來保護您的程式碼品質。
- **具有 Team Foundation 版本控制的集中式版本控制**，包括程式碼審查。
- 使用 Xcode、Eclipse、IntelliJ、Android Studio、Visual Studio、Visual Studio Code 等**連接到您的程式碼**。
- **強大的語義程式碼搜尋**。
- **企業就緒**，因為它與 AZURE ACTIVE DIRECTORY （AD）的原生整合，簡化了管理程式碼存放庫存取的流程。
- 使用分支原則來**確保程式碼品質**，例如程式碼審核的最小數目、需要成功的組建，以及強制執行 Git 合併策略。
- **連接您最愛的開發環境**，以存取存放庫和管理工作。

**參考**
- [開始使用 Azure Repos](https://azure.microsoft.com/services/devops/repos/) 
- [Azure Repos 檔](/azure/devops/repos/?view=azure-devops)