---
title: 探索 Azure Stream Analytics 作業，使用 Visual Studio Code （預覽）
description: 這篇文章會示範如何將 Azure Stream Analytics 作業匯出到本機專案、 列出工作和檢視 job 實體。
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 8674d478646c8f9be6b32521c6624752ac6df052
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827796"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>探索 Azure Stream Analytics 與 Visual Studio Code （預覽）

Azure Stream Analytics for Visual Studio Code 擴充功能讓開發人員的輕量型體驗來管理他們的 Stream Analytics 工作。 它可以在 Windows、 Mac 和 Linux 上使用。 使用 Azure Stream Analytics 擴充功能中，您可以：

- [建立](quick-create-vs-code.md)、 啟動及停止作業
- 將現有作業匯出到本機專案
- 列出工作和檢視 job 實體

## <a name="export-a-job-to-a-local-project"></a>匯出至本機專案的工作

若要將工作匯出至本機專案中，找出您想要在匯出作業**Stream Analytics 總管**在 Visual Studio Code。 然後選取您專案的資料夾。 專案會匯出到您選取時，資料夾，您可以繼續管理工作，從 Visual Studio Code。 如需有關管理 Stream Analytics 工作使用 Visual Studio Code 的詳細資訊，請參閱 Visual Studio Code[快速入門](quick-create-vs-code.md)。

![在 Visual Studio Code 中的匯出 ASA 作業](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>列出工作和檢視 job 實體

您可以使用作業檢視，從 Visual Studio 與 Azure 串流分析工作互動。


1. 按一下  **Azure**在 Visual Studio 程式碼活動列上的圖示，然後展開**Stream Analytics 節點**。 您的作業應該會出現在 訂用帳戶下。

   ![開啟的 Stream Analytics 總管](./media/vscode-explore-jobs/open-explorer.png)

2. 展開作業節點，您可以開啟並檢視 作業查詢、 設定、 輸入、 輸出和函式。 

3. 以滑鼠右鍵按一下作業節點，然後選擇**入口網站中開啟作業檢視**節點，以在 Azure 入口網站中開啟作業檢視。

   ![在入口網站中開啟作業檢視](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>後續步驟

* [在 Visual Studio Code （預覽） 建立 Azure Stream Analytics 雲端作業](quick-create-vs-code.md)
