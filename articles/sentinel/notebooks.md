---
title: 使用 Azure Sentinel 預覽版中的 notebook 追捕功能 |Microsoft Docs
description: 本文說明如何使用 notebook 的 Azure Sentinel 追捕功能。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: ffe3ae5b6aa26d154928a74e51864a0574b82c68
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65228630"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>使用 Jupyter Notebook 來搜尋有安全性威脅

> [!IMPORTANT]
> Azure Sentinel 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Sentinel 的基礎是資料存放區;它結合了高效能查詢、 動態的結構描述，以及大量的資料磁碟區的縮放比例。 Azure Sentinel 入口網站和所有 Azure Sentinel 工具使用通用的 API 來存取此資料存放區。 相同的 API 也是可供外部工具這類[Jupyter](https://jupyter.org/) notebook 和 Python。 雖然許多常見的工作可以在入口網站中實行，Jupyter 會擴充您可以如何處理此資料的範圍。 它會將完整的可程式性結合收集了大量的機器學習服務、 視覺化和資料分析的程式庫。 這些屬性可以讓 Jupyter 安全性調查和追捕的強大工具。

![範例 notebook](./media/notebooks/sentinel-nb-mapandtimeline.png)

我們已整合 Jupyter 體驗 Azure Sentinel 入口網站中，讓您輕鬆地建立和執行 notebook 來分析您的資料。 *Kqlmagic*程式庫提供可讓您從 Azure Sentinel 取得查詢，並直接在 notebook 中執行它們不可或缺的利器。 查詢會使用[Kusto 查詢語言](https://kusto.azurewebsites.net/docs/query/index.html)。 數個 notebook，開發是若干 Microsoft 的安全性分析師隨附 Azure Sentinel。 這些 notebook 的一些特定的案例會建立並可用來當做-是。 其他人會作為範例來說明技術和功能，您可以複製或調整使用自己的 notebook。 也可能會從 Azure Sentinel 社群 GitHub 匯入其他 notebook。

使用整合式的 Jupyter 體驗[Azure Notebooks](https://notebooks.azure.com/)進行儲存、 共用及執行 notebook。 您也可以執行這些 notebook，在本機 （如果您的 Python 環境和 Jupyter 上有您的電腦） 或其他 JupterHub 環境，例如 Azure Databricks 中。

Notebook 會有兩個元件：

- 瀏覽器為基礎的介面讓您輸入並執行查詢和程式碼，並顯示執行結果的位置。
- *核心*，負責剖析和執行程式碼本身。 

Azure Notebooks，此核心會在 Azure 上執行*免費的雲端運算和儲存體*預設。 如果您的 notebook 包含複雜的機器學習服務模型，或視覺效果就應該考慮使用功能更強大的專用計算資源這類[資料科學虛擬機器](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)(DSVM)。 除非您選擇要共用您的帳戶中的 notebook 會保留私用的。

Azure Sentinel notebook 會使用許多熱門的 Python 程式庫，例如 pandas、 matplotlib、 bokeh 和其他項目。 有了大量的其他 Python 封裝可讓您可從中選擇，例如涵蓋範圍包括：

- 視覺效果和圖形
- 資料處理和分析
- 統計資料和數值運算
- 機器學習和深度學習

我們也發行了名為封裝中的一些開放原始碼 Jupyter 安全性工具[msticpy](https://github.com/Microsoft/msticpy/)。 在許多包含 notebook 中使用此套件。 Msticpy 工具的設計是專為協助建立追捕的 notebook，並調查，我們正積極新功能和增強功能。

初始的 notebook 包括：

- **引導式調查-處理序警示**:可讓您快速地分級警示，藉由分析受影響的主機上的活動。
- **引導式追捕-Windows 主機的總管**:可讓您探索主機上的帳戶活動、 程序執行、 網路活動和其他事件。  
- **引導式追捕-Office365 探索**:可疑的 Office 365 活動，在多個 O365 資料集中搜尋。

[Azure Sentinel 社群 GitHub 存放庫](https://github.com/Azure/Azure-Sentinel)任何未來的 Azure Sentinel notebook 的位置建立由 Microsoft 或從社群所貢獻。

## <a name="run-a-notebook"></a>執行 notebook

在下列範例中，我們建立 Azure Notebooks 專案從 Azure Sentinel 入口網站中，填入 notebook 的專案。 之前使用這些 notebook，它是個不錯的主意，建立一份 notebook，並作用於複本。 在複本上的工作可讓您的 notebook 的未來版本中安全地更新，而不覆寫任何資料。

1. 在 Azure Sentinel 入口網站中，按一下**Notebook**導覽功能表中。 若要建立新的 Azure Notebooks 專案時，按一下**複製 Azure Sentinel Notebooks**或開啟現有 notebook 專案按一下**移至您的筆記本**。
  
   ![選取 notebook](./media/notebooks/sentinel-az-notebooks-home.png)

2. 如果您選擇**複製 Azure Sentinel Notebook**在上一個步驟中，會出現下列對話方塊。 按一下 **匯入**複製到您的 Azure Notebooks 專案的 GitHub 存放庫。 如果您沒有現有的 Azure Notebooks 帳戶，您將建立一個，並登入提示。

   ![匯入 notebook](./media/notebooks/sentinel-nb-signin-and-clone.png)

3. 建立新的專案時，您需要將專案命名為-用於一個新的預設名稱或型別。 不會檢查**複製品以遞迴方式**選項-這個選項是指連結的 GitHub 存放庫。 按一下**匯入**開始複製的 GitHub 內容，這可能需要幾分鐘才能完成。

   ![匯入 notebook](./media/notebooks/sentinel-create-nb-project.png)

4. 開啟**Notebook**資料夾，以查看 notebook。 每個筆記本會引導您執行搜尋或調查的步驟。 從 Notebook 本身，或透過簡單的設定程序，可以安裝程式庫和其他 notebook 所需的相依性。 在前述步驟時，會自動佈建您的 notebook 專案回到 Azure Sentinel 訂用帳戶會繫結的組態。 您的筆記本已準備好執行您的 Azure Sentinel Log Analytics 工作區。

   ![匯入存放庫](./media/notebooks/sentinel-open-notebook1.png)

5. 開啟 Notebook。 預設會選取可用的計算執行 notebook （反白顯示）。 如果您已設定使用 （見上文） 的 DSVM，請選取 DSVM，並驗證，才能開啟第一個 notebook。 按一下以開啟 notebook。

   ![選取 notebook](./media/notebooks/sentinel-open-notebook2.png)

6. 選取的 Python 版本。 當您第一次開啟 notebook 時，可能會提示您選取的核心版本。 否則，請選取核心以使用，如下所示。 應該選取的核心 Python 3.6 或更新版本。 (右上方的 [notebook] 視窗)。

   ![選取 notebook](./media/notebooks/sentinel-select-kernel.png)

要查詢 Azure Sentinel 中的資料的快速簡介，看看[GetStarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb)主要的 [Notebook] 資料夾中的 notebook。 其他範例筆記本可在**Notebook 範例**子資料夾。 範例筆記本已儲存的資料，以便更輕鬆地看到想要的輸出 (我們建議您檢視中加以[nbviewer](https://nbviewer.jupyter.org/))。 **Rolegroup**資料夾包含 notebook 的描述，例如： 您的設定預設的 Python 版本，請設定 DSVM，建立 Azure Sentinel 設定為書籤從 notebook 和其他主題。

這些 notebook 是作為這兩個有用的工具和圖例和程式碼範例，您可以使用在開發自己的 notebook。

是否建議，要求的功能，提供 Notebook、 錯誤報告或改進和新增至現有的 notebook，我們歡迎意見反應。 移至[Azure Sentinel 社群 GitHub](https://github.com/Azure/Azure-Sentinel)建立問題或 「 分叉 」，並上傳投稿文章。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何開始在 Azure Sentinel 使用 Jupyter notebook。 若要深入了解 Azure Sentinel，請參閱下列文章：

- [主動積極的威脅](hunting.md)
- [使用書籤來儲存時追捕的有趣資訊](bookmarks.md)