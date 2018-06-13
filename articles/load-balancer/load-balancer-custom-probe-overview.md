---
title: 使用 Load Balancer 自訂探查來監視健全狀況狀態 | Microsoft Docs
description: 了解如何使用 Azure 負載平衡器的自訂探查，來監視負載平衡器後方的執行個體
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/8/2018
ms.author: kumud
ms.openlocfilehash: 0aab72fdf48589a72707ae87f90af11f65f35088
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
ms.locfileid: "30176783"
---
# <a name="understand-load-balancer-probes"></a>了解 Load Balancer 探查

Azure Load Balancer 會使用健康情況探查，來決定哪一個後端集區執行個體應該接收新流程。 當健康情況探查失敗時，Load Balancer 就會停止將新流程傳送到個別狀況不良的執行個體，而該執行個體上的現有流程不會受到影響。  當所有後端集區執行個體探查失敗時，所有現有流程都將在後端集區中的所有執行個體上逾時。

雲端服務角色 (背景工作角色和 Web 角色) 會使用客體代理程式進行探查監視。 當您在 Load Balancer 後方使用 VM 時，必須設定 TCP 或 HTTP 自訂健康情況探查。

## <a name="understand-probe-count-and-timeout"></a>了解探查計數及逾時

探查行為取決於︰

* 允許執行個體標示為已啟動的成功探查數目。
* 導致執行個體標示為已關閉的失敗探查數目。

在 SuccessFailCount 中設定的逾時和頻率值會決定執行個體是否確認為執行中或非執行中。 在 Azure 入口網站中，逾時設定為頻率值的兩倍。

端點 (也就是負載平衡集) 的所有負載平衡執行個體的探查設定必須相同。 針對位在相同託管服務特定端點組合的各個角色執行個體或 VM，您不能有不同的探查設定。 例如，每個執行個體必須具有相同的本機連接埠和逾時。

> [!IMPORTANT]
> 負載平衡器探查使用 IP 位址 168.63.129.16。 這個公用 IP 位址可促進自備 IP Azure 虛擬網路案例中內部平台資源的通訊。 虛擬公用 IP 位址 168.63.129.16 會使用於所有區域中，且不會變更。 建議您在任何本機防火牆原則中允許此 IP 位址。 它不應被視為安全性風險，因為只有內部 Azure 平台可以從該位址取得訊息來源。 如果您的防火牆原則中不允許此 IP 位址，則會發生各種未預期的行為。 這類行為包括設定 168.63.129.16 相同的 IP 位址範圍，以及重複的 IP 位址。

## <a name="learn-about-the-types-of-probes"></a>深入了解探查類型

### <a name="guest-agent-probe"></a>客體代理程式探查

客體代理程式探查僅供 Azure 雲端服務使用。 Load Balancer 會利用 VM 內部的客體代理程式。 然後，只有在執行個體處於就緒狀態時，它才會接聽並以「HTTP 200 確定」回應。 (其他狀態為忙碌、正在回收或正在停止。)

如需詳細資訊，請查看[設定適用於健全狀況探查的服務定義檔案 (csdef)](https://msdn.microsoft.com/library/azure/ee758710.aspx) 或[開始為雲端服務建立公用負載平衡器](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services)。

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>什麼原因會讓客體代理程式探查將執行個體標示為狀況不良？

如果客體代理程式無法以「HTTP 200 確定」回應，則負載平衡器會將執行個體標示為沒有回應。 然後，它會停止傳送流量至該執行個體。 負載平衡器會繼續 ping 執行個體。 如果客體代理程式以 HTTP 200 回應，則負載平衡器會再次傳送流量到該執行個體。

使用 Web 角色時，網站程式碼通常會在不受 Azure 網狀架構或客體代理程式監視的 w3wp.exe 中執行。 在 w3wp.exe 中的失敗 (例如 HTTP 500 回應) 不會向客體代理程式報告。 因此，負載平衡器不會將該執行個體從循環中剔除。

### <a name="http-custom-probe"></a>HTTP 自訂探查

HTTP 自訂探查會覆寫預設客體代理程式探查。 您可以用來建立自己的自訂邏輯，以判斷角色執行個體的健全狀況。 根據預設，負載平衡器會每隔 15 秒探查您的端點。 如果執行個體在逾時期限內以 HTTP 200 回應，它就會被視為處於負載平衡器循環中。 逾時期限預設為 31 秒。

HTTP 自訂探查很適合在您想要實作自己的邏輯，以從負載平衡器循環中移除執行個體時使用。 例如，如果執行個體使用超過 90% CPU，並傳回非 200 狀態，您可以決定移除執行個體。 如果您有使用 w3wp.exe 的 Web 角色，您也能讓網站的監視自動化。 網站程式碼中的失敗，會將非 200 的狀態傳回給負載平衡器探查。

> [!NOTE]
> HTTP 自訂探查僅支援相對路徑和 HTTP 通訊協定。 不支援 HTTPS。

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>什麼原因會讓 HTTP 自訂探查將執行個體標示為狀況不良？

* HTTP 應用程式傳回 200 以外的 HTTP 回應碼 (例如，403、404 或 500)。 此正值通知警示您應立即將應用程式執行個體從服務中剔除。
* HTTP 伺服器在逾時期限之後完全沒有回應。 根據設定的逾時值，在探查標示為非執行中之前 (也就是說，在傳送 SuccessFailCount 探查之前)，多個探查要求並未獲得回應。
* 伺服器會透過 TCP 重設關閉連線。

### <a name="tcp-custom-probe"></a>TCP 自訂探查

TCP 自訂探查透過利用定義的連接埠執行三向交握來初始化連線。

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>什麼原因會讓 TCP 自訂探查將執行個體標示為狀況不良？

* TCP 伺服器在逾時期限之後完全沒有回應。 當探查標示為非執行中時，取決於失敗探查的數目，在探查標示為非執行中之前，這些要求設定為未獲得回應。
* 探查會從角色執行個體接收 TCP 重設。

如需有關如何設定 HTTP 健全狀況探查或 TCP 探查的詳細資訊，請參閱[開始使用 PowerShell 在 Resource Manager 中建立公用負載平衡器](load-balancer-get-started-internet-arm-ps.md)。

## <a name="add-healthy-instances-back-into-the-load-balancer-rotation"></a>將狀況良好的執行個體重新加入負載平衡器循環

TCP 和 HTTP 探查於下列狀況時會視為狀況良好，並將角色執行個體標示為狀況良好：

* 負載平衡器在 VM 第一次開機時會取得正面探查。
* SuccessFailCount 的數目 (如上所述) 可定義將角色執行個體標示為狀況良好所需的成功探查值。 如果已移除角色執行個體，成功且連續的探查數目必須大於或等於 SuccessFailCount 的值才能將角色執行個體標示為執行中。

> [!NOTE]
> 如果角色執行個體的健全狀況出現變動，負載平衡器會先等候較長的時間，才將角色執行個體重新放回狀況良好的狀態。 此額外等候時間可保護使用者和基礎結構，且為刻意設計的原則。

## <a name="use-log-analytics-for-a-load-balancer"></a>使用負載平衡器的記錄分析

您可以使用[記錄分析](load-balancer-monitor-log.md)來檢查負載平衡器探查健全狀況和探查計數。 記錄可以與 Power BI 或 Azure Operation Insights 搭配使用，以提供負載平衡器健康狀態。
