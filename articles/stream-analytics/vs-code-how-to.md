---
title: 探索 Azure Stream Analytics 與 Visual Studio Code （預覽）
description: 這篇文章會示範如何將 Azure Stream Analytics 作業匯出到本機專案、 列出工作和檢視作業的實體，並設定您的 Stream Analytics 作業的 CI/CD 管線。
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/06/2019
ms.topic: conceptual
ms.openlocfilehash: 640a81fcb94194e2e907e9339f016bd35e279fdd
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079206"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>探索 Azure Stream Analytics 與 Visual Studio Code （預覽）

Azure Stream Analytics for Visual Studio Code 擴充功能讓開發人員的輕量型體驗來管理他們的 Stream Analytics 工作。 使用 Azure Stream Analytics 擴充功能中，您可以：

- [建立](quick-create-vs-code.md)、 啟動及停止作業
- 將現有作業匯出到本機專案
- 在本機執行查詢
- 設定 CI/CD 管線

## <a name="export-a-job-to-a-local-project"></a>匯出至本機專案的工作

若要將工作匯出至本機專案中，找出您想要在匯出作業**Stream Analytics 總管**Visual Studio code 中。 然後選取您專案的資料夾。 專案會匯出到您選取時，資料夾，您可以繼續管理工作，從 Visual Studio Code。 如需有關管理 Stream Analytics 工作使用 Visual Studio Code 的詳細資訊，請參閱 Visual Studio Code[快速入門](quick-create-vs-code.md)。

![在 Visual Studio Code 中的匯出 ASA 作業](./media/vs-code-how-to/export-job.png)

## <a name="run-queries-locally"></a>在本機執行查詢

您可以使用 Visual Studio Code 的 Azure Stream Analytics 擴充功能來測試您的 Stream Analytics 作業，在本機使用範例資料。

1. 一旦您已建立您的 Stream Analytics 作業，使用**Ctrl + Shift + P**若要開啟 命令棧板。 然後輸入並選取**ASA:新增輸入**。

    ![在 Visual Studio 程式碼中新增 ASA 輸入](./media/vs-code-how-to/add-input.png)

2. 選取 **本機輸入**。

    ![加入 Visual Studio code 中的 ASA 本機輸入](./media/vs-code-how-to/add-local-input.png)

3. 選取  **+ 新增本機輸入**。

    ![加入新 ASA 本機 Visual Studio code 中輸入](./media/vs-code-how-to/add-new-local-input.png)

4. 輸入您在查詢中使用的相同輸入的別名。

    ![加入新的 ASA 本機輸入的別名](./media/vs-code-how-to/new-local-input-alias.png)

5. 在  **LocalInput_DefaultLocalStream.json**檔案中，輸入您的本機資料檔案所在的檔案路徑。

    ![在 Visual Studio 中，輸入本機檔案路徑](./media/vs-code-how-to/local-file-path.png)

6. 返回 查詢編輯器中，並選取**本機執行**。

    ![選取 執行本機查詢編輯器中](./media/vs-code-how-to/run-locally.png)

## <a name="set-up-a-cicd-pipeline"></a>設定 CI/CD 管線

您可以啟用持續整合和部署使用 Azure Stream Analytics 作業**asa cicd 工具**NPM 套件。 NPM 套件會提供工具，讓您的 Stream Analytics Visual Studio 程式碼專案自動部署。 它可以用在 Windows、 macOS 和 Linux 上而不需要安裝 Visual Studio Code。

一旦[下載套件](https://usqldownload.blob.core.windows.net/ext/asa/asa-cicd-0.0.1-preview-beta.tar)，使用下列命令輸出的 Azure Resource Manager 範本。 如果**outputPath**未指定，範本將會置於**部署**下的專案資料夾**bin**資料夾。

```powershell
asa-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```

## <a name="next-steps"></a>後續步驟

* [在 Visual Studio Code （預覽） 建立 Azure Stream Analytics 雲端作業](quick-create-vs-code.md)