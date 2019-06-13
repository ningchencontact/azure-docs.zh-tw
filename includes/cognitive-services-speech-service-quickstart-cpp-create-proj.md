---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: 27fcf32a9a268488da318567d3edc55d23bd8967
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66482335"
---
1. 啟動 Visual Studio 2019。

1. 請確定**使用 C++ 進行桌面開發**工作負載可供使用。 從 Visual Studio 功能表列選擇 [工具]   >  [取得工具和功能]  ，以開啟 Visual Studio 安裝程式。 如果已啟用此工作負載，請跳至下一個步驟。

    ![Visual Studio 工作負載索引標籤的螢幕擷取畫面](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

    否則，請核取 [使用 C++ 進行桌面開發]  旁邊的方塊。

1. 請確定 **NuGet 套件管理員**元件可用。 切換至 Visual Studio 安裝程式對話方塊的 [個別元件]  索引標籤，如果 [NuGet 套件管理員]  尚未啟用，則加以選取。

      ![Visual Studio 個別元件索引標籤的螢幕擷取畫面](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-nuget-package-manager.png)

1. 如果您需要啟用 C++ 工作負載或 NuGet，請選取 [修改]  (在對話方塊的右下角)。 安裝新功能需要一些時間。 如果這兩項功能都已經啟用，則關閉對話方塊。

1. 建立新 Visual C++ Windows 桌面的 Windows 主控台應用程式。 首先，從功能表選擇 [檔案]   >  [新增]   >  [專案]  。 在 [新增專案]  對話方塊中，展開左窗格中的 [已安裝]   >  [Visual C++]   >  [Windows 桌面]  。 然後選取 [ Windows 主控台應用程式]  。 針對專案名稱，請輸入 *helloworld*。

    ![[新增專案] 對話方塊的螢幕擷取畫面](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. 如果您執行 64 位元的 Windows，可以使用 Visual Studio 工具列中的下拉式功能表，將您的建置平台切換為 `x64`。 (64 位元版本的 Windows 可以執行 32 位元應用程式，因此您不一定需要進行此動作。)

    ![Visual Studio 工具列的螢幕擷取畫面，其中 x64 選項已醒目提示](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-02-switch-to-x64.png)

1. 在 [方案總管] 中，以滑鼠右鍵按一下解決方案，然後選擇 [管理解決方案的 NuGet 套件]  。

    ![方案總管的螢幕擷取畫面，其中 [管理解決方案的 NuGet 套件] 選項已醒目提示](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. 在右上角的 [套件來源]  欄位中，選取 [nuget.org]  。搜尋 `Microsoft.CognitiveServices.Speech` 套件，然後將它安裝到 **helloworld** 專案。

    ![管理解決方案套件對話方塊的螢幕擷取畫面](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

    > [!NOTE]
    > 認知服務語音 SDK 目前的版本為 `1.5.0`。

1. 接受顯示的授權，才會開始安裝 NuGet 套件。

    ![接受授權對話方塊的螢幕擷取畫面](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-05-nuget-license.png)

安裝套件之後，[套件管理員] 主控台上會出現確認訊息。
