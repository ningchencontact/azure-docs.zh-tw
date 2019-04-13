---
title: Azure DevOps，Azure DevTest Labs 中從執行映像處理站 |Microsoft Docs
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
ms.openlocfilehash: 5a3d6e51a71f6aab742fe042d6e6e281192319a4
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523013"
---
# <a name="run-an-image-factory-from-azure-devops"></a>從 Azure DevOps 執行映像處理站
本文章涵蓋 Azure DevOps (前身為 Visual Studio Team Services) 從執行映像處理站所需的所有準備工作。

> [!NOTE]
> 任何協調流程引擎將運作 ！ Azure DevOps 不是必要的。 映像處理站是執行使用 Azure PowerShell 指令碼，因此它可以是手動執行，使用 Windows 工作排程器，其他 CI/CD 系統等等。

## <a name="create-a-lab-for-the-image-factory"></a>建立實驗室以進行映像處理站
設定 映像處理站的第一個步驟是建立在 Azure DevTest Labs 的實驗室。 此實驗室是我們建立的虛擬機器和儲存自訂映像時，影像工廠實驗室。 這個實驗室會被視為整體的映像處理站程序的一部分。 一旦您建立實驗室時，請務必儲存的名稱，因為您稍後將會用它。

## <a name="scripts-and-templates"></a>指令碼和範本
採用映像處理站，為您的小組的下一個步驟是了解可用。 映像處理站的指令碼和範本可用於公開[DevTest Labs GitHub 存放庫](https://github.com/Azure/azure-devtestlab/tree/master/Scripts/ImageFactory)。 以下是外框的部分：

- 映像處理站。 它是根資料夾。 
    - 設定。 映像處理站所需的輸入
        - GoldenImages。 此資料夾包含代表定義的自訂映像的 JSON 檔案。
        - Labs.json。 其中小組註冊以取得特定的自訂映像的檔案。
- 指令碼。 映像處理站的引擎。

這一節中的文章提供有關這些指令碼和範本的更多詳細資料。 

## <a name="create-an-azure-devops-team-project"></a>建立 Azure DevOps 的 team 專案
Azure 的 DevOps 可讓您儲存的原始碼、 執行 Azure PowerShell 在一個地方。 您可以排程重複執行來保持最新的映像。 有很好記錄結果診斷任何問題的功能。  使用 Azure DevOps 不過並不需要，您可以使用任何控管/引擎可以連線到 Azure，可以執行 Azure PowerShell。

如果您有現有的 DevOps 帳戶或您想要改為使用的專案，請略過此步驟。

若要開始，建立 Azure DevOps 中的免費帳戶。 請瀏覽 https://www.visualstudio.com/，然後選取**免費開始**正下方**Azure DevOps** (先前稱為 VSTS)。 您必須選擇唯一的帳戶名稱，然後請務必選擇管理使用 Git 的程式碼。 這建立之後，儲存到您的 team 專案的 URL。 以下是範例 URL: `https://<accountname>.visualstudio.com/MyFirstProject`。

## <a name="check-in-the-image-factory-to-git"></a>簽入至 Git 的映像處理站
所有的 PowerShell、 範本和映像處理站的設定位於[公用 DevTest Labs GitHub 存放庫](https://github.com/Azure/azure-devtestlab/tree/master/Scripts/ImageFactory)。 取得程式碼到新的 team 專案的最快方式是匯入存放庫。 （如此您才能取得額外的文件和範例），這會在整個研發/測試實驗室的儲存機制中提取。 

1. 請瀏覽您在上一個步驟中建立 Azure DevOps 專案 (URL 看起來**https:\//\<accountname >.visualstudio.com/MyFirstProject**)。
2. 選取 **匯入存放庫**。
3. 請輸入**複製 URL** DevTest Labs 存放庫： `https://github.com/Azure/azure-devtestlab`。
4. 選取 [匯入]。

    ![匯入 Git 存放庫](./media/set-up-devops-lab/import-git-repo.png)

如果您決定只檢查中剛好有需要的內容 （影像工廠檔案），請依照下列步驟[此處](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs)來複製 Git 儲存機制並推送位於中的檔案**指令碼/ImageFactory**目錄。

## <a name="create-a-build-and-connect-to-azure"></a>建立組建，並連接至 Azure
此時，您有 Azure DevOps 中的 Git 存放庫中儲存的來源檔案。 現在，您需要設定管線，以執行 Azure PowerShell。 有許多選項來執行這些步驟。 在本文中，您使用組建定義為了簡單起見，但它的運作方式的建置 DevOps，DevOps 發行 （單一或多個環境）、 其他的執行引擎，例如 Windows 工作排程器或任何其他可執行 Azure PowerShell 控管。

> [!NOTE]
> 一項重點，要牢記在心的一些 PowerShell 檔案需要一些時間執行時有很多 （10 +） 的自訂映像來建立。 免費裝載的 DevOps 組建/版本代理程式會有逾時值為 30 分鐘，因此您無法使用免費裝載的代理程式，一旦您開始建置許多映像。 這個逾時問題會套用至任何您決定使用的控管，最好先確認，您可以擴充長時間執行的 Azure PowerShell 指令碼的一般逾時值。 在 Azure DevOps 的情況下您可以使用付費裝載的代理程式，或使用您自己的組建代理程式。

1. 若要開始，請選取**設定組建**DevOps 專案的首頁上：

    ![設定組建按鈕](./media/set-up-devops-lab/setup-build-button.png)
2. 指定**名稱**組建 (例如：建置和傳遞到 DevTest Labs 的映像。） 
3. 選取 **空**組建定義，然後選取**套用**來建立您的組建。 
4. 在這個階段，您可以選擇**託管**組建代理程式。 
5. **儲存**組建定義。

    ![組建定義](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>設定組建變數
若要簡化的命令列參數，會封裝一組組建變數到磁碟機映像處理站的索引鍵值。 選取 **變數**索引標籤，您會看到數個預設變數的清單。 以下是 Azure devops 中輸入變數的清單：


| 變數名稱 | 值 | 注意 |
| ------------- | ----- | ----- |
| ConfigurationLocation | /Scripts/ImageFactory/Configuration | 這是儲存機制中的完整路徑**組態**資料夾。 如果您匯入上述整個存放庫，左邊的值是正確的。 否則請更新指向設定位置。 |
| DevTestLabName | MyImageFactory | Azure DevTest Labs 實驗室中用做為 factory 來產生映像的名稱。 如果您沒有帳戶，建立一個。 請確定實驗室服務端點可存取的相同訂用帳戶。 |
| ImageRetention | 1 | 您想要儲存的每一種類型的映像數目。 設定預設值為 1。 |
| MachinePassword | ******* | 虛擬機器的內建的系統管理員帳戶密碼。 這是暫時性的帳戶，因此請確定它是安全。 選取小的鎖定圖示，以確保它是安全字串右邊。 |
| MachineUserName | ImageFactoryUser | 虛擬機器內建的系統管理員帳戶使用者名稱。 這是暫時性的帳戶。 |
| StandardTimeoutMinutes | 30 | 我們應該等候一般的 Azure 作業逾時。 |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | 其中實驗室存在且其服務端點可存取的訂用帳戶識別碼。 |
| VMSize | Standard_A3 | 要用於虛擬機器的大小**建立**步驟。 建立的 Vm 是暫時性的。 大小必須是這[實驗室的啟用](devtest-lab-set-lab-policy.md)。 請確認有足夠[訂用帳戶核心配額](../azure-subscription-service-limits.md)。 

![建立變數](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>連接到 Azure
下一個步驟是設定服務主體。 這是可讓 DevOps 組建代理程式代表使用者在 Azure 中運作的 Azure Active Directory 中的身分識別。 若要設定，開始將您新增第一個 Azure PowerShell 建置步驟。

1. 選取 **將工作加入**。
2. 搜尋**Azure PowerShell**。 
3. 一旦您找到它，選取**新增**將工作新增至組建。 當您這樣做時，您會看到出現在左邊，新增的工作。

![設定 PowerShell 步驟](./media/set-up-devops-lab/set-up-powershell-step.png)

若要設定的服務主體的最快方式是讓替我們的 Azure DevOps。 

1. 選取 **任務**您剛才新增。
2. 針對**Azure 連接類型**，選擇**Azure Resource Manager**。 
3. 選取 **管理**連結設定服務主體。 

如需詳細資訊，請參閱此[部落格文章](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)。 當您選取**管理**連結，您將登陸在 DevOps 中正確的位置 （部落格文章中的第二個螢幕擷取畫面） 來設定 Azure 的連線。 請務必選擇**Azure Resource Manager 服務端點**時設定此功能。

## <a name="complete-the-build-task"></a>完成建置工作
如果您選取建置工作，您會看到所有詳細資料，在右窗格中，應該會自動填入。 

1. 首先，命名為建置工作：**建立虛擬機器**。 
2. 選擇**服務主體**選擇您建立**Azure Resource Manager**
3. 選擇**服務端點**。 
4. 針對**指令碼路徑**，選取 **...（省略符號）** 右邊。
5. 瀏覽至**MakeGoldenImageVMs.ps1**指令碼。 
6. 指令碼參數看起來應該像這樣： `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![完成的組建定義](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>將組建加入佇列
讓我們確認您已正確設定的新組建排入佇列的所有項目。 執行組建時，切換至[Azure 入口網站](https://portal.azure.com)，然後選取上**的所有虛擬機器**在您的映像處理站實驗室，以確認一切運作正常。 您應該會看到在實驗室中建立三部虛擬機器。

![在實驗室中的 Vm](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>後續步驟 
完成設定 Azure DevTest Labs 為基礎的映像處理站的第一個步驟。 在序列中下一步 文章中，您取得這些 Vm 一般化，並且儲存到自訂映像。 然後，您會讓它們散發到所有其他實驗室。 請參閱系列的下一篇文章：[儲存自訂映像，並散發的多個實驗室](image-factory-save-distribute-custom-images.md)。
