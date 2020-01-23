---
title: 包含檔案
description: 包含檔案
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/31/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: ebcbe7e8af7e01a9b7e75282be4e4f4ceb80b806
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279525"
---
## <a name="run-the-function-locally"></a>在本機執行函式

Azure Functions Core Tools 可讓您在本機開發電腦上執行 Azure Functions 專案。

1. 若要測試您的函式，可在函式程式碼中設定中斷點，並按 F5 以啟動函式應用程式專案。 Core Tools 的輸出會顯示在**終端機**面板中。

1. 在**終端機**面板中，複製 HTTP 觸發函式的 URL 端點。 

    ![Azure 本機輸出](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. 將 HTTP 要求的 URL 貼到瀏覽器的網址列。 將查詢字串 `?name=<yourname>` 附加至此 URL 並執行要求。 觸及中斷點時，執行就會暫停。

1. 當您繼續此執行時，下圖顯示瀏覽器中對 GET 要求所做出的回應︰

    ![瀏覽器中的函式 localhost 回應](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. 若要停止偵錯，請按 Shift + F5。
