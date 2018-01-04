---
title: "若要監視健全狀況狀態使用負載平衡器自訂探查 |Microsoft 文件"
description: "了解如何使用 Azure 負載平衡器的自訂探查，來監視負載平衡器後方的執行個體"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 266132d8cbb6f9922ce7b49759981132c2c17f47
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2017
---
# <a name="understand-load-balancer-probes"></a>了解負載平衡器探查

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Azure 負載平衡器提供了使用探查來監視伺服器執行個體健全狀況的功能。 當探查無法回應時，負載平衡器會停止傳送新的連線至狀況不良的執行個體。 現有的連線不會受到影響，而新的連線會傳送到狀況良好的執行個體。

雲端服務角色 (背景工作角色和 Web 角色) 會使用客體代理程式進行探查監視。 當您使用負載平衡器後方的 Vm 時，必須設定 TCP 或 HTTP 自訂探查。

## <a name="understand-probe-count-and-timeout"></a>了解探查計數及逾時

探查行為取決於︰

* 允許執行個體標示為已啟動的成功探查數目。
* 導致執行個體標示為已關閉的失敗探查數目。

SuccessFailCount 中設定的逾時和頻率值會決定是否要執行或未執行確認執行個體。 在 Azure 入口網站中，逾時設定為頻率值的兩倍。

端點 (也就是負載平衡集) 的所有負載平衡執行個體的探查設定必須相同。 您不能有特定端點組合的相同託管服務中的每個角色執行個體或 VM 不同的探查設定。 例如，每個執行個體必須具有相同的本機連接埠和逾時。

> [!IMPORTANT]
> 負載平衡器探查會使用 IP 位址 168.63.129.16。 這個公用 IP 位址可促進自備 IP Azure 虛擬網路案例中內部平台資源的通訊。 虛擬的公用 IP 位址 168.63.129.16 用於所有區域，而不會變更。 建議您在任何本機防火牆原則中允許此 IP 位址。 它不應被視為安全性風險，因為只有內部 Azure 平台可以從該位址取得訊息來源。 如果您不允許此 IP 位址，在您的防火牆原則，在各種狀況中發生未預期的行為。 行為，包括設定 168.63.129.16 的相同 IP 位址範圍，並重複的 IP 位址。

## <a name="learn-about-the-types-of-probes"></a>深入了解探查類型

### <a name="guest-agent-probe"></a>客體代理程式探查

客體代理程式探查僅適用於 Azure 雲端服務。 負載平衡器會利用在 VM 內部的客體代理程式。 然後會接聽並回應 HTTP 200 OK 回應時，才執行個體處於就緒狀態。 （其他的狀態為忙碌、 回收、 或停止）。

如需詳細資訊，請參閱[健全狀況探查設定服務定義檔 (csdef)](https://msdn.microsoft.com/library/azure/ee758710.aspx)或[開始建立雲端服務公用負載平衡器](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services)。

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>什麼原因會讓客體代理程式探查將執行個體標示為狀況不良？

如果客體代理程式無法以 HTTP 200 OK 回應，負載平衡器會將標示為沒有回應的執行個體。 此外，它則會停止將流量傳送到該執行個體。 負載平衡器會繼續 ping 執行個體。 如果客體代理程式以 HTTP 200 回應，則負載平衡器會再次傳送流量到該執行個體。

當您使用 web 角色時，您的網站程式碼通常會執行的 w3wp.exe，不受 Azure 網狀架構或客體代理程式。 W3wp.exe （例如，HTTP 500 回應） 中的失敗不被回報給客體代理程式。 因此，負載平衡器不接受該執行個體退出循環。

### <a name="http-custom-probe"></a>HTTP 自訂探查

HTTP 自訂探查會覆寫預設的客體代理程式探查。 您可以建立您自己的自訂邏輯，以判斷角色執行個體的健全狀況。 根據預設，負載平衡器會每隔 15 秒探查您的端點。 執行個體視為處於負載平衡器的輪替循環，如果逾時期間內回應 HTTP 200。 逾時期限是預設 31 秒。

HTTP 自訂探查很有用，如果您想要實作您自己的邏輯，以便從負載平衡器的輪替循環中移除執行個體。 例如，您可能會決定移除執行個體，如果它超過 90%的 CPU，並傳回-200 狀態。 如果您有使用 w3wp.exe 的 web 角色，您也會取得自動監視您的網站。 在網站上的程式碼中的失敗會傳回非 200 狀態負載平衡器探查。

> [!NOTE]
> HTTP 自訂探查僅支援相對路徑和 HTTP 通訊協定。 不支援 HTTPS。

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>什麼原因會讓 HTTP 自訂探查將執行個體標示為狀況不良？

* HTTP 應用程式傳回 200 以外的 HTTP 回應碼 (例如，403、404 或 500)。 此正值通知警示您將立即停止服務應用程式執行個體。
* HTTP 伺服器不會完全回應的逾時期限之後。 根據設定的逾時值，多個探查要求可能會回答探查取得標示為不在執行之前 （也就是前 SuccessFailCount 探查傳送）。
* 伺服器會透過 TCP 重設關閉連線。

### <a name="tcp-custom-probe"></a>TCP 自訂探查

TCP 自訂探查起始的連線，藉由執行三向交握已定義的連接埠。

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>什麼原因會讓 TCP 自訂探查將執行個體標示為狀況不良？

* TCP 伺服器不會完全回應的逾時期限之後。 當探查標示為非執行中時，取決於失敗探查的數目，在探查標示為非執行中之前，這些要求設定為未獲得回應。
* 探查會從角色執行個體接收 TCP 重設。

如需如何設定 HTTP 健全狀況探查或 TCP 探查的詳細資訊，請參閱[開始使用 PowerShell 建立公用負載平衡器在資源管理員](load-balancer-get-started-internet-arm-ps.md)。

## <a name="add-healthy-instances-back-into-the-load-balancer-rotation"></a>加入負載平衡器的輪替循環回到狀況良好的執行個體

TCP 和 HTTP 探查於下列狀況時會視為狀況良好，並將角色執行個體標示為狀況良好：

* 負載平衡器在 VM 第一次開機時會取得正面探查。
* SuccessFailCount （稍早所述） 的數字定義成功探查標示為狀況良好的角色執行個體所需的值。 如果已移除角色執行個體，成功且連續的探查數目必須大於或等於 SuccessFailCount 的值才能將角色執行個體標示為執行中。

> [!NOTE]
> 如果角色執行個體的健全狀況波動的情況，負載平衡器的等候時間之前它會將角色執行個體放入狀況良好的狀態。 此額外的等候時間會保護使用者和基礎結構，並是刻意設計的原則。

## <a name="use-log-analytics-for-a-load-balancer"></a>使用負載平衡器的記錄分析

您可以使用[記錄分析](load-balancer-monitor-log.md)檢查負載平衡器探查健全狀況狀態和探查計數。 Power BI 或 Azure Operational Insights 可以使用記錄提供負載平衡器健全狀況狀態相關的統計資料。
