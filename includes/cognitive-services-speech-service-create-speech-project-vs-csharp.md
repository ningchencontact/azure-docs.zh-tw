---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2018
ms.author: wolfma
ms.openlocfilehash: 7a028f65467c517be59be6f0c6d2c45ef9a7685e
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285789"
---
1. 啟動 Visual Studio 2017。

1. 從 Visual Studio 的功能表列，選取 [工具] > [取得工具]，並確定 **.Net 桌面開發**是可用的工作負載。 如果尚未安裝工作負載，請勾選核取方塊，然後按一下 [修改] 以開始安裝。 下載及安裝可能需要幾分鐘的時間。

   如果 **.NET 桌面開發** 旁邊的核取方塊已選取，您現在可以關閉對話方塊。

   ![啟用 .NET 桌面開發](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. 接下來，我們來建立專案。 從功能表列，選取 [檔案] > [新增] > [專案]。 當對話方塊出現時，從左側面板展開 [已安裝] > [Visual C#] > [Windows 桌面] 區段，然後選取 [主控台應用程式 (.NET Framework)\]。 將此專案命名為 helloworld。

    ![建立 Visual C# 主控台應用程式 (.NET Framework)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "建立 Visual C# 主控台應用程式 (.NET Framework)")

1. 現在，專案設定好之後，我們必須安裝[語音 SDK NuGet 套件](https://aka.ms/csspeech/nuget)，並在我們的程式碼中參考此套件。 找出 [方案總管]，並以滑鼠右鍵按一下 helloworld。 在功能表上，選取 [管理 NuGet 套件...]。

   ![以滑鼠右鍵按一下解決方案的管理 NuGet 封裝](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "管理解決方案的 NuGet 封裝")

1. 在 NuGet 套件管理員的右上角，找到 [套件來源] 下拉式清單，並確定 [nuget.org] 已選取。 然後，選取 [瀏覽] 並搜尋 `Microsoft.CognitiveServices.Speech` 套件，然後安裝最新的穩定版本。

   ![安裝 Microsoft.CognitiveServices.Speech NuGet 套件](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png "安裝 NuGet 套件")

1. 接受所有合約和授權，即可開始安裝。

   ![接受授權](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "接受授權")

    安裝套件之後，[套件管理員] 主控台上會出現確認訊息。

1. 下一個步驟是根據您用來建置並執行主控台應用程式的電腦架構，建立與之相符的平台組態。 從功能表列選取 [建置] > [組態管理員...]。

    ![啟動組態管理員](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "啟動組態管理員")

1. 在 [組態管理員] 對話方塊方塊中，找到 [使用中的方案平台] 下拉式清單，然後選取 [新增]。

    ![在 [組態管理員] 視窗下方新增平台](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "在 [組態管理員] 視窗下方新增平台")

1. 如果您執行 64 位元的 Windows，出現 [輸入或選取新平台] 的提示時，請選取 `x64`。 如果您執行 32 位元的 Windows，請選取 `x86`。 完成時，請按一下 [確定] 。

    ![在 64 位元 Windows 上新增名為「x64」的平台](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "新增 x64 平台")
