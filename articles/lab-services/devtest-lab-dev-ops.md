---
title: 整合 Azure DevTest Labs 和 DevOps |Microsoft Docs
description: 了解如何使用持續整合 (CI) 中的 Azure DevTest Labs 實驗室/持續傳遞 (CD) 管線在企業環境中。
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: spelluru
ms.openlocfilehash: 62c44bfea28d47d7c32aa7ef440a40d45c314683
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078920"
---
# <a name="integration-of-azure-devtest-labs-and-azure-devops"></a>Azure DevTest Labs 與 Azure DevOps 整合
DevOps 是一種軟體開發方法與營運 (部門 Ops) 系統整合的軟體開發 (Dev)。 此系統可能會在與業務目標對齊提供新功能、 更新和修正程式。 此方法包含無法設計目標、 使用模式和客戶的意見反應; 為基礎的新功能的所有項目若要修正、 復原及強化系統發生問題時。 這種方法輕鬆識別的元件是持續整合 (CI) / 持續傳遞 (CD) 管線。 CI/CD 管線會使用資訊、 程式碼和透過一系列的步驟，包括建置、 測試和部署，以產生系統認可的資源。 本文著重於不同的方式，有效率地使用在企業環境中的 管線內的實驗室。 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>DevOps 工作流程中使用實驗室的優點 

### <a name="focused-access"></a>具有焦點的存取 
為元件中使用實驗室可讓特定的生態系統，以一組有限的人員相關聯。 通常，小組或群組使用的常見區域或特定的功能將會有指派給他們的實驗室。   

### <a name="infrastructure-replication-in-the-cloud"></a>在雲端中的基礎結構複寫 
開發人員可以快速設定一個包含開發人員方塊與原始程式碼和工具的開發生態系統。 開發人員也可以建立幾乎等同於生產環境設定的環境。 它可協助進行更快速的內部迴圈開發。 

### <a name="pre-production"></a>進入生產階段前 
CI/CD 管線中具有實驗室讓您更有多個不同的進入生產階段前環境或在非同步測試相同時間執行的機器的工作變得更容易。 可部署和管理實驗室內不同的支援基礎結構，例如組建代理程式。 

## <a name="devops-with-devtest-labs"></a>使用 DevTest Labs 的 DevOps 

### <a name="development--operation"></a>開發 / 操作 
實驗室應該著重在功能區域中運作的小組。 此常見的焦點是用來共用的特定區域的資源，例如工具、 指令碼或 Resource Manager 範本。 它同時又能限制負面的影響較小的群組以允許更快速的變更。 這些共用的資源可讓開發人員建立開發 Vm 具有所有必要的程式碼、 工具和設定。 它們可以是動態建立，或建立自訂的基底映像的系統。 不只可以開發人員建立的 Vm，但也他們可以在其中建立所需的範本，以在實驗室中建立適當的 Azure 資源為基礎的研發/測試實驗室環境。 針對在實驗室環境可以完成 「 任何變更或破壞性的工作 」，而不會影響其他人。 假設產品是客戶的電腦上安裝的獨立系統的位置。 在此案例中，DevTest Labs 已改善，包括安裝其他軟體使用成品及預先建置的程式碼進行更快速的內部迴圈測試的客戶組態建立 VM。 
  
## <a name="cicd-pipeline"></a>CI/CD 管線 
CI/CD 管線是一個移動從開發人員，提取要求的程式碼整合與現有的程式碼，並將它部署到生產環境生態系統的 DevOps 中的重要元件。 不需要在實驗室內所有資源。 比方說，Jenkins 主機無法設定實驗室之外為多個持續性的資源。 以下是一些特定的範例整合管線中的實驗室。 

### <a name="build"></a>建置 
建置管線著重於建立套件的元件，將測試一起交給發行管線。 實驗室可以做為組建代理程式和其他支援資源的位置是組建管線的一部分。 讓您動態建置的基礎結構可讓更大的控制。 在實驗室中有多個環境的能力，每次建置可以執行以非同步方式同時使用一部分的環境資訊的組建識別碼，來唯一識別特定的組建資源。   

組建代理程式，來限制存取實驗室的功能來提高安全性，並減少意外損毀的可能性。  

### <a name="test"></a>測試 
DevTest Labs 可讓 CI/CD 管線，以自動建立的 Azure 資源 （Vm、 環境），且可以用來進行自動化和手動測試。 會使用成品或使用從建置程序的資訊來建立不同的自訂設定所需的測試的公式建立的 Vm。   

### <a name="release"></a>版本 
DevTest Labs 常用的 [釋放] 區段中的驗證才部署程式碼。 這就像是測試在建置區段中。 生產資源不應該部署在 DevTest Labs。 

### <a name="customization"></a>自訂 
Azure DevOps 中有現有允許的 Vm 的操作和特定的實驗室環境的工作。 雖然 Azure DevOps 服務是一種方式管理 CI/CD 管線，您可以在任何支援的功能，呼叫 REST Api、 執行 PowerShell 指令碼，或使用 Azure CLI 的系統整合實驗室。 

雖然某些 CI/CD 管線管理員有現有的開放原始碼外掛程式，可以管理 Azure 和 DevTest Labs 中的資源。 您可能需要使用一些自訂的指令碼以符合管線的特定需求。  這一點之後，執行工作時，服務主體搭配適當的角色，才能存取實驗室。 服務主體通常需要參與者角色存取實驗室。 如需詳細資訊，請參閱 <<c0> [ 至 Azure 的 DevOps 持續整合和傳遞管線中整合 Azure DevTests Labs](devtest-lab-integrate-ci-cd-vsts.md)。 
 