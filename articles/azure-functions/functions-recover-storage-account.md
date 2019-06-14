---
title: 如何針對無法連線至 Azure Functions 執行階段的情形進行疑難排解。
description: 了解如何針對無效的儲存體帳戶進行疑難排解。
services: functions
documentationcenter: ''
author: alexkarcher-msft
manager: cfowler
editor: ''
ms.service: azure-functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 6057fa52cd2f1e9b9fd525723f96ab66983fb5d4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "61020293"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>如何針對「無法連線至函式執行階段」的情形進行疑難排解


## <a name="error-text"></a>錯誤文字
本文件的目的是要針對 Functions 入口網站中所顯示的下列錯誤進行疑難排解。

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

### <a name="summary"></a>總結
當 Azure Functions 執行階段無法啟動時，就會發生此問題。 之所以會發生這個錯誤，最常見的原因是函式應用程式無法存取其儲存體帳戶。 [在此深入了解儲存體帳戶需求](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)

### <a name="troubleshooting"></a>疑難排解
我們會逐步說明四種最常見的錯誤案例、如何加以識別，以及如何解決每個案例。

1. 儲存體帳戶已刪除
1. 儲存體帳戶的應用程式設定已刪除
1. 儲存體帳戶的認證無效
1. 無法存取儲存體帳戶
1. 每日執行配額已滿

## <a name="storage-account-deleted"></a>儲存體帳戶已刪除

每個函式應用程式都需要有儲存體帳戶才能運作。 如果該帳戶已刪除，函式就不會運作。

### <a name="how-to-find-your-storage-account"></a>如何尋找儲存體帳戶

藉由在應用程式設定中查閱儲存體帳戶名稱來開始。 `AzureWebJobsStorage` 或 `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 會包含包裝在連接字串中的儲存體帳戶名稱。 若要了解更多細節，請參閱[這裡的應用程式設定參考](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)

在 Azure 入口網站中搜尋儲存體帳戶，以了解其是否仍存在。 如果已遭刪除，您就必須重新建立儲存體帳戶，並取代儲存體連接字串。 函式程式碼將會遺失，所以您必須重新部署一次。

## <a name="storage-account-application-settings-deleted"></a>儲存體帳戶的應用程式設定已刪除

在上一個步驟中，如果您沒有儲存體帳戶連接字串，則表示其可能已遭到刪除或覆寫。 使用部署位置或 Azure Resource Manager 指令碼來設定應用程式設定時，最常會刪除應用程式設定。

### <a name="required-application-settings"></a>必要的應用程式設定

* 必要項
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* 取用方案函式所必備
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

[在此了解這些應用程式設定](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

### <a name="guidance"></a>指引

* 請勿對上述任何設定核取 [位置設定]。 當您交換部署位置時，函式將會中斷。
* 請勿修改這些設定自動化部署的一部分。
* 在建立時，必須提供這些設定，且設定必須有效。 即使事後新增設定，不包含這些設定的自動化部署仍會導致應用程式無法運作。

## <a name="storage-account-credentials-invalid"></a>儲存體帳戶的認證無效

如果您重新產生儲存體金鑰，則必須更新上述儲存體帳戶連接字串。 [在此深入了解儲存體金鑰管理](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)

## <a name="storage-account-inaccessible"></a>無法存取儲存體帳戶

函式應用程式必須能夠存取儲存體帳戶。 導致函式無法存取儲存體帳戶的常見問題包括：

* 部署到 App Service 環境的函式應用程式沒有正確的網路規則，而無法允許流量進出儲存體帳戶
* 儲存體帳戶的防火牆已啟用，且未設定為允許流量進出函式。 [在此深入了解儲存體帳戶防火牆組態](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="daily-execution-quota-full"></a>每日執行配額已滿

如果您已設定每日執行配額，則會暫時停用您的函數應用程式，且許多入口網站控制項將變成無法使用。 

* 若要驗證，請在入口網站中檢查開啟的 [平台功能] > [函數應用程式設定]。 如果您已超過配額，就會看到下列訊息
    * `The Function App has reached daily usage quota and has been stopped until the next 24 hours time frame.`
* 請移除配額，然後重新啟動應用程式來解決此問題。

## <a name="next-steps"></a>後續步驟

現在，您的函式應用程式已恢復運作，接下來請看看我們的快速入門及開發人員參考，以便讓其重新啟動並執行！

* [建立您的第一個Azure Functions](functions-create-first-azure-function.md)  
  直接進入正題並使用 Azure Functions 快速入門建立您的第一個函數。 
* [Azure Functions 開發人員參考](functions-reference.md)  
  提供更多有關 Azure Functions 執行階段的技術資訊，以及可供撰寫函數程式碼及定義觸發程序和繫結時參考。
* [測試 Azure Functions](functions-test-a-function.md)  
  說明可用於測試函式的各種工具和技巧。
* [如何調整 Azure 函式](functions-scale.md)  
  討論 Azure Functions 可用的服務方案，包括使用情況主控方案，以及如何選擇正確的方案。 
* [深入了解 Azure App Service](../app-service/overview.md)  
  Azure Functions 會利用 Azure App Service 來執行核心功能，例如部署、環境變數和診斷。 
