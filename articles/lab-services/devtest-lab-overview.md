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
ms.openlocfilehash: b7cd6bb1fd0377ca1440d9c667453df922aacbd4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561450"
---
# <a name="about-azure-devtest-labs"></a>關於 Azure DevTest Labs
Azure DevTest Labs 可讓小組的開發人員，有效率地自行管理虛擬機器 (Vm) 和 PaaS 資源而不需等待核准。

DevTest Labs 建立實驗室組成預先設定的基底或 Azure Resource Manager 範本。 這些具有所有必要的工具和軟體，您可以使用它來建立環境。 您可以在幾分鐘內，而不是時數或天數來建立環境。

藉由使用 DevTest Labs，您可以執行下列工作來測試您的應用程式的最新版本：

- 使用可重複使用的範本和構件，快速佈建 Windows 和 Linux 環境。
- 將您的部署管線與研發/測試實驗室輕鬆整合，來佈建隨選環境。
- 相應增加您的負載測試所佈建多個測試代理程式，並建立預先佈建的環境，為訓練和示範。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>功能
DevTest Labs Vm 所使用的開發人員提供下列功能：

- 少於五個簡單步驟，快速建立 Vm。
- 從 VM 基底設定、 核准，以及由小組負責人或中央授權的策劃清單中選擇 IT。
- 從預先建立具有所有軟體和工具已安裝的自訂映像建立 Vm。 
- 從基本上是自訂映像，搭配最新的組建時建立的 Vm 已安裝的軟體的公式建立 Vm。 
- 安裝延伸模組在佈建後，在 Vm 上部署的構件。
- 設定自動關機和自動啟動 Vm 上的排程。
- 宣告的預先建立的 VM，而不會建立程序。

DevTest Labs 會使用 PaaS 環境的開發人員提供下列功能：

- 使用 Resource Manager 快速地建立 PaaS 環境少於三個簡單的步驟。
- 從 Resource Manager 範本，這些設定，同時也由小組負責人或中央授權的策劃清單中選擇 IT。
- 使用 Resource Manager 範本的實驗室環境中探索 Azure 加速空的資源群組 （沙箱）。

DevTest Labs 也可讓中央 IT 來控制浪費、 最佳化成本的資源，以及保持在預算內執行下列工作：  

- 在 Vm 上設定自動關機和自動啟動的排程。
- 設定原則的使用者可以建立的 Vm 數目。
- 設定 Vm 的大小和使用者選擇從資源庫映像上的原則。
- 追蹤成本，並在實驗室中設定目標。
- 取得通知高適用於實驗室的預估成本，因此您可以採取必要動作。

DevTest Labs 提供下列優點中建立、 設定及管理雲端中的環境。

## <a name="cost-control-and-governance"></a>成本的控制及管理
DevTest Labs 可讓您更輕鬆地控制成本可讓您執行下列工作：

- [設定您的實驗室原則](devtest-lab-get-started-with-lab-policies.md)，例如 Vm 數目每位使用者或每個實驗室。 
- 建立[原則，以自動關閉](devtest-lab-set-lab-policy.md)並啟動 Vm。
- 追蹤 Vm 和 PaaS 資源的成本內實驗室，以維持在內啟動[預算](devtest-lab-configure-cost-management.md)。
- 保持您的實驗室環境內，因此您不會啟動外部的資源。

## <a name="quickly-get-to-ready-to-test"></a>快速做好測試的準備
DevTest Labs 可讓您建立預先佈建的環境，配備了您的小組開發和測試應用程式需要的所有項目。 只要[領取環境](devtest-lab-add-claimable-vm.md)安裝您的應用程式的最後一個良好組建和開始工作。 或使用更快、 更精簡的環境建立的容器。

## <a name="create-once-use-everywhere"></a>一次建立，隨處可用
擷取並共用 PaaS[環境範本](devtest-lab-create-environment-from-arm.md)並[成品](add-artifact-repository.md)小組或組織內 — 全都放在原始檔控制，輕鬆建立開發人員和測試環境。

## <a name="worry-free-self-service"></a>零煩惱的自助方式
DevTest Labs 可讓您的開發人員和測試人員快速且輕鬆地[建立 IaaS Vm](devtest-lab-add-vm.md)並[PaaS 資源](devtest-lab-create-environment-from-arm.md)使用一組預先設定的資源。

## <a name="use-iaas-and-paas-resources"></a>使用 IaaS 和 PaaS 資源 
開發人員可也啟動 PaaS 資源，例如 Azure Service Fabric 叢集的 Web Apps 功能的 Azure App Service 和 SharePoint 伺服器陣列，使用 Resource Manager 範本。 若要開始 PaaS 在實驗室中，使用 從範本[公用環境儲存機制](devtest-lab-configure-use-public-environments.md)或是[將實驗室連接至您自己的 Git 存放庫](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories)。 您也可以對這些資源保持在預算內追蹤成本。

## <a name="integrate-with-your-existing-toolchain"></a>與現有工具鏈整合
使用預先外掛程式或 API 進行佈建開發/測試環境的直接從您偏好[持續整合 (CI) 工具](devtest-lab-integrate-ci-cd-vsts.md)、 整合式開發環境 (IDE) 或自動化的發行管線。 您也可以使用完整的命令列工具。

## <a name="next-steps"></a>後續步驟
請參閱下列文章：

- 若要深入了解 DevTest Labs，請參閱[研發/測試實驗室概念](devtest-lab-concepts.md)。
- 如需逐步指示的逐步解說，請參閱[教學課程：設定使用 Azure DevTest Labs 的實驗室](tutorial-create-custom-lab.md)。