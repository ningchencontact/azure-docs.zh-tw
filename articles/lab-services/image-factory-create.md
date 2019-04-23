---
title: 在 Azure DevTest Labs 中建立映像處理站 |Microsoft Docs
description: 了解如何在 Azure DevTest Labs 中建立自訂映像處理站。
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: cf1bb31614c04d6073bc40c510fc43b2f8e4e189
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "60148980"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中建立自訂映像處理站
這篇文章會示範如何使用範例指令碼中可用設定的自訂映像處理站[Git 存放庫](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)。

## <a name="whats-an-image-factory"></a>映像處理站是什麼？
映像處理站是設定即程式碼解決方案，建置並散佈映像會自動定期所有所需的組態。 映像處理站中的映像一律是最新狀態，而且持續維護幾乎零一旦整個程序自動化。 因為所有必要的組態已經在映像中，它會使用基礎作業系統建立 VM 之後，手動設定系統節省時間。

重要的加速器，以取得就緒的狀態，在 DevTest Labs 中的開發人員桌面使用自訂映像。 自訂映像的缺點是會有額外維護在實驗室中的項目。 例如，產品的試用版過期一段時間 （或者） 不套用新發行的安全性更新，這會強制我們的自訂映像會定期重新整理。 使用映像處理站中，您必須簽入原始程式碼控制，並讓自動化程序來產生定義為基礎的自訂映像的映像的定義。

解決方案可讓您從自訂映像建立虛擬機器，同時避免其他進行中的維護成本的速度。 使用此解決方案，您可以自動建立自訂映像、 將它們散發給其他 DevTest Labs 中，並淘汰舊的映像。 在下列影片中，您會了解映像處理站，以及它使用 DevTest Labs 的實作方式。  所有的 Azure Powershell 指令碼可在免費取得，且位於此： [ https://aka.ms/dtlimagefactory ](https://aka.ms/dtlimagefactory)。

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>解決方案的高階檢視
解決方案可讓您從自訂映像建立虛擬機器，同時避免其他進行中的維護成本的速度。 使用此解決方案，您可以自動建立自訂映像，並將它們散發給其他 DevTest Labs。 您可以使用 Azure DevOps (前身為 Visual Studio Team Services) 做為協調流程引擎，自動化中的所有作業研發/測試實驗室。

![解決方案的高階檢視](./media/create-image-factory/high-level-view-of-solution.png)

沒有[VSTS 延伸模組，適用於 DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) ，可讓您執行這些個別的步驟：

- 建立自訂影像
- 建立 VM
- 刪除 VM
- 建立環境
- 刪除環境
- 填入的環境

使用 DevTest Labs 延伸模組是簡單的方法，若要開始使用自動建立在 DevTest Labs 中的 自訂映像。

沒有針對更複雜的案例中使用 PowerShell 指令碼的替代實作。 使用 PowerShell，您可以完全自動化可以用於持續整合和持續傳遞 (CI/CD) 工具鏈的 DevTest Labs 為基礎的映像處理站。 遵循此替代方案中的原則是：

- 常見的更新應該不需要變更即可映像處理站。 （例如，加入新類型的自訂映像，會自動停用舊的映像，新增新 'endpoint' DevTest Labs 收到自訂的映像等等）。
- 常見的變更都會受到原始程式碼控制 （基礎結構即程式碼）
- DevTest Labs 接收自訂映像可能無法在相同 Azure 訂用帳戶 （實驗室跨越訂用帳戶）
- PowerShell 指令碼必須是可重複使用，因此我們可以視需要快速啟動額外的處理站

## <a name="next-steps"></a>後續步驟
移至這一節中的下一篇文章：[從 Azure DevOps 執行映像處理站](image-factory-set-up-devops-lab.md)
