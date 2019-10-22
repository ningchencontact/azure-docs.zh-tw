---
title: 使用適用於 Ethereum 的 Azure 區塊鏈開發套件 - Azure 區塊鏈服務
description: 在 Visual Studio Code 中使用適用於 Ethereum 的 Azure 區塊鏈開發套件擴充功能連線到 Azure 區塊鏈服務聯盟網路
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: chrisseg
manager: femila
ms.openlocfilehash: 6364e887c699219d80974d592a8ff7c77cca2621
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329307"
---
# <a name="quickstart-use-visual-studio-code-to-connect-to-an-azure-blockchain-service-consortium-network"></a>快速入門：使用 Visual Studio Code 連線至 Azure 區塊鏈服務聯盟網路

在本快速入門中，您會安裝及使用適用於 Ethereum 的 Azure 區塊鏈開發套件 Visual Studio Code 擴充功能連結至 Azure 區塊鏈服務上的聯盟。 Azure 區塊鏈開發套件簡化了您在 Ethereum 總帳上建立、連線、建置及部署智慧型合約的方式。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

* 完成[快速入門：使用 Azure 入口網站建立區塊鏈成員](create-member.md)或[快速入門：使用 Azure CLI 建立 Azure 區塊鏈服務的區塊鏈成員](create-member-cli.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [適用於 Ethereum 的 Azure 區塊鏈服務開發套件擴充功能](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js](https://nodejs.org)
* [Git](https://git-scm.com)
* [Python](https://www.python.org/downloads/release/python-2715/)。 將 python.exe 新增至您的路徑。 Azure 區塊鏈服務開發套件需要您的路徑中有 Python。
* [Truffle](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI](https://github.com/trufflesuite/ganache-cli)

### <a name="verify-azure-blockchain-development-kit-environment"></a>確認 Azure 區塊鏈服務開發套件環境

Azure 區塊鏈服務開發套件會確認您是否已符合開發環境的必要條件。 若要確認開發環境：

從 VS Code 命令選擇區選擇 [Azure 區塊鏈服務:  顯示歡迎頁面]。

Azure 區塊鏈服務開發套件會執行驗證指令碼，其大約需要一分鐘的時間才能完成。 您可以藉由選取 [終端機] > [新增終端機]  來檢視輸出。 在終端機的功能表列中，於下拉式清單內選取 [輸出]  索引標籤和 [Azure 區塊鏈服務]  。 驗證成功時會如下圖所示：

![有效的開發環境](./media/connect-vscode/valid-environment.png)

 如果您缺少必要工具，名為 [Azure 區塊鏈服務開發套件 - 預覽]  的新索引標籤會列出要安裝的必要應用程式以及可用來下載工具的連結。

![開發套件所需的應用程式](./media/connect-vscode/required-apps.png)

繼續進行本快速入門之前，請先安裝任何缺少的必要條件。

## <a name="connect-to-consortium-member"></a>連線至聯盟成員

您可以使用 Azure 區塊鏈服務開發套件 VS Code 擴充功能來連線至聯盟成員。 在連線至聯盟後，您就可以對 Azure 區塊鏈服務聯盟成員編譯、建置和部署智慧型合約。

如果您無法存取 Azure 區塊鏈服務聯盟成員，請完成必要的[快速入門：使用 Azure 入口網站建立區塊鏈成員](create-member.md)或[快速入門：使用 Azure CLI 建立 Azure 區塊鏈服務的區塊鏈成員](create-member-cli.md)。

1. 在 Visual Studio Code (VS Code) 總管的窗格中，展開 [Azure 區塊鏈服務]  擴充功能。
1. 選取 [連線至聯盟]  。

   ![連線至聯盟](./media/connect-vscode/connect-consortium.png)

    如果系統提示您進行 Azure 驗證，請遵循提示以使用瀏覽器進行驗證。
1. 在命令選擇區的下拉式清單中，選擇 [連線至 Azure 區塊鏈服務聯盟]  。
1. 選擇與 Azure 區塊鏈服務聯盟成員相關聯的訂用帳戶和資源群組。
1. 從清單中選擇聯盟。

Visual Studio 總管的提要欄位中會列出聯盟和區塊鏈成員。

![總管中顯示的聯盟](./media/connect-vscode/consortium-node.png)

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已使用適用於 Ethereum 的 Azure 區塊鏈開發套件 Visual Studio Code 擴充功能連結至 Azure 區塊鏈服務上的聯盟。 請嘗試進行下一個教學課程，使用適用於 Ethereum 和 Truffle 的 Azure 區塊鏈開發套件，透過交易建立、建置、部署及執行智慧型合約函式。

> [!div class="nextstepaction"]
> [使用 Visual Studio Code 建立、建置及部署智慧型合約](send-transaction.md)