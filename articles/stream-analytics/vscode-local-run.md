---
title: 使用 Visual Studio Code 測試 Azure 串流分析查詢
description: 本文說明如何使用適用于 Visual Studio Code 的 Azure 串流分析工具在本機測試查詢。
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 2791fb923f193815d718dbd2269cbcd11583a4ea
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2019
ms.locfileid: "72924980"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio-code"></a>使用 Visual Studio Code 在本機測試串流分析查詢

您可以使用適用于 Visual Studio Code 的 Azure 串流分析工具，以範例資料在本機測試您的串流分析作業。

使用本[快速入門](quick-create-vs-code.md)來瞭解如何使用 Visual Studio Code 建立串流分析作業。

## <a name="prerequisites"></a>必要條件
* 安裝[.net CORE SDK](https://dotnet.microsoft.com/download)。
* 重新啟動 Visual Studio Code。
 
## <a name="run-queries-locally"></a>在本機執行查詢

您可以使用適用于 Visual Studio Code 的 Azure 串流分析擴充功能，透過範例資料在本機測試您的串流分析作業。

1. 建立串流分析作業之後，請使用**Ctrl + Shift + P**來開啟命令選擇區。 然後輸入並選取 [ **ASA：新增輸入**]。

    ![在 Visual Studio 程式碼中新增 ASA 輸入](./media/vscode-local-run/add-input.png)

2. 選取 [**本機輸入**]。

    ![在 Visual Studio 程式碼中新增 ASA 本機輸入](./media/vscode-local-run/add-local-input.png)

3. 選取 [ **+ 新增本機輸入**]。

    ![在 Visual Studio 程式碼中新增 ASA 本機輸入](./media/vscode-local-run/add-new-local-input.png)

4. 輸入您在查詢中使用的相同輸入別名。

    ![新增 ASA 本機輸入別名](./media/vscode-local-run/new-local-input-alias.png)

5. 在**LocalInput_DefaultLocalStream**檔案中，輸入您的本機資料檔案所在的檔案路徑。

    ![在 Visual Studio 中輸入本機檔案路徑](./media/vscode-local-run/local-file-path.png)

6. 返回您的查詢編輯器，然後選取 [在**本機執行**]。

    ![選取 [在本機執行] 查詢編輯器](./media/vscode-local-run/run-locally.png)

## <a name="next-steps"></a>後續步驟

* [在 Visual Studio Code （預覽）中建立 Azure 串流分析雲端作業](quick-create-vs-code.md)

* [使用 Visual Studio Code 探索 Azure 串流分析作業（預覽）](vscode-explore-jobs.md)
