---
title: 在 Azure Sentinel 中使用筆記本的搜尋功能 |Microsoft Docs
description: 本文說明如何使用具有 Azure Sentinel 搜尋功能的筆記本。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 5b90ecc1db686b698668b07bd839304b425445ca
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240533"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>使用 Jupyter 筆記本來尋找安全性威脅

Azure Sentinel 的基礎是資料存放區;它結合高效能查詢、動態架構，以及大規模的資料磁片區規模。 Azure Sentinel 入口網站和所有 Azure Sentinel 工具都會使用通用 API 來存取此資料存放區。 相同的 API 也適用于外部工具，例如[Jupyter](https://jupyter.org/)筆記本和 Python。 雖然在入口網站中可以執行許多常見的工作，Jupyter 也擴充了您可以使用此資料來執行的作業範圍。 它結合了完整的可程式性與大量的程式庫集合，供機器學習、視覺化和資料分析之用。 這些屬性讓 Jupyter 成為安全性調查和搜尋的強大工具。

![範例筆記本](./media/notebooks/sentinel-notebooks-map.png)

我們已將 Jupyter 體驗整合到 Azure Sentinel 入口網站，讓您可以輕鬆地建立及執行筆記本來分析您的資料。 *Kqlmagic*程式庫提供的粘附功能可讓您從 Azure Sentinel 進行查詢，並直接在筆記本內執行。 查詢會使用[Kusto 查詢語言](https://kusto.azurewebsites.net/docs/query/index.html)。 某些由 Microsoft 的安全性分析師所開發的多個筆記本會與 Azure Sentinel 一起封裝。 其中一些筆記本是針對特定案例所建立，並可依自己的情況使用。 其他則是做為範例，說明您可以複製或調整以在自己的筆記本中使用的技術和功能。 也可以從 Azure Sentinel 的社區 GitHub 匯入其他筆記本。

整合式 Jupyter 體驗會使用[Azure Notebooks](https://notebooks.azure.com/)來儲存、共用和執行筆記本。 您也可以在本機執行這些筆記本（如果您的電腦上有 Python 環境和 Jupyter）或其他 JupterHub 環境（例如 Azure Databricks）。

筆記本有兩個元件：

- 以瀏覽器為基礎的介面，您可以在其中輸入並執行查詢和程式碼，以及顯示執行結果的位置。
- 負責剖析和執行程式碼本身的*核心*。 

在 Azure Notebooks 中，此核心預設會在 Azure*免費雲端計算和儲存體*上執行。 如果您的筆記本包含複雜的機器學習模型或視覺效果，您應該考慮使用更強大、專用的計算資源，例如[資料科學虛擬機器](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)（DSVM）。 您帳戶中的筆記本會保持私密，除非您選擇共用。

Azure Sentinel 的筆記本會使用許多熱門的 Python 程式庫，例如 pandas、matplotlib、bokeh 和其他。 有很多其他 Python 套件可供您選擇，涵蓋的範圍如下：

- 視覺效果和圖形
- 資料處理和分析
- 統計資料和數值計算
- 機器學習和深度學習

我們也在名為[msticpy](https://github.com/Microsoft/msticpy/)的套件中發行了一些開放原始碼的 Jupyter 安全性工具。 此套件用於許多內含的筆記本。 Msticpy 工具的設計目的是為了協助建立用於搜尋和調查的筆記本，而且我們正積極處理新的功能和改進。

初始筆記本包括：

- **引導式調查-處理警示**：可讓您藉由分析受影響主機上的活動，快速分級警示。
- **引導式搜尋-Windows host explorer**：可讓您探索帳戶活動、進程執行、網路活動和主機上的其他事件。  
- **引導式搜尋-Office365-探索**：在多個 O365 資料集中尋找可疑的 Office 365 活動。

[Azure Sentinel 的社區 GitHub 存放庫](https://github.com/Azure/Azure-Sentinel)是 Microsoft 所建立或從社區提供的任何未來 Azure Sentinel 筆記本的位置。

## <a name="run-a-notebook"></a>執行筆記本

在下列範例中，我們會從 Azure Sentinel 入口網站建立 Azure Notebooks 專案，並使用筆記本填入專案。 使用這些筆記本之前，最好先製作筆記本複本，並在複本上工作。 使用複本可讓您安全地更新至未來版本的筆記本，而不會覆寫任何資料。

1. 在 Azure Sentinel 入口網站中，按一下導覽功能表中的 [**筆記本**]。 若要建立新的 Azure Notebooks 專案，請按一下 [**複製 Azure Sentinel 筆記本**] 或 [若要開啟現有的筆記本專案]，按一下 [**移至您的筆記本**]。
  
   ![選取筆記本](./media/notebooks/sentinel-azure-notebooks-home.png)

2. 如果您在上一個步驟中選擇 [**複製 Azure Sentinel 筆記本**]，將會出現下列對話方塊。 按一下 [匯**入**]，將 GitHub 存放庫複製到您的 Azure Notebooks 專案。 如果您沒有現有的 Azure Notebooks 帳戶，系統會提示您建立一個並登入。

   ![匯入筆記本](./media/notebooks/sentinel-notebooks-clone.png)

3. 建立新專案時，您必須為專案命名-使用預設名稱，或輸入新的名稱。 不要**以遞迴方式檢查複製**選項-此選項參考連結的 GitHub 存放庫。 按一下 [匯**入**] 會開始複製 GitHub 內容，這可能需要幾分鐘的時間才能完成。

   ![匯入筆記本](./media/notebooks/sentinel-create-project.png)

4. 開啟 [**筆記本**] 資料夾以查看筆記本。 每個筆記本都會引導您完成進行搜尋或調查的步驟。 筆記本本身所需的程式庫和其他相依性可以透過筆記本本身或簡單的設定程式來安裝。 將您的筆記本專案系結回到 Azure Sentinel 訂用帳戶的設定，會在先前的步驟中自動布建。 您的筆記本已準備好針對您的 Azure Sentinel Log Analytics 工作區執行。

   ![匯入存放庫](./media/notebooks/sentinel-open-notebook1.png)

5. 開啟筆記本。 預設會選取 [免費計算] 來執行筆記本（反白顯示）。 如果您已設定要使用的 DSVM （請參閱上述），請在開啟第一個筆記本之前，先選取 DSVM 並進行驗證。 按一下筆記本以開啟它。

   ![選取筆記本](./media/notebooks/sentinel-open-notebook2.png)

6. 選取 Python 版本。 當您第一次開啟筆記本時，它可能會提示您選取核心版本。 如果沒有，請選取要使用的核心，如下所示。 Python 3.6 或更新版本應該是選取的核心（位於 [筆記本] 視窗的右上方）。

   ![選取筆記本](./media/notebooks/sentinel-select-kernel.png)

如需在 Azure Sentinel 中查詢資料的快速簡介，請查看主要 [筆記本] 資料夾中的[GetStarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb)筆記本。 您可以在**範例-筆記本**子資料夾中找到其他範例筆記本。 範例筆記本已與資料一起儲存，讓您更容易看到預期的輸出（我們建議您在[nbviewer](https://nbviewer.jupyter.org/)中加以查看）。 **做法**資料夾包含描述的筆記本，例如：設定您的預設 Python 版本、設定 DSVM、從筆記本建立 Azure Sentinel 的書簽，以及其他主題。

這些筆記本主要是做為有用的工具，以及您可以在開發自己的筆記本時使用的圖例和程式碼範例。

我們歡迎您提供意見反應、建議、功能要求、貢獻的筆記本、錯誤報表，或是現有筆記本的改進專案和新增專案。 前往[Azure Sentinel 社區 GitHub](https://github.com/Azure/Azure-Sentinel)以建立問題或分支，並上傳投稿。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何開始在 Azure Sentinel 中使用 Jupyter 筆記本。 若要深入了解 Azure Sentinel，請參閱下列文章：

- [主動尋找威脅](hunting.md)
- [搜尋時使用書簽來儲存有趣的資訊](bookmarks.md)
