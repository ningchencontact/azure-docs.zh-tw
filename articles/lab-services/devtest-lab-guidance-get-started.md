---
title: 開始使用 Azure DevTest Labs
description: 此文章提供使用 Azure DevTest Labs 的主要案例，以及兩個在組織中開始使用服務的一般途徑。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: spelluru
ms.openlocfilehash: 87baef8ddb5b5d8fc979ba5afb9f9b13cb4fc2ef
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2018
ms.locfileid: "52876531"
---
# <a name="get-started-with-using-azure-devtest-labs"></a>開始使用 Azure DevTest Labs
一旦您決定要探索 DevTest Labs 之後，可依循兩個一般途徑繼續前進：概念證明與經過調整的部署。 

**經過調整的部署**包含檢閱與規劃的週/月，其目的是將 DevTest Labs 部署至擁有數百或數千名開發人員的整個企業。 

**概念證明部署**於單一小組專注投入來建立組織價值的努力。 雖然很容易讓人聯想到經過調整的部署，但此方法往往比證明概念選項更容易失敗。 因此，我們建議從小規模開始、從第一個小組進行了解、透過二至三個其他小組重複使用相同方法，然後根據所獲得的知識來規劃經過調整的部署。 對於成功的概念證明，我們建議您挑選一或兩個小組，並識別他們的案例 (開發環境與測試環境)、記錄其目前的使用案例，以及部署 DevTest Labs。 

## <a name="scenarios"></a>案例
有三個適用於 DevTest Labs 實作的主要案例： 

- 開發人員桌面
- 測試環境
- 實驗室/訓練/Hackathon

## <a name="developer-desktops"></a>開發人員桌面
開發人員針對不同專案的開發電腦通常有不同的需求。 使用 DevTest Labs，開發人員就有權存取預先設定來符合其最常見案例的隨選 VM。 DevTest Labs 提供下列權益：

- 組織可以提供常見的開發和測試環境，以確定可跨小組保有一致性
- 開發人員可以視需要快速佈建開發電腦
- 開發人員可以自助方式佈建資源，而不需要訂用帳戶層級權限
- IT 或管理員可以預先定義網路拓撲，而開發人員可以既簡單又直接的方式直接利用它，而不需要任何特殊的存取權
- 開發人員可以視需要輕鬆地自訂他們的開發電腦
- 系統管理員可以確定下列事項以控制成本：
    - 開發人員無法取得超過開發所需的 VM
    - VM 不使用時會關機
    - 只允許用於特定工作負載的 VM 執行個體大小子集

## <a name="test-environments"></a>測試環境
在整個企業內建立和管理測試環境，可能需要投入更多努力。 使用 DevTest Labs，可以輕鬆地建立、更新或重複測試環境，讓小組能夠在需要時存取完整設定的環境。 在此案例中，DevTest Labs 提供以下權益：

- 測試人員可以利用可重複使用的範本和成品，快速佈建 Windows 和 Linux 環境，來測試其最新版的應用程式。
- 測試人員可以佈建多個測試代理程式，相應增加其負載測試
- 管理員可以將實驗室連線到 Azure DevOps 來啟用 DevOps 案例
- 系統管理員可以確定下列事項以控制成本：
    - 測試人員無法取得超過所需的 VM
    - VM 不使用時會關機
    - 只允許用於特定工作負載的 VM 執行個體大小子集

## <a name="labs-workshops-trainings-and-hackathons"></a>實驗、工作坊、訓練及 Hackathon  
Azure DevTest Labs 中的實驗室可用來做為暫時性活動的絕佳容器，例如工作坊、實際操作實驗室、訓練或 Hackathon。 此服務可讓您建立實驗室，在其中提供自訂範本供每位受訓者建立相同且隔離的訓練環境。 您可以新增原則，以確保訓練環境只會在受訓者需要時才提供給他們使用，且包含足夠的訓練所需資源，例如虛擬機器。 最後，您可以輕易地與受訓者共用實驗室，讓他們只要按一下就能存取。 當課程結束之後，即可輕鬆地刪除該實驗室及所有相關資源。


## <a name="next-steps"></a>後續步驟
請參閱本系列的下一篇文章：[相應增加您的 DevTest Labs 部署](devtest-lab-guidance-scale.md)
