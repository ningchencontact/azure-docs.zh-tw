---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: c8ef95f6a01b64b722544d9397d928387a69d7c7
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56458428"
---
1. 啟動 Visual Studio 2017。

1. 確定可以使用**通用 Windows 平台開發**工作負載。 從 Visual Studio 功能表列選擇 [工具] > [取得工具和功能]，以開啟 Visual Studio 安裝程式。 如果已啟用此工作負載，請關閉對話方塊。

    ![Visual Studio 安裝程式的螢幕擷取畫面，其中 [工作負載] 索引標籤已醒目提示](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

    否則，請選取 [.NET 跨平台開發] 旁邊的方塊，然後選取對話方塊右下角的 [修改]。 安裝新功能需要一些時間。

1. 建立空的 Visual C# 通用 Windows 應用程式。 首先，從功能表中選擇 [檔案] > [新增] > [專案]。 在 [新增專案] 對話方塊中，展開左窗格中的 [已安裝] > [Visual C#] > [Windows Universal]。 然後，選取 [空白應用程式 (通用 Windows)]。 針對專案名稱，請輸入 *helloworld*。

    ![[新增專案] 對話方塊的螢幕擷取畫面](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. 要使用 Speed SDK，您必須為 Windows 10 Fall Creators Update 或更新版本建置您的應用程式。 在彈出的 [新增通用 Windows 平台專案] 視窗中，選擇 [Windows 10 Fall Creators Update (10.0；組建 16299)] 作為 [最低版本]。 在 [目標版本] 方塊中選取此版本或任何更新版本，然後按一下 [確定]。

    ![新增通用 Windows 平台專案視窗的螢幕擷取畫面](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. 如果您執行 64 位元的 Windows，您可以使用 Visual Studio 工具列中的下拉式功能表，將您的建置平台切換為 `x64`。 (64 位元 Windows 可執行 32 位元應用程式，因此您可以依需求將其保留為 `x86`。)

   ![此螢幕擷取畫面顯示將 x64 醒目提示的 Visual Studio 工具列](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > 語音 SDK 僅支援 Intel 相容處理器。 目前不支援 ARM。

1. 安裝並參考 [Speech SDK NuGet 封裝](https://aka.ms/csspeech/nuget)。 在 [方案總管] 中，以滑鼠右鍵按一下解決方案，然後選取 [管理解決方案的 NuGet 套件]。

    ![方案總管的螢幕擷取畫面，其中 [管理解決方案的 NuGet 套件] 選項已醒目提示](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. 在右上角的 [套件來源] 欄位中，選取 [nuget.org]。搜尋 `Microsoft.CognitiveServices.Speech` 套件，然後將它安裝到 **helloworld** 專案。

    ![管理解決方案套件對話方塊的螢幕擷取畫面](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png "安裝 NuGet 套件")

1. 接受顯示的授權，才會開始安裝 NuGet 套件。

    ![接受授權對話方塊的螢幕擷取畫面](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-06-nuget-license.png "接受授權")

1. 下列輸出行會出現在套件管理器主控台中。

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 1.3.0' to helloworld
   ```

1. 由於應用程式使用麥克風進行語音輸入，請將 [麥克風] 功能新增至專案。 在 [方案總管] 中，按兩下 [Package.appxmanifest] 以編輯您的應用程式資訊清單。 接著，切換至 [功能] 索引標籤，選取 [麥克風] 功能的方塊，然後儲存您的變更。

   ![此螢幕擷取畫面顯示將功能和麥克風醒目提示的 Visual Studio 應用程式資訊清單](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)
