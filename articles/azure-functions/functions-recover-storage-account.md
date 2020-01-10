---
title: 如何針對無法連線至 Azure Functions 執行階段的情形進行疑難排解。
description: 了解如何針對無效的儲存體帳戶進行疑難排解。
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 40037252ddf8e505ae7fe734813d598e7de96336
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834224"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>如何針對「無法連線至函式執行階段」的情形進行疑難排解


## <a name="error-text"></a>錯誤文字
本文的目的是要在函式入口網站中顯示下列錯誤時進行疑難排解。

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

### <a name="summary"></a>摘要
當 Azure Functions 執行階段無法啟動時，就會發生此問題。 之所以會發生這個錯誤，最常見的原因是函式應用程式無法存取其儲存體帳戶。 [在此深入了解儲存體帳戶需求](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)

### <a name="troubleshooting"></a>疑難排解
我們會逐步說明四種最常見的錯誤案例、如何加以識別，以及如何解決每個案例。

1. 儲存體帳戶已刪除
1. 儲存體帳戶的應用程式設定已刪除
1. 儲存體帳戶的認證無效
1. 無法存取儲存體帳戶
1. 每日執行配額已滿
1. 應用程式位於防火牆後方


## <a name="storage-account-deleted"></a>儲存體帳戶已刪除

每個函式應用程式都需要有儲存體帳戶才能運作。 如果該帳戶已刪除，函式就不會運作。

### <a name="how-to-find-your-storage-account"></a>如何尋找儲存體帳戶

藉由在應用程式設定中查閱儲存體帳戶名稱來開始。 `AzureWebJobsStorage` 或 `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 會包含包裝在連接字串中的儲存體帳戶名稱。 如需詳細資訊，請參閱[這裡的應用程式設定參考](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)。

在 Azure 入口網站中搜尋儲存體帳戶，以了解其是否仍存在。 如果已遭刪除，您就必須重新建立儲存體帳戶，並取代儲存體連接字串。 函式程式碼將會遺失，所以您必須重新部署一次。

## <a name="storage-account-application-settings-deleted"></a>儲存體帳戶的應用程式設定已刪除

在上一個步驟中，如果您沒有儲存體帳戶連接字串，可能會遭到刪除或覆寫。 使用部署位置或 Azure Resource Manager 指令碼來設定應用程式設定時，最常會刪除應用程式設定。

### <a name="required-application-settings"></a>必要的應用程式設定

* 必要項
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* 取用方案函式所必備
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

請[在這裡閱讀這些應用程式設定的相關資訊](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)。

### <a name="guidance"></a>指導方針

* 請勿對上述任何設定核取 [位置設定]。 當您交換部署位置時，函式將會中斷。
* 請勿將這些設定修改為自動化部署的一部分。
* 在建立時，必須提供這些設定，且設定必須有效。 即使事後新增設定，不包含這些設定的自動化部署仍會導致應用程式無法運作。

## <a name="storage-account-credentials-invalid"></a>儲存體帳戶的認證無效

如果您重新產生儲存體金鑰，則必須更新上述儲存體帳戶連接字串。 [在這裡閱讀更多關於儲存體金鑰管理的資訊](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)。

## <a name="storage-account-inaccessible"></a>無法存取儲存體帳戶

函式應用程式必須能夠存取儲存體帳戶。 導致函式無法存取儲存體帳戶的常見問題包括：

* 部署到 App Service 環境的函式應用程式沒有正確的網路規則，而無法允許流量進出儲存體帳戶
* 儲存體帳戶的防火牆已啟用，且未設定為允許流量進出函式。 [在此深入了解儲存體帳戶防火牆組態](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="daily-execution-quota-full"></a>每日執行配額已滿

如果您已設定每日執行配額，則會暫時停用您的函數應用程式，且許多入口網站控制項將變成無法使用。 

* 若要確認，請在入口網站中開啟 平臺功能 > 函數應用程式設定。 如果您超過配額，則會看到下列訊息：
    * `The Function App has reached daily usage quota and has been stopped until the next 24 hours time frame.`
* 請移除配額，然後重新啟動應用程式來解決此問題。

## <a name="app-is-behind-a-firewall"></a>應用程式位於防火牆後方

如果您的函式應用程式[裝載于內部負載平衡的 App Service 環境](../app-service/environment/create-ilb-ase.md)中，而且設定為封鎖輸入網際網路流量，或設定了[輸入 IP 限制](functions-networking-options.md#inbound-ip-restrictions)以封鎖網際網路存取，則無法連線到您的函式執行時間。 Azure 入口網站會直接呼叫執行中的應用程式來提取函式清單，也會對 KUDU 端點進行 HTTP 呼叫。 [`Platform Features`] 索引標籤下的平台層級設定仍然可以使用。

* 若要驗證您的 ASE 設定，請流覽至 ASE 所在子網的 NSG，並驗證輸入規則，以允許來自您正在存取應用程式之電腦的公用 IP 流量。 您也可以從連線至執行應用程式之虛擬網路的電腦，或在虛擬網路中執行的虛擬機器，使用入口網站。 [如需輸入規則設定的詳細資訊，請參閱這裡](https://docs.microsoft.com/azure/app-service/environment/network-info#network-security-groups)

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
