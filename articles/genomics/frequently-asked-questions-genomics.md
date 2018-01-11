---
title: "Microsoft Genomics： 常見問題 |Microsoft 文件"
titleSuffix: Azure
description: "常見的問題客戶的答案詢問有關 Microsoft Genomics。"
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: article
ms.date: 12/07/2017
ms.openlocfilehash: 2077eeb5177b07c458476ae900f81b72e35f0dc3
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2017
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics： 常見的問題

本文列出 Microsoft Genomics 查詢，您可能必須與相關聯的頂端。 如需有關 Microsoft Genomics 服務的詳細資訊，請參閱[什麼是 Microsoft Genomics？](overview-what-is-genomics.md) 


## <a name="what-is-the-sla-for-microsoft-genomics"></a>Microsoft Genomics 的 SLA 是什麼？
我們保證 99.9%的時間 Microsoft Genomics 服務將可接收工作流程應用程式開發介面的要求。 如需詳細資訊，請參閱 [SLA](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/)。

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>如何確實 Microsoft Genomics 的使用方式顯示在 我的帳單上？
Microsoft Genomics 帳單根據 gigabases 處理每個工作流程數目。 如需詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/details/genomics/)。


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>可以在哪裡找到的所有可能的命令和引數清單`msgen`用戶端？
您可以執行，以取得可用命令與引數的完整清單`msgen help`。 如果沒有其他引數提供，它會顯示一份可用的說明 」 章節，各供一個`submit`， `list`， `cancel`，和`status`。 若要取得特定命令的說明，請輸入`msgen help command`; 例如，`msgen help submit`列出所有送出選項。

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>什麼是最常使用的命令`msgen`用戶端嗎？
最常使用的命令是引數`msgen`包含用戶端： 

 |**命令**          |  **欄位描述** |
 |:--------------------|:-------------         |
 |`list`               |傳回一份您提交的工作。 對於引數，請參閱`msgen help list`。  |
 |`submit`             |提交服務要求工作流程。 對於引數，請參閱`msgen help submit`。|
 |`status`             |傳回所指定的工作流程狀態`--workflow-id`。 另請參閱`msgen help status`。 |
 |`cancel`             |傳送要求以取消指定工作流程的處理`--workflow-id`。 另請參閱`msgen help cancel`。 |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>哪裡取得的值`--api-url-base`？
前往 Azure 入口網站，並開啟 Genomics 帳戶 頁面。 在下**管理**標題之下，選擇**存取金鑰**。 您會發現 API URL 和存取金鑰。

## <a name="where-do-i-get-the-value-for---access-key"></a>哪裡取得的值`--access-key`？
前往 Azure 入口網站，並開啟 Genomics 帳戶 頁面。 在下**管理**標題之下，選擇**存取金鑰**。 您會發現 API URL 和存取金鑰。

## <a name="why-do-i-need-two-access-keys"></a>為什麼需要兩個存取金鑰？
如果您想要更新 （重新建立），需要兩個存取金鑰它們，而不會中斷服務的使用方式。 例如，您要更新的第一個索引鍵。 在此情況下，您可以切換所有新的工作流程使用的第二個索引鍵。 使用第一個索引鍵已在執行工作流程都完成，然後等候。 唯有如此，更新金鑰。

## <a name="do-you-save-my-storage-account-keys"></a>儲存我的儲存體帳戶金鑰？
儲存體帳戶金鑰用來建立 Microsoft Genomics 服務讀取您輸入的檔案並寫入輸出檔的短期存取權杖。 預設 token 持續時間是 48 小時。 Token 持續時間可以變更與`-sas/--sas-duration`送出命令; 選項的值是以小時為單位。

## <a name="what-genome-references-can-i-use"></a>哪些 genome 參考我可以使用？

支援這些參考：
 |參考              | 值`-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 （沒有 alt 分析） | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>如何設定我的命令列引數格式為組態檔？ 

msgen 了解以下列格式的組態檔：
* 從索引鍵以冒號分隔的值做為索引鍵-值組提供所有選項。
空白字元會被忽略。
* 行開頭`#`都會被忽略。
* 完整格式的任何命令列引數可以轉換成索引鍵，移除其前置連字號，並取代連字號與底線的文字之間。 以下是某些轉換範例：

 |命令列引數            | 設定檔案行 |
 |:-------------                   |:-------------                 |
 |`-u/--api-url-base https://url`  | *api_url_base:https://url*    |
 |`-k/--access-key KEY`            | *access_key:KEY*              |      
 |`-pa/--process-args R=B37m1`     | *process_args:R-b37m1*        |  

## <a name="next-steps"></a>後續步驟

若要開始使用 Microsoft Genomics 使用下列資源：
- 開始透過 Microsoft Genomics 服務執行您的第一個工作流程。 [透過 Microsoft Genomics 服務執行的工作流程](quickstart-run-genomics-workflow-portal.md)
- 由 Microsoft Genomics 服務送出處理資料：[配對 FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [多個 FASTQ 或 BAM](quickstart-input-multiple.md) 

