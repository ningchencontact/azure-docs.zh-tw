---
title: 常見問題疑難排解
description: 了解如何針對建立、指派和移除藍圖的問題進行疑難排解。
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/11/2018
ms.topic: troubleshooting
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 42fdd6645a7a0e7cd9a2f0a7bc969e8eee62758c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59789612"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>針對使用 Azure 藍圖發生的錯誤進行疑難排解

您可能會在建立或指派藍圖時遇到錯誤。 此文章說明可能發生的各種錯誤與解決方式。

## <a name="finding-error-details"></a>尋找錯誤詳細資料

許多錯誤都是將藍圖指派給某個範圍而產生的結果。 當指派失敗時，藍圖會提供有關失敗部署的詳細資料。 此資訊會指出問題，以便能夠進行修正，而使下一次部署能成功。

1. 在左側窗格中選取 [所有服務]。 搜尋並選取 [藍圖]。

1. 選取 **指派藍圖**從頁面左邊及使用的搜尋方塊來篩選以尋找失敗的指派的藍圖指派。 您也可以依 [佈建狀態] 欄排序指派表格，以查看所有已群組在一起的失敗指派。

1. 按一下滑鼠左鍵與藍圖_失敗_狀態或以滑鼠右鍵按一下，然後選取**檢視指派詳細資料**。

1. 代表指派失敗的紅色橫幅警告位於藍圖指派頁面的最上方。 按一下該橫幅上的任一處，以取得更多詳細資料。

錯誤通常是由成品 (而非整個藍圖) 造成的。 如果成品建立金鑰保存庫，而且 Azure 原則防止建立金鑰保存庫，則整個指派會失敗。

## <a name="general-errors"></a>一般錯誤

### <a name="policy-violation"></a>案例：違反原則

#### <a name="issue"></a>問題

範本部署因為原則違規而失敗。

#### <a name="cause"></a>原因

原則可能會因為多種因素而與部署產生衝突：

- 要建立的資源受到原則所限制 (通常是 SKU 或位置限制)
- 部署正在設定原則所設定的欄位 (通常會使用標記)

#### <a name="resolution"></a>解決方案

變更藍圖，使其不會與錯誤詳細資料中的原則發生發生衝突。 如果此變更不可行，替代選項就是變更原則指派的範圍，使藍圖不再與原則產生衝突。

### <a name="escape-function-parameter"></a>案例：藍圖參數是函式

#### <a name="issue"></a>問題

為函式的藍圖參數會先經過處理，再傳遞至成品。

#### <a name="cause"></a>原因

將使用如 `[resourceGroup().tags.myTag]` 等函式的藍圖參數傳遞至成品，會造成在成品上設定該函式處理過後的結果，而非動態函式。

#### <a name="resolution"></a>解決方案

若要將函式作為參數傳遞，請使用 `[` 逸出整個字串，讓藍圖參數看起來像 `[[resourceGroup().tags.myTag]`。 逸出字元會導致「藍圖」在處理藍圖時，將值視為字串。 接著「藍圖」會將函式置於成品上，讓它如預期般地為動態函式。 如需詳細資訊，請參閱 <<c0> [ 範本檔案結構語法](../../../azure-resource-manager/resource-group-authoring-templates.md#syntax)。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

- 透過 Azure 專家取得解答[Azure 論壇](https://azure.microsoft.com/support/forums/)。
- 與 [@AzureSupport](https://twitter.com/azuresupport) 連繫－專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，像是解答、支援及專家等。
- 如果需要更多協助，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。