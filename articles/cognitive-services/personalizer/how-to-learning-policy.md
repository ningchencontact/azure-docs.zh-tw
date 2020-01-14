---
title: 管理學習原則-個人化工具
description: 本文包含有關個人化工具常見問題的解答。
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 707ba9fe7b73ef74e21321533ba02b11f2bad850
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2020
ms.locfileid: "75843499"
---
# <a name="manage-learning-policy"></a>管理學習原則

學習原則設定會決定模型定型的_超參數_。 學習原則會定義在 `.json` 檔案中。

## <a name="import-a-new-learning-policy"></a>匯入新的學習原則

1. 開啟 [ [Azure 入口網站](https://portal.azure.com)]，然後選取您的個人化工具資源。
1. 在 [**資源管理**] 區段中選取 [**模型和學習設定**]。
1. 針對 [匯**入學習設定**]，選取您使用上面指定的 JSON 格式建立的檔案，然後選取 [**上傳**] 按鈕。

    等候學習原則上傳成功的通知。

## <a name="export-a-learning-policy"></a>匯出學習原則

1. 開啟 [ [Azure 入口網站](https://portal.azure.com)]，然後選取您的個人化工具資源。
1. 在 [**資源管理**] 區段中選取 [**模型和學習設定**]。
1. 針對 [匯**入學習設定**]，選取 [**匯出學習設定**] 按鈕。 這會將 `json` 檔案儲存到您的本機電腦。

## <a name="next-steps"></a>後續步驟

瞭解學習原則[概念](concept-active-learning.md#learning-settings)

[了解區域可用性](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)