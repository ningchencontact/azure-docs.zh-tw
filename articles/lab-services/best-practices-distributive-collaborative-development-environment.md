---
title: 分散式的 Azure DevTest Labs 資源共同開發 |Microsoft Docs
description: 設定分散式、 最易於合作的開發環境來開發 DevTest Labs 資源提供最佳作法。
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: spelluru
ms.openlocfilehash: 8ffc8ed3f84284ff69e9515cba0982790b823a37
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543774"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Azure DevTest Labs 資源的分散式、 最易於合作開發的最佳做法
不同小組或來開發和維護的程式碼基底的人員，可讓分散式的共同開發。 若要成功，在開發程序取決於能夠建立、 共用及整合的資訊。 此索引鍵的開發原則可用在 Azure DevTest Labs。 有數種類型的實驗室內分散在企業中的不同實驗室的資源。 不同類型的資源中放置焦點分成兩個方面：

- 在內部儲存實驗室 （實驗性） 內的資源
- 儲存中的資源[外部連接到實驗室的儲存機制](devtest-lab-add-artifact-repo.md)（程式碼儲存機制為基礎）。 

本文件說明跨多個小組允許共同作業和散發，同時確保自訂和所有層級最高品質的一些最佳作法。

## <a name="lab-based-resources"></a>實驗室資源

### <a name="custom-virtual-machine-images"></a>自訂的虛擬機器映像
您可以自訂的映像部署至實驗室，在每天晚上的常見來源。 如需詳細資訊，請參閱 <<c0> [ 映像處理站](image-factory-create.md)。    

### <a name="formulas"></a>公式
[公式](devtest-lab-manage-formulas.md)實驗室專屬，且沒有散發機制。 實驗室成員執行所有開發的公式。 

## <a name="code-repository-based-resources"></a>程式碼儲存機制為基礎的資源
有兩個不同的功能為基礎的程式碼存放庫、 成品和環境。 本文章會詳述功能以及如何最有效地設定儲存機制和工作流程以允許自訂的可用成品和組織層級或小組層級環境的能力。  此工作流程以標準為基礎[原始程式碼控制分支策略](/azure/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops)。 

### <a name="key-concepts"></a>重要概念
成品的來源資訊包括中繼資料，指令碼。 環境的來源資訊會包含中繼資料和 Resource Manager 範本使用任何支援的檔案，例如 PowerShell 指令碼、 DSC 指令碼、 Zip 檔等等。  

### <a name="repository-structure"></a>存放庫結構  
原始程式碼控制 (SCC) 的最常見的設定是設定 將程式碼檔案 （Resource Manager 範本、 中繼資料，以及指令碼） 用來儲存實驗室中的多層式結構。 具體來說，建立不同的存放庫來儲存受企業的不同層級的資源：   

- 全公司資源。
- 業務單位/部門全資源
- 小組專用的資源。

每個這些層級連結到主要分支，才能實際執行品質是不同的存放庫。 [分支](/azure/devops/repos/git/git-branching-guidance?view=azure-devops)在每個存放庫會是適用於這些特定資源 （構件或範本） 的開發。 此結構對齊與研發/測試實驗室，您可以輕鬆地連接多個儲存機制和多個分支同時組織的實驗室。 存放庫名稱包含在使用者介面 (UI)，以避免產生混淆，當有相同的名稱、 描述和發行者。
     
下圖顯示兩個存放庫： 公司存放庫是由 IT 部門中，維護和維護的研發部門的部門存放庫。

![範例分散且共同作業的開發環境](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
此分層的結構來開發，同時維持在主要分支的較高層級的品質，同時擁有多個連接至實驗室的儲存機制，可讓更大的彈性。

## <a name="next-steps"></a>後續步驟    
請參閱下列文章：

- 使用對實驗室新增儲存機制[Azure 入口網站](devtest-lab-add-artifact-repo.md)或透過[Azure Resource Management 範本](add-artifact-repository.md)
- [DevTest Labs 構件](devtest-lab-artifact-author.md)
- [研發/測試實驗室環境](devtest-lab-create-environment-from-arm.md)。
