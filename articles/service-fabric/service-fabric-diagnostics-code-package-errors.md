---
title: Azure Service Fabric 診斷程式碼封裝錯誤 |Microsoft Docs
description: 了解如何使用 Azure Service Fabric 的常見程式碼套件錯誤進行疑難排解
services: service-fabric
documentationcenter: .net
author: grzuber
manager: gkhanna
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 235952388d2c044cc141b3020c67944c4250ea3d
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276947"
---
# <a name="diagnose-common-code-package-errors-with-service-fabric"></a>診斷與 Service Fabric 的常見程式碼封裝錯誤

這篇文章說明如何能為意外終止的程式碼封裝，並提供深入了解可能的原因，常見的錯誤碼，以及可能可以解決這個問題的疑難排解步驟。

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>何時未處理序或容器非預期地終止？

當 Service Fabric 會收到要求時啟動的程式碼封裝時，它會開始準備的環境，根據在應用程式和服務資訊清單中設定的組態選項的本機系統上。 下列準備工作可能包括保留網路端點或資源、 設定防火牆規則，或設定資源控管條件約束。 一旦已正確設定環境，Service Fabric 會嘗試啟動程式碼套件。 此步驟中會被視為成功，如果作業系統或容器的執行階段可讓您報告的處理序或容器已成功啟動。 如果啟用成功，您應該會看到指出 SFX 中的健康狀態訊息

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

一旦程式碼封裝已順利上線，Service Fabric 就會開始監視它的存留期。 到目前為止，處理序或容器可能會終止任何時刻、 基於多種原因所造成。 它可能無法初始化 DLL 時，作業系統可能已用盡桌面堆積空間等等。如果您的程式碼套件終止，您應該會看到指出 SFX 中的健康狀態訊息

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

在此健全狀況的事件，是訊息是唯一提供的程序/容器並結束原因的線索，並已產生該堆疊的任何層級所提供的結束程式碼。 很難了解此結束代碼相關，例如，發生作業系統錯誤，是.NET 的問題，還是由您的程式碼所引發。 如此一來，這個文件可以用做為起點來診斷終止結束代碼的來源和可能的解決方案，記住這些是常見案例的一般解決方案，而且可能無法套用至錯誤您所見。

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>如何分辨 Service Fabric 是否終止我的程式碼套件？

Service Fabric 可能是負責終止程式碼封裝以進行各種不同的原因。 比方說，它可能會決定將程式碼套件放在另一個節點進行負載平衡之用。 您可以分辨是否您程式碼封裝已結束，由 Service Fabric 是否您看到任何結束代碼下, 表中：

>[!NOTE]
> 如果您的處理序/容器終止的結束代碼不是下表中，Service Fabric 不負責終止它。

結束代碼 | 描述
--------- | -----------
7147 | 這些錯誤碼會指出，Service Fabric 正常關閉程序/容器傳送 Ctrl + C 訊號。
7148 | 這些錯誤碼會指出，Service Fabric 會終止處理序/容器。 有時候，這個錯誤碼可能表示處理程序/容器，未傳送 Ctrl + C 訊號，因此它必須要終止之後及時回應。


## <a name="other-common-error-codes-and-their-potential-fixes"></a>其他常見的錯誤代碼和可能的修正

結束代碼 | 十六進位值 | 簡短描述 | 根本原因 | 可能的修正程式
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | 此錯誤可能表示電腦已執行桌面堆積空間不足。 這個的原因是如果您有大量的屬於您在節點上執行的應用程式的程序，尤其是可能的。 | 如果您的程式不建置來回應 Ctrl + C 訊號，您可以啟用在叢集資訊清單中的"EnableActivateNoWindow 」 設定。 啟用此設定表示您的程式碼套件會執行但沒有 GUI 視窗，並不會收到 Ctrl + C 訊號，但會減少每個處理序耗用的桌面堆積空間數量。 如果您的程式碼套件需要接收 Ctrl + C 訊號，您可以增加節點的桌面堆積的大小。
3762504530 | 0xe0434352 | N/A | 這個值是從 managed 程式碼 (也就是.NET) 處理的例外狀況的錯誤碼。 | 如果您發現這個結束代碼，則表示您的應用程式引發的例外狀況保持未處理，而且結束處理程序。 偵錯您的應用程式記錄和傾印應該判斷造成錯誤的第一個步驟。

## <a name="next-steps"></a>後續步驟

* 深入了解[診斷其他常見案例](service-fabric-diagnostics-common-scenarios.md)
* Azure 監視器記錄檔和它，請閱讀所提供的功能更詳細了解[什麼是 Azure 監視器記錄檔？](../operations-management-suite/operations-management-suite-overview.md)
* 深入了解 Azure 監視器記錄檔[警示](../log-analytics/log-analytics-alerts.md)來協助偵測與診斷。
* 熟悉[記錄搜尋和查詢](../log-analytics/log-analytics-log-searches.md)作為 Azure 監視器記錄的一部分提供的功能
