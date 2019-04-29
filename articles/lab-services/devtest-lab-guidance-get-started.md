---
title: 使用 Azure DevTest Labs 的常見案例
description: 本文提供使用 Azure DevTest Labs 和兩個一般路徑來開始使用您的組織中的服務的主要案例。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 8736ba4c24ac4c8f8d84345028d1cadfdef38697
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773803"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>使用 Azure DevTest Labs 的常見案例
根據企業需求，DevTest Labs 可以設定以符合不同的需求。  這篇文章討論常見的案例。 每個案例涵蓋因所使用，用以實作這些案例的研發/測試實驗室和資源的優點。  

- 開發人員桌面
- 測試環境
- 訓練研討會、 實際操作實驗室和黑客松
- 沙箱化的調查
- 教室實驗室

## <a name="developer-desktops"></a>開發人員桌面
開發人員針對不同專案的開發電腦通常有不同的需求。 使用 DevTest Labs 中，開發人員可以存取隨選虛擬機器設定為符合最常見的案例。 DevTest Labs 提供下列權益：

- 組織可以提供常見的開發電腦，確保在小組間的一致性。
- 開發人員可以快速佈建隨其開發電腦或[宣告現有預先設定的機器](devtest-lab-add-claimable-vm.md)。
- 開發人員可以佈建資源的自助方式而不需要訂用帳戶層級權限。
- IT 系統管理員可以[預先定義的網路拓樸](devtest-lab-configure-vnet.md)和開發人員可以直接使用它既簡單又直接的方式而不需要任何特殊的存取。
- 開發人員可以輕鬆地[來自訂](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm)其開發電腦所需。
- 系統管理員可以確定下列事項以控制成本：
    - 開發人員[無法取得更多 Vm](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user)超過所需進行開發
    - [Vm 會關閉](devtest-lab-set-lab-policy.md#set-auto-shutdown)在不使用時
    - 只有[允許的 VM 執行個體大小子集](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes)適用於特定的實驗室
    - [管理成本目標和通知](devtest-lab-configure-cost-management.md)每個實驗室。

如需詳細資訊，請參閱 <<c0> [ 適用於開發人員使用 Azure DevTest Labs](devtest-lab-developer-lab.md)。 

## <a name="test-environments"></a>測試環境
在整個企業內建立和管理測試環境，可能需要投入更多努力。 研發/測試實驗室測試環境可以輕鬆地建立、 更新，或重複。 它可讓小組在需要時存取的完整設定的環境。 在此案例中，DevTest Labs 提供以下權益：

- 組織可以提供常見的測試環境跨小組確保一致性。
- 測試人員可以使用可重複使用的範本，快速佈建 Windows 和 Linux 環境中測試其應用程式的最新版本。
- 系統管理員可以連線至 Azure DevOps，以啟用 DevOps 案例的實驗室
- 實驗室擁有者可以控制成本如此可確保：
    - [在環境中的 Vm 會關閉](devtest-lab-set-lab-policy.md#set-auto-shutdown)在不使用時
    - 只有[允許的 VM 執行個體大小子集](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes)特定實驗室
    - [管理成本目標和通知](devtest-lab-configure-cost-management.md)每個實驗室。

如需詳細資訊，請參閱 <<c0> [ 使用 Azure DevTest Labs VM 和 PaaS 測試環境](devtest-lab-test-env.md)。

## <a name="sandboxed-investigations"></a>沙箱化的調查
開發人員通常會調查不同的技術或基礎結構設計。 根據預設，使用 DevTest Labs 中建立的所有環境會都建立自己的資源群組中。 DevTest Labs 使用者取得只有這些資源的讀取存取權。 不過，對於開發人員需要更多控制，整個實驗室的設定可以更新，以提供[參與者權限](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/)他們建立的每個環境的原始 DevTest Labs 使用者。  使用 DevTest Labs 中，開發人員可以指定自動將它們在實驗室中建立的環境的參與者權限。  此案例可讓開發人員加入及/或變更 Azure 資源，其所需的開發或測試環境。 [依資源的成本](devtest-lab-configure-cost-management.md#view-cost-by-resource) 頁面可讓實驗室擁有者得以追蹤用於調查每個環境的成本。

如需詳細資訊，請參閱 <<c0> [ 組環境資源群組的存取權限](https://aka.ms/dtl-sandbox)。

## <a name="trainings-hands-on-labs-and-hackathons"></a>訓練、 實際操作實驗室和黑客松 
在 Azure DevTest Labs 中的對實驗室做為暫時性的活動，例如研討會、 實際操作實驗室、 訓練或黑客松的絕佳容器。  此服務可讓您建立實驗室，在其中提供自訂範本供每位受訓者建立相同且隔離的訓練環境。 在此案例中，DevTest Labs 提供以下權益：

- [原則](devtest-lab-set-lab-policy.md)確保受訓者只會取得資源，例如虛擬機器，他們所需的數目。
- 預先設定和建立的機器都[索取](devtest-lab-add-claimable-vm.md)從受訓者的單一動作。
- 藉由存取與受訓者共用實驗室[實驗室 URL](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab)。
- [到期日](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs)確保虛擬機器上的 後不再需要就會刪除機器。
- 很容易[刪除實驗室](devtest-lab-delete-lab-vm.md#delete-a-lab)」 和 「 全部[相關資源](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab)時透過訓練。

如需詳細資訊，請參閱 <<c0> [ 使用 Azure DevTest Labs 進行訓練](devtest-lab-training-lab.md)。  

## <a name="proof-of-concept-vs-scaled-deployment"></a>與延展的部署概念證明
一旦您決定要探索 DevTest Labs 中，有往前兩個一般路徑：概念與相應部署的證明。  

**經過調整的部署**包含檢閱與規劃的週/月，其目的是將 DevTest Labs 部署至擁有數百或數千名開發人員的整個企業。

A**概念證明**部署著重於心力來自同一個小組以建立組織的值。 雖然很容易讓人聯想到經過調整的部署，但此方法往往比證明概念選項更容易失敗。 因此，我們建議從小規模開始、從第一個小組進行了解、透過二至三個其他小組重複使用相同方法，然後根據所獲得的知識來規劃經過調整的部署。 對於成功的概念證明，我們建議您挑選一或兩個小組，並識別他們的案例 (開發環境與測試環境)、記錄其目前的使用案例，以及部署 DevTest Labs。

## <a name="next-steps"></a>後續步驟
請閱讀下列文章：

- [研發/測試實驗室概念](devtest-lab-concepts.md)
- [DevTest Labs 常見問題集](devtest-lab-faq.md)

