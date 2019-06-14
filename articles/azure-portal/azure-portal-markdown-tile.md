---
title: 在 Azure 儀表板上使用自訂 Markdown 磚
description: 了解如何將 Markdown 磚新增至 Azure 儀表板以顯示靜態內容
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 01/25/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: ec8cbddda4137656a53fd4968c451cd413959274
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60551553"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>在 Azure 儀表板上使用 Markdown 磚以顯示自訂內容

您可以將 Markdown 磚新增至 Azure 儀表板以顯示自訂的靜態內容。 例如，您可以使用 Markdown 檔案來顯示基本指示、影像或一組超連結。

## <a name="add-a-markdown-tile-to-your-dashboard"></a>將 Markdown 磚新增至您的儀表板

1. 從 Azure 入口網站資訊看板選取 [儀表板]  。 如果您已建立任何自訂儀表板，請在儀表板檢視中，使用下拉式清單來選取應該出現自訂 Markdown 磚的儀表板。 選取 [編輯] 圖示以開啟 [磚庫]  。

   ![顯示儀表板編輯檢視的螢幕擷取畫面](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

2. 在 [磚庫]  中，尋找名為 **Markdown** 的磚，然後按一下 [新增]  。 磚會新增至儀表板，且 [編輯 Markdown]  窗格隨即開啟。

1. 編輯 [標題]  、[子標題]  及 [內容]  欄位來自訂磚。 在此處顯示的範例中，Markdown 檔案已編輯成顯示自訂技術支援中心資訊。

   ![顯示 Markdown 磚編輯檢視的螢幕擷取畫面](./media/azure-portal-markdown-tile/azure-portal-edit-markdown-tile.png)

4. 選取 [完成]  以關閉 [編輯 Markdown]  窗格。 您的內容將會出現在 Markdown 磚上，透過拖曳右下角的控點即可調整大小。

   ![顯示自訂 Markdown 磚的螢幕擷取畫面](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Markdown 內容功能和限制

您可以在 Markdown 磚上使用純文字、Markdown 語法及 HTML 內容的任意組合。 Azure 入口網站會使用名為 _marked_ 的開放原始碼程式庫，將您的內容轉換成磚上顯示的 HTML。 _marked_ 所產生的 HTML 會由入口網站進行前處理後才顯示。 此步驟有助於確保您的自訂不會影響入口網站的安全性或版面配置。 在進行該前處理的期間，系統會移除可能造成威脅的任何 HTML 部分。 以下是入口網站不允許的內容類型：

* JavaScript – 將會移除 `<script>` 標籤和內嵌的 JavaScript 評估。
* iframe - 將會移除 `<iframe>` 標籤。
* Style - 將會移除 `<style>` 標籤。 HTML 元素上的內嵌樣式屬性並未受到正式支援。 您可能會發現有些內嵌樣式元素對您而言可以運作，但如果它們干擾到入口網站的版面配置，則可能隨時停止運作。 Markdown 磚適用於使用入口網站預設樣式的基本、靜態內容。

## <a name="next-steps"></a>後續步驟

* 若要建立自訂儀表板，請參閱[在 Azure 入口網站中建立和共用儀表板](../azure-portal/azure-portal-dashboards.md)
