---
title: "在 Azure 虛擬機器上使用 Visual Studio | Microsoft Docs"
description: "在 Azure 虛擬機器上使用 Visual Studio。"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: PhilLee-MSFT
manager: sacalla
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.prod: vs-devops-alm
ms.date: 01/30/2018
ms.author: phillee
keywords: visualstudio
ms.openlocfilehash: 599a890be4d014d22bae899be4cf6e281c4109d4
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/03/2018
---
# <a id="top"> </a> Azure 上的 Visual Studio 映像
使用在預先設定之 Azure 虛擬機器 (VM) 中執行的 Visual Studio，是從零開始到建立已啟動並在執行中之開發環境的最簡單、也最快速的方式。  [Azure Marketplace](https://portal.azure.com/) 中提供具有各種不同 Visual Studio 設定的系統映像。 只要啟動 VM，就大功告成。

不熟悉 Azure 嗎？ [建立 Azure 免費帳戶](https://azure.microsoft.com/free)。

## <a name="what-configurations-and-versions-are-available"></a>有哪些設定和版本可用？
在 Azure Marketplace 中，您可以找到適用於最新主要版本的映象：Visual Studio 2017 和 Visual Studio 2015。  針對每個主要版本，您會看到原始發行 (也稱為 ‘RTW’) 版本和「最新」更新版本。  這每一個不同版本都有 Visual Studio Enterprise 和 Visual Studio Community 版本。  我們至少會每個月更新這些映像一次，以納入最新的 Visual Studio 和 Windows 更新。  雖然映像的名稱會維持不變，但每個映像的描述會納入已安裝的產品版本和映像的「生效」日期。

|               發行版本              |          版本            |     產品版本     |
|:------------------------------------------:|:----------------------------:|:-----------------------:|
| Visual Studio 2017 - 最新版 (15.5 版) |    Enterprise、Community     |      15.5.3 版     |
|         Visual Studio 2017 - RTW           |    Enterprise、Community     |      15.0.7 版     |
|   Visual Studio 2015 - 最新版 (Update 3)   |    Enterprise、Community     |  14.0.25431.01 版  |
|         Visual Studio 2015 - RTW           |              None            | (服務期滿) |

> [!NOTE]
> 根據 Microsoft 服務原則，Visual Studio 2015 的原始發行 (也稱為 ‘RTW’) 版本服務期已滿。  因此，Visual Studio 2015 Update 3 是為 Visual Studio 2015 產品線提供的唯一留存版本。

如需詳細資訊，請參閱 [Visual Studio 服務原則](https://www.visualstudio.com/en-us/productinfo/vs-servicing-vs)。

## <a name="what-features-are-installed"></a>安裝的功能有哪些？
每個映像都包含該 Visual Studio 版本的建議功能集。  一般而言，安裝會包括：

* 所有可用的工作負載 (包括該工作負載的建議選用元件)
* .NET 4.6.2 和 .NET 4.7 SDK、目標套件，以及開發人員工具
* Visual F#
* Visual Studio 的 GitHub 擴充功能
* LINQ to SQL 工具

以下是在組建映像時用來安裝 Visual Studio 的命令列：

```
    vs_enterprise.exe --allWorkloads --includeRecommended --passive ^
       add Microsoft.Net.Component.4.7.SDK ^
       add Microsoft.Net.Component.4.7.TargetingPack ^ 
       add Microsoft.Net.Component.4.6.2.SDK ^
       add Microsoft.Net.Component.4.6.2.TargetingPack ^
       add Microsoft.Net.ComponentGroup.4.7.DeveloperTools ^
       add Microsoft.VisualStudio.Component.FSharp ^
       add Component.GitHub.VisualStudio ^
       add Microsoft.VisualStudio.Component.LinqToSql
```

如果映像並未包含您所需的 Visual Studio 功能，請透過意見反應工具 (位於頁面右上角) 提供該意見反應。

## <a name="what-size-vm-should-i-choose"></a>應該選擇哪個大小的 VM？
佈建新的虛擬機器相當簡單，Azure 提供完整的虛擬機器大小範圍。  就像購買任何硬體一樣，您會想要在效能與成本之間取得平衡。  由於 Visual Studio 是一個強大、多執行緒的應用程式，因此您會希望 VM 的大小能夠包含至少 2 個處理器和 7 GB 的記憶體。  以下是 Visual Studio 映像的建議 VM 大小：

   * Standard_D2_v3
   * Standard_D2s_v3
   * Standard_D4_v3
   * Standard_D4s_v3
   * Standard_D2_v2
   * Standard_D2S_v2
   * Standard_D3_v2
    
如需有關最新機器大小的詳細資訊，請參閱 [Azure 中 Windows 虛擬機器的大小](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes) \(機器翻譯\)。

使用 Azure 時，您無須固守最初的選擇 – 您可以透過調整 VM 大小來重新平衡您的初始選擇。  您可以佈建大小較適當的新 VM，或是調整現有 VM 的大小以配合不同的基礎硬體。  如需詳細資訊，請參閱[調整 Windows VM 大小](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/resize-vm) \(機器翻譯\)。

## <a name="after-i-get-the-vm-running-then-what"></a>讓 VM 執行之後，接著要做什麼？
Visual Studio 依循 Azure 中的「自備授權」模型。  因此，與專用硬體上的安裝類似，前幾個步驟之一，就是為您的 Visual Studio 安裝取得授權。  您可以使用與 Visual Studio 訂用帳戶關聯的 Microsoft 帳戶來登入，以將 Visual Studio 解除鎖定，也可以使用您最初購買時的產品金鑰來將 Visual Studio 解除鎖定。  如需詳細資訊，請參閱[登入 Visual Studio](https://docs.microsoft.com/en-us/visualstudio/ide/signing-in-to-visual-studio)和[如何解除鎖定 Visual Studio](https://docs.microsoft.com/en-us/visualstudio/ide/how-to-unlock-visual-studio)。

## <a name="after-i-build-out-the-dev-vm-how-do-i-save-capture-it-for-future-or-team-use"></a>建置出開發 VM 之後，如何儲存 (擷取) 它以供日後或小組使用？

開發環境的範圍很大，而建置出較複雜環境有一些相關的實際成本。  不過，不論您的環境設定為何，Azure 都可藉由將妥善設定的 VM 儲存/擷取成「基底映像」以供日後使用 (供您自己和/或其他小組成員使用)，讓您輕鬆保留該項投資成果。  然後，在啟動新 VM 時，請從基底映像佈建它，而不是從 Marketplace 映像。

快速總結來說，您將需要執行 sysprep 並關閉執行中的 VM，然後透過 Azure 入口網站的 UI「擷取 (圖 1)」VM 作為映像。  Azure 會將包含該映像的 `.vhd` 檔案儲存在您選擇的儲存體帳戶中。  然後，新的映像就會在您訂用帳戶的資源清單中顯示為映像資源。

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal’s UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(圖 1) 透過 Azure 入口網站的 UI 擷取映像。*</center>

如需詳細資訊，請參閱[將 VM 擷取到映像中](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/capture-image-resource) \(機器翻譯\)。

  **提醒：**請別忘了為 VM 執行 sysprep！  如果您遺漏該步驟，Azure 將無法從映像佈建 VM。

> [!NOTE]
> 您仍然會產生一些儲存映像的成本，但與為您小組中每個需要 VM 的人員從頭重建 VM 的人力成本相比，該增加成本可能微不足道。  例如，建立並儲存一個可供您所有小組成員重複使用的 127 GB 映像，一個月只需幾塊錢。  不過，與每個員工投資在建置及驗證適當設定的開發環境以供其個人使用的時數相比，這些成本就顯得微不足道。

此外，您的開發工作或技術可能需要更大的規模 – 例如各種開發設定和多個機器設定。  您可以使用 Azure DevTest Labs 來建立「配方」，以自動建構您的「最佳映像」，以及管理您小組執行中 VM 的原則。  [使用適用於開發人員的 Azure DevTest Labs](https://docs.microsoft.com/en-us/azure/devtest-lab/devtest-lab-developer-lab) \(機器翻譯\) 是可供您了解 DevTest Labs 的最佳來源。

## <a name="next-steps"></a>後續步驟
既然您已了解預先設定的 Visual Studio 映像，下一步就是建立新的 VM：

* [透過 Azure 入口網站建立 VM](quick-create-portal.md)
* [Windows 虛擬機器概觀](overview.md)
