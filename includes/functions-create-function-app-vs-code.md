---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2018
ms.author: glenga
ms.openlocfilehash: f9b853f0e86fc298ab35421928492ba813d85827
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444603"
---
## <a name="create-an-azure-functions-project"></a>使用函式來建立函式專案 

Visual Studio Code 中的 Azure Functions 專案範本會建立可發佈至 Azure 中函式應用程式的專案。 函式應用程式可讓您將多個函式群組為邏輯單位，以便您管理、部署和共用資源。

1. 在 Visual Studio Code 中按 F1 以開啟命令選擇區。 在命令選擇區中，搜尋並選取 `Azure Functions: Create new project...`。

1. 選擇您專案工作區的目錄位置，然後選擇 [選取]  。

    > [!NOTE]
    > 這些步驟主要設計為在工作區以外的地方完成。 在此案例中，請勿選取屬於工作區的專案資料夾。

1. 按照提示提供下列資訊：

    | Prompt | 值 | 說明 |
    | ------ | ----- | ----------- |
    | 為您的函式應用程式專案選取語言 | C# 或 JavaScript | 這篇文章支援 C# 和 JavaScript。 針對 Python，請參閱此 [Python 文章](https://code.visualstudio.com/docs/python/tutorial-azure-functions)，針對 PowerShell，請參閱此 [PowerShell 文章](../articles/azure-functions/functions-create-first-function-powershell.md)。  |
    | 選取您專案第一個函式的範本 | HTTP 觸發程序 | 在新的函式應用程式中建立由 HTTP 觸發的函式。 |
    | 提供函式名稱 | HttpTrigger | 按 Enter 鍵以使用預設名稱。 |
    | 提供命名空間 | My.Functions | (僅限 C#) C# 類別程式庫必須擁有命名空間。  |
    | 授權層級 | 函式 | 需要[函式金鑰](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys)以呼叫函式的 HTTP 端點。 |
    | 選取您要如何開啟專案 | 新增到工作區 | 在目前的工作區中建立函式應用程式。 |

Visual Studio Code 會在新的工作區中建立函式應用程式專案。 此專案包含 [host.json](../articles/azure-functions/functions-host-json.md) 和 [local.settings.json](../articles/azure-functions/functions-run-local.md#local-settings-file) 組態檔，以及任何特定語言的專案檔。 

在函式應用程式專案的 HttpTrigger 資料夾中，也會建立由 HTTP 觸發的新函式。