---
title: 關於 Azure DevTest Labs | Microsoft Docs
description: 了解 DevTest Labs 如何讓您輕鬆地建立、管理和監視 Azure 虛擬機器
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1b9eed3b-c69a-4c49-a36e-f388efea6f39
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: 599b4325e0eb9aa7f6ccca2626c6d4b14f38149d
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339324"
---
# <a name="about-azure-devtest-labs"></a>關於 Azure DevTest Labs
Azure DevTest Labs 是可讓開發人員小組有效率地自助虛擬機器和 （或） 所需的開發、 測試、 訓練和示範等，而不需等待常數核准所需的工具上的 PaaS 資源的服務。 

實驗室已經是由預先設定的基底或有所有必要的工具和軟體開發人員可用來建立環境的 Resource Manager 範本所組成。 開發人員可以建立其環境，在幾分鐘內，而不是時數或天數。 

使用 DevTest Labs，可以測試您的應用程式的最新版本，執行下列工作：

- 使用可重複使用的範本和成品的快速佈建 Windows 和 Linux 環境
- 輕鬆整合您部署管線與 DevTest Labs 佈建隨環境
- 相應增加您的負載測試所佈建多個測試代理程式，並建立預先佈建的環境，為訓練和示範。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]
> 
> 

## <a name="capabilities"></a>功能
DevTest Labs 虛擬機器所使用的開發人員提供下列功能：

- 小於 5 個簡單步驟，快速建立虛擬機器。
- 從 VM 基底設定、 核准，以及由小組負責人或中央授權的策劃清單中選擇 IT。
- 從預先建立的自訂映像具有所有軟體和工具安裝在映像來建立機器。 
- 建立公式，基本上是自訂的映像和最新的組建會在建立虛擬機器的時間安裝軟體的機器。
- 安裝延伸模組，它佈建完成後，在 VM 上部署的構件。
- 設定自動關機和自動啟動排程的當天結尾有關機及之後版本的電腦上，執行隔天早上。
- 只要宣告預先建立的虛擬機器，而不需要經歷建立機器的程序。 

DevTest Labs 會使用 PaaS 環境的開發人員提供下列功能：

- 建立 PaaS 的 Azure Resource Manager 環境，快速地遵循少於三個簡單的步驟。
- 從 Resource Manager 範本、 設定、 核准，並獲得授權的小組負責人或中央的策劃清單中選擇 IT。
- 空的資源群組 (Sandbox) 來瀏覽整個 Azure 的同時仍保有實驗室的內容中使用 Resource Manager 範本時啟動。
- 研發/測試實驗室在建立、設定及管理雲端中的開發人員和測試環境方面可提供下列優點

除了在小組的開發人員的自助服務模型，此服務可讓中央 IT 來控制浪費、 最佳化資源成本，並保持在預算內執行下列工作： 

- 設定自動關機] 及 [自動啟動排程在虛擬機器上。
- 可以建立設定原則的虛擬機器的使用者數目。
- 從選取的虛擬機器大小和資源庫映像的使用者設定原則。
- 追蹤成本，並在實驗室中設定目標。
- 取得通知高預估的成本，實驗室，以便您可以採取必要動作。 

DevTest Labs 提供建立、 設定及管理雲端中的環境中的下列優點：

## <a name="cost-control-and-governance"></a>成本的控制及管理
DevTest Labs 可讓您更輕鬆地控制成本可讓您執行下列工作：

- 設定實驗室-例如虛擬機器 (VM) 數目的原則，每個使用者和每個實驗室的 Vm 數目。 
- 建立原則來自動關閉及啟動 Vm。
- 可讓您在虛擬機器和 PaaS 資源啟動您的實驗室中，您可以在預先定義的預算內即可保持追蹤成本。 
- 協助開發人員持續在實驗室的內容，使它們不會得到分內快速啟動其基礎的資源群組或訂用帳戶以外的任何資源。

## <a name="quickly-get-to-ready-to-test"></a>快速做好測試的準備
研發/測試實驗室讓您能夠建立預先佈建的環境，完全滿足小組開發及測試應用程式時的一切需求。 只是宣告的最後一個良好組建，您的應用程式安裝所在的環境和開始動工吧。 或是使用容器，更快速且高效地建立環境。

## <a name="create-once-use-everywhere"></a>一次建立，隨處可用
擷取並共用 PaaS 環境範本及成品，您的小組或組織在原始檔控制-內建立開發人員和測試環境更加輕易。

## <a name="worry-free-self-service"></a>零煩惱的自助方式
DevTest Labs 可讓您的開發人員和測試人員快速並輕鬆地建立虛擬機器 (IaaS) 和 PaaS 資源中按幾下，使用一組預先設定的資源。

## <a name="use-iaas-and-paas-resources"></a>使用 IaaS 和 PaaS 資源 
DevTest Labs 也可讓開發人員加速 PaaS 資源，例如 Web Apps、 Service Fabric 叢集，SharePoint 伺服器陣列，並依此類推。 在實驗室內使用 Resource Manager 範本。 使用 Resource Manager 範本從我們的公用環境存放庫，或將實驗室連接至您自己的 Git 儲存機制，來開始著手在實驗室中的 PaaS。 您也可以在這些資源，以維持在預先定義的預算內追蹤成本。 

## <a name="integrate-with-your-existing-toolchain"></a>與現有工具鏈整合
使用預製的外掛程式或我們的 API 來佈建開發/測試環境，直接從您偏好的持續整合 (CI) 工具、 整合式開發環境 (IDE) 或自動化的發行管線。 您也可以使用我們全方位的命令列工具。

## <a name="next-steps"></a>後續步驟
請參閱下列文章： 

- 若要深入了解 DevTest Labs，請參閱[研發/測試實驗室概念](devtest-lab-concepts.md)
- 如需逐步指示的逐步解說，請參閱[教學課程：設定使用 Azure DevTest Labs 的實驗室](tutorial-create-custom-lab.md)


