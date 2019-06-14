---
title: 測試 Azure Stream Analytics 查詢，在本機使用 Visual Studio Code （預覽）
description: 本文說明如何測試查詢，在本機使用 Azure Stream Analytics Tools for Visual Studio Code。
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: f477a0f99c3eaa82568d8188bfaae03818fb72dc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827946"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio-code"></a>測試 Stream Analytics 查詢，在本機使用 Visual Studio Code

您可以使用 Azure Stream Analytics tools for Visual Studio Code 來測試您的 Stream Analytics 作業，在本機使用範例資料。

使用此[快速入門](quick-create-vs-code.md)以了解如何建立使用 Visual Studio Code 的 Stream Analytics 作業。

## <a name="run-queries-locally"></a>在本機執行查詢

您可以使用 Visual Studio Code 的 Azure Stream Analytics 擴充功能來測試您的 Stream Analytics 作業，在本機使用範例資料。

1. 一旦您已建立您的 Stream Analytics 作業，使用**Ctrl + Shift + P**若要開啟 命令選擇區。 然後輸入並選取**ASA:新增輸入**。

    ![在 Visual Studio 程式碼中新增 ASA 輸入](./media/vscode-local-run/add-input.png)

2. 選取 **本機輸入**。

    ![加入 Visual Studio code 中的 ASA 本機輸入](./media/vscode-local-run/add-local-input.png)

3. 選取  **+ 新增本機輸入**。

    ![加入新 ASA 本機 Visual Studio code 中輸入](./media/vscode-local-run/add-new-local-input.png)

4. 輸入您在查詢中使用的相同輸入的別名。

    ![加入新的 ASA 本機輸入的別名](./media/vscode-local-run/new-local-input-alias.png)

5. 在  **LocalInput_DefaultLocalStream.json**檔案中，輸入您的本機資料檔案所在的檔案路徑。

    ![在 Visual Studio 中，輸入本機檔案路徑](./media/vscode-local-run/local-file-path.png)

6. 返回 查詢編輯器中，並選取**本機執行**。

    ![選取 執行本機查詢編輯器中](./media/vscode-local-run/run-locally.png)

## <a name="next-steps"></a>後續步驟

* [在 Visual Studio Code （預覽） 建立 Azure Stream Analytics 雲端作業](quick-create-vs-code.md)

* [探索 Azure Stream Analytics 作業，使用 Visual Studio Code （預覽）](vscode-explore-jobs.md)
