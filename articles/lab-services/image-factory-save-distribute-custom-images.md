---
title: 儲存並發佈 Azure DevTest Labs 中的映像 |Microsoft Docs
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
ms.openlocfilehash: feabd055833e5f0d850138af528cce1da82cae49
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622596"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>儲存自訂映像並散發至多個實驗室
這篇文章涵蓋了可讓您以儲存從已建立的虛擬機器 (Vm) 的自訂映像的步驟。 其中也涵蓋如何散發組織中其他 DevTest Labs 自訂映像。

## <a name="prerequisites"></a>必要條件
下列項目應該已經準備就緒：

- 映像處理站，在 Azure DevTest Labs 的實驗室。
- Azure DevOps 專案用來自動化映像處理站。
- 來源包含的指令碼和組態 （在本例中，在相同的 DevOps 專案是由前一個步驟所述） 的程式碼位置。
- 組建定義來協調 Azure Powershell 工作。

如有需要後續步驟[從 Azure DevOps 執行映像處理站](image-factory-set-up-devops-lab.md)來建立或設定這些項目。 

## <a name="save-vms-as-generalized-vhds"></a>將 Vm 儲存為一般化的 Vhd
儲存現有的 Vm 一般化的 vhd。  沒有將現有的 Vm 儲存為一般化的 Vhd 的範例 PowerShell 指令碼。 若要使用它，首先，新增另一個**Azure Powershell**工作對組建定義，如下圖所示：

![新增 Azure PowerShell 步驟](./media/save-distribute-custom-images/powershell-step.png)

一旦您有新的工作清單中，選取的項目，因此我們可以填入所有詳細資料，如下圖所示： 

![PowerShell 設定](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>一般化與特製化的自訂映像
在  [Azure 入口網站](https://portal.azure.com)，當從虛擬機器建立自訂映像，您可以選擇將一般化或特製化的自訂映像。

- **特製化的自訂映像：** 未在電腦上執行 Sysprep/解除佈建。 這表示映像的 OS 磁碟上現有的虛擬機器 (snapshot) 的完整複本。  相同的檔案、 應用程式、 使用者帳戶、 電腦名稱，並依此類推，都存在時我們會從這個自訂映像建立新的機器。
- **一般化的自訂映像：** 在電腦上執行 Sysprep/解除佈建。  此程序執行時，它會移除使用者帳戶、 移除的電腦名稱，去除使用者登錄 hive 控制檔等，其目標是將映像一般化，因此您可以建立另一個虛擬機器時自訂。  當您將虛擬機器一般化 （透過執行 sysprep） 程序會終結目前的虛擬機器，不會再也無法運作。

貼齊在映像處理站中的自訂映像的指令碼會將 Vhd 儲存在先前步驟中建立任何虛擬機器 (識別依據在 Azure 中的資源標記)。

## <a name="update-configuration-for-distributing-images"></a>更新發佈映像的組態
此程序的下一個步驟是從映像處理站實驗室出的自訂映像推送到需要的任何其他實驗室。 此程序的核心部分是**labs.json**組態檔。 您可以找到此檔案中的**組態**映像處理站中包含的資料夾。

有兩個 labs.json 組態檔中所列的重要事項：

- 用來唯一識別特定的目的地實驗室使用的訂用帳戶識別碼和實驗室名稱。
- 應該設定根目錄的相對路徑的形式推送至實驗室的映像的特定集。 您可以指定 （若要取得所有映像，該資料夾中） 的整個資料夾太。

如下所列的兩個實驗室的範例 labs.json 檔案。 在此情況下，您要將映像發佈至兩個不同的實驗室。

```json
{
   "Labs": [
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2012R2",
               "Win2016/Datacenter.json"
         ]
      },
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2016/Datacenter.json"
         ]
      }
   ]
}
```

## <a name="create-a-build-task"></a>建立建置工作
使用您稍早在本文中，所見的相同步驟加入額外**Azure Powershell**建置工作，您的組建定義。 填入詳細資料，如下圖所示： 

![建置工作，以將映像](./media/save-distribute-custom-images/second-build-task-powershell.png)

參數如下： `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

這項工作會出現在映像處理站任何自訂映像和其推送到 Labs.json 檔案中定義的所有實驗室。

## <a name="queue-the-build"></a>將組建加入佇列
散發組建工作完成後，藉此確定一切運作的新組建排入佇列。 在建置成功完成之後，新的自訂映像會顯示在 Labs.json 組態檔中已輸入目的地實驗室。

## <a name="next-steps"></a>後續步驟
在系列的下一篇文章中,，您可以更新映像處理站與保留原則和清除步驟：[設定保留原則，以及執行清除指令碼](image-factory-set-retention-policy-cleanup.md)。
