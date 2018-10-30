---
title: 監視和管理 Azure Stack VaaS 入口網站中的測試 | Microsoft Docs
description: 監視和管理 Azure Stack VaaS 入口網站中的測試。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 03efe32e7a9a29318e4f97ce5636616fad443284
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2018
ms.locfileid: "49956658"
---
# <a name="monitor-and-manage-tests-in-the-vaas-portal"></a>監視和管理 VaaS 入口網站中的測試

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

在排程對 Azure Stack 解決方案的測試之後，「驗證即服務 (VaaS)」就會開始報告測試執行狀態。 這項資訊會連同重新排程和取消測試等動作顯示在 VaaS 入口網站中。

## <a name="navigate-to-the-workflow-tests-summary-page"></a>瀏覽至工作流程測試摘要頁面

1. 在解決方案儀表板上，選取至少有一個工作流程的現有解決方案。

    ![工作流程圖格](media/tile_all-workflows.png)

1. 在工作流程圖格上選取 [管理]。 下一個頁面會列出已為選取的解決方案建立的工作流程。

1. 選取工作流程名稱以開啟其測試摘要。

## <a name="change-workflow-parameters"></a>變更工作流程參數

每個工作流程類型都可讓您編輯在工作流程建立期間指定的[測試參數](azure-stack-vaas-parameters.md#test-parameters)。

1. 在測試摘要頁面上，選取 [編輯] 按鈕。

1. 根據 [Azure Stack 驗證即服務的工作流程一般參數](azure-stack-vaas-parameters.md)提供新的值。

1. 選取 [提交] 以儲存這些值。

> [!NOTE]
> 在 [測試進行] 工作流程中，您必須完成測試選取並移至檢閱頁面，才可儲存新的參數值。

### <a name="add-tests-test-pass-only"></a>新增測試 (僅限測試進行)

在 [測試進行] 工作流程中，[新增測試] 和 [編輯] 按鈕都可讓您在工作流程中排程新的測試。

> [!TIP]
> 如果您只想要為 [測試進行] 工作流程排程新的測試，而不需要編輯參數，請選取 [新增測試]。

## <a name="managing-test-instances"></a>管理測試執行個體

在非官方執行 (即**測試進行**工作流程) 中，測試摘要頁面會列出對 Azure Stack 解決方案排程的測試。

在官方執行 (即**驗證**工作流程) 中，測試摘要頁面會列要完成 Azure Stack 解決方案驗證所需的測試。 驗證測試會從這個頁面排程。

每個排程的測試執行個體都會顯示下列資訊：

| 欄 | 說明 |
| --- | --- |
| 測試名稱 | 測試的名稱和版本。 |
| 類別 | 測試的用途。 |
| 建立時間 | 排程測試的時間。 |
| 已啟動 | 測試開始執行的時間。 |
| Duration | 測試執行的時間長度。 |
| 狀態 | 測試的狀態或結果。 執行前或進行中的狀態為：`Pending`、`Running`。 終端機狀態為：`Cancelled`、`Failed`、`Aborted`、`Succeeded`。 |
| 代理程式名稱 | 執行測試的代理程式名稱。 |
| 作業總計 | 在測試期間嘗試執行的作業總數。 |
| 通過的作業 | 在測試期間執行成功的作業數目。 |
|  失敗的作業 | 在測試期間執行失敗的作業數目。 |

### <a name="actions"></a>動作

當您在測試執行個體資料表中按一下執行個體的內容功能表 **[...]** 時，每個測試執行個體都會列出可供您執行的動作。

#### <a name="view-information-about-the-test-definition"></a>檢視測試定義的相關資訊

從內容功能表中選取 [檢視資訊]，以檢視關於測試定義的一般資訊。 這項資訊由相同名稱和版本的每個測試執行個體所共用。

| 測試屬性 | 說明 |
| -- | -- |
| 測試名稱 | 測試的名稱。 |
| 測試版本 | 測試的版本。 |
| 發行者 | 測試的發行者。 |
| 類別 |  測試的用途。 |
| 目標服務 | 要測試得 Azure Stack 服務。 |
| 說明 | 測試的描述。 |
| 預估持續時間 (分鐘) | 測試的預期執行階段。 |
| 連結 | 任何與測試或連絡點有關的資訊。 |

#### <a name="view-test-instance-parameters"></a>檢視測試執行個體參數

從內容功能表中選取 [檢視參數]，以檢視在排程時提供給測試執行個體的參數。 密碼之類的敏感性字串並不會顯示。 此動作僅適用於已排程的測試。

此視窗包含所有測試執行個體的下列中繼資料：

| 測試執行個體屬性 | 說明 |
| -- | -- |
| 測試名稱 | 測試的名稱。 |
| 測試版本 | 測試的版本。 |
| 測試執行個體識別碼 | 用來識別特定測試執行個體的 GUID。 |

#### <a name="view-test-instance-operations"></a>檢視測試執行個體作業

從內容功能表中選取 [檢視作業]，以詳盡檢視在測試期間執行的作業所處的狀態。 此動作僅適用於已排程的測試。

![檢視作業](media/manage-test_context-menu-operations.png)

#### <a name="download-logs-for-a-completed-test-instance"></a>下載已完成的測試執行個體產生的記錄

從內容功能表中選取 [下載記錄]，以下載在測試執行期間輸出的記錄檔 (`.zip`)。 此動作僅適用於已完成的測試，也就是狀態為 `Cancelled`、`Failed`、`Aborted` 或 `Succeeded` 的測試。

#### <a name="reschedule-a-test-instance-or-schedule-a-test"></a>重新排程測試執行個體或排程測試

從管理頁面排程測試的程序，取決於用來執行測試的工作流程類型。

##### <a name="test-pass-workflow"></a>測試進行工作流程

在「測試進行」工作流程中，**重新排程**測試執行個體時會重複使用與原始測試執行個體相同的一組參數，並*取代*原始結果，包括其記錄。 在重新排程時，您將必須重新輸入密碼等敏感性字串。

1. 從內容功能表中選取 [重新排程]，以開啟重新排程測試執行個體的提示。

1. 輸入任何適用的參數。

1. 選取 [提交] 以重新排程測試執行個體，並取代現有的執行個體。

##### <a name="validation-workflows"></a>驗證工作流程

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

#### <a name="cancel-a-test-instance"></a>取消測試執行個體

已排程的測試若處於 `Pending` 或 `Running` 狀態，表示可能已被取消。  

1. 從內容功能表中選取 [取消]，以開啟取消測試執行個體的提示。

1. 選取 [提交] 以取消測試執行個體。

## <a name="next-steps"></a>後續步驟

- [對驗證即服務進行疑難排解](azure-stack-vaas-troubleshoot.md)
