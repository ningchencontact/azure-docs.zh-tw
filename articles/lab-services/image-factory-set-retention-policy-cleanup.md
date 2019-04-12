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
ms.openlocfilehash: 48412b3006a462fcc9c77219f42fb41d08f2df61
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/11/2019
ms.locfileid: "59490734"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中建立自訂映像處理站
本文章涵蓋設定保留原則、 清理 factory，並從所有其他 DevTest Labs 組織中淘汰舊的映像。 

## <a name="prerequisites"></a>必要條件
請確定您已進一步進行之前，遵循這些文章：

- [建立映像處理站](image-factory-create.md)
- [從 Azure DevOps 執行映像處理站](image-factory-set-up-devops-lab.md)
- [儲存自訂映像並散發至多個實驗室](image-factory-save-distribute-custom-images.md)

下列項目應該已經準備就緒：

- 映像處理站，在 Azure DevTest Labs 的實驗室
- 一或多個目標的處理站會將散發黃金映像的 Azure DevTest Labs
- Azure DevOps 專案，用來自動化映像處理站。
- 包含指令碼和 （在本例中，在上述範例中使用相同的 DevOps 專案） 的設定原始程式碼位置
- 組建定義來協調 Azure Powershell 工作
 
## <a name="setting-the-retention-policy"></a>設定保留原則
設定清除步驟之前，定義您想要保留在 DevTest Labs 中的多少歷史映像。 當您遵循[從 Azure DevOps 執行映像處理站](image-factory-set-up-devops-lab.md)文章中，設定各種建置變數。 其中一項**ImageRetention**。 您將此變數設定為`1`，這表示研發/測試實驗室將不會維護歷程記錄的自訂映像。 只有最新的分散式的影像才可用。 如果您變更這個變數設為`2`、 最新散發映像，以及將保留先前的。 您可以設定此值，以定義您想要維護您的 DevTest Labs 中的歷史映像數目。

## <a name="cleaning-up-the-factory"></a>清除 處理站
清除 處理站的第一個步驟是從映像處理站中移除黃金映像 Vm。 沒有要執行這項工作，就像我們先前的指令碼的指令碼。 第一個步驟是新增另一個**Azure Powershell**工作對組建定義，如下圖所示：

![PowerShell 步驟](./media/set-retention-policy-cleanup/powershell-step.png)

一旦您有新的工作清單中，選取的項目，並填寫所有詳細資料，如下圖所示：

![清除舊的映像 PowerShell 工作](./media/set-retention-policy-cleanup/configure-powershell-task.png)

指令碼參數： `-DevTestLabName $(devTestLabName)`。

## <a name="retire-old-images"></a>淘汰舊的映像 
這項工作中移除任何舊的映像，保留 只記錄符合**ImageRetention**建置變數。 新增其他**Azure Powershell**建置我們的組建定義的工作。 一旦新增，選取工作，並填寫詳細資料，如下圖所示： 

![淘汰舊的映像 PowerShell 工作](./media/set-retention-policy-cleanup/retire-old-image-task.png)

指令碼參數如下： `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>將組建加入佇列
既然您已完成的組建定義，藉此確定一切運作的新組建排入佇列。 新的自訂映像出現在目的地實驗室並如果核取影像工廠實驗室順利完成建置之後，您會看到沒有佈建的 Vm。 此外您進一步的組建排入佇列的如果您會看到舊的自訂映像從淘汰 DevTest Labs 中根據建置變數中設定的保留值的清除工作。

> [!NOTE]
> 如果您已執行組建管線結尾的最後一篇文章系列中，手動刪除之前佇列新組建映像處理站實驗室中建立虛擬機器。  我們設定所有項目，並確認它有效，只需要執行手動清除步驟。



## <a name="summary"></a>總結
現在您已執行的映像處理站可以產生和發佈至您的實驗室依需求的自訂映像。 此時，它只需取得您的映像已正確地設定和用來識別目標實驗室。 如先前的文章中所述**Labs.json**檔案位於您**組態**資料夾可讓您指定哪些映像應在每個目標實驗室中可用。 將其他 DevTest Labs 新增到您的組織時，您只需要在新的實驗室 Labs.json 中新增項目。

將新的映像新增至您的處理站也很簡單。 當您要包含新的映像在您開啟您的處理站[Azure 入口網站](https://portal.azure.com)，瀏覽至您的處理站 DevTest Labs、 選取按鈕以新增 VM，然後選擇所需的 marketplace 映像和成品。 而不是選取**Create**按鈕以進行新的 VM，請選取**檢視 Azure Resource Manager 範本**"並將範本儲存為內某處的.json 檔案**GoldenImages**存放庫中的資料夾。 下次您執行您的映像處理站，它會建立自訂映像。


## <a name="next-steps"></a>後續步驟
1. [排程組建/發行](/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=designer)定期執行映像處理站。 它會重新整理您的映像處理站產生定期執行。
2. 讓更多的黃金映像處理站。 您也可以考慮[建立成品](devtest-lab-artifact-author.md)指令碼的 VM 安裝程式工作的其他部分，並將成品包含在您的原廠映像。
4. 建立[分隔組建/發行](/azure/devops/pipelines/overview?view=azure-devops-2019)來執行**DistributeImages**個別指令碼。 當您變更 Labs.json 並取得映像複製到目標實驗室中，而不需要再重新建立所有映像時，您可以執行此指令碼。

